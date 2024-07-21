APPLICATION FAILED TO START
***************************

Description:

Field microSecurityRedisTemplate in com.ykb.architecture.micro.microsecurity.integration.rabbit.ClientPushEventListener required a bean of type 'org.springframework.data.redis.core.RedisTemplate' that could not be found.

The injection point has the following annotations:
	- @org.springframework.beans.factory.annotation.Autowired(required=true)
	- @org.springframework.beans.factory.annotation.Qualifier("microSecurityRedisTemplate")


Action:

Consider defining a bean of type 'org.springframework.data.redis.core.RedisTemplate' in your configuration.
