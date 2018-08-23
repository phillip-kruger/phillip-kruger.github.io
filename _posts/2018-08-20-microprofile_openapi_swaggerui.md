---
title: Swagger UI on MicroProfile OpenAPI
tags: [MicroProfile, Payara, OpenAPI, Swagger]
image: "/images/MicroProfile.jpg"
bigimg: "/images/MicroProfile_openapi/banner.jpg"
---

[MicroProfile OpenApi](https://github.com/eclipse/microprofile-open-api) gives us a standardized way to describe our [JAX-RS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) API's using [OpenApi](https://www.openapis.org/) 3.
If you have used [swagger-jaxrs](https://github.com/swagger-api/swagger-core/wiki/swagger-core-jax-rs-project-setup-1.5.x) and [swagger-annotations](https://github.com/swagger-api/swagger-core/wiki/annotations-1.5.x) before, this will feel very familiar to you as OpenApi is built on the Swagger base.

*On Nov. 5, 2015, SmartBear in conjunction with 3Scale, Apigee, Capital One, Google, IBM, Intuit, Microsoft, PayPal, and Restlet announced the formation of the Open API Initiative, an open source project under the Linux Foundation. As part of the formation of the OAI, SmartBear donated the Swagger specification to the Linux Foundation, meaning that the OpenAPI Specification is semantically identical to the specification formerly known as the Swagger 2.0 specification* - [www.openapis.org/faq](https://www.openapis.org/faq#OAIFAQ-History)

# Quick overview

## Application

Firstly, in your class that extends ```javax.ws.rs.core.Application```, add the header information about your API:

```java
    
    @ApplicationPath("/api")
    @OpenAPIDefinition(info = @Info(
            title = "Example application", 
            version = "1.0.0", 
            contact = @Contact(
                    name = "Phillip Kruger", 
                    email = "phillip.kruger@phillip-kruger.com",
                    url = "http://www.phillip-kruger.com")
            ),
            servers = {
                @Server(url = "/example",description = "localhost")
            }
    )
    public class ApplicationConfig extends Application {

    }

```
(see full example [here](https://github.com/phillip-kruger/microprofile-extensions/blob/master/example/src/main/java/com/github/phillipkruger/microprofileextentions/example/ApplicationConfig.java))

## Service

Then in your service(s), add the annotations describing your service:

* ```@Tag```
* ```@Operation```
* ```@APIResponse```
* etc.

example:

```java

    @RequestScoped
    @Path("/config")
    @Tag(name = "Config Retrieval service", description = "Get the value for a certain config")
    public class ConfigRetrievalService {

        @Inject
        private Config config;

        @GET @Path("/{key}")
        @Operation(description = "Get the value for this key")
        @APIResponses({
            @APIResponse(responseCode = "200", description = "Successful, returning the value")
        })
        @Produces(MediaType.TEXT_PLAIN)
        public Response getConfigValue(@PathParam("key") String key) {
            String value = config.getValue(key, String.class);
            log.log(Level.INFO, "{0} = {1}", new Object[]{key, value});


            return Response.ok(value).build();
        }

    }
```

(see the full example [here](https://github.com/phillip-kruger/microprofile-extensions/blob/master/example/src/main/java/com/github/phillipkruger/microprofileextentions/example/ConfigRetrievalService.java), and another (fuller) example [here](https://github.com/phillip-kruger/microprofile-demo/blob/master/membership/src/main/java/com/github/phillipkruger/membership/MembershipService.java))

## Getting the openapi yaml

Now if you run your application, you can go to ```/openapi``` to get the yaml:

```yaml

    openapi: 3.0.0
    info:
      title: Example application
      contact:
        name: Phillip Kruger
        url: http://www.phillip-kruger.com
        email: phillip.kruger@phillip-kruger.com
      version: 1.0.0
    servers:
    - url: /example
      description: localhost
    tags:
    - name: Simulate some exeption
      description: Create some exception
    - name: Config Retrieval service
      description: Get the value for a certain config
    paths:
      /api/config/{key}:
        get:
          tags:
          - Config Retrieval service
          description: Get the value for this key
          operationId: getConfigValue
          parameters:
          - name: key
            in: path
            required: true
            style: simple
            schema:
              type: string
          responses:
            200:
              description: Successful, returning the value
      /api/exception:
        get:
          tags:
          - Simulate some exeption
          description: Throw an exeption
          operationId: doSomething
          responses:
            412:
              description: You made a mistake
    components: {}

```

# Adding Swagger UI

Above is a quick overview of MicroProfile OpenAPI. Read more about it here: 

* [Specification](http://download.eclipse.org/microprofile/microprofile-open-api-1.0/microprofile-openapi-spec.html)
* [Github](https://github.com/eclipse/microprofile-open-api)

The latest [Swagger UI](https://swagger.io/tools/swagger-ui/) works on OpenAPI, and you can manually add it to your project 
(see [this great post](https://www.kodnito.com/posts/documenting-rest-api-using-microprofile-openapi-swagger-ui-payara-micro/) by [Hayri Cicek‏](https://twitter.com/cicekhayri))
, or you can use [this](https://github.com/phillip-kruger/microprofile-extensions/tree/master/openapi-ext) useful library that will add it automatically:

In your ```pom.xml```:

```xml

    <dependency>
        <groupId>com.github.phillip-kruger.microprofile-extensions</groupId>
        <artifactId>openapi-ext</artifactId>
        <version>1.0.9</version>
    </dependency>
```

This will pull in Swagger UI via [webjars](http://webjars.org/) and generate the ```index.html``` from a template that you can configure using [MicroProfile Config API](https://github.com/eclipse/microprofile-config).

Just adding the above will already give you the default UI, example:

http://localhost:8080/example/api/openapi-ui/

![swagger-ui](/images/MicroProfile_openapi/vanilla.png)

## Personalize your UI

Using the Config API you can Personalize the UI. Here are the config keys you can use:

* **openapi-ui.copyrightBy** - Adds a name to the copyright in the footer. Defaults to none.
* **openapi-ui.copyrightYear** - Adds the copyright year. Defaults to current year.
* **openapi-ui.title** - Adds the title in the window. Defaults to "MicroProfile - Open API".
* **openapi-ui.serverInfo** - Adds info on the server. Defaults to the system server info.
* **openapi-ui.contextRoot** - Adds the context root. Defaults to the current value.
* **openapi-ui.swaggerUiTheme** - Use a theme from swagger-ui-themes. Defaults to "flattop".
* **openapi-ui.swaggerHeaderVisibility** - Show/hide the swagger logo header. Defaults to "visible".
* **openapi-ui.exploreFormVisibility** - Show/hide the explore form. Defaults to "hidden".
* **openapi-ui.serverVisibility** - Show/hide the server selection. Defaults to "hidden".
* **openapi-ui.createdWithVisibility** - Show/hide the created with footer. Defaults to "visible".

Example: Adding this to ```META-INF/microprofile.properties```

```

    openapi-ui.copyrightBy=Phillip Kruger
    openapi-ui.title=My awesome services
    openapi-ui.swaggerHeaderVisibility=hidden
    openapi-ui.serverVisibility=visible
```

will change the UI:

![swagger-ui](/images/MicroProfile_openapi/configured1.png)

### Theme

The default UI uses the flattop theme from [swagger-ui-themes](http://meostrander.com/swagger-ui-themes/), but you can also change that:

```

    openapi-ui.swaggerUiTheme=monokai
```

![swagger-ui](/images/MicroProfile_openapi/configured2.png)

### Logo

Lastly, you can change the logo to your company logo by including a file named ```openapi.png``` in ```/src/main/resources/```:

![swagger-ui](/images/MicroProfile_openapi/configured3.png)

# Apiee

For application servers that do not have MicroProfile, see [Apiee](https://github.com/phillip-kruger/apiee)