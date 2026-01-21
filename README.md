
java.lang.NullPointerException: Cannot invoke "java.util.List.stream()" because "<local4>.returnMapDTOList" is null



    void insertReturnMap() {
        ReturnMapDTO returnMapDTO2 = new ReturnMapDTO();
        returnMapDTO2.setReturnMapCode("NEVSEHIR");
        returnMapDTO2.setBankReturnCode("00000");
        returnMapDTO2.setBankReturnText("SUCCESS");
        returnMapDTO2.setInstitutionReturnText("SUCCESS");
        returnMapDTO2.setInstitutionReturnCode("0000");
        returnMapDTO2.setReturnType(EnumReturnType.SUCCESS);
        addReturnMap(returnMapDTO2);
    }


       protected void addReturnMap(ReturnMapDTO returnMapDTO) {
        returnMapDTOList.add(returnMapDTO);
    }
