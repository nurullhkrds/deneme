        redisConfig = new RedisConfig();
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureValueTtl", 1);
        ReflectionTestUtils.setField(redisConfig, "institutionFeatureListTtl", 2);
        ReflectionTestUtils.setField(redisConfig, "getProcessChannelForProcessTtl", 3);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionForProcessTtl", 4);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelForProcessTtl", 5);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionProcessTtl", 6);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionChannelProcessTtl", 7);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionDebtTypeForProcessTtl", 8);
        ReflectionTestUtils.setField(redisConfig, "getInstitutionByIdTtl", 9);
        ReflectionTestUtils.setField(redisConfig, "institutionUserInterfaceListTtl", 10);
        ReflectionTestUtils.setField(redisConfig, "findChannelByChannelCodeTtl", 11);
        ReflectionTestUtils.setField(redisConfig, "serviceName", "testServiceName");
    }
