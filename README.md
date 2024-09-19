public enum EnumAccountingNarrative {

    PRODUCT(EnumAccountingNarrativeKey.PRODUCT.getValue(), Boolean.FALSE, null) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getInstitution().getProduct().getCode()), dto);
            }
            return nextChain(text, dto);
        }
    },

    INSTITUTION(EnumAccountingNarrativeKey.INSTITUTION.getValue(), Boolean.TRUE, PRODUCT) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getInstitution().getInstitutionCode()), dto);
            }
            return nextChain(text, dto);
        }
    },

    SUBSCRIBER_NO(EnumAccountingNarrativeKey.SUBSCRIBER_NO.getValue(), Boolean.TRUE, INSTITUTION) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getSubscriberNo()), dto);
            }
            return nextChain(text, dto);
        }
    },

    BILL_NO(EnumAccountingNarrativeKey.BILL_NO.getValue(), Boolean.TRUE, SUBSCRIBER_NO) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getBillNo()), dto);
            }
            return nextChain(text, dto);
        }
    },

    AMOUNT(EnumAccountingNarrativeKey.AMOUNT.getValue(), Boolean.TRUE, BILL_NO) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getPaymentAmount().toPlainString()), dto);
            }
            return nextChain(text, dto);
        }
    },

    CURRENCY(EnumAccountingNarrativeKey.CURRENCY.getValue(), Boolean.TRUE, AMOUNT) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getCurrency().getValue()), dto);
            }
            return nextChain(text, dto);
        }
    },

    NAME(EnumAccountingNarrativeKey.NAME.getValue(), Boolean.TRUE, CURRENCY) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getSubscriberName()), dto);
            }
            return nextChain(text, dto);
        }
    },

    DUE_DATE(EnumAccountingNarrativeKey.DUE_DATE.getValue(), Boolean.TRUE, NAME) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, DateUtils.formatLocalDate(dto.getProvisionDTO().getBillDueDate(), DateUtils.DATE_FORMAT_DD_MM_YYYY_WITH_SLASH)), dto);
            }
            return nextChain(text, dto);
        }
    },
    TERM(EnumAccountingNarrativeKey.TERM.getValue(), Boolean.TRUE, DUE_DATE) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getBillTerm()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_1(EnumAccountingNarrativeKey.ADDITIONAL_INFO_1.getValue(), Boolean.TRUE, TERM) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo1()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_2(EnumAccountingNarrativeKey.ADDITIONAL_INFO_2.getValue(), Boolean.TRUE, ADDITIONAL_INFO_1) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo2()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_3(EnumAccountingNarrativeKey.ADDITIONAL_INFO_3.getValue(), Boolean.TRUE, ADDITIONAL_INFO_2) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo3()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_4(EnumAccountingNarrativeKey.ADDITIONAL_INFO_4.getValue(), Boolean.TRUE, ADDITIONAL_INFO_3) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo4()), dto);
            }
            return nextChain(text, dto);
        }
    },

    ADDITIONAL_INFO_5(EnumAccountingNarrativeKey.ADDITIONAL_INFO_5.getValue(), Boolean.TRUE, ADDITIONAL_INFO_4) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo5()), dto);
            }
            return nextChain(text, dto);
        }
    },

    ADDITIONAL_INFO_6(EnumAccountingNarrativeKey.ADDITIONAL_INFO_6.getValue(), Boolean.TRUE, ADDITIONAL_INFO_5) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo6()), dto);
            }
            return nextChain(text, dto);
        }
    },

    ADDITIONAL_INFO_7(EnumAccountingNarrativeKey.ADDITIONAL_INFO_7.getValue(), Boolean.TRUE, ADDITIONAL_INFO_6) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo7()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_8(EnumAccountingNarrativeKey.ADDITIONAL_INFO_8.getValue(), Boolean.TRUE, ADDITIONAL_INFO_7) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo8()), dto);
            }
            return nextChain(text, dto);
        }
    },
    ADDITIONAL_INFO_9(EnumAccountingNarrativeKey.ADDITIONAL_INFO_9.getValue(), Boolean.TRUE, ADDITIONAL_INFO_8) {
        @Override
        public String getNarrative(String text, CreateAccountingDTO dto) {
            if (containsKey(text)) {
                return nextChain(replace(text, dto.getProvisionDTO().getAdditionalInfo9()), dto);
            }
            return nextChain(text, dto);
        }
    };

    private String value;


    private boolean checkNext;


    private EnumAccountingNarrative next;


    public boolean containsKey(String text) {
        return text.contains(value);
    }

    public String replace(String text, String value) {
        return value != null ? text.replace(this.value, value) : text.replace(this.value, "");
    }

    public String nextChain(String text, CreateAccountingDTO dto) {
        if (checkNext)
            return getNext().getNarrative(text, dto);
        return text;
    }

    public abstract String getNarrative(String text, CreateAccountingDTO dto);

    public static String createNarrative(String text, CreateAccountingDTO dto) {
        if (StringUtils.isBlank(text)) {
            StringBuilder stringBuilder = new StringBuilder();
            stringBuilder.append(dto.getProvisionDTO().getSubscriberNo());
            stringBuilder.append("-");
            return stringBuilder.toString();
        }
        return ADDITIONAL_INFO_9.getNarrative(text, dto);
    }


    private static Map<String, EnumAccountingNarrative> eMap;


    static {
        eMap = new TreeMap<>();
        for (EnumAccountingNarrative num : EnumAccountingNarrative.values()) {
            eMap.put(num.getValue(), num);
        }
    }


    EnumAccountingNarrative(String value, Boolean checkNext, EnumAccountingNarrative next) {
        this.value = value;
        this.checkNext = checkNext;
        this.next = next;
    }

    public String getValue() {
        return value;
    }


    public Boolean getDescription() {
        return checkNext;
    }


    public EnumAccountingNarrative getNext() {
        return next;
    }

}
