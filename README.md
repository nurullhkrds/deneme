return insChnnlProcessListOjectArray.stream()
        .map(institutionChannelProcessMapper::objectArrayToWebDTO)
        .toList();
