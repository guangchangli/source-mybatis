# mybatis

1. ### 架构

   - 基础支持层
   - 核心处理层
   - 接口层

   ``` zhanw
   基础支持层
   数据源模块
   事务管理模块
   缓存模块 mybatis 两级缓存是在同一个 jvm 中，共享堆内存，太大不好 
   Binging模块 将自定义方法和映射文件对应起来，尽早发现问题
   反射模块  封装了反射 
   类型转换  Type (别名，jdbc 类型 java 类型转换 入参数绑定，结果集映射)
   日志模块
   资源加载 io 封装类加载器，确定类加载器的顺序，提供加载类文件以及资源文件的功能
   解析器模块 parsing 初始化 解析 config.xml 封装 xpath 动态sql占位符
   ```

   ```
   注解模块 annotations
   异常模块 exceptions  定义了专有的 PersistenceException 和 TooManyResultsException 异常
   配置解析模块 building mapping 配置解析 sql 解析后映射
   sql 解析  scripting 解析 动态 sql 形成数据库执行的语句 处理占位符 绑定入参
   sql 执行 executor cursor 执行器 执行结果游标
     执行涉及到的主要组件 executor StatementHandler ParamerHandler ResultSetHandler
   插件层 plugins 可以自定义拦截 sql 改写
   接口层 session
   
   ```

   **整体流程(逆时针)**

   ```
   接口层													SqlSession
   ----------------------------｜-----------｜^ --------------------
   
   核心层 												
   														   Executor
   															｜         ｜^													 缓存模块
   															StatementHandler												scripting 模块
   											|													|^								->  事务模块
   															
   						ParameterHandler											ResultSetHandler
               ｜																				｜^
               Statement															ResultSet
               			｜															|^
               											数据库
   ```

2. ### 解析器模块(org.apache.ibatis.parsing)

   - 功能

     ```
     封装 XPath,初始化解析config.xml 支持映射文件，支持动态 sql 占位符
     ```

     ```
      * <p>An XPath object is not thread-safe and not reentrant.
      * In other words, it is the application's responsibility to make
      * sure that one {@link XPath} object is not used from
      * more than one thread at any given time, and while the <code>evaluate</code>
      * method is invoked, applications may not recursively call
      * the <code>evaluate</code> method.
      * <p>
     ```

     ```
     XPathParser 提供了一系列的 #eval* 方法，用于获得 Boolean、Short、Integer、Long、Float、Double、String、Node 类型的元素或节点的“值”
     ```

     ```
     org.apache.ibatis.builder.xml.XMLMapperEntityResolver ，实现 EntityResolver 接口，MyBatis 自定义 EntityResolver 实现类，用于加载本地的 mybatis-3-config.dtd 和 mybatis-3-mapper.dtd
     ```

     ```
     org.apache.ibatis.parsing.PropertyParser ，动态属性解析器
     ```

3. ### 反射

