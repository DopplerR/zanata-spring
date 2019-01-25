# Zanata Spring

[![Build Status](https://travis-ci.org/porscheinformatik/zanata-spring.svg?branch=master)](https://travis-ci.org/porscheinformatik/zanata-spring)

This is a (very) small library providing an Spring `MessageSource` that
delagates to the [Zanata REST API](https://zanata.ci.cloudbees.com/job/zanata-api-site/site/zanata-common-api/rest-api-docs/index.html).

## Usage

Simply declare the `ZanataMessageSource` as a bean. You can configure the `MessageSource` via:

 - zanataBaseUrl (required) - the base URL of your Zanaza instance 
 - project (required) - the project id
 - iteration - the iteration/version of the project, if not specified "master" will be used
 - baseNames - the message bundle names,  if not specified ["messages"] will be used

Usually you might want to have the local message bundles as a backup when Zanata is not running. Therefore you can set 
a `ResourceBundleMessageSource` as the parent of the `ZanataMessageSource`. 

Here is a full example bean configuration for a Spring Boot app (with "messages" as the default bundle):

```java
@Bean
public MessageSource messageSource() {
    ReloadableResourceBundleMessageSource localMessageSource = new ReloadableResourceBundleMessageSource();
    localMessageSource.setBasename("messages");
    localMessageSource.setFallbackToSystemLocale(false);

    ZanataMessageSource zanataMessageSource = new ZanataMessageSource();
    zanataMessageSource.setZanataBaseUrl("https://my-zanata.internal");
    zanataMessageSource.setProject("MY-ZANAZA-PROJECT");
    
    RestTemplate restTemplate = new RestTemplate();
            restTemplate.getInterceptors()
                .add(new ZanataAuthHeaderInterceptor("<username>", "<token>"));
            
    zanataMessageSource.setRestTemplate(restTemplate);           
    zanataMessageSource.setParentMessageSource(localMessageSource);
    return zanataMessageSource;
}
```