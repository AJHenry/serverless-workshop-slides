# Intro to GraphQL

<p class="text-xl">Powered by</p>

<center>
<img src="images/serverless-logo.png" width="400">
<svg viewBox="0 0 816 240" style="fill:#ffffff;height:56px;display:block"><path d="M166.22 158.36h-22l-25.27-71.7-14.19 39.22h21.84l6 17.07H99.43l-5.74 15.43h-22l35.1-91.1h24.31zm392.25 0v-17.07h-38.52v-74h-19.53v91.1zm112.57 0v-17.07h-38.5v-74h-19.56v91.1zM392.25 83.59a29.29 29.29 0 1 0 29.29 29.29 29.32 29.32 0 0 0-29.29-29.29m0-17.83a47.12 47.12 0 1 1-47.11 47.12 47.12 47.12 0 0 1 47.12-47.12zm368.57 17.83a29.29 29.29 0 1 0 29.29 29.29 29.32 29.32 0 0 0-29.29-29.29m0-17.83a47.12 47.12 0 1 1-47.12 47.12 47.12 47.12 0 0 1 47.12-47.12zm-466 31.38c0 16.19-13.12 29.87-29.32 29.87h-23.06v31.35h-19.4v-91.1h42.5c16.17 0 29.29 13.68 29.29 29.87zm-17.83 0c0-6.33-5.15-12-11.49-12h-23.06v24.04h23.1c6.31 0 11.47-5.71 11.47-12.05zm-70.71 88.87a5 5 0 0 0-4.09 2.12s-4.37 5-6.73 7.35a108.71 108.71 0 1 1-8.49-161.28 12.25 12.25 0 1 0 6.87-7.32 118.67 118.67 0 1 0 16 167.71 5 5 0 0 0-3.55-8.56z"></path></svg>
</center>
--

## Presented By

<div class="flex flex-row justify-center align-center">

<div class="flex flex-col px-4 items-center">
<img src="images/andrew.jpg" class="w-40"/>
Andrew Henry
<p class="text-lg">Software Engineer</p>
</div>

<div class="flex flex-col px-4 items-center">
<img src="images/nick.png" class="w-40"/>
Nick Smider
<p class="text-lg">Software Engineer</p>
</div>
</div>

---

## What we'll cover

What is GraphQL anyway?

Why should I use it?

How to use it

Real life examples

---

## What is GraphQL

> GraphQL is a query language [...] that provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more, and enables powerful developer tools.

<sub class="flex justify-end mr-48">— facebook</sub>

--

## In simpler terms

GraphQL is a specification that defines a schema on an API server, which validates client calls to it.

<br/>

GraphQL does _not_ define how or where data is stored though, just how it is accessed.

--

## Architecture

<img src="images/gql-arch.png" />
<p class="text-sm flex flex-end -8">via<a href="https://nordicapis.com/serverless-graphql-architecture-with-graphcool/" class="px-1"> Nordic API</a></p>

---

## Fetching Data with REST

The most popular protocol used to fetch data today

```bash
https://api.github.com/users/AJHenry
```

```json
{
  "login": "AJHenry",
  "id": 24923406,
  "name": "Andrew",
  "company": "@capitalone",
  "bio": "Dunkin > Starbucks ☕"
  // ...
}
```

--

## Where REST Shines

When you need a lot of data

```bash
https://api.github.com/users/AJHenry/repos
```

When you need to interact with your application

```bash
POST   https://example.com/recipes/
GET    https://example.com/recipes/2975
GET    https://example.com/recipes/2975/ingredients
POST   https://example.com/ingredients
GET    https://example.com/ingredient/76
```

--

## Where REST Falls Short

What if I only want my `id` and `company` fields?

> Too bad, you get what you get

<p class="text-2xl">or</p>

```url
https://api.github.com/users/AJHenry?fields=company,id,{*}
```

How do I know what you'll send me?

> Trust my API docs are correct

--

## Can GraphQL do any better?

<center>
<img src="images/yes.gif" class="flex flex-row justify-center items-center"/>
</center>

---

## Topics

- `schema` - The "contract" between the API and the client.

- `resolver` - The function that fills the data in the "contract".

- `query` - A query is how you fetch or read data from the server.

- `mutation` - A mutation is how you write or update values to the server.

--

## Try it Out

Use the following URL to access the GraphQL playground
<br/>
<br/>

```
http://localhost:3000/graphql
```

--

## GraphQL Playground

<img src="images/graphql-playground.png" />

---

## GraphQL's Schema

A GraphQL service is created by defining types and fields on those types (`schema`), then providing functions for each field on each type (`resolver`).

<p class="flex flex-start text-2xl">Schema</p>

```gql
type Query {
  hello: String!
}
```

<p class="flex flex-start text-2xl">Function</p>

```typescript
function hello() {
  return "world";
}
```

--

## Type Validation

GraphQL is strongly typed, so the server knows exactly what to send and the client always knows what they are getting

<p class="flex flex-start text-2xl">Schema</p>

```gql
type NutritionFacts {
  id: ID!
  calories: Int
}

type Ingredient {
  id: ID!
  name: String!
  nutrition: NutritionFacts
}
```

_ID is a special type, but it's value is a string_

--

Notice how all of the response types match exactly what the schema told us

<p class="flex flex-start text-2xl">Response</p>

```json
{
  "id": "5d1947c7-f347-4691-98a5-81e540bad172",
  "name": "chicken breast",
  "nutrition": {
    "id": "1f1f8361-f9ae-4ada-8852-dbfa49cd3b8c",
    "calories": 231
  }
}
```

This makes GraphQL a self-documenting API too!

--

## Nesting Types

Types can be combined to include nested types

```gql
type NutritionFacts {
  id: ID!
  calories: Int!
}

type Ingredient {
  id: ID!
  name: String!
  nutrition: NutritionFacts
  substitutions: [Ingredient]
}
```

Here we have `Ingredient` type, which contains a field for substitutions, which is a list of `Ingredient`s

--

## Resolvers

The job of a `resolver` is to fill in the data for the fields requested.

You might have a resolver for an `Ingredient` that fetches data from a database

<p class="flex flex-start text-2xl">Query Type</p>

```gql
type {
    ingredient(id:String!): Ingredient
}
```

<p class="flex flex-start text-2xl">Function</p>

```typescript
// Return entire ingredient object
function getIngredient(id: string) {
  return db.getIngredientById(id);
}
```

--

Even though the function returns _all_ the ingredient data, if your client only asks for the `name` field, then that is the _only_ data that the server will send back

<p class="flex flex-start text-2xl">Query</p>

```gql
query {
  ingredient(id: "018bd5b2-c2ed-4a1c-b053-0f0a4867e279") {
    name
  }
}
```

<p class="flex flex-start text-2xl">Response</p>

```json
{
  "data": {
    "ingredient": {
      "name": "chicken breast"
    }
  }
}
```

---

## Fetching Data

Once you have a GQL server up and running, it can receive queries to validate and execute

<p class="flex flex-start text-2xl">Query</p>

```gql
query {
  hello
}
```

<p class="flex flex-start text-2xl">Response</p>

```json
{
  "data": {
    "hello": "world"
  }
}
```

--

## Query Type

A `query` is a type in a schema that allows a user to fetch data by specifying _exactly_ what fields they want

```gql
query {
  ingredients {
    id
    name
  }
}
```

You can also alias multiple queries

```gql
query FindRecipes {
  recipes {
    title
  }
}

query FindIngredients {
  ingredients {
    name
  }
}
```

--

## Response Shape

Notice how the response is the same shape as the object we requested

```json
{
  "data": {
    "ingredients": [
      {
        "name": "corn-syrup"
      },
      {
        "name": "chicken breast"
      },
      {
        "name": "honey"
      }
      // ...
    ]
  }
}
```

We asked for the name field for all the ingredients, and that's all we got!

--

## Nested types

You can also query for nested data in the same way you would any other field

<p class="flex flex-start text-2xl">Query</p>

```gql
query FindSubstitution {
  ingredient(id: "a7031408-5737-455c-9d1b-35c5faf948df") {
    name
    id
    substitutions {
      id
      name
    }
  }
}
```

Notice how we are specifying the fields `id` and `name` on the substitutions field

--

## Parameters

Queries can take in arguments as part of their type definitions which are then passed to the resolver to handle

```gql
type query {
  recipes(search: String) Recipe[]
}
```

Notice how the recipes field can take an optional argument `search`, which is a string

```gql
query FindChickenRecipes {
  recipes(search: "chicken") {
    title
  }
}
```

--

## SubParameters

You can also add parameters to fields on types

_This query doubles the recipe_

```gql
query DoubleRecipes {
  recipes {
    title
    ingredients(multiply: 2) {
      name
      amount
      unit
    }
  }
}
```

The `ingredient` field can take an optional parameter `multiply` to multiply the ingredient amounts

---

## Updating Data

`Mutations` are how a client can update and write data to a GQL server

<p class="flex flex-start text-2xl">Mutation</p>

```gql
mutation UpdateRecipe {
  updateRecipe(
    id: "4f145adf-6aaa-4c78-8c45-459a57ed4fad"
    data: { title: "Garlic Honey Glazed Salmon" }
  ) {
    id
    title
  }
}
```

Mutations follow the same syntax of queries but with 1 difference

_Notice the `mutation` keyword instead of `query`_

<!--
<p class="flex flex-start text-2xl">Response</p>

```json
{
  "data": {
    "updateRecipe": {
      "id": "4f145adf-6aaa-4c78-8c45-459a57ed4fad",
      "title": "Garlic Honey Glazed Salmon"
    }
  }
}
``` -->

--

That means you can also use variables within your `Mutations` fields!

```gql
mutation UpdateRecipe {
  updateRecipe(
    id: "4f145adf-6aaa-4c78-8c45-459a57ed4fad"
    data: { title: "Garlic Honey Glazed Salmon" }
  ) {
    id
    title
    ingredients(multiply: 3) {
      amount
      unit
    }
  }
}
```

However, while query fields are executed in parallel, mutation fields run in series, one after the other

--

## Special Input Type

`Mutations` use a special `Input` type to tell what data a server can accept

<p class="flex flex-start text-2xl">Schema</p>

```gql
input RecipeInput {
  title: String
  description: String
  ingredients: [ID]
  steps: [String]
  nutrition: ID
  duration: DurationInput
  yield: Int
}

type Mutation {
  updateRecipe(id: ID, data: RecipeInput): Recipe
}
```

--

You _cannot_ use return types for mutation fields

```gql
type RecipeInput {
  title: String
  description: String
}

type Mutation {
  updateRecipe(id: ID, data: RecipeInput): Recipe
}
```

<br/>
<br/>

The schema validator will throw an error if you do!

```error
Error: The type of RecipeInput must be Input Type
```

---

# Summary

## 1. Create your Schema

This establishes the contract between your server and the clients it serves

- Schemas are strongly typed, so you must be explicit
- You can nest types to create complex objects
- You can add parameters to your fields

--

## 2. Make your Resolvers

Resolvers fulfill the data that is defined on the fields

- The data that is returned from the resolver must match the types of the schema
- The GraphQL server will remove any fields that the user didn't request
- Your resolvers can be from any data source

--

## 3. Fetch your Data

Fetch and update data from your server with queries and mutations 

- Use `query` for fetching data and `mutation` for adding/updating data
- The server will only send back the fields that you request
- The response will be the same shape of the request
- You can use the GraphQL Playground to generate queries that your clients can use

--

## Additional Topics

Here are some additional topics for learning more about GQL

- `Fragments` - Fragments let you construct reusable sets of fields, and then include them in queries where you need to
- `Directives` - Affect executions of queries dynamically with modifiers like `@skip(if: Boolean)`
- `Meta fields` - For advanced typing

---

# Why go Serverless?

--

## Where REST Shines Again

With a typical REST Api, we could have different services handle different endpoints

Service A
```bash
https://api.myapp.com/students/{studentId}
```

Service B
```bash
https://api.myapp.com/classes/{classId}
```

Having different services handle different endpoints can make load distribution easier.

--

## GraphQL Complicates Things

With GraphQL, we only ever have one endpoint that accepts queries and mutations for our schemas.

```bash
https://api.myapp.com/graphql
```

--

## Scaling

The problem we need to solve is how we can scale our single GraphQL endpoint efficiently. 

Serverless functions scale to the exact size you need at any given time and integrate well with many cloud provider's API Gateway implementations. 

What would this architecture look like?

-- 

## AWS Example

<img class="bg-white" src="images/aws-arch.png" />
<p class="text-sm flex flex-end -8">via<a href="https://www.serverless.com/blog/running-scalable-reliable-graphql-endpoint-with-serverless" class="px-1"> Serverless </a></p>

--

## Cost Benefits

When running servers, you pay for every second they are up and running.

When leveraging serverless functions, you pay for invocation time and if you aren't recieving traffic, you won't be charged.

--

## Take Advantage of Resources

Additionally, some providers like AWS provide free tier resources. AWS Lambda provides ["1M free requests per month and 400,000 GB-seconds of compute time per month,"](https://aws.amazon.com/lambda/pricing/) making it a great option for projects and experiments.