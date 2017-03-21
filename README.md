# Global configuration model

Carbon configuration provides a framework for managing server configurations. With global configuration model, server
 will have only one configuration file for all server configurations. So that,

   * Global configuration file (deployment.yaml) includes minimal sets of configurations which need to override very
      often by default (e.g: server ports etc).
   * All user settable configurations must have default values and they are burnt into compile codes.
   * Any default configuration can overridden by adding the particular configuration to the global configuration
     file(deployment.yaml).
   * Defining all configurations in the configuration file in not required. add only if we need to override the
     default value.
   * If configurations are not specified in the configuration file, values defined in the bean class will apply by
     default.

In global configuration file (deployment.yaml),

   * Each component will have their own configurations with a unique namespace (e.g: wso2.carbon, wso2.transports.netty etc) and it will be a fragment of deployment.yaml file. Please find the sample file below.
   * At runtime, the relevant config bean will be generated by overriding the default values specified in bean class with values mentioned in deployment.yaml file.

Sample file looks like,

```yaml
  # Carbon Configuration Parameters
wso2.carbon:
  id: carbon-kernel
  version: 5.2.0-SNAPSHOT
  ports:
    offset: 0
  ...

  # Netty Transport Configurations
wso2.transports.netty:
  listeners:
    - id: msf4j-http
      host: 127.0.0.1
      port: 8080
      bossThreadPoolSize: 2
      workerThreadPoolSize: 250
      parameters:
        - name: "executor.workerpool.size"
          value: 60

...
```

Following annotations are introduced for configuration bean classes.

 * `org.wso2.carbon.config.annotations.Configuration`: This is a class-level annotation, which corresponds to a configuration bean to be used by a component.
 * `org.wso2.carbon.config.annotations.Element`: This is a field-level annotation, which corresponds to a field of the class.
 * `org.wso2.carbon.config.annotations.Ignore`: This is a field-level annotation, which specifies that the field needs to be ignored when the configuration is generated.

Sample bean class with above annotations looks like,

 ```java
 @Configuration(namespace = "wso2.carbon", description = "Carbon Configuration Parameters")
 public class CarbonConfiguration {

     @Element(description = "value to uniquely identify a server", required = true)
     private String id = "carbon-kernel";

     @Element(description = "server name")
     private String name = "WSO2 Carbon Kernel";

     @Element(description = "server version")
     private String version = "5.2.0";

     @Ignore
     private String tenant = Constants.DEFAULT_TENANT;

 }
 ```

The framework provides maven plugin to read all configuration bean classes in the component and create the relevant
segment of the configuration file(for documentation purposes) automatically at compile time.

The framework provides OSGI service(ConfigProvider) to read configuration from deployment.yaml file. Following apis
are read configuration from the file.

* `getConfigurationObject(Class<T> configClass)`: Returns configuration object of the class with overriding the values of deployment.yaml. If configuration doesn't exist in deployment.yaml, returns object with default values.
* `getConfigurationMap(String namespace)`: Returns configuration map of the namespace, if configuration exists for the given namespace in deployment.yaml.

For more information, Please refer document link below.

* [Using the Global Configuration Model](docs/DeveloperTools/UpdatingConfigurations.md)
