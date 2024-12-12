    private String lpadWithZeros(String input, int totalLength) {
        if (input == null) {
            return null;
        }
        if (input.length() > totalLength) {
            return input.substring(0, totalLength);
        }
        return String.format("%0" + totalLength + "d", Long.parseLong(input));
    }
