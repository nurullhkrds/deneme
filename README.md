package com.ykb.payments.bill.transaction.institution.util;

import java.util.*;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import com.ykb.payments.bill.transaction.payment.web.response.SubsrciberNoPartResponseWebDTO;
import org.apache.commons.lang3.Range;
import org.apache.commons.lang3.StringUtils;

import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import com.ykb.payments.bill.transaction.payment.dto.SubscriberNoPartRequestDTO;

public class SubscriberNumberUtils {

	public static List<SubsrciberNoPartResponseWebDTO> parseSubscriberNoIntoParts(List<InstitutionUserIntfDTO> subscriberInfolist, String subscriberNumber){
		List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList = new ArrayList<>();
		SubsrciberNoPartResponseWebDTO subscriberNoPartRequestDTO ;
		if(subscriberInfolist.size() == 1 ){
			subscriberNoPartRequestDTO = new SubsrciberNoPartResponseWebDTO();
			subscriberNoPartRequestDTO.setPartNo(subscriberInfolist.get(0).getScreenOrderNo());
			subscriberNoPartRequestDTO.setPartKey(subscriberInfolist.get(0).getLabel());
			subscriberNoPartRequestDTO.setPartValue(subscriberNumber);
			subscriberNoPartList.add(subscriberNoPartRequestDTO);
			return subscriberNoPartList;
		}
		String subscriberNoPart = null;
		for (InstitutionUserIntfDTO subscriberPartInfo : subscriberInfolist) {
			Pattern pattern = Pattern.compile(subscriberPartInfo.getRegex());
			List<String> namedGroups = getNamedGroup(subscriberPartInfo.getRegex());
			Matcher matcher = pattern.matcher(subscriberNumber);
			if (matcher.find()) {
				for (String namedGroup : namedGroups) {
					subscriberNoPart = subscriberNoPart + matcher.group(namedGroup);
				}
			}
			subscriberNoPartRequestDTO  = new SubsrciberNoPartResponseWebDTO();
			subscriberNoPartRequestDTO.setPartNo(subscriberPartInfo.getScreenOrderNo());
			subscriberNoPartRequestDTO.setPartKey(subscriberPartInfo.getLabel());
			subscriberNoPartRequestDTO.setPartValue(subscriberNoPart);
			subscriberNoPartList.add(subscriberNoPartRequestDTO);

		}
		return  subscriberNoPartList;
	}

	public static String formatSubscriberNumberParts(List<InstitutionUserIntfDTO> subscriberInfolist,
			List<SubscriberNoPartRequestDTO> subscriberNoPartList) {
		String mergedSubscriberNumber ;
		
		for (InstitutionUserIntfDTO subscriberPartInfo : subscriberInfolist) {
			Optional<SubscriberNoPartRequestDTO> subscriberNoPartDTO = subscriberNoPartList.stream()
					.filter(subscriberNoPart -> subscriberNoPart.getPartNo() == subscriberPartInfo.getScreenOrderNo())
					.findFirst();

			if (subscriberNoPartDTO.isPresent()) {
				if (subscriberNoPartDTO.get().getPartValue().length() <= subscriberPartInfo.getMaxLength()
						&& subscriberPartInfo.getCompleteLengthFlag()) {
					subscriberNoPartDTO.get().setPartValue(StringUtils.leftPad(subscriberNoPartDTO.get().getPartValue(),
							subscriberPartInfo.getMaxLength(), '0'));

				}
			}

		}
		mergedSubscriberNumber = mergeSubscriberNumberParts(subscriberNoPartList);

		return mergedSubscriberNumber;

	}

	public static String mergeSubscriberNumberParts(List<SubscriberNoPartRequestDTO> subscriberNoPartList) {	
		return StringUtils
				.join(subscriberNoPartList.stream().sorted(Comparator.comparingInt(SubscriberNoPartRequestDTO::getPartNo))
						.map(SubscriberNoPartRequestDTO::getPartValue).toArray(String[]::new));
	
	}
	
	public static boolean checkSubscriberNumberParts(List<InstitutionUserIntfDTO> institutionUserIntListDTO,
			List<SubscriberNoPartRequestDTO> subscriberNoPartList) {
		boolean valid = false;	
			for (InstitutionUserIntfDTO subscriberPartInfo : institutionUserIntListDTO) {
				
				valid = subscriberNoPartList.stream().filter(
						subscriberNoPart -> subscriberNoPart.getPartNo() == subscriberPartInfo.getScreenOrderNo())
						.filter(subscriberNoPart -> applyLengthControl(subscriberNoPart.getPartValue(),
								subscriberPartInfo.getMaxLength(), subscriberPartInfo.getMinLength()))
						.filter(subscriberNoPart -> applyNumericAndNegativeValueControl(subscriberNoPart.getPartValue(),
								subscriberPartInfo.getIsNumeric()))
						.filter(subscriberNoPart -> applyRegexPatternControl(subscriberNoPart.getPartValue(),
								subscriberPartInfo.getRegex()))
						.findAny().isPresent();
			}		
		
		return valid;
	}
	
	private static boolean applyLengthControl(String subscriberNo, Integer subscriberNoMaxLength,
			Integer subscriberNoMinLength) {		
		return Range.between(subscriberNoMinLength, subscriberNoMaxLength).contains(subscriberNo.length());
	}
	
	private static boolean applyNumericAndNegativeValueControl(String subscriberNo, Boolean numericFlag) {
		boolean isSubscriberNoNumeric = StringUtils.isNumeric(subscriberNo);
		boolean isSubscriberNoPositive = false;

		if (isSubscriberNoNumeric) {
			isSubscriberNoPositive = Long.valueOf(subscriberNo) >= 0;
		}

		return (numericFlag && isSubscriberNoNumeric && isSubscriberNoPositive)
				|| (!numericFlag && !isSubscriberNoNumeric);
		
	}
	
	private static boolean applyRegexPatternControl(String subscriberNo, String regexPattern) {
		return StringUtils.isNotEmpty(regexPattern) ? subscriberNo.matches(regexPattern) :true;
			
	}
	//TODO: Regex Util yazilmali, barcode da kullaniliyor.
	private static List<String> getNamedGroup(String regex) {
		List<String> namedGroups = new ArrayList<String>();

		Matcher m = Pattern.compile("\\(\\?<([a-zA-Z][a-zA-Z0-9]*)>").matcher(regex);
		while (m.find()) {
			namedGroups.add(m.group(1));
		}
		Collections.sort(namedGroups);
		return namedGroups;
	}
	
}




@ExtendWith(MockitoExtension.class)
@DisplayName("Unit tests for SubscriberNumberUtils methods.")
public class SubscriberNumberUtilsTest {


    @Test
    void shouldParseSubscriberNoIntoPartsWhenSubscriberInfolistSizeIsOne2(){

        InstitutionUserIntfDTO institutionUserIntfDto = getInstitutionUserIntfDto();
        List<SubsrciberNoPartResponseWebDTO> actual = SubscriberNumberUtils.parseSubscriberNoIntoParts(List.of(institutionUserIntfDto, institutionUserIntfDto), "123");
        Assertions.assertEquals(2, actual.size());
    }

    @Test
    void shouldParseSubscriberNoIntoPartsWhenSubscriberInfolistSizeIsOne3(){
        SubscriberNoPartRequestDTO subscriberNoPartRequestDTO = getSubscriberNoPartRequestDTO();
        InstitutionUserIntfDTO institutionUserIntfDto = getInstitutionUserIntfDto();
        boolean actual = SubscriberNumberUtils.checkSubscriberNumberParts(List.of(institutionUserIntfDto, institutionUserIntfDto), List.of(subscriberNoPartRequestDTO));
        Assertions.assertFalse(actual);
    }


    @Test
    void shouldParseSubscriberNoIntoPartsWhenSubscriberInfolistSizeIsOne(){

        List<InstitutionUserIntfDTO> intfDTOS = List.of(getInstitutionUserIntfDto());
        List<SubsrciberNoPartResponseWebDTO> actual = SubscriberNumberUtils.parseSubscriberNoIntoParts(intfDTOS, "123");
        Assertions.assertEquals(1, actual.size());
    }

    @Test
    void shouldMergeSubscriberNumberParts(){
        List<SubscriberNoPartRequestDTO> intfDTOS = List.of(getSubscriberNoPartRequestDTO());
        String actual = SubscriberNumberUtils.mergeSubscriberNumberParts(intfDTOS);
        Assertions.assertEquals(3, actual.length());
    }

    private InstitutionUserIntfDTO getInstitutionUserIntfDto() {
        InstitutionUserIntfDTO dto = new InstitutionUserIntfDTO();
        dto.setScreenOrderNo(1);
        dto.setLabel("testLabel");
        dto.setRegex("testRegex");
        dto.setMinLength(1);
        dto.setMaxLength(10);
        dto.setIsNumeric(true);

        return dto;
    }

    private SubscriberNoPartRequestDTO getSubscriberNoPartRequestDTO(){
        SubscriberNoPartRequestDTO dto = new SubscriberNoPartRequestDTO();
        dto.setPartNo(1);
        dto.setPartValue("123");
        dto.setPartKey("partKey");
        return dto;
    }

}
  buda bunun testi fakat 
"	for (InstitutionUserIntfDTO subscriberPartInfo : subscriberInfolist) {
			Optional<SubscriberNoPartRequestDTO> subscriberNoPartDTO = subscriberNoPartList.stream()
					.filter(subscriberNoPart -> subscriberNoPart.getPartNo() == subscriberPartInfo.getScreenOrderNo())
					.findFirst();

			if (subscriberNoPartDTO.isPresent()) {
				if (subscriberNoPartDTO.get().getPartValue().length() <= subscriberPartInfo.getMaxLength()
						&& subscriberPartInfo.getCompleteLengthFlag()) {
					subscriberNoPartDTO.get().setPartValue(StringUtils.leftPad(subscriberNoPartDTO.get().getPartValue(),
							subscriberPartInfo.getMaxLength(), '0'));

				}
			}

		}" buranın testi yapılmamış
