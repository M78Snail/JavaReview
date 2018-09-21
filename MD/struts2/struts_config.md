## **Struts2框架配置文件加载的顺序**

1. Struts2框架的核心是StrutsPrepareAndExecuteFilter过滤器，该过滤器有两个功能
    * Prepare       -- 预处理，加载核心的配置文件
    * Execute       -- 执行，让部分拦截器执行

2. StrutsPrepareAndExecuteFilter过滤器会加载哪些配置文件呢？
    * 通过源代码可以看到具体加载的配置文件和加载配置文件的顺序
        * init_DefaultProperties();                 -- 加载org/apache/struts2/default.properties
        * init_TraditionalXmlConfigurations();      -- 加载struts-default.xml,struts-plugin.xml,struts.xml
        * init_LegacyStrutsProperties();            -- 加载自定义的struts.properties.
        * init_CustomConfigurationProviders();      -- 加载用户自定义配置提供者
        * init_FilterInitParameters() ;             -- 加载web.xml

3. 重点了解的配置文件
    * **default.properties**        -- 在org/apache/struts2/目录下，代表的是配置的是Struts2的常量的值
    * **struts-default.xml**        -- 在Struts2的核心包下，代表的是Struts2核心功能的配置（Bean、拦截器、结果类型等）
    * **struts.xml**                -- 重点中的重点配置，代表WEB应用的默认配置，在工作中，基本就配置它就可以了！！（可以配置常量）
    * **web.xml**                   -- 配置前端控制器（可以配置常量）

    * 注意：
        * 前3个配置文件是struts2框架的默认配置文件，基本不用修改。
        * 后3个配置文件可以允许自己修改struts2的常量。但是有一个特点：后加载的配置文件修改的常量的值，会覆盖掉前面修改的常量的值。

4. 总结（重点掌握的配置文件）
    * 先加载default.properties文件，在org/apache/struts2/default.properties文件，都是常量。
    * 又加载struts-default.xml配置文件，在核心的jar包最下方，struts2框架的核心功能都是在该配置文件中配置的。
    * 再加载struts.xml的配置文件，在src的目录下，代表用户自己配置的配置文件
    * 最后加载web.xml的配置文件

    * 后加载的配置文件会覆盖掉之前加载的配置文件（在这些配置文件中可以配置常量）

5. 注意一个问题
    * 哪些配置文件中可以配置常量？
        * default.properties        -- 默认值，咱们是不能修改的！！
        * struts.xml                -- 可以配置，开发中基本上都在该配置文件中配置常量
        * struts.properties         -- 可以配置，基本不会在该配置文件中配置
        * web.xml                   -- 可以配置，基本不会在该配置文件中配置

    * 后加载的配置文件会覆盖掉之前加载的配置！！