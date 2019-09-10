# [DocTree][DocTree]
- [@Deprecated][Deprecated]
- [@Documented][Documented]
- [@Inherited][Inherited] 继承父类注解
- [@Retention(RetentionPolicy.RUNTIME)][Retention]
- [@Target(ElementType.ANNOTATION_TYPE)][Target]
- [@Entity][Entity]

# [junit][junit]
- [RunWith][RunWith] [@Inherited][Inherited]
- [Test][Test]

# [spring-boot-test][spring-boot-test]
- [SpringBootTest][SpringBootTest] [@Inherited][Inherited]

<!-- stereotype -->
# [spring-core][spring-core]
- [Component][Component]
- [Controller][Controller] [@Component][Component]
- [Repository][Repository] [@Component][Component]
- [Service][Service] [@Component][Component]

<!-- annotation spring-context -->
# [spring-context][spring-context]
- [Bean][Bean]
- [Configuration][Configuration] [@Component][Component]
- [Conditional][Conditional]
- [ComponentScan][ComponentScan]
- [Indexed][Indexed]
- [Profile][Profile] [@Conditional(ProfileCondition.class)][Conditional]

# [spring-test][spring-test]
- [ContextConfiguration][ContextConfiguration] [@Inherited][Inherited]

<!-- annotation spring-web-->
# [spring-web][spring-web]
- [GetMapping][GetMapping] [@RequestMapping(method = RequestMethod.GET)][RequestMapping]
- [Mapping][Mapping]
- [PathVariable][PathVariable]
- [RequestMapping][RequestMapping]
- [ResponseBody][ResponseBody]
- [RestController][RestController] [Controller][Controller] [ResponseBody][ResponseBody]


# [spring-boot][spring-boot]
- [SpringBootApplication][SpringBootApplication] [SpringBootConfiguration][SpringBootConfiguration] [EnableAutoConfiguration][EnableAutoConfiguration] [ConfigurationPropertiesScan][ConfigurationPropertiesScan]
 [ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })][ComponentScan]
- [ThymeleafProperties][ThymeleafProperties]
    ```
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    ```

# [spring-context][spring-context]

- [ApplicationContext][ApplicationContext]
    ```
	public interface ApplicationContext extends EnvironmentCapable, ListableBeanFactory, HierarchicalBeanFactory,
		MessageSource, ApplicationEventPublisher, ResourcePatternResolver {

	}
    ```
- [AbstractApplicationContext extends DefaultResourceLoader
		implements ConfigurableApplicationContext][AbstractApplicationContext]
    ```
    protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
        refreshBeanFactory();
        return getBeanFactory();
    }

    public void refresh() throws BeansException, IllegalStateException {
        synchronized (this.startupShutdownMonitor) {
            // Prepare this context for refreshing.
            //配置开始时间、active flag 还有其他初始化配置文件
            prepareRefresh();

            // Tell the subclass to refresh the internal bean factory.
            ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

            // Prepare the bean factory for use in this context.
            prepareBeanFactory(beanFactory);

            onRefresh();	
        }
    }
    ```
- [AbstractRefreshableApplicationContext extends AbstractApplicationContext][AbstractRefreshableApplicationContext]
    ```124
	private DefaultListableBeanFactory beanFactory;
    protected final void refreshBeanFactory() throws BeansException {
        if (hasBeanFactory()) {
            destroyBeans();
            closeBeanFactory();
        }
        try {
            DefaultListableBeanFactory beanFactory = createBeanFactory();
            beanFactory.setSerializationId(getId());
            customizeBeanFactory(beanFactory);
            loadBeanDefinitions(beanFactory); //载入bean定义 读取xml文件，从xml读取配置，生成bean并且注入
            synchronized (this.beanFactoryMonitor) {
                this.beanFactory = beanFactory;
            }
        }
        catch (IOException ex) {
            throw new ApplicationContextException("I/O error parsing bean definition source for " + getDisplayName(), ex);
        }
    }
    ```
- [AbstractRefreshableConfigApplicationContext][AbstractRefreshableConfigApplicationContext]
    ```
    public abstract class AbstractRefreshableConfigApplicationContext extends AbstractRefreshableApplicationContext
		implements BeanNameAware, InitializingBean {
	private String[] configLocations;
	}
	```
- [AbstractXmlApplicationContext extends AbstractRefreshableConfigApplicationContext][AbstractXmlApplicationContext]
    ```
    @Override
    protected void loadBeanDefinitions(DefaultListableBeanFactory beanFactory) throws BeansException, IOException {
        // Create a new XmlBeanDefinitionReader for the given BeanFactory.
        XmlBeanDefinitionReader beanDefinitionReader = new XmlBeanDefinitionReader(beanFactory);

        // Configure the bean definition reader with this context's
        // resource loading environment.
        beanDefinitionReader.setEnvironment(this.getEnvironment());
        beanDefinitionReader.setResourceLoader(this);
        beanDefinitionReader.setEntityResolver(new ResourceEntityResolver(this));

        // Allow a subclass to provide custom initialization of the reader,
        // then proceed with actually loading the bean definitions.
        initBeanDefinitionReader(beanDefinitionReader);
        loadBeanDefinitions(beanDefinitionReader);
    }
    //bean已经完成刷新，成功从xml文件中取出内容加入容器,父容器初始化完成,配置了DispatchServlet相关配置的开始进行子容器的初始化。
    ```

# [spring-core][spring-core]
- [DefaultResourceLoader implements ResourceLoader][DefaultResourceLoader]
- [ResourceLoader][ResourceLoader]

# [spring-web][spring-web]
- [ConfigurableWebApplicationContext][ConfigurableWebApplicationContext]
	```95
	void setConfigLocation(String configLocation);
	```
- [context][context]
	- [ContextLoader][ContextLoader] [ContextLoader.properties][ContextLoader.properties]
        ```260
        private static final String DEFAULT_STRATEGIES_PATH = "ContextLoader.properties";
        public static final String CONFIG_LOCATION_PARAM = "contextConfigLocation";
    
        public WebApplicationContext initWebApplicationContext(ServletContext servletContext) {
            if (this.context == null) {
                this.context = createWebApplicationContext(servletContext);
            }
            ...
            configureAndRefreshWebApplicationContext(cwac, servletContext);
        }
            
        protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac, ServletContext sc) {
            wac.setServletContext(sc);
            String configLocationParam = sc.getInitParameter(CONFIG_LOCATION_PARAM);
            if (configLocationParam != null) {
                wac.setConfigLocation(configLocationParam);
            }
        }
        ```
        
	- [ContextLoaderListener extends ContextLoader implements javax.servlet.ServletContextListener][ContextLoaderListener]
        ```102
        public void contextInitialized(ServletContextEvent event) {
            initWebApplicationContext(event.getServletContext());
        }
        ```
	- [ContextLoaderServlet] <!-- Log4jConfigServlet ContextLoaderPlugIn-->
	- [XmlWebApplicationContext][XmlWebApplicationContext]
        ```
		public static final String DEFAULT_CONFIG_LOCATION = "/WEB-INF/applicationContext.xml";
	    public static final String DEFAULT_CONFIG_LOCATION_PREFIX = "/WEB-INF/";
	    public static final String DEFAULT_CONFIG_LOCATION_SUFFIX = ".xml";

        protected String[] getDefaultConfigLocations() {
            if (getNamespace() != null) {
                return new String[] {DEFAULT_CONFIG_LOCATION_PREFIX + getNamespace() + DEFAULT_CONFIG_LOCATION_SUFFIX};
            }
            else {
                return new String[] {DEFAULT_CONFIG_LOCATION};
            }
        }
        ```
	- [WebApplicationContext extends ApplicationContext][WebApplicationContext]
        ```
        ServletContext getServletContext();
        ```

# [spring-webmvc][spring-webmvc]
- [DispatcherServlet extends FrameworkServlet][DispatcherServlet]
- [FrameworkServlet extends HttpServletBean implements ApplicationContextAware][FrameworkServlet]
    ```
	protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
	}
	protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
		Class<?> contextClass = getContextClass();
		if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
			throw new ApplicationContextException(
					"Fatal initialization error in servlet with name '" + getServletName() +
					"': custom WebApplicationContext class [" + contextClass.getName() +
					"] is not of type ConfigurableWebApplicationContext");
		}
		ConfigurableWebApplicationContext wac =
				(ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

		wac.setEnvironment(getEnvironment());
		wac.setParent(parent);
		String configLocation = getContextConfigLocation();
		if (configLocation != null) {
			wac.setConfigLocation(configLocation);
		}
		configureAndRefreshWebApplicationContext(wac);

		return wac;
	}

	protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac) {
		if (ObjectUtils.identityToString(wac).equals(wac.getId())) {
			// The application context id is still set to its original default value
			// -> assign a more useful id based on available information
			if (this.contextId != null) {
				wac.setId(this.contextId);
			}
			else {
				// Generate default id...
				wac.setId(ConfigurableWebApplicationContext.APPLICATION_CONTEXT_ID_PREFIX +
						ObjectUtils.getDisplayString(getServletContext().getContextPath()) + '/' + getServletName());
			}
		}

		wac.setServletContext(getServletContext());
		wac.setServletConfig(getServletConfig());
		wac.setNamespace(getNamespace());
		wac.addApplicationListener(new SourceFilteringListener(wac, new ContextRefreshListener()));

		// The wac environment's #initPropertySources will be called in any case when the context
		// is refreshed; do it eagerly here to ensure servlet property sources are in place for
		// use in any post-processing or initialization that occurs below prior to #refresh
		ConfigurableEnvironment env = wac.getEnvironment();
		if (env instanceof ConfigurableWebEnvironment) {
			((ConfigurableWebEnvironment) env).initPropertySources(getServletContext(), getServletConfig());
		}

		postProcessWebApplicationContext(wac);
		applyInitializers(wac);
		wac.refresh();
	}
    ```
- [AbstractAnnotationConfigDispatcherServletInitializer][AbstractAnnotationConfigDispatcherServletInitializer]
    ```
    //ContextLoaderListener
	protected Class<?>[] getRootConfigClasses() {
		<!-- return new Class[] { RootConfig.class }; -->
	}
    //DispatcherServlet
	protected Class<?>[] getServletConfigClasses() {
		<!-- return new Class[] { WebMvcConfig.class }; -->
	}

	protected String[] getServletMappings() {
		<!-- return new String[] { "/" }; -->
	}
    ```
- [web.xml][web.xml] ContextLoaderListener XmlWebApplicationContext


[java]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/
[DocTree]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/com/sun/source/doctree/DocTree.java
[Deprecated]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/java/lang/annotation/Deprecated.java
[Documented]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/java/lang/annotation/Documented.java
[Inherited]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/java/lang/annotation/Inherited.java
[Retention]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/java/lang/annotation/Retention.java
[Target]:https://github.com/jsonlog/jdkSample/blob/jdk8u/src/share/classes/java/lang/annotation/Target.java

[Entity]:../../../source/blob/master/javax.persistence/javax.persistence-api/javax/persistence/Entity.java

[junit]:https://github.com/jsonlog/source/blob/master/junit/junit
[RunWith]:https://github.com/jsonlog/source/blob/master/junit/junit/org/junit/runner/RunWith.java
[Test]:https://github.com/jsonlog/source/blob/master/junit/junit/org/junit/Test.java

[spring-beans]: spring-beans
[BeanFactory]: spring-beans/src/main/java/org/springframework/beans/factory/BeanFactory.java

[spring-boot]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot
[SpringBootConfiguration]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/SpringBootConfiguration.java

[spring-boot-test]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-test
[SpringBootTest]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-test/src/main/java/org/springframework/boot/test/context/SpringBootTest.java

[ConfigurationProperties]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/context/properties/ConfigurationProperties.java
[ConfigurationPropertiesScan]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/context/properties/ConfigurationPropertiesScan.java

[spring-boot-autoconfigure]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure
[EnableAutoConfiguration]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/EnableAutoConfiguration.java
[SpringBootApplication]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/SpringBootApplication.java

[ThymeleafProperties]:https://github.com/jsonlog/spring-boot/blob/master/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/thymeleaf/ThymeleafProperties.java

[spring-context]: spring-context
[Bean]:spring-context/src/main/java/org/springframework/context/annotation/Bean.java
[Component]:spring-context/src/main/java/org/springframework/stereotype/Component.java
[Controller]:spring-context/src/main/java/org/springframework/stereotype/Controller.java
[Indexed]:spring-context/src/main/java/org/springframework/stereotype/Indexed.java
[Repository]:spring-context/src/main/java/org/springframework/stereotype/Repository.java
[Service]:spring-context/src/main/java/org/springframework/stereotype/Service.java
[ApplicationContext]: spring-context/src/main/java/org/springframework/context/ApplicationContext.java

[AnnotationConfigUtils]:spring-context/src/main/java/org/springframework/context/annotation/AnnotationConfigUtils.java
[ClassPathBeanDefinitionScanner]:spring-context/src/main/java/org/springframework/context/annotation/ClassPathBeanDefinitionScanner.java
[ComponentScan]:spring-context/src/main/java/org/springframework/context/annotation/ComponentScan.java
[Conditional]:spring-context/src/main/java/org/springframework/context/annotation/Conditional.java
[Configuration]:spring-context/src/main/java/org/springframework/context/annotation/Configuration.java
[Profile]:spring-context/src/main/java/org/springframework/context/annotation/Profile.java

[AbstractApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractApplicationContext.java
[AbstractRefreshableApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractRefreshableApplicationContext.java
[AbstractRefreshableConfigApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractRefreshableConfigApplicationContext.java
[AbstractXmlApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractXmlApplicationContext.java

[spring-context.xsd]:spring-context/src/main/resources/org/springframework/context/config/spring-context.xsd

[spring-core]: spring-core
[DefaultResourceLoader]: spring-core/src/main/java/org/springframework/core/io/DefaultResourceLoader.java
[ResourceLoader]: spring-core/src/main/java/org/springframework/core/io/ResourceLoader.java
[ClassUtils]: spring-core/src/main/java/org/springframework/util/ClassUtils.java

[spring-test]: spring-test
[ContextConfiguration]:spring-test/src/main/java/org/springframework/test/context/ContextConfiguration.java

[spring-web]: spring-web
[GetMapping]:spring-web/src/main/java/org/springframework/web/bind/annotation/GetMapping.java
[Mapping]:spring-web/src/main/java/org/springframework/web/bind/annotation/Mapping.java
[PathVariable]:/Users/log/push/spring-projects/spring-framework/spring-web/src/main/java/org/springframework/web/bind/annotation/PathVariable.java
[RequestMapping]:spring-web/src/main/java/org/springframework/web/bind/annotation/RequestMapping.java
[RequestParam]:spring-web/src/main/java/org/springframework/web/bind/annotation/RequestParam.java
[RestController]:spring-web/src/main/java/org/springframework/web/bind/annotation/RestController.java
[ResponseBody]:spring-web/src/main/java/org/springframework/web/bind/annotation/ResponseBody.java
[context]: spring-web/src/main/java/org/springframework/web/context
[ConfigurableWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/ConfigurableWebApplicationContext.java
[ContextLoader]: spring-web/src/main/java/org/springframework/web/context/ContextLoader.java
[ContextLoaderListener]: spring-web/src/main/java/org/springframework/web/context/ContextLoaderListener.java
[XmlWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/support/XmlWebApplicationContext.java
[WebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/WebApplicationContext.java
[ContextLoader.properties]: spring-web/src/main/resources/org/springframework/web/context/ContextLoader.properties
<!-- org.springframework.web.context.WebApplicationContext=org.springframework.web.context.support.XmlWebApplicationContext
 -->

[spring-webmvc]: spring-webmvc
[DispatcherServlet]: spring-webmvc/src/main/java/org/springframework/web/servlet/DispatcherServlet.java
[FrameworkServlet]: spring-webmvc/src/main/java/org/springframework/web/servlet/FrameworkServlet.java
[AbstractAnnotationConfigDispatcherServletInitializer]:spring-webmvc/src/main/java/org/springframework/web/servlet/support/AbstractAnnotationConfigDispatcherServletInitializer.class

[web.xml]: spring-webmvc/src/test/resources/org/springframework/web/context/WEB-INF/web.xml


<!-- spring.factories -->