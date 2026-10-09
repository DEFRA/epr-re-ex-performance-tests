# epr-re-ex-performance-tests

A JMeter based test runner for the CDP Platform.

- [Build](#build)
- [Run](#run)
- [Local Testing](#local-testing)
  - [Launch the required services using epr-re-ex-service](#launch-the-required-services-using-epr-re-ex-service)
  - [Running performance tests locally](#running-performance-tests-locally)
- [Licence](#licence)
  - [About the licence](#about-the-licence)

## Build

Test suites are built automatically by the [.github/workflows/publish.yml](.github/workflows/publish.yml) action whenever a change is committed to the `main` branch.
A successful build results in a Docker container that is capable of running your tests on the CDP Platform and publishing the results to the CDP Portal.

## Run

The performance test suites are designed to be run from the CDP Portal.
The CDP Platform runs test suites in much the same way it runs any other service, it takes a docker image and runs it as an ECS task, automatically provisioning infrastructure as required.

The portal profile sets the thread count: `mid` for 100 threads, `max` for 200, otherwise 50.

## Local Testing

### Launch the required services using epr-re-ex-service

Ensure you have the latest version of [epr-re-ex-service](https://github.com/DEFRA/epr-re-ex-service) checked out.

```
docker compose -f compose.yml --profile all up -d
```

This will launch the required services for local testing.
With `-Jenv=local` the scenario calls them on `localhost` at the compose default ports (frontend `3000`, backend `3001`, admin frontend `3002`, entra stub `3010`, defra id stub `3200`, cdp uploader `7337`, cognito stub `9229`).

### Running performance tests locally

Download [JMeter](https://jmeter.apache.org/download_jmeter.cgi) and either open `scenarios/epr-re-ex-test.jmx` in the GUI (`jmeter -Jenv=local`) or run it headless:

```
jmeter -n -t scenarios/epr-re-ex-test.jmx -l report.csv -Jenv=local -JthreadCount=1
```

Without `-JthreadCount` the scenario falls back to 1 frontend thread and 10 admin frontend threads.

## Licence

THIS INFORMATION IS LICENSED UNDER THE CONDITIONS OF THE OPEN GOVERNMENT LICENCE found at:

<http://www.nationalarchives.gov.uk/doc/open-government-licence/version/3>

The following attribution statement MUST be cited in your products and applications when using this information.

> Contains public sector information licensed under the Open Government licence v3

### About the licence

The Open Government Licence (OGL) was developed by the Controller of Her Majesty's Stationery Office (HMSO) to enable
information providers in the public sector to license the use and re-use of their information under a common open
licence.

It is designed to encourage use and re-use of information freely and flexibly, with only a few conditions.
