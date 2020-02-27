# PagingQuery

* native
* Simple
* findAll
* em

## 方式一

本地sql查询.

> 注意表名啥的都用数据库中的名称，适用于特定数据库的查询

```java
public interface UserRepository extends JpaRepository<User, Long> {

  @Query(value = "SELECT * FROM USERS WHERE LASTNAME = ?1",
    countQuery = "SELECT count(*) FROM USERS WHERE LASTNAME = ?1",
    nativeQuery = true)
  Page<User> findByLastname(String lastname, Pageable pageable);
}
```

## 方式二

jpa已经实现的分页接口，适用于简单的分页查询

```java
PagingAndSortingRepository<User, Long> repository = // … get access to a bean
Page<User> users = repository.findAll(new PageRequest(1, 20));

//service
Sort sort = new Sort(Sort.Direction.DESC,"createTime"); //创建时间降序排序
Pageable pageable = new PageRequest(pageNumber,pageSize,sort);
this.depositRecordRepository.findAllByUserIdIn(userIds,pageable);

//repository
Page<DepositRecord> findAllByUserIdIn(List<Long> userIds,Pageable pageable);
```

## 方式三

動態查詢

```java
public interface UserRepository extends JpaRepository<User, Long> {
    Page<User> findAll(Specification<User> spec, Pageable pageable);
} 

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsersPage(PageParam pageParam, String nickName) {
        //规格定义
        Specification<User> specification = new Specification<User>() {

            /**
             * 构造断言
             * @param root 实体对象引用
             * @param query 规则查询对象
             * @param cb 规则构建对象
             * @return 断言
             */
            @Override
            public Predicate toPredicate(Root<User> root, CriteriaQuery<?> query, CriteriaBuilder cb) {
                List<Predicate> predicates = new ArrayList<>(); //所有的断言
                if(StringUtils.isNotBlank(nickName)){ //添加断言
                    Predicate likeNickName = cb.like(root.get("nickName").as(String.class),nickName+"%");
                    predicates.add(likeNickName);
                }
                return cb.and(predicates.toArray(new Predicate[0]));
            }
        };
        //分页信息
        Pageable pageable = new PageRequest(pageParam.getPage()-1,pageParam.getLimit()); //页码：前端从1开始，jpa从0开始，做个转换
        //查询
        return this.userRepository.findAll(specification,pageable);
    }

}
```

## 方式四

使用entityManager

```java
@Service
@Transactional
public class IncomeService{

    /**
     * 实体管理对象
     */
    @PersistenceContext
    EntityManager entityManager;

    public Page<IncomeDaily> findIncomeDailysByPage(PageParam pageParam, String cpId, String appId, Date start, Date end, String sp) {
        StringBuilder countSelectSql = new StringBuilder();
        countSelectSql.append("select count(*) from IncomeDaily po where 1=1 ");

        StringBuilder selectSql = new StringBuilder();
        selectSql.append("from IncomeDaily po where 1=1 ");

        Map<String,Object> params = new HashMap<>();
        StringBuilder whereSql = new StringBuilder();
        if(StringUtils.isNotBlank(cpId)){
            whereSql.append(" and cpId=:cpId ");
            params.put("cpId",cpId);
        }
        if(StringUtils.isNotBlank(appId)){
            whereSql.append(" and appId=:appId ");
            params.put("appId",appId);
        }
        if(StringUtils.isNotBlank(sp)){
            whereSql.append(" and sp=:sp ");
            params.put("sp",sp);
        }
        if (start == null)
        {
            start = DateUtil.getStartOfDate(new Date());
        }
        whereSql.append(" and po.bizDate >= :startTime");
        params.put("startTime", start);

        if (end != null)
        {
            whereSql.append(" and po.bizDate <= :endTime");
            params.put("endTime", end);
        }

        String countSql = new StringBuilder().append(countSelectSql).append(whereSql).toString();
        Query countQuery = this.entityManager.createQuery(countSql,Long.class);
        this.setParameters(countQuery,params);
        Long count = (Long) countQuery.getSingleResult();

        String querySql = new StringBuilder().append(selectSql).append(whereSql).toString();
        Query query = this.entityManager.createQuery(querySql,IncomeDaily.class);
        this.setParameters(query,params);
        if(pageParam != null){ //分页
            query.setFirstResult(pageParam.getStart());
            query.setMaxResults(pageParam.getLength());
        }

        List<IncomeDaily> incomeDailyList = query.getResultList();
      if(pageParam != null) { //分页
            Pageable pageable = new PageRequest(pageParam.getPage(), pageParam.getLength());
            Page<IncomeDaily> incomeDailyPage = new PageImpl<IncomeDaily>(incomeDailyList, pageable, count);
            return incomeDailyPage;
        }else{ //不分页
            return new PageImpl<IncomeDaily>(incomeDailyList);
        }
    }

    /**
     * 给hql参数设置值
     * @param query 查询
     * @param params 参数
     */
    private void setParameters(Query query,Map<String,Object> params){
        for(Map.Entry<String,Object> entry:params.entrySet()){
            query.setParameter(entry.getKey(),entry.getValue());
        }
    }
}
```

