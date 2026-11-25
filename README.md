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

`-JaccreditationYear` sets the year of the accreditation that PRNs are issued against and reported on, and the `accreditations/{year}` segment of frontend URLs. The scenario uploads `scenarios/summary-log-{year}.xlsx`, whose loads are dated in January of that year, and reports on January. Only 2026 and 2027 have a workbook, and the 2027 one is on the 2027 template. The default is the year a month ago, so 2026 until 31 January 2027 and 2027 from 1 February. That is the one year in which January has ended, so its report can be created, and PRNs can still be issued.

The backend stores registrations and accreditations for 2026 only, so the seeded registration always runs from 2026 with a stored 2026 accreditation. For 2027 the scenario also seeds an accreditation through the backend's `PUT /v1/dev/external-accreditations/{registrationNumber}/2027` dev route. The backend serves that accreditation only while it reads 2027 accreditations from its `external-accreditations` collection, which it does while `FEATURE_FLAG_REG_ACCRED_EXTERNAL_ACCREDITATIONS` is off. If that flag is switched on in an environment, this step must change before the scenario can run there. The route accepts 2027 only, so the scenario needs changing before the default reaches 2028 on 1 February 2028.

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
