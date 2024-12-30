		for (SubscriberNoPartRequestDTO subscriberNoPart : subscriberNoPartList) {

			if (!partNo.equals(subscriberNoPart.getPartNo())) {

				continue;

			}

			for (InstUserIntfSubtypeDTO instUserIntfSubtypeDTO : instUserIntfSubtypeDTOList) {

				if (!instUserIntfSubtypeDTO.getKey().equals(subscriberNoPart.getPartKey())) {

					continue;

				}

				subscriberNoPart.setAdditionalInfo1(instUserIntfSubtypeDTO.getAdditionalInfo1());

				subscriberNoPart.setAdditionalInfo2(instUserIntfSubtypeDTO.getAdditionalInfo2());

				subscriberNoPart.setAdditionalInfo3(instUserIntfSubtypeDTO.getAdditionalInfo3());

			}

		}
 


		subscriberNoPartList.stream()

				.filter(subscriberNoPart -> partNo.equals(subscriberNoPart.getPartNo()))

				.forEach(subscriberNoPart ->

						instUserIntfSubtypeDTOList.stream()

								.filter(instUserIntfSubtypeDTO -> instUserIntfSubtypeDTO.getKey().equals(subscriberNoPart.getPartKey()))

								.findFirst()

								.ifPresent(instUserIntfSubtypeDTO -> {

									subscriberNoPart.setAdditionalInfo1(instUserIntfSubtypeDTO.getAdditionalInfo1());

									subscriberNoPart.setAdditionalInfo2(instUserIntfSubtypeDTO.getAdditionalInfo2());

									subscriberNoPart.setAdditionalInfo3(instUserIntfSubtypeDTO.getAdditionalInfo3());

								})

				);
 
