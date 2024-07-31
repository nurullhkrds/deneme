    @Test
    public void testToHarmoniSubscriberNo1() {
        SubsrciberNoPartResponseWebDTO dto1=new SubsrciberNoPartResponseWebDTO();
        dto1.setPartKey("1234");
        dto1.setPartNo(1);


        List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList = List.of(dto1);

        String result = mapper.toHarmoniSubscriberNo1(subscriberNoPartList);

        assertEquals("1234", result);
    }
org.opentest4j.AssertionFailedError: 
Expected :1234
Actual   :null
