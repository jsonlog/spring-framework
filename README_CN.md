

# [spring-context][spring-context]
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
	protected String[] getDefaultConfigLocations() {
		if (getNamespace() != null) {
			return new String[] {DEFAULT_CONFIG_LOCATION_PREFIX + getNamespace() + DEFAULT_CONFIG_LOCATION_SUFFIX};
		}
		else {
			return new String[] {DEFAULT_CONFIG_LOCATION};
		}
	}
	```

# [spring-webmvc][spring-webmvc]
- [web.xml][web.xml] [ContextLoaderListener][ContextLoaderListener] [XmlWebApplicationContext][XmlWebApplicationContext]

[spring-context]: spring-context
[AbstractApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractApplicationContext.java
[AbstractRefreshableApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractRefreshableApplicationContext.java
[AbstractXmlApplicationContext]: spring-context/src/main/java/org/springframework/context/support/AbstractXmlApplicationContext.java

[spring-web]: spring-web
[context]: spring-web/src/main/java/org/springframework/web/context
[ConfigurableWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/ConfigurableWebApplicationContext.java
[ContextLoader]: spring-web/src/main/java/org/springframework/web/context/ContextLoader.java
[ContextLoaderListener]: spring-web/src/main/java/org/springframework/web/context/ContextLoaderListener.java
[XmlWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/support/XmlWebApplicationContext.java
[ContextLoader.properties]: spring-web/src/main/resources/org/springframework/web/context/ContextLoader.properties
<!-- org.springframework.web.context.WebApplicationContext=org.springframework.web.context.support.XmlWebApplicationContext
 -->

[spring-webmvc]: spring-webmvc
[web.xml]: spring-webmvc/src/test/resources/org/springframework/web/context/WEB-INF/web.xml


<!-- spring.factories -->