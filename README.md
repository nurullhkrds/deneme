 return insChnnlProcessListOjectArray.stream()
                .map(institutionChannelProcessMapper::objectArrayToWebDTO)
                .collect(Collectors.toList());
Replace this usage of 'Stream.collect(Collectors.toList())' with 'Stream.toList()'
