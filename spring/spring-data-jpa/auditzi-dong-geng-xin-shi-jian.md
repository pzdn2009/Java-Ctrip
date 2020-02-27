# Audit自動更新時間

* @CreatedDate、
* @LastModifiedDate、
* @CreatedBy、
* @LastModifiedBy

## Sample

```java
@Getter
@Setter
@NoArgsConstructor
@ToString
@JsonInclude(JsonInclude.Include.NON_NULL)
@MappedSuperclass
@EntityListeners(AuditingEntityListener.class)
public class BaseDomain {

    /**
     * 默認自增主鍵
     */
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    /**
     * 創建日期
     */
    @CreatedDate
    private LocalDateTime createTime;

    /**
     * 創建人
     */
    @CreatedBy
    private String createUser;

    /**
     * 更新日期
     */
    @LastModifiedDate
    private LocalDateTime updateTime;

    /**
     * 更新人
     */
    @LastModifiedBy
    private String updateUser;
}
```

然后还需要在启动类加上**@EnableJpaAuditing**注解。

## CreatedBy，LastModifiedBy

```java
@Configuration
public class UserIDAuditorBean implements AuditorAware<String> {

    @Override
    public Optional<String> getCurrentAuditor() {
        return Optional.of("pengzhen");
    }
}
```

