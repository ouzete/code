实现Myibatis动态sql跨数据库的处理 
Spring动态配置多数据源，即在大型应用中对数据进行切分，并且采用多个数据库实例进行管理，这样可以有效提高系统的水平伸缩性。而这样的方案就会不 同于常见的单一数据实例的方案，这就要程序在运行时根据当时的请求及系统状态来动态的决定将数据存储在哪个数据库实例中，以及从哪个数据库提取数据。 
　　Spring配置多数据源的方式和具体使用过程。 
　　Spring对于多数据源，以数据库表为参照，大体上可以分成两大类情况： 
　　一是，表级上的跨数据库。即，对于不同的数据库却有相同的表（表名和表结构完全相同）。 
　　二是，非表级上的跨数据库。即，多个数据源不存在相同的表。 
　　Spring2.x的版本中采用Proxy模式，就是我们在方案中实现一个虚拟的数据源，并且 用它来封装数据源选择逻辑，这样就可以有效地将数据源选择逻辑从Client中分离出来。Client提供选择所需的上下文（因为这是Client所知道 的），由虚拟的DataSource根据Client提供的上下文来实现数据源的选择。 
　　具体的实现就是，虚拟的DataSource仅需继承AbstractRoutingDataSource实现determineCurrentLookupKey（）在其中封装数据源的选择逻辑。 
　　一、动态配置多数据源 
　　1. 数据源的名称常量类： 
package com.hope.common.data; 

public class DataSourceConst { 
	public static final String MANAGER = "MANAGER"; 
	public static final String STMT = "STMT"; 
	public static final String NODB = "NODB"; 
} 
2. 建立一个获得和设置上下文环境的类，主要负责改变上下文数据源的名称： 

public class DataSourceHandle { 
	private static final ThreadLocal contextHolder = new ThreadLocal(); // 线程本地环境 

// 设置数据源类型 
public static void setDataSourceType(String dataSourceType) { 
	contextHolder.set(dataSourceType); 
	} 

	// 获取数据源类型 
public static String getDataSourceType() { 
	return (String) contextHolder.get(); 
	} 

	// 清除数据源类型 
public static void clearDataSourceType() { 
	contextHolder.remove(); 
	} 
} 
　 
3. 建立动态数据源类，注意，这个类必须继承AbstractRoutingDataSource，且实现方法 determineCurrentLookupKey，该方法返回一个Object，一般是返回字符串： 
package com.hope.datasource; 
　　public class DynamicDataSource extends AbstractRoutingDataSource { 

     public DynamicDataSource(){ 
    
     } 
	@Override 
	protected Object determineCurrentLookupKey() { 
	// 在进行DAO操作前，通过上下文环境变量，获得数据源的类型 
return DataSourceHandle.getDataSourceType(); 
	} 

}　　4. 编写spring的配置文件配置多个数据源 
　<!-- spring配置多数据源 --> 
	<bean id="parentDataSource" 
	class="org.springframework.jdbc.datasource.DriverManagerDataSource"> 
	<property name="driverClassName" value="net.sourceforge.jtds.jdbc.Driver"></property> 
	<property name="username" value="sa"></property> 
	<property name="password" value="sa</property> 

	</bean> 
	<!-- 以下是配置各个数据库的特性 --> 

	<!-- 数据库 --> 

	<bean id="NODBDataSource" parent="parentDataSource"> 
	<property name="url" value="jdbc:jtds:sqlserver://localhost:1433/" /> 
	</bean> 
	<bean id="managerDataSource" parent="parentDataSource"> 
	<property name="url" 
	value="jdbc:jtds:sqlserver://localhost:1433/test1 
	</bean> 
	<bean id="statDataSource" parent="parentDataSource"> 
	<property name="url" value="jdbc:jtds:sqlserver://localhost:1433/test2 
	</bean> 


	<bean id="dataSource" class="com.hope.common.data.DynamicDataSource"> 
	<property name="targetDataSources"> 
	<map key-type="java.lang.String"> 
	<entry value-ref="managerDataSource" key="MANAGER"></entry> 
	<entry value-ref="statDataSource" key="STMT"></entry> 
	<entry value-ref="NODBDataSource" key="NODB"></entry> 
	</map> 
	</property> 
	<property name="defaultTargetDataSource" ref="managerDataSource"></property> 
	</bean> 

　 
　　5. ibatis 相关sql 
　　     
  <select id="selectDBDictionary" resultMap="BaseResultMap" parameterType="java.lang.Integer" > 
  
 select a.id,a.parentid,a.cname,a.priority,a.ifactive from hopetas_manager.dbo.dictionary a inner join zj.dbo.dictionary b on a.id=b.id  where a.parentid= #{id,jdbcType=INTEGER} 

   </select>　 

6.程序调用： 在DAO操作前处理set setDataSourceType 的值 

// 利用递归来处理字段 
public void getDict0(Eement element, Integer id,Integer j) { 
// 设置数据源 
DataSourceHandle.setDataSourceType(DataSourceConst.NODB); 
	ArrayList list = (ArrayList) this.dictMapper 
	.selectDBDictionary(id); 
	String str = "dir"; 
	j++; 
	if (list.size() != 0 && list != null) { 
	for (Iterator iter = list.iterator(); iter.hasNext();) { 
	Dictionary dict = (Dictionary) iter.next(); 
	Element el = element.addElement(str+j); 
	Element prop1 = el.addAttribute("id", dict.getId().toString()); 
	Element prop2 = el.addAttribute("name", dict.getCname() 
	.toString()); 
	getDict0(el, dict.getId(),j); 
	} 
	} 

	} 

