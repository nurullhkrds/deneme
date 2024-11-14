	private Query getInsertQueryFromSession(String serviceName, String operationName) throws HmnException {
		String queryString = null;
		Query query = null;
		ProduceQueryCacheHelper cache = HmnCacheableInstanceFactory.getHmnCacheableSingletoneObject(ProduceQueryCacheHelperImpl.class);
		Map<String, String> queryMap = cache.getInsertSQLQueries(serviceName, operationName);
		if (queryMap != null) {
			queryString = queryMap.get(serviceName + Constants.sign + operationName);
		}
		if (queryString != null) {
			query = HmnProcessUtil.getClientSession().createSQLQuery(queryString);
		}
		return query;
	}
