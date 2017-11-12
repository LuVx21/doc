---
title: Mybatis
date: 2016-08-25
tags:
- Java
- MyBatis
---


# jdbc开发

1）优点：简单易学,上手快,非常灵活构建SQL，效率高
2）缺点：代码繁琐，难以写出高质量的代码（例如：资源的释放，SQL注入安全性等）
开发者既要写业务逻辑，又要写对象的创建和销毁，必须管底层具体数据库的语法
（例如：分页）。
3）适合于超大批量数据的操作，速度快

# 回顾hibernate单表开发

1）优点：不用写SQL，完全以面向对象的方式设计和访问，不用管底层具体数据库的语法，（例如：分页）便于理解。
2）缺点：处理复杂业务时，灵活度差, 复杂的HQL难写难理解，例如多表查询的HQL语句
3）适合于中小批量数据的操作，速度慢

# 关于MyBatis

1）基于JDBC&hibernate二种支持，我们需要在中间找到一个平衡点呢？结合它们的优点，摒弃它们的缺点，
这就是myBatis，现今myBatis被广泛的企业所采用。
2）MyBatis 本是apache的一个开源项目iBatis, 2010年这个项目由apache software foundation 迁移到了google code，并且改名为MyBatis 。2013年11月迁移到Github。
3）iBATIS一词来源于“internet”和“abatis”的组合，是一个基于Java的持久层框架。iBATIS提供的持久层框架包括SQL Maps和Data Access Objects（DAO）
4）jdbc/dbutils/springdao，hibernate/springorm，mybaits同属于ORM解决方案之一

# MyBatis-Demo

创建一个mybatis-day01这么一个javaweb工程或java工程
导入mybatis和数据库的jar包到/WEB-INF/lib目录下
创建Student.java
public class Student {
    private Integer id;
    private String name;
    private Double sal;
    getter...;
    setter...;
}
在entity目录下创建StudentMapper.xml配置文件
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace属性是名称空间，必须唯一 -->
<mapper namespace="cn.itcast.javaee.mybatis.app04.Student">

    <!--
         resultMap标签:映射实体与表
         type属性：表示实体全路径名,下面使用了别名,对应配置文件<typeAliases></typeAliases>标签
         id属性：为实体与表的映射取一个任意的唯一的名字
    -->
    <resultMap type="student" id="studentMap">
        <!--
             id标签:映射主键属性
             result标签：映射非主键属性
             property属性:实体的属性名
             column属性：表的字段名
        -->
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="sal" column="sal"/>
    </resultMap>

    <!--
        insert标签：要书写insert,sql语句,类似还有select,delete,update等
        id属性：为insert,sql语句取一个任意唯一的名字
        parameterType:要执行的dao中的方法的参数，如查询时候,根据什么查询就是什么数据类型,如果是类的话，必须使用全路径类,下面的student对应配置文件中<typeAliases></typeAliases>标签.
        还有resultType属性,定义返回值的类型.
    -->
    <insert id="add1">
        insert into students(id,name,sal) values(1,'哈哈',7000)
    </insert>

    <insert id="add2" parameterType="student">
        insert into students(id,name,sal) values(#{id},#{name},#{sal})
    </insert>
</mapper>
在src目录下创建mybatis.xml配置文件
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE configuration PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">

<configuration>
    <!-- 加载类路径下的属性文件 -->
    <properties resource="db.properties"/>

    <!-- 设置类型别名 -->
    <typeAliases>
        <typeAlias type="cn.itcast.javaee.mybatis.app04.Student" alias="student"/>
    </typeAliases>

    <!-- 设置一个默认的连接环境信息,特别是内部有多个数据库连接时 -->
    <environments default="mysql_developer">
        <!-- 连接环境信息，取一个任意唯一的名字 -->
        <environment id="mysql_developer">
            <!-- mybatis使用jdbc事务管理方式 -->
            <transactionManager type="jdbc"/>
            <!-- mybatis使用连接池方式来获取连接 -->
            <dataSource type="pooled">
                <!-- 配置与数据库交互的4个必要属性,具体内容存储在db.properties中,上面已引用 -->
                <property name="driver" value="${mysql.driver}"/>
                <property name="url" value="${mysql.url}"/>
                <property name="username" value="${mysql.username}"/>
                <property name="password" value="${mysql.password}"/>
            </dataSource>
        </environment>
        <!-- 链接其他数据库可以继续添加上述的内容 -->
    </environments>

    <!-- 加载映射文件-->
    <mappers>
        <mapper resource="cn/itcast/javaee/mybatis/app14/StudentMapper.xml"/>
    </mappers>
</configuration>
在util目录下创建MyBatisUtil.java类
package cn.itcast.javaee.mybatis.util;

import java.io.IOException;
import java.io.Reader;
import java.sql.Connection;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

/**
 * 工具类
 * @author AdminTC
 */
public class MybatisUtil {
    private static ThreadLocal<SqlSession> threadLocal = new ThreadLocal<SqlSession>();
    private static SqlSessionFactory sqlSessionFactory;
    /**
     * 加载位于src/mybatis.xml配置文件
     */
    static{
        try {
            Reader reader = Resources.getResourceAsReader("mybatis.xml");
            sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
        } catch (IOException e) {
            e.printStackTrace();
            throw new RuntimeException(e);
        }
    }
    /**
     * 禁止外界通过new方法创建
     */
    private MybatisUtil(){}
    /**
     * 获取SqlSession
     */
    public static SqlSession getSqlSession(){
        //从当前线程中获取SqlSession对象
        SqlSession sqlSession = threadLocal.get();
        //如果SqlSession对象为空
        if(sqlSession == null){
            //在SqlSessionFactory非空的情况下，获取SqlSession对象
            sqlSession = sqlSessionFactory.openSession();
            //将SqlSession对象与当前线程绑定在一起
            threadLocal.set(sqlSession);
        }
        //返回SqlSession对象
        return sqlSession;
    }
    /**
     * 关闭SqlSession与当前线程分开
     */
    public static void closeSqlSession(){
        //从当前线程中获取SqlSession对象
        SqlSession sqlSession = threadLocal.get();
        //如果SqlSession对象非空
        if(sqlSession != null){
            //关闭SqlSession对象
            sqlSession.close();
            //分开当前线程与SqlSession对象的关系，目的是让GC尽早回收
            threadLocal.remove();
        }
    }
}
在dao目录下创建StudentDao.java类并测试
/**
 * 持久层
 * @author AdminTC
 */
public class StudentDao {
    /**
     * 增加学生
     */
    public void add1() throws Exception{
        SqlSession sqlSession = null;
        try{
            sqlSession = MybatisUtil.getSqlSession();
            //事务开始（默认）
            //读取StudentMapper.xml映射文件中的SQL语句
            int i = sqlSession.insert("cn.itcast.javaee.mybatis.app04.Student.add1");
            System.out.println("本次操作影响了"+i+"行");
            //事务提交
            sqlSession.commit();
        }catch(Exception e){
            e.printStackTrace();
            //事务回滚
            sqlSession.rollback();
            throw e;
        }finally{
            MybatisUtil.closeSqlSession();
        }
    }
    /**
     * 增加学生
     */
    public void add2(Student student) throws Exception{
        SqlSession sqlSession = null;
        try{
            sqlSession = MybatisUtil.getSqlSession();
            //事务开始（默认）
            //读取StudentMapper.xml映射文件中的SQL语句
            sqlSession.insert(Student.class.getName()+".add2",student);
            //事务提交
            sqlSession.commit();
        }catch(Exception e){
            e.printStackTrace();
            //事务回滚
            sqlSession.rollback();
            throw e;
        }finally{
            MybatisUtil.closeSqlSession();
        }
    }
}
MyBatis基础详解

上面的demo即是MyBatis入门级写法,下面对基础知识进行详解,可参看上面demo中代码.
MyBatis工作流程

1. 通过Reader对象读取src目录下的mybatis.xml配置文件(该文本的位置和名字可任意)
2. 通过SqlSessionFactoryBuilder对象创建SqlSessionFactory对象
3. 从当前线程中获取SqlSession对象
4. 事务开始，在mybatis中默认
5. 通过SqlSession对象读取StudentMapper.xml映射文件中的操作编号，从而读取sql语句
6. 事务提交，必写
7. 关闭SqlSession对象，并且分开当前线程与SqlSession对象，让GC尽早回收
2个XML文件详解

实体映射文件

参看上面在entity目录下创建StudentMapper.xml配置文件
MyBatis配置文件

参看上面在src目录下创建mybatis.xml配置文件
分页查询

    /**
     * 无条件分页查询学生
     */
    public List<Student> findAllWithFy(int start,int size) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            Map<String,Integer> map = new LinkedHashMap<String,Integer>();
            map.put("pstart",start);
            map.put("psize",size);
            return sqlSession.selectList("mynamespace.findAllWithFy",map);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }

/**
     * 有条件分页查询学生
     */
    public List<Student> findAllByNameWithFy(String name,int start,int size) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            Map<String,Object> map = new LinkedHashMap<String,Object>();
            map.put("pname","%"+name+"%");
            map.put("pstart",start);
            map.put("psize",size);
            return sqlSession.selectList("mynamespace.findAllByNameWithFy",map);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }

对应映射文件
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mynamespace">
    <insert id="add" parameterType="cn.itcast.javaee.mybatis.app10.Student">
        insert into students(id,name,sal) values(#{id},#{name},#{sal})
    </insert>
    <select id="findAllWithFy" parameterType="map" resultType="cn.itcast.javaee.mybatis.app10.Student">
        select id,name,sal from students limit #{pstart},#{psize}
    </select>
    <select id="findAllByNameWithFy" parameterType="map" resultType="cn.itcast.javaee.mybatis.app10.Student">
        select id,name,sal from students where name like #{pname} limit #{pstart},#{psize}
    </select>
</mapper>

动态查询

查询条件不确定，需要根据情况产生SQL语法，这种情况叫动态SQL
查询

    /**
     * 动态SQL--查询
     */
    public List<Student> dynaSQLwithSelect(String name,Double sal) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            Map<String,Object> map = new LinkedHashMap<String, Object>();
            map.put("pname",name);
            map.put("psal",sal);
            return sqlSession.selectList("mynamespace.dynaSQLwithSelect",map);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mynamespace">
    <select id="dynaSQLwithSelect" parameterType="map" resultType="cn.itcast.javaee.mybatis.app11.Student">
        select id,name,sal from students
        <where>
            <if test="pname!=null">
                and name=#{pname}
            </if>
            <if test="psal!=null">
                and sal=#{psal}
            </if>
        </where>
    </select>
</mapper>

更新

    /**
     * 动态SQL--更新
     */
    public void dynaSQLwithUpdate(Student student) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            sqlSession.update("mynamespace.dynaSQLwithUpdate",student);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mynamespace">
    <update id="dynaSQLwithUpdate" parameterType="cn.itcast.javaee.mybatis.app12.Student">
        update students
        <set>
            <if test="name!=null">
                name=#{name},
            </if>
            <if test="sal!=null">
                sal=#{sal},
            </if>
        </set>
        where id=#{id}
    </update>
</mapper>

删除

    /**
     * 动态SQL--删除
     */
    public void dynaSQLwithDelete(int... ids) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            sqlSession.delete("mynamespace.dynaSQLwithDelete",ids);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }
    //上述中参数也可以使用list,其余不变,但映射文件中,collection="array"应该为collection="list"
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mynamespace">
    <!-- item表示迭代的参数 -->
    <delete id="dynaSQLwithDelete">
        delete from students where id in
        <!--
        <foreach collection="array" open="(" close=")" separator="," item="ids">
            ${ids}
        </foreach>
        -->
        <!--
             foreach用于迭代数组元素
             open表示开始符号
             close表示结束符合
             separator表示元素间的分隔符
             item表示迭代的数组，属性值可以任意，但提倡与方法的数组名相同
             #{ids}表示数组中的每个元素值
         -->
        <foreach collection="list" open="(" close=")" separator="," item="ids">
            ${ids}
        </foreach>
    </delete>
</mapper>

插入

    /**
     * 动态SQL--插入
     */
    public void dynaSQLwithInsert(Student student) throws Exception{
        SqlSession sqlSession = MyBatisUtil.getSqlSession();
        try{
            sqlSession.insert("mynamespace.dynaSQLwithInsert",student);
        }catch(Exception e){
            e.printStackTrace();
            sqlSession.rollback();
            throw e;
        }finally{
            sqlSession.commit();
            MyBatisUtil.closeSqlSession();
        }
    }

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="mynamespace">
    <sql id="key">
        <trim suffixOverrides=",">
            <if test="id!=null">
                id,
            </if>
            <if test="name!=null">
                name,
            </if>
            <if test="sal!=null">
                sal,
            </if>
        </trim>
    </sql>
    <sql id="value">
        <trim suffixOverrides=",">
            <if test="id!=null">
                #{id},
            </if>
            <if test="name!=null">
                #{name},
            </if>
            <if test="sal!=null">
                #{sal},
            </if>
        </trim>
    </sql>
    <insert id="dynaSQLwithInsert" parameterType="cn.itcast.javaee.mybatis.app14.Student">
        insert into students(<include refid="key"/>) values(<include refid="value"/>)
    </insert>
</mapper>