   // Yeni Uyarı Mesajı Ekleme
            if ("A".equals(active)) {
                // Dekont tablosunda kontrol yap
                ParamModelDTO[] slipFields = Session.SLIP_FIELDS.getSessionValue(cc, ParamModelDTO[].class);
                if (slipFields == null) {
                    MessagesUtil.addError("DEKONT tablosunda herhangi bir tanımınız yoktur.", events);
                    retval = false;
                } else {
                    Set<String> requiredValues = new HashSet<>(Arrays.asList("DOVIZ", "ISLEMTUTAR", "KKMASRAF", "BSMV", "TOPLAMTUTAR"));
                    Set<String> foundValues = new HashSet<>();

                    for (ParamModelDTO field : slipFields) {
                        if (requiredValues.contains(field.getId())) {
                            foundValues.add(field.getId());
                        }
                    }

                    requiredValues.removeAll(foundValues);
                    if (!requiredValues.isEmpty()) {
                        MessagesUtil.addError(String.join(", ", requiredValues) + " alanları zorunludur.", events);
                        retval = false;
                    }
                }

                MessagesUtil.addWarning("Aktif kolonunu 'Evet' olarak değiştirdiniz. Bu değişikliğin sonuçlarını kontrol ediniz.", events);
            }
