# JPA註解

## Entity的註解

* @Entity。對應關係型DB表
* @Document。支持Mongo表
* @Id。對應主鍵
* @GeneratedValue\(strategy=GenerationType.AUTO\)。主键的产生策略。
  * Identity：表自动增长字段，Oracle不支持这种方式；
  * AUTO：JPA自动选择合适的策略，是默认选项；
  * Sequence：通过序列产生主键，通过@SequenceGenerator注解指定序列名，Mysql不支持这种方式。
  * TABLE：通过表产生主键，框架借由表模拟产生主键，使用该策略可以使用更易于数据库的移植。 
* @Lob。NVARCHAR\(max\)
* @Column。對應列名。
  * name
  * unique
  * nullable
  * inserttable
  * updateable
  * columnDefinition
  * secondaryTable
* @Temporal。时间类型。
  * TemporalType.DATE
  * TemporalType.TIME
  * TemporalType.TIMESTAMP
* @Transient 
* @Enumerated
* @Version
* @OneToOne
* @OneToMany
* @ManyToOne
* @ManyToMany
* @Formula 一个SQL表达式，这种属性是只读的,不在数据库生成属性\(可以使用sum、average、max等\)
* @OrderBy\(name = "group\_name ASC, name DESC"\)     
* @JoinColumn\(name = "ONE\_ID", referencedColumnName = "ONE\_ID"\)//设置对应数据表的列名和引用的数据表的列名 
* @MappedSuperclass
* @Embedded
* @Embeddable
* InheritanceType
  * SINGLE\_TABLE：全部合併在一個表
  * TABLE\_PER\_CLASS：父子表獨立，屬性冗餘
  * JOINED：父子表獨立，通過外鍵

Ref:[https://blog.csdn.net/dragonpeng2008/article/details/52297426](https://blog.csdn.net/dragonpeng2008/article/details/52297426) Ref：[http://www.oracle.com/technetwork/cn/middleware/ias/toplink-jpa-annotations-100895-zhs.html](http://www.oracle.com/technetwork/cn/middleware/ias/toplink-jpa-annotations-100895-zhs.html)

