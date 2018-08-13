---
title: A configurable JAX-RS ExceptionMapper with MicroProfile Config
tags: [MicroProfile, Payara, Configuration, REST, JAX-RS]
image: "/images/MicroProfile.jpg"
bigimg: "/images/Jax-rs_config/banner.jpg"
---

When you create REST services with JAX-RS, you typically either return nothing (so HTTP 201/2/4 etc) or some data, potentially in JSON format (so HTTP 200), or some Exception / Error (so HTTP 4xx or 5xx).

We usually translate a Runtime Exception into some HTTP 5xx and a Checked Exception into some 4xx. (With the exception of Bean validation's ```ConstraintViolationException```)

Because we want to keep our boundary clean, we do not include the full Java stacktrace in the body of the response when we translate an Exception to a HTTP response. 
We usually just add a "REASON" Header with the HTTP 5xx (or sometimes 4xx) response. However, this means that most of our ExceptionMappers looks pretty much the same (something like this):

```java

    @Provider
    public class SomeExceptionMapper implements ExceptionMapper<SomeException> {

        @Override
        @Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})
        public Response toResponse(SomeException exception) {
            return Response.status(500).header("reason", exception.getMessage()).build();
        }

    }
```

# Using MicroProfile Config API 

We can use MicroProfile Config API to create a configurable Exception Mapper, that allows the consumer to configure the mapping (Exception to HTTP Response Code).

Our ```@Provider``` will handle all Runtime Exceptions:

```java

    @Provider
    public class RuntimeExceptionMapper implements ExceptionMapper<RuntimeException> {
        // ...
    }
```

We ```@Inject``` both the Config and the Providers:

```java

    @Inject
    private Config config;
    
    @Context 
    private Providers providers;
```

When we implement the ```toResponse``` method, we see if there is a mapping for this Exception class:

```java

    @Override
    @Produces({MediaType.APPLICATION_JSON,MediaType.APPLICATION_XML})
    public Response toResponse(RuntimeException exception) {
        return handleThrowable(exception);
    }
    
    private Response handleThrowable(Throwable exception) {
        if(exception instanceof WebApplicationException) {
            return ((WebApplicationException) exception).getResponse();
        }
        if(exception!=null){
            String configkey = exception.getClass().getName() + STATUS_CODE_KEY;
            Optional<Integer> possibleDynamicMapperValue = config.getOptionalValue(configkey,Integer.class);
            if(possibleDynamicMapperValue.isPresent()){
                int status = possibleDynamicMapperValue.get();
                // You switched it off
                if(status<0)return handleNotMapped(exception);
                String reason = getReason(exception);
                log.log(Level.FINEST, reason, exception);
                return Response.status(status).header(REASON, reason).build();
            } else if(exception.getCause()!=null && exception.getCause()!=null && providers!=null){
                final Throwable cause = exception.getCause();
                return handleThrowable(cause);
            } else {
                return handleNotMapped(exception);
            }
        }
        return handleNullException();
    }
```

(full example [here](https://github.com/phillip-kruger/microprofile-extensions/blob/master/jaxrs-ext/src/main/java/com/github/phillipkruger/microprofileextentions/jaxrs/RuntimeExceptionMapper.java))

So we can add configuration for mappings like this:

```
    ## 503 Service Unavailable: The server is currently unavailable (because it is overloaded or down for maintenance). Generally, this is a temporary state.
    org.eclipse.microprofile.faulttolerance.exceptions.CircuitBreakerOpenException/mp-jaxrs-ext/statuscode=503
    
    ## 401 Unauthorized (RFC 7235): Similar to 403 Forbidden, but specifically for use when authentication is required and has failed or has not yet been provided.
    javax.ws.rs.NotAuthorizedException/mp-jaxrs-ext/statuscode=401
```

In the above example we will map a CircuitBreakerOpenException (from MicroProfile Fault tolerance API) to a 503 and NotAuthorizedException to a 401.

# Example screenshot

![](/images/Jax-rs_config/jaxrs-ext.png)

# Use it as a library.

You can also bundle all of this in a jar file to be used by any of your projects. I made the above available in [maven central](https://search.maven.org/artifact/com.github.phillip-kruger.microprofile-extensions/jaxrs-ext/1.0.9/jar) and [github](https://github.com/phillip-kruger/microprofile-extensions/tree/master/jaxrs-ext), so you can also use that directly.

Just add this to your pom.xml

```xml
    <dependency>
        <groupId>com.github.phillip-kruger.microprofile-extensions</groupId>
        <artifactId>jaxrs-ext</artifactId>
        <version>1.0.9</version>
    </dependency>
```

It comes with a few pre-defined mappings, but you can override it in your configuration.