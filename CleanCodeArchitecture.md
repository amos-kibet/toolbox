## [Clean Code Architecture](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)

`Quote`: Your architectures should tell readers about the system, not about the frameworks you used in your system ~ Robert C. Martin

Resilient software is divided into layers. It should be:

- **Independent of Frameworks** -- Libraries and frameworks should be treated as tools and not dependencies

- **Testable** -- Can be tested without external dependencies.

- **Independent of UI** -- You can easily switch CLI for web or Raspberry Pi

- **Independent of any external agency** -- Business rules don't know anything about outside world

One can follow these steps to achience clean architecture:

- Start with models, schema and validation. Write test spec.

- Defer DB decision by using in-memory JSON store

- Create a data-access API that depends on model and in-memory DB. Write test spec.

- First tech decision: UI interface (e.g. web or CLI). Depend on above data-access layer. Write integration test spec.

- Second tech decision: DB choice (MongoDB, SQL). Replace in-memory DB store. Write data-access methods specific to the DB choice. Ensure previously written test specs pass.

Clean architecture intergrates other common architectures like:

- [Hexagonal Architecture](https://en.wikipedia.org/wiki/Hexagonal_architecture_%28software%29)

- [Onion Architecture](http://jeffreypalermo.com/blog/the-onion-architecture-part-1/)

- [Screaming Architecture](http://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html)

See below

![](https://fullstackroyhome.files.wordpress.com/2019/03/cleanarchitecture.jpg)

In the heart/core of an application, we have two layers:

- Entities Layer -- contains all the business entities that construct our application

- Use Cases Layer -- contains all scenarios that the application supports

### Entities Layer

Frameworks are rare here, and code changes often due to changes in business logic/rules. Use of `import` or `require` is rare here.

As you go to outer layers, frameworks start to appear.

Entities contain the application models, which are defined without coupling it with a specific database framework

Example:

```
Student
    Id
    FirstName
    LastName
    Email

Course
    Id
    Name
```

### Use Cases Layer

Centralizes all business logic.

Example:

Following above entities description, our application API nees to support these use-cases:

```
Get a list of all students
Get single student details
Add new student
etc
```

Taking the `Add new student` use case, the main responsibilities of this use case are:

```
Business rules validation
Check that the student doesn't exist in the DB
Create a new student object
Add the new student to the DB
Update the university CRM system
```

The above use case wants to access the DB, but it's a violation of clean architecture. To solve this, we will abstract DB interactions, using classes, interfaces and constructors.

### Controllers, Presenters & Gateways

**Controllers**

- Receives user input

- Validates

- Converts to a model that the use case expects

- Calls the use case and pass it the new model

**Presenter**

- Gets data from application repository & build a ViewModel

- Does formatting, like strings and dates

- Adds presentation data like flags

- Prepares data to be dispalyed in the UI

Controller & Presenter can be implemented together

Dependencies are injected to the controller. It extracts what it needs and passes them down to the use case.

### Frameworks

Here is where all the details go. Th web, the database are all details.

In a Node.js API, the web details can be implemented by Express.

[Further Read](https://mannhowie.com/clean-architecture-node)
