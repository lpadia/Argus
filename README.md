Argus  [![Build Status](https://travis-ci.org/SalesforceEng/Argus.svg?branch=master)](https://travis-ci.org/SalesforceEng/Argus) [![Coverage](https://codecov.io/github/SalesforceEng/Argus/coverage.svg?branch=master)](https://codecov.io/github/SalesforceEng/Argus?branch=master) [![Static Analysis](https://scan.coverity.com/projects/8155/badge.svg)](https://scan.coverity.com/projects/salesforceeng-argus)
=====

Argus is a time-series monitoring and alerting platform. It consists of various services to configure alerts, ingest and transform metrics and events, send notifications, create namespaces, and establish and enforce policies and quotas for usage.

Its architecture allows all of these services to be retargeted to new technology as it becomes available, with little to no impact on the users.

To find out more, [see the wiki.](https://github.com/SalesforceEng/Argus/wiki)

![Argus UI](https://cloud.githubusercontent.com/assets/15337203/12775758/53f98b02-ca05-11e5-88b0-1fd11afe335f.png)

## Building Argus

### Installing Resource Filters

Argus uses the `argus-build.properties` file as a resource filter for the build and all the module builds. After you clone the project for the first time, or after you change this file, you must create and install the dependency jars which will contain these filters. Those dependency jars are then pulled in by the modules, expanded, and have their values applied to the module-specific builds. Luckily, it's a straightforward operation. After you clone the project or update the `argus-build.properties` file, execute the following command from within the parent project.

```
mvn -DskipTests=true --non-recursive install
```

### Running Unit Tests

Once the resource filters are installed, you can run unit tests. Running the unit tests doesn't require any changes to the argus-build.properties file. Install the resource filters and execute the `test` goal.

```
mvn test
```

>**Note**: Only unit tests are run by `codecov.io`. The coverage reported is less than the coverage obtained by running the full test suite.

### Running Integration Tests

The integration tests for Argus use the `LDAPAuthService` implementation of the `AuthService` interface and the `DefaultTSDBService` implementation of the `TSDBService` interface (which targets OpenTSDB). To run the integration tests you must update the `argus-build.properties` file to correctly set up the external LDAP you're testing against and the OpenTSDB endpoints to use.  The snippet below shows the specific properties that should be modified in the `argus-build.properties` file. After you make these updates, you must reinstall the resource filter dependencies as previously described and execute the `clean` goal, before running the integration tests.

```
# The LDAP endpoint to use
service.property.auth.ldap.endpoint=ldaps://ldaps.yourdomain.com:636
# A list of comma separated search paths used to query the DN of users attempting to authenticate.
# This example lists two separate search bases.  One for users and one for service accounts.
service.property.auth.ldap.searchbase=OU=active,OU=user,DC=yourdomain,DC=com:OU=active,OU=robot,DC=yourdomain,DC=com
# This specifies of the DN for the privileged user that is used to bind and subsequently execute the search for user DN's
service.property.auth.ldap.searchdn=CN=argus_admin,OU=active,OU=user,DC=yourdomain,DC=com
# The password for the privileged user above.
service.property.auth.ldap.searchpwd=Argu5R0cks!
# The LDAP field with which the username provided during a login attempt, will be matched.
# This is used so Argus can obtain the DN for the user attempting to login, and subsequently attempt to bind as that user.
service.property.auth.ldap.usernamefield=sAMAccountName
# The TSDB read endpoint
service.property.tsdb.endpoint.read=http://readtsdb.yourdomain.com:4466
# The TSDB write endpoint
service.property.tsdb.endpoint.write=http://writetsdb.yourdomain.com:4477
```

Once the modifications have been made and the resource filters reinstalled, you're ready to run the complete suite of tests, including the integration tests.

```
mvn verify
```

### Generating Coverage Reports

Coverage is calculated every time tests are run for all modules exception ArgusWeb. To generate a coverage report for a module, `cd` into the module subdirectory and run the report generation target.

```
mvn jacoco:report
```

Coverage reports are generated in the `target/site/jacoco` directory.

### Deploying & Running Argus

For information on how to deploy, configure, and run Argus, [see the wiki](https://github.com/SalesforceEng/Argus/wiki).
