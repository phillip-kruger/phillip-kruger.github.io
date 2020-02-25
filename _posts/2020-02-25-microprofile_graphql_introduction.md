---
title: MicroProfile GraphQL introduction
tags: [MicroProfile, GraphQL]
image: "/images/MicroProfile.jpg"
bigimg: "/images/MicroProfile_GraphQL_introduction/banner.jpg"
---

[MicroProfile GraphQL](https://github.com/eclipse/microprofile-graphql) has just released it's first version (1.0). 
In this blog post we will explore some of the functionalities available in this release.

We will reference an example application, where we use [Thorntail](https://thorntail.io/) as a runtime, manually adding the [SmallRye GraphQL Implementation](https://github.com/smallrye/smallrye-graphql).

## What is GraphQL?

> "GraphQL is an open-source data query and manipulation language for APIs, and a runtime for fulfilling queries with existing data. 
> GraphQL interprets strings from the client, and returns data in an understandable, predictable, pre-defined manner. 
> GraphQL is an alternative, though not necessarily a replacement for REST."

Read the full [GraphQL Specification](http://spec.graphql.org/draft/)

## What is MicroProfile GraphQL?

> "The intent of the MicroProfile GraphQL specification is to provide a "code-first" set of APIs that will enable users to quickly develop portable GraphQL-based applications in Java.
> There are 2 main requirements for all implementations of this specification, namely:
> * Generate and make the GraphQL Schema available. This is done by looking at the annotations in the users code, and must include all GraphQL Queries and Mutations as well as all entities as defined implicitly via the response type or argument(s) of Queries and Mutations.
> * Execute GraphQL requests. This will be in the form of either a Query or a Mutation. As a minimum the specification must support executing these requests via HTTP."

Read the full [MicroProfile GraphQL Specification](https://download.eclipse.org/microprofile/microprofile-graphql-1.0/microprofile-graphql.html)

## The Example

We will reference an example throughout this post where we have a system that score people based on some activities they do.

- How fit they are, example how often they go to the gym
- How safe they drive, not exceeding the speed limit etc.
- How many steps they give a day, etc.

We have a Person object that contains all the details about the person (name, surname, address, phone, social media etc.) 
and we have a score object that contains the score for a certain action.


## Query data

In GraphQL, `Query` is similar to REST's `GET`. A Query does not change data, it only receives data.
The difference is that with GraphQL you can actually ask what you want.

### Using JAX-RS

Let's say you want some information about a person, in JAX-RS we would create something like this:

```java
@Path("/person")
@Consumes(MediaType.APPLICATION_JSON) @Produces(MediaType.APPLICATION_JSON)
@Tag(name = "Person service",description = "Person Services")
public class PersonRestApi {

    @Inject 
    private PersonDB personDB;

    @GET 
    @Path("/{id}")
    @Operation(description = "Get a person using the person's Id")
    public Response getPerson(@PathParam("id") int id){
        return Response.ok(personDB.getPerson(id)).build();
    }
}
```

NOTE: To document the endpoint, you also need [MicroProfile OpenAPI](https://github.com/eclipse/microprofile-open-api) (`@Tag` and `@Operation`). 

You can get the schema at `/openapi`, that will describe the services and models available.

Doing a GET on `/person/1` will return the Person in json format. 

Depending on the size of Person, this can be a big file:

```json
{
  "addresses": [
    {
      "code": "49512-5971",
      "lines": [
        "212 Neomi Ridges",
        "Lake Winfordview",
        "Wisconsin",
        "Myanmar"
      ]
    },
    {
      "code": "09444-8413",
      "lines": [
        "1443 Bogan Harbor",
        "East Jacinto",
        "New York",
        "Timor-Leste"
      ]
    }
  ],
  "biography": "Perferendis asperiores non. Cumque voluptas et nisi rerum tenetur quaerat. Doloribus nihil et. Autem autem sapiente et ut inventore ipsum sint. Aut error pariatur quidem itaque deserunt. Dolorum aspernatur exercitationem. In tenetur quia iste qui quia officiis.",
  "birthDate": "25/01/1967",
  "coverphotos": [
    "http://lorempixel.com/g/1920/1200/city/"
  ],
  "creditCards": [
    {
      "expiry": "2013-9-12",
      "number": "1234-2121-1221-1211",
      "type": "solo"
    }
  ],
  "emailAddresses": [
    "leo.lesch@gmail.com",
    "laurie.ankunding@gmail.com"
  ],
  "favColor": "lime",
  "gender": "Female",
  "id": 1,
  "idNumber": "334-58-1049",
  "imClients": [
    {
      "identifier": "Slack",
      "im": "cory.langworth"
    },
    {
      "identifier": "ICQ",
      "im": "booker.boyle"
    }
  ],
  "interests": [
    "PUBG",
    "meditation"
  ],
  "joinDate": "14/09/2019",
  "locale": "en-ZA",
  "maritalStatus": "Divorced",
  "names": [
    "Nicholas",
    "Edwin"
  ],
  "nicknames": [
    "Oscar Ruitt"
  ],
  "occupation": "Officer",
  "organization": "Hodkiewicz Group",
  "phoneNumbers": [
    {
      "number": "1-852-821-3630",
      "type": "Cell"
    },
    {
      "number": "783.874.6411",
      "type": "Home"
    },
    {
      "number": "(489) 031-5336 x8428",
      "type": "Work"
    }
  ],
  "profilePictures": [
    "https://s3.amazonaws.com/uifaces/faces/twitter/stan/128.jpg"
  ],
  "relations": [
    {
      "personURI": "/rest/person/46",
      "relationType": "Spouse"
    }
  ],
  "skills": [
    "Communication",
    "Confidence"
  ],
  "socialMedias": [
    {
      "name": "@jordan.hane",
      "username": "Twitter"
    },
    {
      "name": "annamarie.casper",
      "username": "Facebook"
    }
  ],
  "surname": "Zulauf",
  "taglines": [
    "You will find only what you bring in.",
    "Chuck Norris' keyboard doesn't have a F1 key, the computer asks him for help."
  ],
  "title": "Dr.",
  "userAgent": "Mozilla/5.0 (iPhone; CPU iPhone OS 12_0_1 like Mac OS X) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/12.0 Mobile/15E148 Safari/604.1",
  "username": "trent.kub",
  "website": "http://www.kasey-nikolaus.io"
}
```

Now if you are building a mobile app, or even if you consume this from another service, you might only care about 
the `name`, `surname` and `idNumber` as an example, but you are getting a lot of data back. You now need to filter out all the irrelevant (for you) information.

### Using MicroProfile GraphQL

Now let's add MicroProfile GraphQL to this and see how we can query the data:

First add this to your pom.xml (at least until the runtime supports this as out of the box functionality):

```xml
<dependency>
    <groupId>io.smallrye</groupId>
    <artifactId>smallrye-graphql-servlet</artifactId>
    <version>1.0.0</version>
</dependency>
```

Now we can add a GraphQL Endpoint (similar to our REST service above)

```java
@GraphQLApi
public class ProfileGraphQLApi {
    
    @Inject 
    private PersonDB personDB;

    @Query
    @Description("Get a person using the person's Id")
    public Person getPerson(@Name("personId") int personId){
        return personDB.getPerson(personId);
    }
}

```

As you can see the code is exactly the same as the REST service, but we have some new annotations:

- `@GraphQLApi` this tells the runtime this is a GraphQL Endpoint, and all methods annotated with `@Query` and `@Mutation` will be exposed.
- `@Query` this is a queryable method.

You can now `POST` a query to `/graphql`. Let's use the same example above, you want to get the `name`, `surname` and `idNumber` of the person:

```graphql
{
  person(personId:1){
    names
    surname
    idNumber
  }
}
```

This will return exactly what you asked for:

```json
{
  "data": {
    "person": {
      "names": [
        "Nicholas",
        "Edwin"
      ],
      "surname": "Zulauf",
      "idNumber": "334-58-1049"
    }
  }
}
```

and as you can see the payload is much less that the REST example.

We just solved the over-fetching problem:

**"Over-fetching is fetching too much data, so there is data in the response you don't use."**

Now let's say we also want to get the scores for this person. In the REST example we need to get the `idNumber` from our original huge json, 
and then make a subsequent call to the score service:

```java
@ApplicationScoped
@Path("/score")
@Consumes(MediaType.APPLICATION_JSON) @Produces(MediaType.APPLICATION_JSON)
@Tag(name = "Score service",description = "Score Services")
public class ScoreRestApi {
    
    @Inject 
    private ScoreDB scoreDB;
    
    @GET 
    @Path("/{idNumber}")
    @Operation(description = "Get a person's scores using the person's Id Number")
    public Response getScores(@PathParam("idNumber") String idNumber){
        return Response.ok(scoreDB.getScores(idNumber)).build();
    }
}
```

Now make the call: `curl -X GET "http://localhost:8080/rest/score/334-58-1049"`

This will return:

```json
[
  {
    "id": "4873aa28-cc7e-49b0-841f-ea4df4db0fa2",
    "name": "Driving",
    "value": 31
  },
  {
    "id": "8645363d-fa46-4cb9-8bca-e14435dd5d17",
    "name": "Fitness",
    "value": 85
  },
  {
    "id": "4b2af0b2-2d8b-4210-96e9-2692769c5ef7",
    "name": "Activity",
    "value": 91
  },
  {
    "id": "36d4585f-7061-4ad4-b5cc-5561eba983e7",
    "name": "Financial",
    "value": 22
  }
]
```

(and we might not care about the `id`)

In MicroProfile GraphQL, we are going to add a special query that allows you to add fields in graphQL, even though they are not in your Java POJO model.
This is possible because every field in GraphQL is just another `Query`. For the normal case, we just fetch properties from an object, but you can also fetch fields using another query. To add a scores field to person, we can add the following method to our existing Endpoint:

```java
public List<Score> getScores(@Source Person person) {
    return scoreDB.getScores(person.getIdNumber());
}
```

Now you can add that field in your original `query`, also specifying which sub-fields you are interested in:

```graphql
{
  person(personId:1){
    names
    surname
    idNumber
    scores {
      name
      value
    }
  }
}
```

The functionality above will now include the scores, with the score name and score value in your original query:

```json
{
  "data": {
    "person": {
      "names": [
        "Nicholas",
        "Edwin"
      ],
      "surname": "Zulauf",
      "idNumber": "334-58-1049",
      "scores": [
        {
          "name": "Driving",
          "value": 27
        },
        {
          "name": "Fitness",
          "value": 36
        },
        {
          "name": "Activity",
          "value": 88
        },
        {
          "name": "Financial",
          "value": 88
        }
      ]
    }
  }
}
```

Now we solved the under-fetching problem:

**"Under-fetching is not having enough data with a call to an endpoint, leading you to call a second endpoint."**

So in REST we had to make a call, that returned too much data and we had to filter the data to get what we wanted (over-fetching) and then we had to
make another call to another service to get the rest of the data we needed (under-fetching)

In GraphQL we could make one call to return all the data we need.

And when you don't need need the score data, that method will never be called, making the back-end more efficient.

## Mutations

In GraphQL, everything other that fetching data (GET) is called a `mutation` (PUT, POST, DELETE).

Every mutation changes the data, and then returns the result.

Let's say we need to create / update a person, we can add the following method to our existing Endpoint:

```java
@Mutation
public Person updatePerson(Person person){
    return personDB.updatePerson(person);    
}
```

And to delete a person:

```java
@Mutation
public Person deletePerson(int id){
    return personDB.deletePerson(id);    
}
```

We can now add a person using this mutation (note there is no id field):

```graphql
mutation CreatePerson{
  updatePerson(person : 
    {
      names: "Phillip"
    }
  ){
    id
    names
    surname
    profilePictures
    website
  }
}

```

this will return the newly created person:

```json
{
  "data": {
    "updatePerson": {
      "id": 101,
      "names": [
        "Phillip"
      ],
      "surname": null,
      "profilePictures": null,
      "website": null
    }
  }
}
```

Now update the missing fields using a mutation (note the inclusion of the id):

```graphql
mutation UpdatePerson{
  updatePerson(person : 
    {
      id: 101, 
      names:"Phillip",
      surname: "Kruger", 
      profilePictures: [
        "https://pbs.twimg.com/profile_images/1170690050524405762/I8KJ_hF4_400x400.jpg"
      ],
      website: "http://www.phillip-kruger.com"
    }){
    id
    names
    surname
    profilePictures
    website
  }
}

```

this will return the updated person:

```json
{
  "data": {
    "updatePerson": {
      "id": 101,
      "names": [
        "Phillip"
      ],
      "surname": "Kruger",
      "profilePictures": [
        "https://pbs.twimg.com/profile_images/1170690050524405762/I8KJ_hF4_400x400.jpg"
      ],
      "website": "http://www.phillip-kruger.com"
    }
  }
}
```

Lastly we can delete that person:

```graphql
mutation DeletePerson{
  deletePerson(id :101){
    id
    names
    surname
    profilePictures
    website
  }
}
```

The above functionality will return the data as it was just before we removed it:

```json
{
  "data": {
    "deletePerson": {
      "id": 101,
      "names": [
        "Phillip"
      ],
      "surname": "Kruger",
      "profilePictures": [
        "https://pbs.twimg.com/profile_images/1170690050524405762/I8KJ_hF4_400x400.jpg"
      ],
      "website": "http://www.phillip-kruger.com"
    }
  }
}
```

If you now do a `query` for person 101:

```graphql
{
  person(personId:101){
    id
    names
    surname
  }
}
```

you will not receive any person, as we deleted that person:

```json
  "data": {
    "person": null
  }
}
```

## Partial results

Because you can 'stitch' fields together like we showed above with the `@Source` annotation, there is a possibility that some of the queries will fail.
Let's say the score data is in a totally separate system than the person data, and that score system is down, however a query came in that requests person data and the scores. 
Rather than failing the whole query, we can return partial results, so still return all the person data you asked for, but include the error for the scores. So this query:

```graphql
{
  person(personId:1){
    names
    surname
    idNumber
    scores {
      name
      value
    }
  }
}
```

Will now return something like this:

```json
{
  "data": {
    "person": {
      "names": [
        "Nicholas",
        "Edwin"
      ],
      "surname": "Zulauf",
      "idNumber": "334-58-1049",
      "scores": null
    }
  },
  "errors": [
    {
      "message": "Scores for person [334-58-1049] is not available",
      "locations": [
        {
          "line": 6,
          "column": 5
        }
      ],
      "path": [
        "person",
        "scores"
      ]
    }
  ]
}
```

## Interfaces

Let's say that the score is one way that a person can be measured, but there are other things that can also be used to measure a person (age, weight etc.)

We can define an interface that Score (and Age and Weight) implements, and this model with the interface will be available in the schema of our endpoint:

```
interface Measurable {
  value: BigInteger
}

type Age implements Measurable {
  value: BigInteger
}

type Weight implements Measurable {
  value: BigInteger
}

type Score implements Measurable {
  events: [Event]
  id: String
  name: ScoreType
  value: BigInteger
}
```

## Introspection

Because GraphQL has a type system built in, we can define our model as part of our schema. With REST (JAX-RS) you need to add something like MicroProfile OpenAPI 
to describe the endpoint.

In MicroProfile GraphQL you can get the schema that has been generated by doing a GET on `/graphql/schema.graphql`

You can also use GraphQL Query to inspect the schema. This is called introspection. Example, if you want to get all the types that are available:

```graphql
{
  __schema{
    types {
      name
      kind
    }
  }
}
```

This will return:

```json
{
  "data": {
    "__schema": {
      "types": [
        {
          "name": "Action",
          "kind": "ENUM"
        },
        {
          "name": "Address",
          "kind": "OBJECT"
        },
        {
          "name": "AddressInput",
          "kind": "INPUT_OBJECT"
        },
        {
          "name": "BigDecimal",
          "kind": "SCALAR"
        },
        ... abbreviated
      ]
    }
  }
}
```

## Other annotations

You can define the model metadata using annotations. GraphQL has built in annotations for all options, but also support JsonB annotations. 
Most annotations can be used on a field or a method and the following will apply:

- If the annotation is on the field, it applies to both output type (result of a query/mutation) and the input type (input parameter to a query/mutation)
- If the annotation is only on a getter method, it applies only to the output type (result of a query/mutation)
- If the annotation is only on a setter method, it applies only to the input type (input parameter to a query/mutation)

Here are some of the options available:

- `@Name` (or `@JsonbProperty`) will name the field. If the annotation is not present, the field name (or method name without the get/set/is) will be used.
- `@Description` can be used to provide a description in the generated schema for entity types and fields.
- `@DefaultValue` annotation may be used to specify a value in an input type to be used if the client did not specify a value.
- `@Ignore` (or `@JsonbTransient`) will omit a field when creating the schema.
- `@NonNull` will mark a field as not null in the schema.

You can also define the format for Numbers and Dates:

- `@NumberFormat` (or `@JsonbNumberFormat`) allows you to define the format of a number type.
- `@DateFormat` (or `@JsonbDateFormat`) allows you to define the format of a date / time type.

## In the pipeline

So what is next for MicroProfile GraphQL? There are a few features that are listed in the plan for the next release, i.e.:

* **Context** - A request scoped object that has information of the request. This will allow developers to optimize their code further downstream. 
An example would be to create better SQL based on the fields being requested.
* **Pagination** - Even though you can technically add pagination by manually adding `first` and `offset` parameters, this feature will allow developers to
just annotate a method with `@Paginate` and then the pagination fields will be available. This makes your code cleaner.
* **Support for other MicroProfile APIs** - define how other MicroProfile APIs can be used in conjunction with GraphQL. 
An example would be to make sure GraphQL requests can report metrics using MicroProfile Metrics.
* **Custom Scalar** - At the moment, there is a set of pre-defined Scalars that developers can use. We will investigate how to allow developers
to create and define their own scalars.

See the full [current list](https://github.com/eclipse/microprofile-graphql/issues?q=is%3Aopen+is%3Aissue+milestone%3A1.1) 

## Links

- The application we referenced in this blog post can be found at [https://github.com/phillip-kruger/graphql-example](https://github.com/phillip-kruger/graphql-example)
- Read the full specification [https://download.eclipse.org/microprofile/microprofile-graphql-1.0/microprofile-graphql.html](https://download.eclipse.org/microprofile/microprofile-graphql-1.0/microprofile-graphql.html)
- We used the SmallRye Implementation that you can find at [https://github.com/smallrye/smallrye-graphql](https://github.com/smallrye/smallrye-graphql)
