---
layout: post
title: 实体bean,entity注解配置
description: 实体bean,entity注解配置
category: javaweb
---
### 实体bean,entity注解配置
   持久化是位于JDBC之上的一个更高层抽象。持久层将对象映射到数据库，以便在查询、装载、更新或删除对象的时候，无须使用像JDBC那样繁琐的API。EJB的早期版本中，持久化是EJB平台的一部分。EJB3.0开始，持久化已经自成规范，被称为Java Persistence API。

Java Persistence API定义了一种定义，可以将常规的普通Java对象（有时被称作POJO）映射到数据库。这些普通Java对象被称作Entity Bean。除了是用Java Persistence元数据将其映射到数据库外，Entity Bean与其他Java类没有任何区别。事实上，创建一个Entity Bean对象相当于新建一条记录，删除一个Entity Bean会同时从数据库中删除对应记录，修改一个Entity Bean时，容器会自动将Entity Bean的状态和数据库同步。

Java Persistence API还定义了一种查询语言（JPQL），具有与SQL相类似的特征，只不过做了裁减，以便处理Java对象而非原始的关系表。

### 持久化persistence.xml配置文件
   一个实体Bean应用由实体类和persistence.xml文件组成。persistence.xml文件在jar文件的META-INF目录。persistence.xml文件指定实体Bean使用的数据源及EntityManager对象的默认行为。persistence.xml文件的配置说明如下：

```
<?xml version="1.0" encoding="UTF-8"?>

<persistence xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

                   xsi:schemaLocation="http://java.sun.com/xml/ns/persistence

                                     http://java.sun.com/xml/ns/persistence/persistence_1_0.xsd" version="1.0">

              <persistence-unit name="test" transaction-type="JTA">

                  <jta-data-source>java:/user</jta-data-source>

                  <properties>

                       <property name="hibernate.dialect" value="org.hibernate.dialect.SQLServerDialect"/>

                  </properties>

              </persistence-unit>

</persistence>
```

persistence-unit节点可以有一个或多个，每个persistence-unit节点定义了持久化内容名称、使用的数据源及持久化产品专有属性。name属性定义持久化名称。jta-data-source节点指定实体Bean使用的数据源JNDI名称，如果应用发布在JBoss下数据源名称带有java:/前缀，数据源名称大小写敏感。properties节点用作指定持久化产品的各项属性，各个应用服务器使用的持久化产品都不一样，如JBoss使用Hibernate，WebLogic10使用Kodo。
下面为引入插件配置

##JBoss数据源的配置

各种数据库德数据源配置模版可以在[JBoss安装目录]\docs\examples\jca目录中找到，默认名称为：数据库名+-ds.xml。
不管使用哪种数据库都需要把它的驱动类jar包放置在[JBoss安装目录]\server\default\lib目录下，放置后需要启动JBoss服务器。
数据源文件配置好后需要放置在[JBoss安装目录]/server/default/deploy目录。

### SQL Server配置代码：
```
 <?xml version="1.0" encoding="UTF-8"?>

<datasources>

              <local-tx-datasource>

                   <jndi-name>user</jndi-name>

                   <connection-url>jdbc:sqlserver://localhost:1433;DatabaseName=rep</connection-url>

                   <driver-class>com.microsoft.sqlserver.jdbc.SQLServerDriver</driver-class>

                   <user-name>sa</user-name>        <password>123</password>

                   <metadata>

                       <type-mapping>MS SQLSERVER2000</type-mapping>

                   </metadata>

              </local-tx-datasource>

</datasources>
```
### 单表映射的实体Bean
```
 @Entity

@Table(name="tbl_user")

publicclass User implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="id")

                   private Integer id;

                   @Column(name="name")

                   private String name;

                   @Column(name="age")

                   private String age;

                   public String getAge() {   returnage;  }

                   publicvoid setAge(String age) {        this.age = age;   }

                   public String getName() {       returnname; }

                   publicvoid setName(String name) {  this.name = name; }

                   public User() {   }

                   public Integer getId() {   returnthis.id;  }

                   publicvoid setId(Integer id) {     this.id = id; }

}
```
从上面代码来看开发实体Bean非常简单，比起普通的JavaBean就是多了些注释。@Entity注释指名这是一个实体Bean，@Table注释指定了Entity所要映射带数据库表，其中@Table.name()用来指定映射表的表名。如果缺省@Table注释，系统默认采用类名作为映射表的表名。实体Bean的每个实例代表数据表中的一行数据，行中的一列对应实例中的一个属性。

@Column注释定义了将成员属性映射到关系表中的哪一列和该列的结构信息，属性如下：

1）name：映射的列名。如：映射tbl_user表的name列，可以在name属性的上面或getName方法上面加入；

2）unique：是否唯一；

3）nullable：是否允许为空；

4）length：对于字符型列，length属性指定列的最大字符长度；

5）insertable：是否允许插入；

6）updatetable：是否允许更新；

7）columnDefinition：定义建表时创建此列的DDL；

8）secondaryTable：从表名。如果此列不建在主表上（默认是主表），该属性定义该列所在从表的名字。

@Id注释指定表的主键，它可以有多种生成方式：

1）TABLE：容器指定用底层的数据表确保唯一；

2）SEQUENCE：使用数据库德SEQUENCE列莱保证唯一（Oracle数据库通过序列来生成唯一ID）；

3）IDENTITY：使用数据库的IDENTITY列莱保证唯一；

4）AUTO：由容器挑选一个合适的方式来保证唯一；

5）NONE：容器不负责主键的生成，由程序来完成。

@GeneratedValue注释定义了标识字段生成方式。

@Temporal注释用来指定java.util.Date或java.util.Calender属性与数据库类型date、time或timestamp中的那一种类型进行映射。

@Temporal(value=TemporalType.TIME)

private Date birthday;

### 会话Bean代码
```
 @Stateless

publicclass UserDAO implements UserDAORemote {

                   @PersistenceContext(unitName="test")

                   private EntityManager em;

                   publicboolean insertUser(User user){

                       try{

                            em.persist(user);

                            returntrue;

                       }catch(Exception e){

                            returnfalse;

                       }

                   }

}
```
```
task copyJars(type: Copy) {
    from configurations.runtime
    into "lib" //复制到lib目录
}

//让gradle支持中文
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}

test {
    useJUnit()

    jacoco {
        destinationFile = file("$buildDir/jacoco/test.exec")
    }
}

tasks.withType(Test) {
    testLogging {
        // set options for log level LIFECYCLE
        events "passed", "skipped", "failed", "standardOut"
        showExceptions true
        exceptionFormat "full"
        showCauses true
        showStackTraces true

        // set options for log level DEBUG and INFO
        debug {
            events "started", "passed", "skipped", "failed", "standardOut", "standardError"
            exceptionFormat "full"
        }
        info.events = debug.events
        info.exceptionFormat = debug.exceptionFormat

        afterSuite { desc, result ->
            if (!desc.parent) { // will match the outermost suite
                def output = "结果: ${result.resultType} (${result.testCount} 个测试, ${result.successfulTestCount} 个成功, ${result.failedTestCount} 个失败, ${result.skippedTestCount} 个跳过)"
                def repeatLength = output.length()
                println('\n' + ('——' * repeatLength) + '\n' + output + '\n' + ('——' * repeatLength))
            }
        }
    }
}

jacocoTestReport {
    reports {
        xml.enabled false
        csv.enabled false
        html.destination "${buildDir}/jacocoHtml"
    }
}

build.dependsOn jacocoTestReport

task integrationTest(type: Test) {
    include "test/java/**"
}
```
上面使用了一个对象：EntityManager，EntityManager是由EJB容器自动地管理和配置的，不需要用户自己创建，它用作操作实体Bean。

如果persistence.xml文件中配置了多个不同的持久化内容。在注入EntityManager对象时必须指定持久化名称，可以通过@PersistenceContext注释的unitName属性进行制定。例如：
```
@PersistenceContext(unitName="test")

private EntityManager em;
```
### 属性映射
如果不想让一些成员属性映射成数据库字段，可以使用@Transient注释进行标注。

@Transient

private String sex;

如果想映射枚举对象到数据库就需要使用@Enumerated注释进行标注。

@Enumerated(EnumType.STRING)

@Column(name="address_type")

private AddressType type;//地址类型

有时可能需要存放一些文本或大文本数据进数据库，JDBC使用java.sql.Blob类型存放二进制数据，java.sql.Clob类型存放字符数据，这些数据都是非常占内存的，@Lob注释用作映射这些大数据类型，当属性的类型为byte[],Byte[]或java.io.Serializable时，@Lob注释映射为数据库的Blob类型，当属性的类型为char[]，Character[]或java.lang.String时，@Lob注释将映射为数据库的Clob类型。

对于加了@Lob注释的大数据类型，为了避免每次加载实体时占用大量内存，有必要对该属性进行延时加载，这是需要用到@Basic注释。@Basic注释的定义：FetchType属性指定是否延时加载，默认为立即加载，optional属性指定在生成数据库结构时字段能否为null。

@Lob

@Basic(fetch=FetchType.LAZY)

@Column(name="info")

private String content;

### 持久化实体管理器EntityManager

EntityManager是用来对实体Bean进行操作的辅助类。可以用来产生/删除持久化的实体Bean，通过主键查找实体Bean，也可以通过EJB3QL语言查找满足条件的实体Bean。实体Bean被EntityManager管理时，EntityManager跟踪他的状态改变，在任何决定更新实体Bean的时候便会把发生改变的值同步到数据库中。当实体Bean从EntityManager分离后，他是不受管理的，EntityManager无法跟踪他的任何状态改变。

## Entity获取find()或getReference()

如果知道Entity的唯一标识符，可以用find()或getReference()方法获取Entity。

public User findUser(Integer userid){

                   returnem.find(User.class, userid);

}

当在数据库中没有找到记录时，getReference()和find()是有区别的，find()方法会返回null，而getReference()方法会抛出javax.persistence.EntityNotFoundException例外，另外getReference()方法不保证实体Bean已被初始化。如果传递进getReference()或find()方法的参数不是实体Bean，都会引发IllegalArgumentException。

### 添加persist()
```
     @PersistenceContext(unitName="test")

              private EntityManager em;

              publicboolean insertUser(User user){

                  try{

                       em.persist(user);

                       returntrue;

                  }catch(Exception e){

                       returnfalse;

                  }

              }
```

如果传递进persist()方法的参数不是实体Bean，都会引发IllegalArgumentException例外。

### 更新实体

当实体正在被容器管理时，可以调用实体的set方法对数据进行修改，在容器决定flush时，更新的数据才会同步到数据库。如果希望修改后的数据实时同步到数据库，可以执行EntityManager.flush()方法。
```
publicboolean updateUser(Integer userid){

                  User user=(User)em.find(User.class,userid);

                  try{

                       user.setName("yyyyyy");

                       returntrue;

                  }catch(Exception e){

                       returnfalse;

                  }

              }
```

### 合并merge()

merge()方法是在实体Bean已经脱离了EntityManager的管理时使用，当容器决定flush时，数据将会同步到数据库中。
```
publicboolean updateUser(User user){

                  try{

                       em.merge(user);

                       returntrue;

                  }catch(Exception e){

                       returnfalse;

                  }

              }
```

执行em.merge(user)方法时，容器的工作规则：1）如果此时容器中已经存在一个受容器管理的具有相同ID的user实例，容器就会把参数user的内容拷贝进这个受管理的实例，merge()方法返回受管理的实例，但参数user仍然是分离的不受管理的。容器在决定Flush时把实例同步到数据库中；2）容器中不存在具有相同ID的user实例。容器根据传入的user参数拷贝出一个受容器管理的person实例，同时merge()会返回出这个受管理的实例，但是参数user仍然是分离的不受管理的。容器在决定Flush时把实例同步到数据库中。

如果传入merge()方法的参数不是实体Bean，会引发一个IllegalArgumentException例外。

### 删除remove()
```
publicboolean deleteUser(Integer id){

                  try{

                       User user=em.find(User.class, id);

                       em.remove(user);

                       returntrue;

                  }catch(Exception e){

                       returnfalse;

                  }

              }
```

### 执行JPQL操作createQuery()

除了使用find()或getReference()方法来获得Entity Bean之外，还可以通过JPQL得到实体Bean。要执行JPQL语句，必须通过EntityManager的createQuery或createNamedQuery()方法创建一个Query对象。
```
public List queryList(){

              Query query=em.createQuery("select u from User u where u.id=3");

              return query.getResultList();

          }
```

### 执行SQL操作createNativeQuery()
这里操作的是SQL语句，并非JPQL。

```
Query query=em.createQuery("select * from tbl_user u");
```

### 刷新实体refresh()
如果怀疑当前被管理的实体已经不是数据库中最新的数据，可以通过refresh()方法刷新实体，容器会把数据库中的新值重写进实体。这种情况一般发生在获取实体之后，有人更新了数据库中的记录，这是需要得到最新数据。当然再次调用find或getReference()方法也可以得到最新数据，但这种做法并不优雅。

```
em.refresh(user);
```

### 检测实体当前是否在被管理中contains()
contains()方法是用一个实体作为参数，如果这个实体对象当前正被持久化内容管理，返回为true，否则为false。
```
try{

                   User user=em.find(User.class, id);

                   if(em.contains(user)){

                        System.out.println("被管理中");

                   }else{

                        System.out.println("没有管理");

                   }

                   returntrue;

              }catch(Exception e){

                   returnfalse;

              }
```

### 分离所有当前正在被管理的实体clear()
在处理大量实体的时候，如果不把已经处理过的实体从EntityManager中分离出来，将会消耗大量的内存。调用EntityManager的clear()方法后，所有正在被管理的实体将会从持久化内容中分离出来。

有一点需要注意，在事务没有提交前，如果调用clear()方法之前对实体所作的任何改变将会丢失，所以建议在调用clear()方法之前先调用flush()方法保存更改。

### 将实体的改变立刻刷新到数据库中flush()
当实体管理器对象在一个Session Bean中使用时，它是和服务器的事务上下文绑定的。实体管理器在服务器的事务提交时并且同步内容。在一个Session Bean中，服务器的事务默认地会在调用堆栈的最后提交。为了只在当事务提交时才将改变更新到数据库中，容器将所有数据库操作集中到一个批量中，这样就减少了与数据库的交互。

当调用persist()、merge()或remove()这些方法时，更新并不会立刻同步到数据库中，直到容器决定刷新到数据库中时才会执行，默认情况下，容器决定刷新是在“相关查询”执行前或事务提交时发生，当然“相关查询”除find()和getReference()之外，这两个方法是不会引起容器触发刷新动作的，默认的刷新模式是可以改变的。

### 改变实体管理器的Flush模式setFlushMode()

默认情况下，实体管理器的Flush模式为AUTO。

两者的区别及使用场合：

FlushModeType.AUTO：刷新在查询语句执行前（除了find()和getReference()）或事务提交时才发生，在大量更新数据的过程中没有任何查询语句的执行时使用。

FlushModeType.COMMIT：刷新只在事务提交时才发生，在大量更新数据的过程中存在查询的执行时使用。

### 获取持久化实现者的引用getDelegate()

可以获取EntityManager持久化实现者的引用。

@PersistenceContext(unitName="test")

private EntityManager em;

HibernateEntityManager hibernate=(HibernateEntityManager)em.getDelegate();

## 关系/对象映射
### 映射的表名或列名与数据库保留字同名时的处理

当映射的表名或列名于数据库保留字同名时，持久化引擎转译后的SQL在执行时将会出错。

如：

@Entity

@Table(name=”Order”)

public class Order implements Serializable{}

表名Order与排序保留字“Order”相同，导致SQL语法出错。

针对上面的情况，在JBoss持久化产品中没有解决方案。一种变通的方法就是加上单引号或者方括号的解决方案。该方法针对具体数据库，不利于数据库移植。建议大家不要使用保留字作为表名或列名。

### 一对一映射

一对一关系，需要在关系维护端的@OneToOne注释中定义mappedBy属性。在关系被维护端建立外键列指向关系维护的主键列。
User代码：
```
 @Entity

@Table(name="tbl_user")

publicclass User  implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   private Integer id;

                   @Column(name="name")

                   private String name;

                   private String sex;

                   private String age;

                   @Temporal(value=TemporalType.DATE)

                   private Date birthday;

                   @OneToOne(optional=true,cascade=CascadeType.ALL,mappedBy="user")

                   private Card card;

                   public User() {   }

public Card getCard() {         returncard;      }

                   publicvoid setCard(Card card) {        this.card = card;      }

                   public String getAge() {        returnage;       }

                   publicvoid setAge(String age) {        this.age = age;        }

                   public Date getBirthday() {         returnbirthday;       }

                   publicvoid setBirthday(Date birthday) { this.birthday = birthday;      }

                   public String getName() {       returnname;      }

                   publicvoid setName(String name) {      this.name = name;      }

                   public String getSex() {        returnsex;            }

publicvoid setSex(String sex) {        this.sex = sex;        }

                   public Integer getId() {        returnthis.id;       }

                   publicvoid setId(Integer id) {         this.id = id; }

                   publicint hashCode() {    return (this.id == null) ? 0 : this.id.hashCode();   }

publicboolean equals(Object object) {

                       if (object instanceof User) {

                            final User obj = (User) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

                       }

                       returnfalse;

                   }

}
```
@OneToOne注释指明User与Card为一对一关系，@OneToOne注释有5个属性：targetEntity、cascade、fetch、optional和mappedBy。

1）targetEntity：Class类型的属性

2）mappedBy：String类型的属性。定义类之间的双向关联。如果类之间是单向关系，不需要提供定义，如果类和类之间形成双向关系。就需要使用这个属性进行定义，否则可能引起数据一致性的问题。

3）cascade：CascadeType类型。该属性定义类和类之间的级联关系。定义级联关系将被容器视为当前类对象及其关联类对象采取相同的操作，而且这种关系是递归的。cascade的值只能从CascadeType.PERSIST(级联新建)、CascadeType.REMOVE(级联删除)、CascadeType.REFRESH(级联刷新)、Cascade.MERGE(级联更新)中选择一个或多个。还有一个选择是使用CascadeType.ALL，表示选择全部四项。

4）fetch：FetchType类型的属性。可选择项包括：FetchType.EAGER和FetchType.LAZY。前者表示关系类在主体类加载的时候同时加载，后者表示关系类在被访问时才加载。默认值是FetchType.LAZY。

5）optional：表示被维护对象是否需要存在。如果为真，说明card属性可以null，也就是允许没有身份证，未成年人就是没有身份证。

Card代码：
```
@Entity

@Table(name="tbl_card")

publicclass Card implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="id")

                   private Integer id;

                   @Column(name="cardno")

                   private String cardno;

                   @OneToOne(optional=false,cascade=CascadeType.REFRESH)

                   @JoinColumn(referencedColumnName="id")

                   private User user;

public Card() {   }

                   public User getUser() {         returnuser; }

                   publicvoid setUser(User user) {        this.user = user;      }

                   public String getCardno() {         returncardno;         }

                   publicvoid setCardno(String cardno) {       this.cardno = cardno;       }

                   public Integer getId() {        returnthis.id;       }

                   publicvoid setId(Integer id) {         this.id = id; }

                   publicint hashCode() {        return (this.id == null) ? 0 : this.id.hashCode();        }

                   publicboolean equals(Object object) {

                       if (object instanceof Card) {

                            final Card obj = (Card) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

                       }

                       returnfalse;

                   }

}
```
@OneToOne注释指明Card与User为一对一关系，Card是关系被维护端，optional=false设置user属性值不能为空，也就是身份证必须有对应的主人。@JoinColumn(name=”user_id” referencedColumnName=”id” unique=”true”)指明tbl_card表的user_id列作为外键与tbl_user表的person_id列进行关联，unique=true指明user_id列的值不可重复。

### 一对多及多对一映射
```
@Entity

@Table(name="tbl_order")

publicclass Order implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="order_id")

                   private Integer id;

                   private String name;

                   @OneToMany(targetEntity=OrderItem.class,cascade=CascadeType.ALL,mappedBy="order")

                   private Set set=new HashSet();

                   public Order() { }

               public String getName() {       returnname;      }

                   publicvoid setName(String name) {      this.name = name;      }

                   public Set getSet() {      returnset;       }

                   publicvoid setSet(Set set) {       this.set = set;   }

                   public Integer getId() {   returnthis.id;       }

                   publicvoid setId(Integer id) {     this.id = id; }

                   publicint hashCode() {    return (this.id == null) ? 0 : this.id.hashCode();   }

                   publicboolean equals(Object object) {

                       if (object instanceof Order) {

                            final Order obj = (Order) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

}

                       returnfalse;

                   }

}

---------------------------------------------------------------------------------------------------------

@Entity

@Table(name="tbl_item")

publicclass OrderItem implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="item_id")

                   private Integer id;

                   private String goodsname;

                   @ManyToOne(cascade=CascadeType.REFRESH,optional=false)

                   @JoinColumn(name="item_order_id",referencedColumnName="order_id")

                   private Order order;

                   public OrderItem() {  }

               public String getGoodsname() {      returngoodsname;      }

                   publicvoid setGoodsname(String goodsname) {      this.goodsname = goodsname;      }

                   public Order getOrder() {       returnorder;          }

publicvoid setOrder(Order order) { this.order = order;    }

                   public Integer getId() {   returnthis.id;       }

                   publicvoid setId(Integer id) {     this.id = id; }

publicint hashCode() {    return (this.id == null) ? 0 : this.id.hashCode();   }

                   publicboolean equals(Object object) {

                       if (object instanceof OrderItem) {

                            final OrderItem obj = (OrderItem) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

}

                       returnfalse;

                   }

}
```
### 多对多映射
```
@Entity

@Table(name="tbl_student")

publicclass Student implements Serializable{、

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="student_id")

                   private Integer id;

                   private String name;

                   @ManyToMany(cascade=CascadeType.ALL,targetEntity=Teacher.class)

                   @JoinTable(name="tbl_stu_teacher",inverseJoinColumns={

                            @JoinColumn(name="teacher_id",referencedColumnName="teacher_id")},

                            joinColumns={@JoinColumn(name="student_id",referencedColumnName="student_id")})

                   private Set set=new HashSet();

                   public Student() {    }

                   public String getName() {       returnname; }

               publicvoid setName(String name) {  this.name = name; }

                   public Set getSet() {      returnset;       }

                   publicvoid setSet(Set set) {       this.set = set;   }

                   public Integer getId() {   returnthis.id;       }

                   publicvoid setId(Integer id) {     this.id = id; }

                   publicint hashCode() {    return (this.id == null) ? 0 : this.id.hashCode();   }

                   publicboolean equals(Object object) {

                       if (object instanceof Student) {

                            final Student obj = (Student) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

                       }

                       returnfalse;

                   }

}

-------------------------------------------------------------------------------------------------------------

@Entity

@Table(name="tbl_teacher")

publicclass Teacher implements Serializable{

                   @Id

                   @GeneratedValue(strategy = GenerationType.AUTO)

                   @Column(name="teacher_id")

                   private Integer id;

                   private String name;

                   @ManyToMany(targetEntity=Student.class,mappedBy="set")

                   private Set set=new HashSet();

                   public Teacher() {    }

               public String getName() {       returnname;      }

                   publicvoid setName(String name) {      this.name = name;      }

                   public Set getSet() {      returnset;       }

                   publicvoid setSet(Set set) {       this.set = set;   }

                   public Integer getId() {   returnthis.id;       }

                   publicvoid setId(Integer id) {     this.id = id; }

                   publicint hashCode() {    return (this.id == null) ? 0 : this.id.hashCode();   }

                   publicboolean equals(Object object) {

if (object instanceof Teacher) {

                            final Teacher obj = (Teacher) object;

                            return (this.id != null) ? this.id.equals(obj.id)    : (obj.id == null);

                       }

                       returnfalse;

                   }

}
```
