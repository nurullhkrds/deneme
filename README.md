private static final Pattern CHANNEL_TX_PATTERN =
        Pattern.compile("^[A-Za-z0-9/-]{1,64}$");

String txId = request.getHeader("channelTransactionId");

if (txId == null || txId.isBlank() || !CHANNEL_TX_PATTERN.matcher(txId).matches()) {
    response.setStatus(HttpStatus.BAD_REQUEST.value());
    response.getWriter().write("""
        {
          "code": "CHANNEL_TRANSACTION_ID_INVALID",
          "message": "Invalid channelTransactionId format"
        }
    """);
    return;
}
