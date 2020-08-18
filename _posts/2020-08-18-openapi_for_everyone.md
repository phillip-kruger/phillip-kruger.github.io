---
title: MicroProfile OpenAPI for everyone
tags: [Quarkus, MicroProfile, OpenAPI]
image: "/images/Quarkus.png"
bigimg: "/images/OpenAPI_for_everyone/banner.jpg"
---

[MicroProfile OpenAPI](https://github.com/eclipse/microprofile-open-api) is primarily for adding [OpenAPI](https://swagger.io/specification/) 
to [JAX-RS](https://projects.eclipse.org/projects/ee4j.jaxrs) Endpoints. In this blog post we will look at how the 
[SmallRye Implementation](https://github.com/smallrye/smallrye-open-api) extend this with some extra features, and support for more web frameworks, when used in Quarkus. 

## Using Quarkus.

The example code is available [here](https://github.com/phillip-kruger/openapi-example). You can also initialize a project 
using [code.quarkus.io](https://code.quarkus.io/) - just make sure to include the SmallRye OpenAPI Extension.

## JAX-RS

Let's start with a basic JAX-RS Example in Quarkus. We have a `Greeting` Object, that has a `message` and a `to` field, 
and we will create `GET`, `POST` and `DELETE` endpoints for the greeting.

Apart from the usual Quarkus setup, you also need the following in your `pom.xml` to create a JAX-RS Endpoint:

```xml
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-smallrye-openapi</artifactId>
    </dependency>

    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-resteasy</artifactId>
    </dependency>

    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-resteasy-jsonb</artifactId>
    </dependency>
```

In Quarkus you do not need an `Application` class, we can just add the Endpoint class:

```java
@Path("/jax-rs")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class JaxRsGreeting {

    @GET
    @Path("/hello")
    public Greeting helloJaxRs() {
        return new Greeting("Hello", "JAX-RS");
    }

    @POST
    @Path("/hello")
    public Greeting newHelloJaxRs(Greeting greeting) {
        return greeting;
    }
    
    @DELETE
    @Path("/hello/{message}")
    public void deleteHelloJaxRs(@PathParam("message") String message) {
        // Here do the delete.
    }
}
```

So far we have not added any MicroProfile OpenAPI Annotations, but because we added the `quarkus-smallrye-openapi` extension, 
we will already have a Schema document generated under `/openapi`

```yml
---
openapi: 3.0.3
info:
  title: Generated API
  version: "1.0"
paths:
  /jax-rs/hello:
    get:
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /jax-rs/hello/{message}:
    delete:
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
components:
  schemas:
    Greeting:
      type: object
      properties:
        message:
          type: string
        to:
          type: string

```

See [quarkus.io/guides/rest-json](https://quarkus.io/guides/rest-json) for more information.

## OpenAPI

You can add more information to the generated schema document by using MicroProfile OpenAPI. 

### Header information using config

One feature that we added to SmallRye is the ability to add header information, that you typically
add to the `Application` class using annotations, with MicroProfile config. This is useful in Quarkus where you 
do not need an `Application` class. So adding the following to the `application.properties` will give you some header information:

```properties
mp.openapi.extensions.smallrye.info.title=OpenAPI for Everyone
%dev.mp.openapi.extensions.smallrye.info.title=OpenAPI for Everyone (development)
%test.mp.openapi.extensions.smallrye.info.title=OpenAPI for Everyone (test)
mp.openapi.extensions.smallrye.info.version=1.0.0
mp.openapi.extensions.smallrye.info.description=Example on how to use OpenAPI everywhere
mp.openapi.extensions.smallrye.info.contact.email=phillip.kruger@redhat.com
mp.openapi.extensions.smallrye.info.contact.name=Phillip Kruger
mp.openapi.extensions.smallrye.info.contact.url=https://www.phillip-kruger.com
mp.openapi.extensions.smallrye.info.license.name=Apache 2.0
mp.openapi.extensions.smallrye.info.license.url=http://www.apache.org/licenses/LICENSE-2.0.html
```

Now look at the header of the generated schema document under `/openapi`:

```yml
---
openapi: 3.0.3
info:
  title: OpenAPI for Everyone (development)
  description: Example on how to use OpenAPI everywhere
  contact:
    name: Phillip Kruger
    url: https://www.phillip-kruger.com
    email: phillip.kruger@redhat.com
  license:
    name: Apache 2.0
    url: http://www.apache.org/licenses/LICENSE-2.0.html
  version: 1.0.0

  # rest of the schema document ...
```

### Adding some OpenAPI Annotations to your operations:

You can use any of the annotation in MicroProfile OpenAPI to further describe your endpoint, for example the `Tag` annotation:

```java
@Path("/jax-rs")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
@Tag(name = "JAX-RS Resource", description = "Basic Hello World using JAX-RS")
public class JaxRsGreeting {
    \\...
}
```
### Auto generate the operation id

Some tools that use the schema document to generate client stubs, needs an `operationId` in the schema document that is used to name the client stub methods.
We added support in SmallRye to auto generate this using either the method name (`METHOD`), class and method name (`CLASS_METHOD`), or package, class and method name (`PACKAGE_CLASS_METHOD`). 
To do this add the following to `application.properties`:

```properties
mp.openapi.extensions.smallrye.operationIdStrategy=METHOD
```

You will now see the `operationId` in the schema document for every operation:

```yml
---
openapi: 3.0.3

  # header omitted ...

  /jax-rs/hello:
    get:
      tags:
      - JAX-RS Resource
      operationId: helloJaxRs
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - JAX-RS Resource
      operationId: newHelloJaxRs
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /jax-rs/hello/{message}:
    delete:
      tags:
      - JAX-RS Resource
      operationId: deleteHelloJaxRs
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
```

### Changing the OpenAPI version

Some API gateways might require a certain OpenAPI version to work. The schema document generated by the SmallRye extension 
will generate a with `3.0.3` as the version, but since there is only minor differences between these version, you can change that to
`3.0.0`, `3.0.1` or `3.0.2`. You can do this by adding this in `application.properties`:

```properties
mp.openapi.extensions.smallrye.openapi=3.0.2
```
Now the version generated will be:

```yml
---
openapi: 3.0.2

  # Rest of the document ...
```

See [quarkus.io/guides/openapi-swaggerui](https://quarkus.io/guides/openapi-swaggerui) for more information.

## Spring Web

Recently support for Spring Web has been added in SmallRye, this means that, not only will 
you see the default OpenAPI document when you use Spring Web in Quarkus, but you can also use MicroProfile OpenAPI to
further describe your Spring Web endpoints.

Let's add a Spring Rest Controller to our current application. First add this in your `pom.xml`:

```xml
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-spring-web</artifactId>
</dependency>
```

Now you can create a similar endpoint to the JAX-RS one we have looked at so far, but using Spring Web:

```java
@RestController
@RequestMapping(value = "/spring", produces = MediaType.APPLICATION_JSON_VALUE)
@Tag(name = "Spring Resource", description = "Basic Hello World using Spring")
public class SpringGreeting {

    @GetMapping("/hello")
    public Greeting helloSpring() {
        return new Greeting("Hello", "Spring");
    }
    
    @PostMapping("/hello")
    public Greeting newHelloSpring(@RequestBody Greeting greeting) {
        return greeting;
    }
    
    @DeleteMapping("/hello/{message}")
    public void deleteHelloSpring(@PathVariable(name = "message") String message) {

    }
}
```

The Spring annotations will be scanned and this will be added to your schema document:

```yml
---
openapi: 3.0.3

  # header omitted ...

  /spring/hello:
    get:
      tags:
      - Spring Resource
      operationId: helloSpring
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Spring Resource
      operationId: newHelloSpring
      requestBody:
        content:
          '*/*':
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /spring/hello/{message}:
    delete:
      tags:
      - Spring Resource
      operationId: deleteHelloSpring
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
```

See [quarkus.io/guides/spring-web](https://quarkus.io/guides/spring-web) for more information.

## Vert.x Reactive Routes

In Quarkus, you can also build Vert.x endpoints using Reactive Routes. Similarly to Spring Web, your endpoints will be available in the OpenAPI Schema 
and can be further described using MicroProfile OpenAPI. To add a Vert.x reactive Route in Quarkus, you need the following in your `pom.xml`:

```xml
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-vertx-web</artifactId>
</dependency>
```

Now you can create the endpoint:

```java
@ApplicationScoped
@RouteBase(path = "/vertx", produces = "application/json")
@Tag(name = "Vert.x Resource", description = "Basic Hello World using Vert.x")
public class VertxGreeting {

    @Route(path = "/hello", methods = HttpMethod.GET)
    public Greeting helloVertX() { 
        return new Greeting("Hello", "Vert.x");
    }
    
    @Route(path = "/hello", methods = HttpMethod.POST)
    public Greeting newHelloVertX(@Body Greeting greeting) {
        return greeting;
    }
    
    @Route(path = "/hello/:message", methods = HttpMethod.DELETE)
    public void deleteHelloVertX(@Param("message") String message) {

    }
}
```

and now your Vert.x Routes are available in OpenAPI:

```yml
  /vertx/hello:
    get:
      tags:
      - Vert.x Resource
      operationId: helloVertX
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Vert.x Resource
      operationId: newHelloVertX
      requestBody:
        content:
          '*/*':
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /vertx/hello/{message}:
    delete:
      tags:
      - Vert.x Resource
      operationId: deleteHelloVertX
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
```

See [quarkus.io/guides/reactive-routes](https://quarkus.io/guides/reactive-routes) for more information.

## Endpoints generated with Panache

In Quarkus your can generate your JAX-RS endpoint using Panache. These generated classes will also be scanned and added to the 
OpenAPI schema document if you have the `quarkus-smallrye-openapi` extension in your `pom.xml`.

See [quarkus.io/guides/rest-data-panache](https://quarkus.io/guides/rest-data-panache) for more information.

## Any other Web Framework

You can also add any other endpoint to your document by providing that part of the Schema document in a `yaml` file. Let's say for instance you
have a Servlet that expose some methods and you want to add those to the schema document. (Servlet is just an example, any web framework can work here)

So first we add this to the `pom.xml` to add Servlet support in Quarkus:

```xml
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-undertow</artifactId>
</dependency>
```

We can now create a Servlet Endpoint like this for example:

```java
@WebServlet("/other/hello/*")
public class ServletGreeting extends HttpServlet {

    private static final Jsonb JSONB = JsonbBuilder.create();
    
    @Override
    protected void doGet(HttpServletRequest request, HttpServletResponse response)throws ServletException, IOException {     
        response.setContentType("application/json");
        Greeting greeting = new Greeting("Hello", "Other");
        PrintWriter out = response.getWriter();   
        out.print(JSONB.toJson(greeting));
    }
    
    @Override
    protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("application/json");
        Greeting greeting = JSONB.fromJson(request.getInputStream(), Greeting.class);
        PrintWriter out = response.getWriter();   
        out.print(JSONB.toJson(greeting));
    }

    @Override
    protected void doDelete(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        // Here do the delete
    }
}
```

Now we need a OpenAPI Schema document that maps to these endpoints. You need to add this to a file called `openapi.yml` in `src/main/resources/META-INF`:

```yml
---
openapi: 3.0.3
tags:
- name: Other Resource
  description: Basic Hello World using Something else
paths:
  /other/hello:
    get:
      tags:
      - Other Resource
      operationId: helloOther
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Other Resource
      operationId: newHelloOther
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /other/hello/{message}:
    delete:
      tags:
      - Other Resource
      operationId: deleteHelloOther
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
```

This will be merged with the rest of the endpoints to expose all paths in your document. So in the end your `/openapi` output will look like this:

```yml
---
openapi: 3.0.2
info:
  title: OpenAPI for Everyone (development)
  description: Example on how to use OpenAPI everywhere
  contact:
    name: Phillip Kruger
    url: https://www.phillip-kruger.com
    email: phillip.kruger@redhat.com
  license:
    name: Apache 2.0
    url: http://www.apache.org/licenses/LICENSE-2.0.html
  version: 1.0.0
tags:
- name: Other Resource
  description: Basic Hello World using Something else
- name: Spring Resource
  description: Basic Hello World using Spring
- name: JAX-RS Resource
  description: Basic Hello World using JAX-RS
- name: Vert.x Resource
  description: Basic Hello World using Vert.x
paths:
  /other/hello:
    get:
      tags:
      - Other Resource
      operationId: helloOther
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Other Resource
      operationId: newHelloOther
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /other/hello/{message}:
    delete:
      tags:
      - Other Resource
      operationId: deleteHelloOther
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
  /jax-rs/hello:
    get:
      tags:
      - JAX-RS Resource
      operationId: helloJaxRs
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - JAX-RS Resource
      operationId: newHelloJaxRs
      requestBody:
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /jax-rs/hello/{message}:
    delete:
      tags:
      - JAX-RS Resource
      operationId: deleteHelloJaxRs
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
  /spring/hello:
    get:
      tags:
      - Spring Resource
      operationId: helloSpring
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Spring Resource
      operationId: newHelloSpring
      requestBody:
        content:
          '*/*':
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /spring/hello/{message}:
    delete:
      tags:
      - Spring Resource
      operationId: deleteHelloSpring
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
  /vertx/hello:
    get:
      tags:
      - Vert.x Resource
      operationId: helloVertX
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
    post:
      tags:
      - Vert.x Resource
      operationId: newHelloVertX
      requestBody:
        content:
          '*/*':
            schema:
              $ref: '#/components/schemas/Greeting'
      responses:
        "200":
          description: OK
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Greeting'
  /vertx/hello/{message}:
    delete:
      tags:
      - Vert.x Resource
      operationId: deleteHelloVertX
      parameters:
      - name: message
        in: path
        required: true
        schema:
          type: string
      responses:
        "204":
          description: No Content
components:
  schemas:
    Greeting:
      type: object
      properties:
        message:
          type: string
        to:
          type: string

```

This contains resources from JAX-RS, Spring Web, Vert.x Reactive Routes and Servlet.

## Swagger UI

In Quarkus, Swagger-UI is included by default and when you now browse to [localhost:8080/swagger-ui](http://localhost:8080/swagger-ui) 
you will see the UI with all your endpoints:

![swagger-ui](/images/OpenAPI_for_everyone/swagger-ui.png)

# Summary

In this post we looked at how Quarkus extends the MicroProfile OpenAPI specification to make it ever easier to document your Endpoints, and 
how you can document any web framework using it. 

If you find any issues or have any suggestions, head over to the [SmallRye](https://github.com/smallrye/smallrye-open-api) project and 
let's discuss it there.