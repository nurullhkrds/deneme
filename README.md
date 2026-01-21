NotBlank
@Size(min = 1, max = 32)
@Pattern(regexp = "^[0-9]+$")
private String subscriberNo;



@NotBlank
@Size(min = 1, max = 20)
@Pattern(regexp = "^[A-Za-z0-9_-]+$")
private String institutionCode;

@NotBlank
@Size(min = 1, max = 20)
@Pattern(regexp = "^[A-Za-z0-9_-]+$")
private String productCode;


@Component
public class ChannelTransactionIdFilter extends OncePerRequestFilter {

  private static final String HEADER = "channelTransactionId"; // sizde x-trace-id ise onu yaz
  private static final Pattern PATTERN = Pattern.compile("^[A-Za-z0-9_.-]{1,64}$");

  @Override
  protected void doFilterInternal(HttpServletRequest req, HttpServletResponse resp, FilterChain chain)
      throws IOException, ServletException {

    String id = req.getHeader(HEADER);

    // Zorunluysa:
    if (id == null || id.isBlank() || !PATTERN.matcher(id).matches()) {
      resp.setStatus(400);
      resp.setContentType("application/json");
      resp.getWriter().write("{\"code\":\"CHANNEL_TRANSACTION_ID_INVALID\",\"message\":\"Invalid channelTransactionId\"}");
      return;
    }

    // log correlation için (opsiyonel)
    MDC.put("channelTransactionId", id);
    try {
      chain.doFilter(req, resp);
    } finally {
      MDC.remove("channelTransactionId");
    }
  }
}
