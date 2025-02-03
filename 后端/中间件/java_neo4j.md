#### java连接neo4j

依赖

```
<dependency>
    <groupId>org.neo4j.driver</groupId>
    <artifactId>neo4j-java-driver</artifactId>
    <version>4.2.0</version>
</dependency>
```

配置yml

```
neo4j:
  uri: neo4j://localhost
  username: neo4j
  password: neo4jneo4j
```

配置类

```
import org.neo4j.driver.*;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class Neo4jConfig {
    @Value("${neo4j.username}")
    String username;

    @Value("${neo4j.password}")
    String password;

    @Value("${neo4j.uri}")
    String uri;

    @Bean
    public Session getSession() {
        Driver driver = GraphDatabase.driver(uri, AuthTokens.basic(username, password));
        SessionConfig sessionConfig = SessionConfig.defaultConfig();
        return driver.session(sessionConfig);
    }
}
```

使用

```
@Resource
Session session;

@Test
public void connectNeo4j() {
    String cql = "match (n:学生) return n";
    Result result = session.run(cql);
    List<Map> list = new ArrayList<>();
    while (result.hasNext()) {
        Value value = result.next().get(0);
        list.add(value.asMap());
    }
    System.out.println(list);
}
```