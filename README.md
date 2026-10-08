# STARTER-jpa03
Running at: <https://jpa03-zsjkwon.dokku-02.cs.ucsb.edu>

# Configuring GitHub Pages for the documentation

This repo contains Github Actions scripts that automatically create and publish documentation for the code:
* javadoc for the backend Java code
* jacoco (test coverage) and pitest (mutation testing) reports for the backend Java code

To set this up, follow the instructions here: [`docs/github-pages.md`](docs/github-pages.md)

# Java 25 setup with SDKMAN

This project follows the course instructions for Java 25.0.4, using the
recommended `25.0.4-tem` distribution via SDKMAN, with Maven 3.9.16
(also provided by the included Maven Wrapper, `./mvnw`).
See the [course software installation instructions](https://ucsb-cs156.github.io/f26/info/software.html)
for details on installing SDKMAN, Java and Maven.

If you use SDKMAN, the setup is:

```bash
sdk install java 25.0.4-tem
sdk install maven 3.9.16
sdk env install
java -version
mvn --version
```

The project includes a `.java-version` file and an `.sdkmanrc` file so that
the correct Java version is selected automatically when SDKMAN is present
(run `sdk env` in this directory to select it).  Every `mvn` command below
can also be run as `./mvnw`, which downloads and uses the pinned Maven
version without a separate Maven install.

Both `java -version` and `mvn --version` should report Java 25.0.4 before you
run any of the commands below; with an older Java you may see confusing build
errors (for example, JaCoCo or Pitest complaining about an
`Unsupported class file major version`).

# Getting Started on localhost

Before running the application for the first time on localhost, you must: 

* Set up Google OAuth as documented in [`docs/oauth.md`](docs/oauth.md) 

Otherwise, when you try to login for the first time, you 
will likely see an error such as:

<img src="https://user-images.githubusercontent.com/1119017/149858436-c9baa238-a4f7-4c52-b995-0ed8bee97487.png" alt="Authorization Error; Error 401: invalid_client; The OAuth client was not found." width="400"/>

Then to run the application, use `mvn spring-boot:run`

The app should be available on <http://localhost:8080>
  
# Getting Started on Dokku

Follow the steps here: [`docs/dokku.md`](docs/dokku.md) 

These references may also be helpful:
  * <https://ucsb-cs156.github.io/topics/dokku/getting_started.html>
  * <https://ucsb-cs156.github.io/topics/dokku/postgres_database.html>

# Accessing swagger

Swagger is a tool that allows you to work directly with the RESTful API endpoints of the backend.
It is similar to the tool Postman, but more convenient because it is built directly into the application.

To access the Swagger API endpoints, use:

* <http://localhost:8080/swagger-ui/index.html>

You can also append `/swagger-ui/index.html` to the URL manually when running on Dokku.


# To generate javadoc (locally, for development)

* cd to top level of repo
* use: `mvn javadoc:javadoc`
* open in a web browser: `target/site/apidocs/index.html`

You can also see the javadoc for the main branch and all open pull requests on the 
github pages site associated with the repo; see [/docs/github-pages.md](/docs/github-pages.md) for more info.

* For documentation on Javadoc, see: <https://www.oracle.com/java/technologies/javase/javadoc-tool.html>

# SQL Database access

On localhost:
* The SQL database is an H2 database and the data is stored in a file under `target`
* Each time you do `mvn clean` the database is completely rebuilt from scratch
* You can access the database console via a special route, <http://localhost:8080/h2-console>
* For more info, see [docs/h2-database.md](/docs/h2-database.md)

On Dokku:
* The SQL database is a postgres database provisioned automatically by Dokku
* More info and instructions for accessing the SQL prompt are at [docs/postgres-database.md](/docs/postgres-database.md)

# Unit Testing

To run unit tests, use: `mvn test`

Unit tests are any methods labelled with the `@Test` annotation that are under the `/src/test/java` hierarchy, and have file names that end in `Test.java` or `Tests.java`

Tests ending under `test` in the `web` directory that end in `IT.java` instead of `Test[s].java` are Playwright end-to-end/integration tests.

## Integration Tests

To run only the integration tests, use:
```
INTEGRATION=true mvn test-compile failsafe:integration-test
```

To run only the integration tests *and* see the tests run as you run them,
use:

```
INTEGRATION=true HEADLESS=false mvn test-compile failsafe:integration-test
```

To run a particular integration test (e.g. only `HomePageWebIT.java`) use `-Dit.test=ClassName`, for example:

```
INTEGRATION=true mvn test-compile failsafe:integration-test -Dit.test=HomePageWebIT
```

or to see it run live:
```
INTEGRATION=true HEADLESS=false mvn test-compile failsafe:integration-test -Dit.test=HomePageWebIT
```

Integration tests are any methods labelled with `@Test` annotation, that are under the `/src/test/java` hierarchy, and have names starting with `IT` (specifically capital I, capital T).

By convention, we are putting end-to-end integration tests (the ones that run with Playwright) under the package `src/test/java/edu/ucsb/cs156/example/web`.

Unless you want a particular integration test to *also* be run when you type `mvn test`, do *not* use the suffixes `Test` or `Tests` for the filename.

Note that while `mvn test` is typically sufficient to run tests, we have found that if you haven't compiled the test code yet, running `mvn failsafe:integration-test` may not actually run any of the tests.
