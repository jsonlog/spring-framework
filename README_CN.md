

# [spring-web](spring-web)
- [ConfigurableWebApplicationContext][ConfigurableWebApplicationContext]
```95
	void setConfigLocation(String configLocation);
```
- [context][context]
	- [ContextLoader][ContextLoader]
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
        
- [ContextLoaderListener extends ContextLoader][ContextLoaderListener]
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

# [spring-webmvc](spring-webmvc)
- [web.xml][web.xml]
- [#ContextLoaderListener][ContextLoaderListener]
- [#XmlWebApplicationContext][XmlWebApplicationContext]




[ConfigurableWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/ConfigurableWebApplicationContext.java
[context]: spring-web/src/main/java/org/springframework/web/context
[ContextLoader]: spring-web/src/main/java/org/springframework/web/context/ContextLoader.java
[ContextLoaderListener]: spring-web/src/main/java/org/springframework/web/context/ContextLoaderListener.java
[XmlWebApplicationContext]: spring-web/src/main/java/org/springframework/web/context/support/XmlWebApplicationContext.java
[web.xml]: spring-webmvc/src/test/resources/org/springframework/web/context/WEB-INF/web.xml
