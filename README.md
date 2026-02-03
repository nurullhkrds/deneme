        if(isFixedPriceProcess()){
            if (isOnlineProcess()){
                // TODO: Go Fixed Price and Online Service Call
            }else {
                // TODO: Go Fixed Price and Offline Database Query
            }
        }else {
            if(isOnlineProcess()){
                addProcessStep(new QueryFromService());
                addProcessStep(new EliminateBills());
            }else{
                addProcessStep(new QueryFromDatabase());
            }
        }
