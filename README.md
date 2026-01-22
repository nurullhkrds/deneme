mvn -q dependency:tree -Dincludes=org.hibernate:hibernate-core
mvn -q dependency:tree -Dincludes=org.springframework.data:spring-data-jpa
mvn -q dependency:tree -Dincludes=javax.persistence:javax.persistence-api
mvn -q dependency:tree -Dincludes=jakarta.persistence:jakarta.persistence-api
