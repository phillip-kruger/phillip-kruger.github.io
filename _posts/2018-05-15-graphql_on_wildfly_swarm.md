---
title: GraphQL on Wildfly swarm
tags: [Java EE, Wildfly swarm, GraphQL]
image: "/images/wildfly_swarm.png"
bigimg: "/images/GraphQL_on_WildflySwarm/banner.jpg"
---

*"GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. 
GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for 
exactly what they need and nothing more, makes it easier to evolve APIs over time, and enables powerful developer tools."*

-- from [https://graphql.org/](https://graphql.org/)

Anyone that has been building [REST](https://en.wikipedia.org/wiki/Representational_state_transfer) services that is being used by multiple consumers, like other services or web sites or mobile devices, will know that is 
very hard to build that perfect Endpoint that satisfied all the needs. You typically end up with variations of the same service, for all that special cases :)

Now, we all know we should just be using [HATEOAS](https://en.wikipedia.org/wiki/HATEOAS)... and it was on my TODO list (promise !), until I stumble upon [GraphQL](https://graphql.org).

So in this blog post I explain how you can add GraphQL to you existing [JAX-RS](https://en.wikipedia.org/wiki/Java_API_for_RESTful_Web_Services) application, without to much effort.

# Example project

The example project is available in [Github](https://github.com/phillip-kruger/membership), and very easy to get started

```bash
git clone https://github.com/phillip-kruger/membership.git
cd membership
mvn clean install
```
This will start a fatjar wildfly-swarm with the example application [http://localhost:8080/membership/](http://localhost:8080/membership/)

## High level

![high-level](https://raw.githubusercontent.com/phillip-kruger/membership/master/membership.png)

The example is a basic Membership service, where you can get all members, or a specific member. You can add, edit and remove a member.

The application is a typical JAX-RS, CDI, EJB, JPA, Bean validation Java EE application, and we are adding a new GraphQL Endpoint.

The GraphQL part is using the following libraries:

* [graphql-java](https://github.com/graphql-java/graphql-java)
* [graphql-java-servlet](https://github.com/graphql-java/graphql-java-servlet)
* [graphQL-spqr](https://github.com/leangen/GraphQL-SPQR)
* [graphiql](https://github.com/graphql/graphiql)


The only java classes I added to expose my existing JAX-RS as GraphQL as well:

* [MembershipGraphQLListener](https://github.com/phillip-kruger/membership/blob/master/src/main/java/com/github/phillipkruger/membership/graphql/MembershipGraphQLListener.java) - to register the "/graphql" servlet listener.
* [MembershipGraphQLApi](https://github.com/phillip-kruger/membership/blob/master/src/main/java/com/github/phillipkruger/membership/graphql/MembershipGraphQLApi.java) - the GraphQL Endpoint. Just wrapping the existing Stateless service.
* [MembershipErrorHandler](https://github.com/phillip-kruger/membership/blob/master/src/main/java/com/github/phillipkruger/membership/graphql/MembershipErrorHandler.java) - to handle Exceptions.

Using the annotations from [graphQL-spqr](https://github.com/leangen/GraphQL-SPQR), the MembershipGraphQLApi class really just describe the service and wrap the existing `@Stateless` service:

```java

    @RequestScoped
    public class MembershipGraphQLApi {
    
        @Inject
        private MembershipService membershipService;
        
        // ...
        @GraphQLQuery(name = "memberships")
        public List<Membership> getAllMemberships(Optional<MembershipFilter> filter,
                                    @GraphQLArgument(name = "skip") Optional<Integer> skip,
                                    @GraphQLArgument(name = "first") Optional<Integer> first) {
            return membershipService.getAllMemberships(filter, skip, first);   
        }
        // ...
    }
```

My hope - we will soon have a JAX-QL (or something) as part of JavaEE (or Jakarta EE, or MicroProfile) to make this even easier !!

## First some REST

I am using [MicroProfile OpenAPI](https://github.com/eclipse/microprofile-open-api) and [Swagger UI](https://swagger.io/swagger-ui/) to create Open API definitions for the REST Endpoint.

You can test some queries using [http://localhost:8080/membership/rest/openapi-ui/](http://localhost:8080/membership/rest/openapi-ui/)

![swagger-ui](/images/GraphQL_on_WildflySwarm/swagger_screenshot.png)

**Example** - Getting all memberships:

`GET http://localhost:8080/membership/rest`

This will return:

```javascript

    [
      {
        "membershipId": 1,
        "owner": {
          "id": 1,
          "names": [
            "Natus",
            "Phillip"
          ],
          "surname": "Kruger"
        },
        "type": "FULL"
      },
      {
        "membershipId": 2,
        "owner": {
          "id": 2,
          "names": [
            "Charmaine",
            "Juliet"
          ],
          "surname": "Kruger"
        },
        "type": "FULL"
      },
      {
        "membershipId": 3,
        "owner": {
          "id": 3,
          "names": [
            "Koos"
          ],
          "surname": "van der Merwe"
        },
        "type": "FULL"
      },
      {
        "membershipId": 4,
        "owner": {
          "id": 4,
          "names": [
            "Minki"
          ],
          "surname": "van der Westhuizen"
        },
        "type": "FREE"
      }
    ]

```

**Example** - Getting a certain membership (1):

`GET http://localhost:8080/membership/rest/1`

This will return:

```javascript

    {
      "membershipId": 1,
      "owner": {
        "id": 1,
        "names": [
          "Natus",
          "Phillip"
        ],
        "surname": "Kruger"
      },
      "type": "FULL"
    }
```

## Now let's look at GraphQL

The application includes the [GraphiQL](https://github.com/graphql/graphiql) UI (as a webjar), that makes it easy to test some GraphQL Queries

You can test some queries using [http://localhost:8080/membership/graph/graphiql/](http://localhost:8080/membership/graph/graphiql/)

![swagger-ui](/images/GraphQL_on_WildflySwarm/graphiql_screenshot.png)

So let's see if GraphQL delivers on the "No more Over- and Under Fetching" promise.

### Get all memberships and all fields (so the same as the REST get all)

```javascript

    query Memberships {
        memberships{
            ...fullMembership
        }
    }

    fragment fullMembership on Membership {
        membershipId
        owner{
            ...owner
        }
        type
    }

    fragment owner on Person {
        id
        names
        surname  
    }
```

This will return all values, however, it's now easy to define what fields should be included...

### Get all memberships and but only the id field

```javascript

    query Memberships {
        memberships{
            ...membershipIdentifiers
        }
    }

    fragment membershipIdentifiers on Membership {
        membershipId
    }

```

The resulting payload is now much smaller:

```javascript

    {
      "data": {
        "memberships": [
          {
            "membershipId": 1
          },
          {
            "membershipId": 2
          },
          {
            "membershipId": 3
          },
          {
            "membershipId": 4
          }
        ]
      }
    }

```

### Now let's get only specific types of memberships (so get all FREE memberships)

```javascript

    query FilteredMemberships {
        memberships(filter:{
            type:FREE
        }){
            ...fullMembership
        }
    }

    fragment fullMembership on Membership {
        membershipId
        owner{
            ...owner
        }
        type
    }

    fragment owner on Person {
        id
        names
        surname  
    }
```

This will return just the free memberships. Cool !

### Or even better, all members that's surname starts with "Kru"

```javascript

    query FilteredMemberships {
        memberships(filter:{
            surnameContains: "Kru"
        }){
            ...fullMembership
        }
    }

    fragment fullMembership on Membership {
        membershipId
        owner{
            ...owner
        }
        type
    }

    fragment owner on Person {
        id
        names
        surname  
    }
```

Great !! We found two people:

```javascript

    {
      "data": {
        "memberships": [
          {
            "membershipId": 1,
            "owner": {
              "id": 1,
              "names": [
                "Natus",
                "Phillip"
              ],
              "surname": "Kruger"
            },
            "type": "FULL"
          },
          {
            "membershipId": 2,
            "owner": {
              "id": 2,
              "names": [
                "Charmaine",
                "Juliet"
              ],
              "surname": "Kruger"
            },
            "type": "FULL"
          }
        ]
      }
    }

```

### Getting a certain membership, using a variable on the client:

```javascript

    query Membership($id:Int!) {
        membership(membershipId:$id){
            ...fullMembership
        }
    }

    fragment fullMembership on Membership {
        membershipId
        owner{
          ...owner
        }
        type
    }

    fragment owner on Person {
        id
        names
        surname  
    }
```

**The variable:**

```javascript

    {"id":1}

```

### Include fields on a certain condition:

```javascript

    query Membership($id:Int!,$withOwner: Boolean!) {
        membership(membershipId:$id){
            ...fullMembership
        }
    }

    fragment fullMembership on Membership {
        membershipId
        owner @include(if: $withOwner){
          ...owner
        }
        type
    }

    fragment owner on Person {
        id
        names
        surname  
    }

```

**The variable:**

```javascript

    {"id":1,"withOwner": false}

```

this will exclude the owner (true to include):

```javascript
    {
      "data": {
        "membership": {
          "membershipId": 1,
          "type": "FULL"
        }
      }
    }
```

### Pagination

Let's use the get all query, but paginate.

```javascript
    query Memberships($itemsPerPage:Int!,$pageNumber:Int!) {
        memberships(
            first:$itemsPerPage,
                skip:$pageNumber) {
            membershipId
                owner{
                    names
                    surname
                }
            type
        }
    }
```

**The variable:**

```javascript

    {"itemsPerPage": 2,"pageNumber": 1}

```

This will return the first 2 results, and then you can page by increasing the "pageNumber" value.

### Mutations 

#### Create

```javascript

    mutation CreateMember {
        createMembership(membership: {type:FULL,owner: {names: "James",surname:"Small"}}) {
            membershipId
        }
    }

```

This will create the new membership and return the id.

#### Update

```javascript

    mutation EditMember($membership: MembershipInput!) {
        createMembership(membership:$membership) {
            membershipId
        }
    }

**The variable:**

```javascript

    {
        "membership": {
          "membershipId": 2,
            "owner": {
                "names": [
                "Charmaine",
                "Juliet"
              ],
                "surname": "Krüger"
            },
            "type": "FULL"
        }
    }

```

(added a umlaut on the u of Kruger, now it should be "Krüger")

#### Delete

```javascript

    mutation DeleteMembership($id:Int!){
        deleteMembership(membershipId:$id){
          membershipId
        }
    }

```

**The variable:**

```javascript

{"id":1}

```

This will delete membership 1.

### Exception.

The [MembershipErrorHandler](https://github.com/phillip-kruger/membership/blob/master/src/main/java/com/github/phillipkruger/membership/graphql/MembershipErrorHandler.java) translate
a ConstraintViolationException (that is thrown when the bean validation fails) and create a nice error message for GraphQL.

So let's try and create a member with a surname of just one letter.

```javascript

    mutation CreateMember($membership: MembershipInput!) {
        createMembership(membership:$membership) {
            membershipId
        }
    }

```

**The variable:**

```javascript

    {
         "membership": {
             "owner": {
                 "names": "Christina",
                 "surname": "S"
             },
             "type": "FULL"
         }
     }  

```

This will return the bean validation error message:

```javascript

    {
      "data": {
        "createMembership": null
      },
      "errors": [
        {
          "message": "Surname 'S' is too short, minimum 2 characters",
          "path": null,
          "extensions": null
        }
      ]
    }

````

If you look at the Person POJO:

```java

    @NotNull(message = "Surname can not be empty") 
    @Size(min=2, message = "Surname '${validatedValue}' is too short, minimum {min} characters")
    private String surname;

```

### Introspection

The other nice thing about GraphQL is that it has a Schema & Type System that you can query:

```javascript

    {
        __schema {
            queryType {
                name
                fields {
                    name
                }
            }
            mutationType{
                name
                fields{
                    name
                }
            }
            subscriptionType {
                name
                fields{
                    name
                }
            }
        }
    }

```

Above will describe the queries and mutations available on this endpoint.

You can also describe you Models:

```javascript

    {
        __type(name: "Membership") {
            name
            kind
            fields {
                name
                args {
                    name
                }
            }
        }
    }

```

## Summary

In this example we did not remove REST, but just added GraphQL as an alternative option for the consumer.

By now it should be clear that the client has much more options to filter and query the data exactly like it needs it. 
All this without the server having to do any extra work. This allows for rapid product iterations on the client side.

The payload over the wire is optimized and we are saving bandwith ! 

Again, my hope - we will soon have a JAX-QL (or something) as part of JavaEE (or Jakarta EE, or MicroProfile) to make this even easier !!