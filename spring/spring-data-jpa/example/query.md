# @Query

```java
public interface UserRepository extends JpaRepository<User, Long> {

  //1 参数
  @Query("select u from User u where u.emailAddress = ?1")
  User findByEmailAddress(String emailAddress);

  //2 Like
  @Query("select u from User u where u.firstname like %?1")
  List<User> findByFirstnameEndsWith(String firstname);

  //3 native
  @Query(value = "SELECT * FROM USERS WHERE EMAIL_ADDRESS = ?1", nativeQuery = true)

  //4 native + 分页
  @Query(value = "SELECT * FROM USERS WHERE LASTNAME = ?1",
    countQuery = "SELECT count(*) FROM USERS WHERE LASTNAME = ?1",
    nativeQuery = true)
  Page<User> findByLastname(String lastname, Pageable pageable);

  //5 +排序
  @Query("select u from User u where u.lastname like ?1%")
  List<User> findByAndSort(String lastname, Sort sort);

  //6 +排序2，返回Object
  @Query("select u.id, LENGTH(u.firstname) as fn_len from User u where u.lastname like ?1%")
  List<Object[]> findByAsArrayAndSort(String lastname, Sort sort);

  //7 命名参数
  @Query("select u from User u where u.firstname = :firstname or u.lastname = :lastname")
  User findByLastnameOrFirstname(@Param("lastname") String lastname, @Param("firstname") String firstname);

  //8 使用SpeL
  @Query("select u from #{#entityName} u where u.lastname = ?1")
  List<User> findByLastname(String lastname);

  //9 更新操作
  @Modifying
  @Query("update User u set u.firstname = ?1 where u.lastname = ?2")
  int setFixedFirstnameFor(String firstname, String lastname);

  //10 删除操作
  @Modifying
  @Query("delete from User u where user.role.id = ?1")
  void deleteInBulkByRoleId(long roleId);


}
```

* 参数：?1,  ?2,  ?3
* Like：%?1,  %?2%
* 原生查询，nativeQuery为true
* 排序：

  ```java
  repo.findByAndSort("lannister", new Sort("firstname"));               
  repo.findByAndSort("targaryen", JpaSort.unsafe("LENGTH(firstname)")); 
  repo.findByAsArrayAndSort("bolton", new Sort("fn_len"));
  ```

* {\#entityName}：如果@Entity指定了name，则使用指定的，反之，使用Entity的类名。

