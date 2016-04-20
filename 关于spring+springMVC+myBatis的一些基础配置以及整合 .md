web.xml

 

 <?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.4" 
xmlns="http://java.sun.com/xml/ns/j2ee" 
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee 
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd">
  <welcome-file-list>
    <welcome-file>index.jsp</welcome-file>
  </welcome-file-list>
  
  <!--
  设置session过期的时间
  -->
  <session-config>
  <session-timeout>20</session-timeout>
  </session-config>
  
  <!--
  读取spring的配置文件
  -->
 <context-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath:config/spring.xml;classpath:config/spring-myBatis.xml</param-value>
  </context-param>
  
  <listener>
  <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>
  
   <listener>
    <listener-class>org.springframework.web.util.IntrospectorCleanupListener</listener-class>
  </listener>
  
<!--
  设置字符编码，将所有的字符编码同意设置为utf-8
  -->
  <filter>
  <filter-name>filterEncoding</filter-name>
  <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
  <init-param>
  <param-name>encoding</param-name>
  <param-value>utf-8</param-value>
  </init-param>
  </filter>
  
  <filter-mapping>
  <filter-name>filterEncoding</filter-name>
  <url-pattern>/</url-pattern>
  </filter-mapping>
  
  <!--
  生成一次性验证码的servlet
  -->
  <servlet>
  <servlet-name>verifyCode</servlet-name>
  <servlet-class>com.longhang.tool.verifyCode.VerifyCodeServlet</servlet-class>
  </servlet>
  
  <!--
  将所有*.do的请求交给springMVC的DispatcherServlet来处理
  -->
  <servlet>
  <servlet-name>DispatcherServlet</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <init-param>
  <param-name>contextConfigLocation</param-name>
  <param-value>classpath:config/springMVC-config.xml</param-value>
  </init-param>
  <load-on-startup>1</load-on-startup>
  </servlet>
  
  <servlet-mapping>
  <servlet-name>verifyCode</servlet-name>
  <url-pattern>/verifyCode</url-pattern>
  </servlet-mapping>
  
  <servlet-mapping>
  <servlet-name>DispatcherServlet</servlet-name>
  <url-pattern>*.do</url-pattern>
  </servlet-mapping>
</web-app>

springMVC的配置文件

<?xml version="1.0" encoding="UTF-8"?>   


  <beans xmlns="http://www.springframework.org/schema/beans"     


       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:p="http://www.springframework.org/schema/p"     


        xmlns:context="http://www.springframework.org/schema/context"     


   xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans-3.0.xsd   


       http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-3.0.xsd   


       http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-3.0.xsd   


       http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-3.0.xsd">   

 <!--
  配置自动扫描的包，让其扫描    com.longhang,controller下面的所有包
 -->
  
  <context:component-scan base-package = "com.longhang.controller"></context:component-scan>
  
 <!--  
  配置视图解析器
  将视图逻辑名解析为/*.jsp
 -->
  <bean class = "org.springframework.web.servlet.view.InternalResourceViewResolver">
  <property name = "prefix" value = "/"></property>
  <property name = "suffix" value = ".jsp"></property>
  </bean>
  </beans>


spring.xml的配置

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context" xsi:schemaLocation="
http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
http://www.springframework.org/schema/context
http://www.springframework.org/schema/context/spring-context-3.0.xsd
">
<context:property-placeholder location="classpath:config/druid.properties" />
<!-- 自动扫描(自动注入) -->
<context:component-scan base-package = "com.longhang.service"></context:component-scan>


</beans>



spring-myBatis.xml配置

<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tx="http://www.springframework.org/schema/tx" xmlns:aop="http://www.springframework.org/schema/aop" xsi:schemaLocation="
http://www.springframework.org/schema/beans 
http://www.springframework.org/schema/beans/spring-beans-3.0.xsd 
http://www.springframework.org/schema/tx 
http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
http://www.springframework.org/schema/aop 
http://www.springframework.org/schema/aop/spring-aop-3.0.xsd
">
    <!-- 配置数据源 --><!--
    <bean name = "datasource" class = "com.alibaba.druid.pool.DruidDataSource" init-method = "init" destroy-method = "close">
    <property name ="url" value = "${jdbc_url}"></property>
    <property name="username" value="${jdbc_userName}" />
 <property name="password" value="${jdbc_password}" />
    </bean>
    
     --><bean name = "datasource" class = "com.alibaba.druid.pool.DruidDataSource" init-method = "init" destroy-method = "close">
    <property name ="url" value = "jdbc:mysql://localhost:8000/bookShopping"></property>
    <property name="username" value="root" />
 <property name="password" value="13072399672" />
     </bean>
    
    <!--配置sqlSessionFactory 并读取mybatis的一些配置-->
    <bean name = "sqlSessionFactory" class = "org.mybatis.spring.SqlSessionFactoryBean">
    <property name = "dataSource" ref = "datasource"></property>
    <property name="mapperLocations" value="classpath:mapper/*.xml"/>
    </bean>
    
    <!--
    自动扫描 将Mapper接口生成代理注入到Spring
    -->
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
<property name="basePackage" value="com.longhang.dao" />
<property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
</bean>
    
    <!--
    配置事物
    -->
   <bean id = "transactionManager" class = "org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name = "dataSource" ref = "datasource"></property>
    </bean>
    
    <!--
    <tx:annotation-driven transaction-manager = "transactionManager"/>
    -->
  
   <!--
    事物的具体内容
   -->
<tx:advice id="transactionAdvice" transaction-manager="transactionManager">
<tx:attributes>
<tx:method name="add*" propagation="REQUIRED" />
<tx:method name="append*" propagation="REQUIRED" />
<tx:method name="insert*" propagation="REQUIRED" />
<tx:method name="save*" propagation="REQUIRED" />
<tx:method name="update*" propagation="REQUIRED" />
<tx:method name="modify*" propagation="REQUIRED" />
<tx:method name="edit*" propagation="REQUIRED" />
<tx:method name="delete*" propagation="REQUIRED" />
<tx:method name="remove*" propagation="REQUIRED" />
<tx:method name="repair" propagation="REQUIRED" />
<tx:method name="delAndRepair" propagation="REQUIRED" />


<tx:method name="get*" propagation="SUPPORTS" />
<tx:method name="find*" propagation="SUPPORTS" />
<tx:method name="load*" propagation="SUPPORTS" />
<tx:method name="search*" propagation="SUPPORTS" />
<tx:method name="datagrid*" propagation="SUPPORTS" />


<tx:method name="*" propagation="SUPPORTS" />
</tx:attributes>
</tx:advice>

<!--
    定义一个切面,在定义的切面上加入事物
   -->
    <aop:config>
<aop:pointcut id="transactionPointcut" expression="execution(* com.longhang.service..*Impl.*(..))" />
<aop:advisor pointcut-ref="transactionPointcut" advice-ref="transactionAdvice" />
</aop:config>

</beans>


关于mybatis的映射文件

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd" >
<mapper namespace="com.longhang.dao.userDao.UserDao" >
  <resultMap id = "baseResultMap" type = "com.longhang.entity.user.User">
  <id column = "uid" property = "uid" jdbcType = "CHAR"/>
  <result column = "loginname" property = "loginname" jdbcType = "VARCHAR"/>
  <result column = "loginpass" property = "loginpass" jdbcType = "VARCHAR"/>
  <result column = "email" property = "email" jdbcType = "VARCHAR"/>
  <result column = "status" property = "status" jdbcType = "VARCHAR"/>
  <result column = "activationCode" property = "activationCode" jdbcType = "CHAR"/>
  </resultMap>
  
  
  <sql id = "base_column_list">
  uid,loginname,loginpass,email,status,activationCode
  </sql>
 
  <!--根据id查询
   返回的类型为User
  -->
  <select id="findById" resultMap="baseResultMap" parameterType="java.lang.String" >
    select 
    <include refid="base_column_list" />
    from l_user
    where uid = #{uid,jdbcType=CHAR}
  </select>
  
  <!--
  根据activationCode查询
  返回值是User
  -->
  
  <select id = "findByActivationCode" resultMap = "baseResultMap" parameterType = "java.lang.String">
  select <include refid = "base_column_list"/>
  from l_user
  where activationCode = #{activationCode}
  </select>
  
  <!--
  根据loginname查看总条数
  -->
  <select id = "countNumberByLoginName" resultType = "int" parameterType="java.lang.String">
  select count(*) from l_user where loginname = #{loginname}
  </select>
  
  <!-- 
  根据email查看总条数
   -->
   <select id = "countNumberByEmail" resultType = "int" parameterType = "java.lang.String">
    select count(*) from l_user where email = #{email}
   </select>
   
   <!--
   
   -->
   <select id = "isActivation" resultType = "int" parameterType = "java.lang.String">
   select count(*) from l_user where activationCode = #{activationCode}
   </select>


  <delete id = "deleteById" parameterType = "java.lang.String">
  delete from l_user
  where uid = #{uid,jdbcType=CHAR}
  </delete>
  
  
<insert id = "addUser" parameterType = "com.longhang.entity.user.User">
  insert into l_user(uid,loginname,loginpass,email,status,activationCode)
  values(#{uid},#{loginname},#{loginpass},#{email},#{status},#{activationCode})
 </insert>
    <!--
  <insert id="insertSelective" parameterType="com.bky.model.Add" >
    insert into tadd
    <trim prefix="(" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        id,
      </if>
      <if test="tname != null" >
        tname,
      </if>
      <if test="tpwd != null" >
        tpwd,
      </if>
    </trim>
    <trim prefix="values (" suffix=")" suffixOverrides="," >
      <if test="id != null" >
        #{id,jdbcType=VARCHAR},
      </if>
      <if test="tname != null" >
        #{tname,jdbcType=VARCHAR},
      </if>
      <if test="tpwd != null" >
        #{tpwd,jdbcType=VARCHAR},
      </if>
    </trim>
  </insert>-->
  <!--
  <update id="updateByPrimaryKeySelective" parameterType="com.bky.model.Add" >
    update tadd
    <set >
      <if test="tname != null" >
        tname = #{tname,jdbcType=VARCHAR},
      </if>
      <if test="tpwd != null" >
        tpwd = #{tpwd,jdbcType=VARCHAR},
      </if>
    </set>
    where id = #{id,jdbcType=VARCHAR}
  </update>-->


  <update id = "updateById" parameterType="com.longhang.entity.user.User">
  update l_user
  set uid = #{uid},
     loginname = #{loginname},
     loginpass = #{loginpass},
     email = #{email},
     status = #{status},
     activationCode = #{activationCode}
     where uid = #{uid}
  </update>
  
  <update id="updatePropByID"> 
update  l_user set
<foreach collection="prop" item="item" index="index" separator=",">
          ${item.key} = #{item.value}
        </foreach>
        <where>
        uid = #{uid}
        </where>
</update>

<update id="updatePropByCondition">
<![CDATA[  
update  l_user set
]]>
<foreach collection="prop" item="item" index="index" separator=",">
          ${item.key} = #{item.value}
        </foreach>
        <where>
        <if test="condition != null">
        ${condition}
        </if>
        </where>
</update>
  
  <select id="getAll" resultMap="baseResultMap">
  SELECT * FROM uid
  </select>
</mapper>


接口

package com.longhang.dao.userDao;


import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.Map.Entry;


import org.apache.ibatis.annotations.Param;


import com.longhang.entity.user.User;


public interface UserDao {
public User findById(String uid);
public int deleteById(String uid);
public int addUser(User user);
public int updateById(String uid);
public List<User> getAll();
 
public int countNumberByLoginName(String loginname);
public int countNumberByEmail(String email);
public int isActivation(String activationCode);
public User findByActivationCode(String activationCode);
 
public int updatePropByID(@Param("prop")Set<Entry<String, Object>> prop,@Param("uid")String id);
public int updatePropByCondition(@Param("prop")Set<Entry<String,Object>> prop,@Param("condition")String condition);
}
