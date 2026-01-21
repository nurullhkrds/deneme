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

    private static final String APPLICATION_NAME = "PAYMENTS.BILL.bill-transaction";

    private static final String HEADER_CHANNEL_TRANSACTION_ID = "channelTransactionId";
    private static final String HEADER_CHANNEL_SESSION_ID = "channelSessionId";

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                    HttpServletResponse response,
                                    FilterChain filterChain) throws ServletException, IOException {

        String channelTransactionId = request.getHeader(HEADER_CHANNEL_TRANSACTION_ID);
        String channelSessionId = request.getHeader(HEADER_CHANNEL_SESSION_ID);

        // channelTransactionId ZORUNLU
        if (isBlank(channelTransactionId)) {
            writeValidationError(
                    response,
                    "channelTransactionId",
                    "channelTransactionId is required"
            );
            return;
        }

        if (!ValidationPatterns.CHANNEL_TRANSACTION_ID_PATTERN
                .matcher(channelTransactionId)
                .matches()) {

            writeValidationError(
                    response,
                    "channelTransactionId",
                    "channelTransactionId must contain only letters, digits, / and -"
            );
            return;
        }

        // channelSessionId opsiyonel ama doluysa kontrol edelim
        if (!isBlank(channelSessionId) &&
                !ValidationPatterns.CHANNEL_SESSION_ID_PATTERN.matcher(channelSessionId).matches()) {

            writeValidationError(
                    response,
                    "channelSessionId",
                    "channelSessionId has invalid format"
            );
            return;
        }

        // MDC (log correlation)
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

    private void writeValidationError(HttpServletResponse response,
                                      String field,
                                      String message) throws IOException {

        response.setStatus(HttpStatus.BAD_REQUEST.value());
        response.setContentType("application/json");
        response.setCharacterEncoding("UTF-8");

        response.getWriter().write("""
            {
              "exceptionData": {
                "applicationName": "%s",
                "errorCode": -998,
                "errorMessage": "validation error",
                "traceId": null,
                "errors": [
                  {
                    "field": "%s",
                    "message": "%s"
                  }
                ]
              },
              "parameters": {}
            }
            """.formatted(APPLICATION_NAME, field, message));
    }
}
