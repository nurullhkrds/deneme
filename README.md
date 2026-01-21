package com.yourcompany.security;

import com.yourcompany.validation.ValidationPatterns;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.slf4j.MDC;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;

@Component
public class ChannelHeadersValidationFilter extends OncePerRequestFilter {

    // Sizin header isimleriniz neyse birebir yaz
    public static final String HEADER_CHANNEL_TRANSACTION_ID = "channelTransactionId"; // veya "x-trace-id"
    public static final String HEADER_CHANNEL_SESSION_ID = "channelSessionId";         // veya "x-session-id"

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        String channelTransactionId = request.getHeader(HEADER_CHANNEL_TRANSACTION_ID);
        String channelSessionId = request.getHeader(HEADER_CHANNEL_SESSION_ID);

        // ZORUNLU diyorsan:
        if (isBlank(channelTransactionId)) {
            writeBadRequest(response, "CHANNEL_TRANSACTION_ID_REQUIRED", "channelTransactionId header is required");
            return;
        }
        if (!ValidationPatterns.CHANNEL_TRANSACTION_ID_PATTERN.matcher(channelTransactionId).matches()) {
            writeBadRequest(response, "CHANNEL_TRANSACTION_ID_INVALID", "Invalid channelTransactionId format");
            return;
        }

        // sessionId zorunlu mu? sizde zorunluysa aynı şekilde required yap.
        // Opsiyonel olsun istersen: sadece doluysa validate et:
        if (!isBlank(channelSessionId) &&
                !ValidationPatterns.CHANNEL_SESSION_ID_PATTERN.matcher(channelSessionId).matches()) {
            writeBadRequest(response, "CHANNEL_SESSION_ID_INVALID", "Invalid channelSessionId format");
            return;
        }

        // Log correlation (opsiyonel ama çok faydalı)
        MDC.put("channelTransactionId", channelTransactionId);
        if (!isBlank(channelSessionId)) {
            MDC.put("channelSessionId", channelSessionId);
        }

        try {
            filterChain.doFilter(request, response);
        } finally {
            MDC.remove("channelTransactionId");
            MDC.remove("channelSessionId");
        }
    }

    private boolean isBlank(String s) {
        return s == null || s.trim().isEmpty();
    }

    private void writeBadRequest(HttpServletResponse response, String code, String message) throws IOException {
        response.setStatus(HttpStatus.BAD_REQUEST.value());
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");
        response.getWriter().write("""
                {
                  "code": "%s",
                  "message": "%s"
                }
                """.formatted(code, message));
    }
}
