## Antaeus

Antaeus (/ænˈtiːəs/), in Greek mythology, a giant of Libya, the son of the sea god Poseidon and the Earth goddess Gaia. He compelled all strangers who were passing through the country to wrestle with him. Whenever Antaeus touched the Earth (his mother), his strength was renewed, so that even if thrown to the ground, he was invincible. Heracles, in combat with him, discovered the source of his strength and, lifting him up from Earth, crushed him to death.

Welcome to our challenge.

## The challenge

As most "Software as a Service" (SaaS) companies, Pleo needs to charge a subscription fee every month. Our database contains a few invoices for the different markets in which we operate. Your task is to build the logic that will schedule payment of those invoices on the first of the month. While this may seem simple, there is space for some decisions to be taken and you will be expected to justify them.

## Instructions

Fork this repo with your solution. Ideally, we'd like to see your progression through commits, and don't forget to update the README.md to explain your thought process.

Please let us know how long the challenge takes you. We're not looking for how speedy or lengthy you are. It's just really to give us a clearer idea of what you've produced in the time you decided to take. Feel free to go as big or as small as you want.

## Developing

Requirements:
- \>= Java 11 environment

Open the project using your favorite text editor. If you are using IntelliJ, you can open the `build.gradle.kts` file and it is gonna setup the project in the IDE for you.

### Building

```
./gradlew clean build
```

### Running

There are 2 options for running Anteus. You either need libsqlite3 or docker. Docker is easier but requires some docker knowledge. We do recommend docker though.

*Running Natively*

Native java with sqlite (requires libsqlite3):

If you use homebrew on MacOS `brew install sqlite`.

```
./gradlew run
```

*Running through docker*

Install docker for your platform

To build `pleo-antaeus` container with recent sources and run it with pre-configured volume execute

```
./docker-start.sh
```

To clean up all resources generated by `pleo-anteaus` docker image execute

```
./docker-clean.sh
```

### App Structure
The code given is structured as follows. Feel free however to modify the structure to fit your needs.
```
├── buildSrc
|  | gradle build scripts and project wide dependency declarations
|  └ src/main/kotlin/utils.kt 
|      Dependencies
|
├── pleo-antaeus-app
|       main() & initialization
|
├── pleo-antaeus-core
|       This is probably where you will introduce most of your new code.
|       Pay attention to the PaymentProvider and BillingService class.
|
├── pleo-antaeus-data
|       Module interfacing with the database. Contains the database 
|       models, mappings and access layer.
|
├── pleo-antaeus-models
|       Definition of the Internal and API models used throughout the
|       application.
|
└── pleo-antaeus-rest
        Entry point for HTTP REST API. This is where the routes are defined.
```

### Main Libraries and dependencies
* [Exposed](https://github.com/JetBrains/Exposed) - DSL for type-safe SQL
* [Javalin](https://javalin.io/) - Simple web framework (for REST)
* [kotlin-logging](https://github.com/MicroUtils/kotlin-logging) - Simple logging framework for Kotlin
* [JUnit 5](https://junit.org/junit5/) - Testing framework
* [Mockk](https://mockk.io/) - Mocking library
* [Sqlite3](https://sqlite.org/index.html) - Database storage engine

Happy hacking 😁!

## Progress

Tue, May 4th
1. Project build - **10 mins**
  * updated gradle version to 6.3 to avoid build failure: java.lang.NoClassDefFoundError: Could not initialize class org.codehaus.groovy.vmplugin.v7.Java7)
  * **TODO**: fix warning during app start-up [See](https://docs.gradle.org/6.3/userguide/command_line_interface.html#sec:command_line_warnings)
2. Application Startup - **20 mins**
  * updated docker start related documentation
  * investigated existing docker-start.sh and docker-clean.sh scripts - **1 hour**
    * **TODO**: create separate Dockerfile for dev-mode to avoid image rebuild
3. Tested existing endpoints via Postman - **10 mins**
   
Wed, May 5th
4. Exploration
  * Event-driven architecture
  * Batch processing
  * Akka
  * Kotlin Coroutins
5. Requirements:
  * test coverage:
    * functional tests with happy path and few corner cases (pending payments, network outages, slow payments)
    * performance testing
      * 10_0000 invoices
      * 1000_0000 invoices
  * no third-party libs
  * !!!put down Use Cases current impl should support
  * rest
    * filter out invoices by status: PAID, PENDING
    * filter out invoices by targetDate
  * billing  
    * record invoice payments separately: required to track unsuccessful tx
  * payment provider
    * I plan to reduce scope implementing provider, maybe make mock more sophisticated? 
  * events logging:
    * billing started
    * billing occurred
    * billing failed
6. High-level architecture:
  * Scheduler that initiates billing process - cron scheduler https://github.com/justwrote/kjob
  * Invoice Generator that retrieves invoices that are ready for billing - by chunks
  * Billing Processor - performs invoice billing (maybe it worth to perform batch update) 
  * Utilize Coroutines + Channel/Actor
Thu, May 6th - ***5hours***
7. added Kotlin Coroutines support to the project: including tests
8. Created draft prototype with coroutines: started with fan-out work distribution through channel; plan to use Actor as the next step
9. Draft business logic implementation:
  * extended Invoice with fields
    * targetDate - date up to which invoicing system expects to bill for
    * createdAt - time, when invoice was created
    * still considering required Invoice statuses
  * Added InvoicePayment Entity: each record keeps the history of payments/attempts for the given invoice
10. Implemented draft Billing Service
