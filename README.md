          
                // Yeni Uyarı Mesajı Ekleme
                if ("A".equals(active)) {
                    // Dekont 
               
                	 List<AutoTransactionReceiptModelDTO> receiptList=listData(Session.CORPORATE.getSessionValue(cc, String.class),Session.PRODUCT.getSessionValue(cc, String.class));
                    if (receiptList == null) {
                        MessagesUtil.addError("DEKONT tablosunda herhangi bir tanımınız yoktur.", events);
                        retval = false;
                    } else {
                    	List<String> requiredValues = new ArrayList<String>();
                	    requiredValues.add("DOVIZ");
                	    requiredValues.add("ISLEMTUTAR");
                	    requiredValues.add("KKMASRAF");
                	    requiredValues.add("BSMV");
                	    requiredValues.add("TOPLAMTUTAR");
                	    List<String> foundValues =  new ArrayList<String>();

                        for (AutoTransactionReceiptModelDTO field : receiptList) {
                            if (requiredValues.contains(field.getField())) {
                                foundValues.add(field.getField());
                            }
                        }

                        requiredValues.removeAll(foundValues);
                        if (!requiredValues.isEmpty()) {
                            MessagesUtil.addError(String.join(", ", requiredValues) + " alanları zorunludur.", events);
                            retval = false;
                        }
                    }

                }
