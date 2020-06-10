---
title: Supersonic Subatomic GraphQL
tags: [Quarkus, MicroProfile, GraphQL]
image: "/images/Quarkus.png"
bigimg: "/images/Supersonic_Subatomic_GraphQL/banner.jpg"
---

[MicroProfile GraphQL](https://github.com/eclipse/microprofile-graphql) is now included in the just released [version 1.5.0](https://quarkus.io/blog/quarkus-1-5-final-released/) of [Quarkus](https://quarkus.io/)

You can now use [code.quarkus.io](https://code.quarkus.io/) to get going with Quarkus and include the [SmallRye GraphQL Extension](https://github.com/smallrye/smallrye-graphql)

![codequarkusio](/images/Supersonic_Subatomic_GraphQL/code_quarkus.png)

This will create a Quarkus starter application with the following dependencies:

```xml
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-smallrye-graphql</artifactId>
    </dependency>
```

NOTE: You can remove the `quarkus-resteasy` dependency as we do not need JAX-RS.

## Your first GraphQL Endpoint.

Let's change the `ExampleResource` Rest service to be a GraphQL endpoint.

1. Replace the `@Path("/hello")` class annotation with `@GraphQLApi`.
1. Replace the `@GET` method annotation with `@Query`.
1. Remove the `@Produces(MediaType.TEXT_PLAIN)` method annotation and all JAX-RS imports.

That is it! Your `ExampleResource` should look like this now:

```java
package org.acme;

import org.eclipse.microprofile.graphql.GraphQLApi;
import org.eclipse.microprofile.graphql.Query;

@GraphQLApi
public class ExampleResource {

    @Query
    public String hello() {
        return "hello";
    }
}
```

You can now run the application using Quarkus dev mode:

```
mvn quarkus:dev
```

Now browse to [localhost:8080/graphql-ui/](http://localhost:8080/graphql-ui/) and run the following query:

```json
{
  hello
}
```

This will return:

```json
{
  "data": {
    "hello": "hello"
  }
}
```

Also see the [Quarkus GraphQL Guide](https://quarkus.io/guides/microprofile-graphql)

## A more detailed example

Let's look at a more detailed example, get the source from [this GitHub project](https://github.com/phillip-kruger/graphql-example)

In this project browse to the quarkus example:

```
cd quarkus-example
```

Look at `ProfileGraphQLApi.java` that is marked as a `@GraphQLApi`:

```java
    @Query("person")
    public Person getPerson(@Name("personId") int personId){
        return personDB.getPerson(personId);
    }
```

Above method will get a person by `personId`. As you can see the method is made queryable with the `@Query` annotation. You can optionally provide the name ("person" in this case),
however the default would be "person" anyway (method name without "get"). You can also optionally name the parameter, but the default would be the parameter name ("personId").

The Person Object is a POJO that represents a Person (User or Member) in the system. It has many fields, some that are other complex POJOs:

![codequarkusio](/images/Supersonic_Subatomic_GraphQL/person.png)

However, the `Query` annotation makes it possible to query the exact fields we are interested in.

Run the example application:

```
mvn quarkus:dev
```

Now browse to [localhost:8080/graphql-ui/](http://localhost:8080/graphql-ui/) and run the following query:

```
{
  person(personId:1){
    names
    surname
    scores{
      name
      value
    }
  }
}
```

Notice that you have 'code insight' in the editor. That is because GraphQL has a schema and also supports introspection.

We can request only the fields we are interested in, making the payload much smaller.

![graphiql](/images/Supersonic_Subatomic_GraphQL/graphiql.png)

We can also combine queries, i.e., lets say we want to get the fields for person 1 as shown above, and also the name and surname for person 2, we can do the following:

```
{
  person1: person(personId:1){
    names
    surname
    scores{
      name
      value
    }
  }
  person2: person(personId:2){
    names
    surname
  }
}
```
This will return :

```
{
  "data": {
    "person1": {
      "names": [
        "Christine",
        "Fabian"
      ],
      "surname": "O'Reilly",
      "scores": [
        {
          "name": "Driving",
          "value": 15
        },
        {
          "name": "Fitness",
          "value": 94
        },
        {
          "name": "Activity",
          "value": 63
        },
        {
          "name": "Financial",
          "value": 22
        }
      ]
    },
    "person2": {
      "names": [
        "Masako",
        "Errol"
      ],
      "surname": "Zemlak"
    }
  }
}
```

### Source fields

If you look closely at our query, you will see we asked for the `scores` field of the person, however, the `Person` POJO does not contain a `scores` field.
We added the `scores` field by adding a `@Source` field to the person:

```java
    @Query("person")
    public Person getPerson(@Name("personId") int personId){
        return personDB.getPerson(personId);
    }

    public List<Score> getScores(@Source Person person) {
        return scoreDB.getScores(person.getIdNumber());
    }
```

So we can add fields that merge onto the output by adding the `@Source` parameter that matches the response type.

### Partial results

The above example merges two different data sources, but let's say the score system is down. We will then still return the data we have, and an error
for the score:

```
{
  "errors": [
    {
      "message": "Scores for person [797-95-4822] is not available",
      "locations": [
        {
          "line": 5,
          "column": 5
        }
      ],
      "path": [
        "person",
        "scores2"
      ],
      "extensions": {
        "exception": "com.github.phillipkruger.user.graphql.ScoresNotAvailableException",
        "classification": "DataFetchingException"
      }
    }
  ],
  "data": {
    "person": {
      "names": [
        "Christine",
        "Fabian"
      ],
      "surname": "O'Reilly",
      "scores2": null
    }
  }
}
```

### Native mode

Let's run this example in native mode:

```
mvn -Pnative clean install
```

This will create a native executable and will now start the application very quickly:

![native](/images/Supersonic_Subatomic_GraphQL/native.png)


## In the pipeline

This is the first version of the MicroProfile GraphQL Spec and there are many things in the pipeline. One of those is a client.
We are proposing two types of clients:

### Dynamic
The dynamic client will allow you to build a query using a builder:

```java
// Building of the graphql document.
Document myDocument = document(
                operation(Operation.Type.QUERY,
                        field("people",
                                field("id"),
                                field("name")
                        )));

// Serialization of the document into a string, ready to be sent.
String graphqlRequest = myDocument.toString();
```

For more details see: [github.com/worldline/dynaql](https://github.com/worldline/dynaql)

### Type safe

The type safe client will be closer to MicroProfile RESTClient. Looking at the same example as above, lets see how we can to use it.
Browse to the `quarkus-client` folder. This example uses [Quarkus Command Mode](https://quarkus.io/blog/introducing-command-mode/) to make a Query.

The client is not yet a Quarkus Extension, so we add it in our project like this:

```xml
<dependency>
    <groupId>io.smallrye</groupId>
    <artifactId>smallrye-graphql-client</artifactId>
    <version>${smallrye-graphql.version}</version>
</dependency>
```

Now we can create a POJO that contains only fields that we are interested in. Looking at `Person` and `Score` in the client module, it is much smaller than the definition on the server side:

![client](/images/Supersonic_Subatomic_GraphQL/client.png)

All we need to do now is to add an interface that defines the queries that we are interested in:

```java
@GraphQlClientApi
public interface PersonGraphQLClient {

    public Person person(int personId);

}
```

And now we can use this:

```java

    //@Inject
    //PersonGraphQLClient personClient; or
    PersonGraphQLClient personClient = GraphQlClientBuilder.newBuilder().build(PersonGraphQLClient.class);

    // ...
    Person person = personClient.person(id);

```

Running the Quarkus app we can now make a call to the server and print the response:

```
java -jar quarkus-client-1.0.0-SNAPSHOT-runner.jar 2
```

The number (2) is the `personId` in our example:

![client_output](/images/Supersonic_Subatomic_GraphQL/client_output.png)

## Summary

This is a short and quick introduction to MicroProfile GraphQL that is now available in Quarkus.
There are many more [features](https://download.eclipse.org/microprofile/microprofile-graphql-1.0.2/microprofile-graphql.html)
and even more [planned](https://github.com/eclipse/microprofile-graphql/issues), so stay tuned.
