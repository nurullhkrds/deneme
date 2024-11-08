public static String validateAndTruncateInfo(String info) {
        if (info == null) {
            return null;
        }
        return info.length() <= 50 ? info : info.substring(0, 50);
    }
