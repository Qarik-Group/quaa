# Quaa - Quick UAA everywhere

`quaa` (pronounced Qu-Aye-Aye) is a family of projects to quickly deploy/run the Cloud Foundry UAA locally, or to a remote platform/cloud.

* [Run locally](https://github.com/starkandwayne/quick-uaa-local)
* [Deploy to any Cloud Foundry](https://github.com/starkandwayne/quick-uaa-deployment-cf)
* [Deploy to any Cloud with BOSH](https://github.com/starkandwayne/quick-uaa-deployment)
* [>> Request another target](https://github.com/starkandwayne/quaa/issues/new)

Each distribution of Quaa includes a `quaa` CLI with a consistent set of handy subcommands:

```plain
quaa up
quaa info
quaa env
quaa logs
quaa ssh
quaa auth-client
```

The latter `quaa auth-client` immediately allows you to start interacting & configuring your UAA with the [new `uaa` CLI](https://github.com/cloudfoundry-incubator/uaa-cli) (no support is included for the legacy `uaac` CLI; I like the core team's new `uaa` CLI too much). Example `uaa` commands:

```plain
uaa clients
uaa users
uaa groups
uaa create-client
uaa create-user
uaa add-member
uaa curl
```

Each distribution of Quaa also installs the `uaa` CLI for you.

## What is the Cloud Foundry UAA?

The Cloud Foundry UAA (User Account & Authentication) can be the backbone of both your company's user-facing authentication & authorization (who is this user and what are they allowed to do within your app), and your internal inter-microservice authorization (which other apps are authorized to interact with an app as a client peer).

The UAA is open source, flexible, configurable, secure, and can run it anywhere you can run a Java/Tomcat application.

Unfortunately, the UAA core team only supports running the UAA using Cloud Foundry BOSH, or build-from-source `gradlew` commands.

You need Quaa because you don't know what BOSH is, don't yet know why you need to know BOSH, or don't want to run the UAA with BOSH (e.g. deploy to Cloud Foundry).

Quaa fills in the gap - it provides a single CLI experience `quaa up` to deploy/run the UAA in a variety of ways.

Quaa makes it very easy to explore using the UAA for your current applications' authentication and authorization.