### 16.3.6 对比由容器管理的和本地定义的资源

开发者可以在不修改一行代码的情况下，在容器管理的JNDI`SessionFactory`和本地定义的`SessionFactory`之间进行切换。是否将资源定义保留在容器中，还是仅仅留在应用中，都取决于开发者使用的事务策略。相对于Spring定义的本地`SessionFactory`来说，手动注册的JNDI`SessionFactory`没有什么优势。通过Hibernate的JCA连接器来发布一个`SessionFactory`只会令代码更符合J2EE服务标准，但是并不会带来任何实际的价值。

Spring对事务支持不限于容器。使用除JTA之外的任何策略配置，事务都可以在独立或测试环境中工作。特别是在单数据库事务的典型情况下，Spring的单一资源本地事务支持是一种轻量级和强大的替代JTA的解决方案。当开发者使用本地EJB无状态会话Bean来驱动事务时，即使只访问单个数据库，并且只使用无状态会话Bean来通过容器管理的事务来提供声明式事务，开发者的代码依然是依赖于EJB容器和JTA的。同时，以编程方式直接使用JTA也需要一个J2EE环境的。 JTA不涉及JTA本身和JNDI DataSource实例方面的容器依赖关系。对于非Spring，JTA驱动的Hibernate事务，开发者必须使用Hibernate JCA连接器或开发额外的Hibernate事务代码，并为JVM级缓存正确配置`TransactionManagerLookup`。

Spring驱动的事务可以与本地定义的Hibernate`SessionFactory`一样工作，就像本地JDBC DataSource访问单个数据库一样。但是，当开发者有分布式事务的要求的情况下，只能选择使用Spring JTA事务策略。JCA连接器是需要特定容器遵循一致的部署步骤的，而且显然JCA支持是需要放在第一位的。JCA的配置需要比部署本地资源定义和Spring驱动事务的简单web应用程序需要更多额外的的工作。同时，开发者还需要使用容器的企业版，比如，如果开发者使用的是WebLogic Express的非企业版，就是不支持JCA的。具有跨越单个数据库的本地资源和事务的Spring应用程序适用于任何基于J2EE的Web容器（不包括JTA，JCA或EJB），如Tomcat，Resin或甚至是Jetty。此外，开发者可以轻松地在桌面应用程序或测试套件中重用中间层代码。

综合前面的叙述，如果不使用EJB，请尽量使用本地的`SessionFactory`设置和Spring的`HibernateTransactionManager`或`JtaTransactionManager`。开发者能够得到了前面提到的所有好处，包括适当的事务性JVM级缓存和分布式事务支持，而且没有容器部署的不便。只有必须配合EJB使用的时候，JNDI通过JCA连接器来注册Hibernate`SessionFactory`才有价值。

