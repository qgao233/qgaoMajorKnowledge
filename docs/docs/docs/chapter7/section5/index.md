# sqlSessionFactory和sqlSession

## sqlSessionFactory

XML配置文件或一个预先定制的Configuration的实例构建出SqlSessionFactory的实例.

每一个MyBatis的应用程序都以一个SqlSessionFactory对象的实例为核心.同时SqlSessionFactory也是线程安全的,SqlSessionFactory一旦被创建,应该在应用执行期间都存在.在应用运行期间不要重复创建多次,建议使用单例模式.SqlSessionFactory是创建SqlSession的工厂.

## sqlSession

SqlSession是MyBatis的关键对象,是执行持久化操作的独享,类似于JDBC中的Connection.它是应用程序与持久层之间执行交互操作的一个单线程对象,也是MyBatis执行持久化操作的关键对象.

SqlSession对象完全包含以数据库为背景的所有执行SQL操作的方法,它的底层封装了JDBC连接,可以用SqlSession实例来直接执行被映射的SQL语句.每个线程都应该有它自己的SqlSession实例.

SqlSession的实例不能被共享,同时SqlSession也是线程不安全的,绝对不能将SqlSeesion实例的引用放在一个类的静态字段甚至是实例字段中.也绝不能将SqlSession实例的引用放在任何类型的管理范围中,比如Servlet当中的HttpSession对象中.使用完SqlSeesion之后关闭Session很重要,应该确保使用finally块来关闭它.

## 使用

```
public class MyBatisTest {

    public static void main(String[] args) {
        try {
            //读取mybatis-config.xml文件
            InputStream resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
            //初始化mybatis,创建SqlSessionFactory类的实例
            SqlSessionFactory sqlSessionFactory =  new SqlSessionFactoryBuilder().build(resourceAsStream);
            //创建session实例
            SqlSession session = sqlSessionFactory.openSession();
            /*
             * 接下来在这里做很多事情,到目前为止,目的已经达到得到了SqlSession对象.通过调用SqlSession里面的方法,
             * 可以测试MyBatis和Dao层接口方法之间的正确性,当然也可以做别的很多事情,在这里就不列举了
             */
            //插入数据
            User user = new User();
            user.setC_password("123");
            user.setC_username("123");
            user.setC_salt("123");
            //第一个参数为方法的完全限定名:位置信息+映射文件当中的id
            session.insert("com.cn.dao.UserMapping.insertUserInformation", user);
            //提交事务
            session.commit();
            //关闭session
            session.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

得到SqlSession对象之后就可以利用SqlSession内部的方法进行CRUD操作了。

注意一点，Connection对象是在SqlSession对象创建之后进行CURD操作中创建的。深入查找之后找到在ManagedTransaction类中找到获取Connection对象的关键代码如下：

```
protected void openConnection() throws SQLException {
    if (log.isDebugEnabled()) {
      log.debug("Opening JDBC Connection");
    }
    //dataSource 来源有三种，JndiDatasource，PooledDataSource，UnpooledDataSource，配置文件中定义
    this.connection = this.dataSource.getConnection();
    if (this.level != null) {
      this.connection.setTransactionIsolation(this.level.getLevel());
    }
}
```

