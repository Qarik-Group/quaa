# Quaa - Quick UAA everywhere

`quaa` (pronounced Qu-Aye-Aye) is a family of projects to quickly deploy/run the Cloud Foundry UAA locally, or to a remote platform/cloud.

* [Run locally](https://github.com/starkandwayne/quick-uaa-local) (or with [MacOS Homebrew](#homebrew-installation))
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
quaa int
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

## Installation

Most methods of installation are with `git clone`, adding the project's `bin` folder to the `$PATH`, and proceeding with `quaa up`. The exception, `brew install starkandwayne/cf/quaa`, is discussed later.

Consider [Quick UAA Local](https://github.com/starkandwayne/quick-uaa-local), which can run anywhere that has Java8 and bash already installed.

```plain
git clone https://github.com/starkandwayne/quick-uaa-local ~/workspace/quick-uaa-local
cd ~/workspace/quick-uaa-local

# either
direnv allow
# or manually run
source "$(bin/quaa env)"

quaa up
```

If you have [`direnv`](https://direnv.net/) installed, you will be prompted to run `direnv allow` when you change into the project directory. Alternately, manually run `source "$(bin/quaa env)"` to add the project's `bin` directory into your path.

The helper CLI `bosh` will be downloaded. It is used internally to generate UAA configuration files with random secrets, certificates, and keys. It also allows you to more easily explore deploy-time changes to UAA configuration with [JSON patches](https://github.com/cppforlife/go-patch).

The behaviour of `quaa up` will depend upon which Quaa project you are using.

* [Run locally](https://github.com/starkandwayne/quick-uaa-local) - will download a pre-compiled UAA `.war` file, download Apache Tomcat, configure the UAA, and run Tomcat within your current terminal window.

    Subsequent `quaa` commands will need to be run in a new terminal window.

* [Deploy to any Cloud Foundry](https://github.com/starkandwayne/quick-uaa-deployment-cf) - will download a pre-compiled UAA `.war` file, configure the UAA, and push the application to your current target Cloud Foundry.

    It requires that you've already provisioned a PostgreSQL or MySQL database in the same space.

* [Deploy to any Cloud with BOSH](https://github.com/starkandwayne/quick-uaa-deployment) - will download a pre-compiled UAA BOSH release, configure the BOSH deployment, and deploy it to your target BOSH environment. By default it deploys to a local VirtualBox. You can provide flags to target specific Clouds/IaaS. For example `quaa up --cpi aws`, or `quaa up --cpi vsphere`.

    For BOSH users, this is using `bosh create-env` for deployment rather than `bosh deploy`. That is, it does not require a running BOSH director.

The Cloud Foundry UAA is both a web application for human users, and an HTTP API for client applications.

Run `quaa info` to discover the URL and credentials for your UAA:

```plain
$ quaa info
  url: http://localhost:8080
  client: uaa_admin
  client_secret: 1fa7h127dtys76rfhzzu
  username: admin
  password: 137m0ttuwt3uzikfp0tr
```

Note: the Quick UAA Local edition (and the MacOS Homebrew version) only run as HTTP currently. The Cloud Foundry and BOSH deployments are secure HTTPS deployments.

If you visit the `url: http://localhost:8080` in a browser you can login as the initialised `admin` user. A better name for this user might be `tutorial@example.com`. You won't use `admin` again; instead you will create users for each human - either via the `uaa` CLI, via the UAA API (say from your signup/account-creation application), or users are automatically provided by federated SAML/Active Directory backends.

To install and use the `uaa` CLI as the `uaa_admin` client:

```plain
$ quaa auth-client
installing uaa cli '0.0.1' into: ~/workspace/quick-uaa-local/bin/
Target set to http://localhost:8080
Access token successfully fetched and added to context.
```

You can now start to learn to craft your UAA magic.

To look up the initial clients (applications that want to integrate with the UAA):

```plain
$ uaa clients
[
  {
    "client_id": "uaa_admin",
    "scope": [
      "uaa.none"
    ],
    "resource_ids": [
      "none"
    ],
    "authorized_grant_types": [
      "client_credentials"
    ],
    "authorities": [
      "clients.read",
      "password.write",
      "clients.secret",
      "clients.write",
      "uaa.admin",
      "scim.write",
      "scim.read"
    ],
    "lastModified": 1537710582891
  }
]
```

The client `uaa_admin` matches the result of `quaa info`, and is the UAA client used to authorize `quaa auth-client`. Don't delete it :)

Your current UAA is not federated to any external user directory, so create yourself a user account. Spoil yourself every once in a while.

```plain
$ uaa create-user drnic@starkandwayne.com \
  --email drnic@starkandwayne.com \
  --givenName "Dr Nic" \
  --familyName "Williams" \
  --password drnic_secret
```

To view the two UAA users (`drnic@starkandwayne.com` and the bootstrapped `admin` user):

```plain
uaa users
```

### Homebrew Installation

Instead of the `git clone` approach for [Quick UAA Local](https://github.com/starkandwayne/quick-uaa-local) you can use Homebrew to install the `quaa` CLI and its associated files:

```plain
brew install starkandwayne/cf/quaa
```

This will also install the `uaa` CLI, plus the internally used `bosh` CLI.

When you first run `quaa up` it will download Apache Tomcat.

## Continued Learning

Your learning and exploration can continue from this point onwards.

From the UAA core team:

* [UAA Overview](https://docs.cloudfoundry.org/uaa/uaa-overview.html)
* [UAA Architecture](https://docs.cloudfoundry.org/concepts/architecture/uaa.html)
* [UAA API docs](https://docs.cloudfoundry.org/api/uaa)
* [UAA Source Code](https://github.com/cloudfoundry/uaa)

From Stark & Wayne:

* [Ultimate Guide to UAA - WIP](https://ultimate-guide-to-uaa-staging.cfapps.io/), by Dr Nic Williams (initial creator of Quaa projects)
* [Example UAA applications and tutorials](https://github.com/starkandwayne/ultimate-guide-to-uaa-examples)