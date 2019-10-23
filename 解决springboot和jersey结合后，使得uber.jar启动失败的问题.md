## 解决springboot和jersey结合后，使得uber.jar启动失败的问题

https://www.jianshu.com/p/b00bdb90b796
https://docs.spring.io/spring-boot/docs/1.5.x/reference/htmlsingle/#howto-extract-specific-libraries-when-an-executable-jar-runs



```
@Component
public class JerseyConfig extends ResourceConfig {
    public JerseyConfig(){
        register(JacksonJsonProvider.class);
      //  packages("com.serena.zjservice.server");

        // replace packages to this 
        ClassPathScanningCandidateComponentProvider scanner = new ClassPathScanningCandidateComponentProvider(false);
        // add more annotation filters if you need
        scanner.addIncludeFilter(new AnnotationTypeFilter(Path.class));
        scanner.addIncludeFilter(new AnnotationTypeFilter(Provider.class));
        this.registerClasses(scanner.findCandidateComponents("com.serena.zjservice.server").stream()
                .map(beanDefinition -> ClassUtils
                        .resolveClassName(beanDefinition.getBeanClassName(), this.getClassLoader()))
                .collect(Collectors.toSet()));
    }
}	
```

