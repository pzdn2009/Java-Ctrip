# ONG Starter & 配置一览

## 约定

* 官方的以`spring-boot-starter-*`开头；
* 自定义的以`*-spring-boot-starter`命名；

## Starter列表

| Table 1. Spring Boot application starters |  |
| :--- | :--- |


<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter</code>
      </td>
      <td style="text-align:left">
        <ol>
          <li>Core starter, including auto-configuration support, logging and YAML</li>
          <li><pre><code class="lang-text">description = &quot;Core starter, including auto-configuration support, logging and YAML&quot;

dependencies {
	api(project(&quot;:spring-boot-project:spring-boot&quot;))
	api(project(&quot;:spring-boot-project:spring-boot-autoconfigure&quot;))
	api(project(&quot;:spring-boot-project:spring-boot-starters:spring-boot-starter-logging&quot;))
	api(&quot;jakarta.annotation:jakarta.annotation-api&quot;)
	api(&quot;org.springframework:spring-core&quot;)
	api(&quot;org.yaml:snakeyaml&quot;)
}</code></pre>

          </li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-activemq</code>
      </td>
      <td style="text-align:left">Starter for JMS messaging using Apache ActiveMQ</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-amqp</code>
      </td>
      <td style="text-align:left">
        <ol>
          <li>Starter for using Spring AMQP and Rabbit MQ</li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-aop</code>
      </td>
      <td style="text-align:left">Starter for aspect-oriented programming with Spring AOP and AspectJ</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-artemis</code>
      </td>
      <td style="text-align:left">Starter for JMS messaging using Apache Artemis</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-batch</code>
      </td>
      <td style="text-align:left">Starter for using Spring Batch</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-cache</code>
      </td>
      <td style="text-align:left">Starter for using Spring Framework&#x2019;s caching support</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-cassandra</code>
      </td>
      <td style="text-align:left">Starter for using Cassandra distributed database and Spring Data Cassandra</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-cassandra-reactive</code>
      </td>
      <td style="text-align:left">Starter for using Cassandra distributed database and Spring Data Cassandra
        Reactive</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-couchbase</code>
      </td>
      <td style="text-align:left">Starter for using Couchbase document-oriented database and Spring Data
        Couchbase</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-couchbase-reactive</code>
      </td>
      <td style="text-align:left">Starter for using Couchbase document-oriented database and Spring Data
        Couchbase Reactive</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-elasticsearch</code>
      </td>
      <td style="text-align:left">Starter for using Elasticsearch search and analytics engine and Spring
        Data Elasticsearch</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-jdbc</code>
      </td>
      <td style="text-align:left">
        <ol>
          <li>Starter for using Spring Data JDBC</li>
          <li><pre><code class="lang-java">description = &quot;Starter for using JDBC with the HikariCP connection pool&quot;

dependencies {
	api(project(&quot;:spring-boot-project:spring-boot-starters:spring-boot-starter&quot;))
	//optional
	api(&quot;com.zaxxer:HikariCP&quot;)
	api(&quot;org.springframework:spring-jdbc&quot;)
}</code></pre>

          </li>
        </ol>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-jpa</code>
      </td>
      <td style="text-align:left">Starter for using Spring Data JPA with Hibernate</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-ldap</code>
      </td>
      <td style="text-align:left">Starter for using Spring Data LDAP</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-mongodb</code>
      </td>
      <td style="text-align:left">Starter for using MongoDB document-oriented database and Spring Data MongoDB</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-mongodb-reactive</code>
      </td>
      <td style="text-align:left">Starter for using MongoDB document-oriented database and Spring Data MongoDB
        Reactive</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-neo4j</code>
      </td>
      <td style="text-align:left">Starter for using Neo4j graph database and Spring Data Neo4j</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-r2dbc</code>
      </td>
      <td style="text-align:left">Starter for using Spring Data R2DBC</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-redis</code>
      </td>
      <td style="text-align:left">Starter for using Redis key-value data store with Spring Data Redis and
        the Lettuce client</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-redis-reactive</code>
      </td>
      <td style="text-align:left">Starter for using Redis key-value data store with Spring Data Redis reactive
        and the Lettuce client</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-rest</code>
      </td>
      <td style="text-align:left">Starter for exposing Spring Data repositories over REST using Spring Data
        REST</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-data-solr</code>
      </td>
      <td style="text-align:left">Starter for using the Apache Solr search platform with Spring Data Solr</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-freemarker</code>
      </td>
      <td style="text-align:left">Starter for building MVC web applications using FreeMarker views</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-groovy-templates</code>
      </td>
      <td style="text-align:left">Starter for building MVC web applications using Groovy Templates views</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-hateoas</code>
      </td>
      <td style="text-align:left">Starter for building hypermedia-based RESTful web application with Spring
        MVC and Spring HATEOAS</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-integration</code>
      </td>
      <td style="text-align:left">Starter for using Spring Integration</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-jdbc</code>
      </td>
      <td style="text-align:left">Starter for using JDBC with the HikariCP connection pool</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-jersey</code>
      </td>
      <td style="text-align:left">Starter for building RESTful web applications using JAX-RS and Jersey.
        An alternative to <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#spring-boot-starter-web"><code>spring-boot-starter-web</code></a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-jooq</code>
      </td>
      <td style="text-align:left">Starter for using jOOQ to access SQL databases. An alternative to <a href="https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#spring-boot-starter-data-jpa"><code>spring-boot-starter-data-jpa</code></a> or
        <a
        href="https://docs.spring.io/spring-boot/docs/current/reference/html/using-spring-boot.html#spring-boot-starter-jdbc"><code>spring-boot-starter-jdbc</code>
          </a>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-json</code>
      </td>
      <td style="text-align:left">Starter for reading and writing json</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-jta-atomikos</code>
      </td>
      <td style="text-align:left">Starter for JTA transactions using Atomikos</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-jta-bitronix</code>
      </td>
      <td style="text-align:left">Starter for JTA transactions using Bitronix. Deprecated since 2.3.0</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-mail</code>
      </td>
      <td style="text-align:left">Starter for using Java Mail and Spring Framework&#x2019;s email sending
        support</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-mustache</code>
      </td>
      <td style="text-align:left">Starter for building web applications using Mustache views</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-oauth2-client</code>
      </td>
      <td style="text-align:left">Starter for using Spring Security&#x2019;s OAuth2/OpenID Connect client
        features</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-oauth2-resource-server</code>
      </td>
      <td style="text-align:left">Starter for using Spring Security&#x2019;s OAuth2 resource server features</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-quartz</code>
      </td>
      <td style="text-align:left">Starter for using the Quartz scheduler</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-rsocket</code>
      </td>
      <td style="text-align:left">Starter for building RSocket clients and servers</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-security</code>
      </td>
      <td style="text-align:left">Starter for using Spring Security</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-test</code>
      </td>
      <td style="text-align:left">Starter for testing Spring Boot applications with libraries including
        JUnit Jupiter, Hamcrest and Mockito</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-thymeleaf</code>
      </td>
      <td style="text-align:left">Starter for building MVC web applications using Thymeleaf views</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-validation</code>
      </td>
      <td style="text-align:left">Starter for using Java Bean Validation with Hibernate Validator</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-web</code>
      </td>
      <td style="text-align:left">Starter for building web, including RESTful, applications using Spring
        MVC. Uses Tomcat as the default embedded container</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-web-services</code>
      </td>
      <td style="text-align:left">Starter for using Spring Web Services</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-webflux</code>
      </td>
      <td style="text-align:left">Starter for building WebFlux applications using Spring Framework&#x2019;s
        Reactive Web support</td>
    </tr>
    <tr>
      <td style="text-align:left"><code>spring-boot-starter-websocket</code>
      </td>
      <td style="text-align:left">Starter for building WebSocket applications using Spring Framework&#x2019;s
        WebSocket support</td>
    </tr>
  </tbody>
</table>

