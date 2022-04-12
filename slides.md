---
theme: ./themes/theme.json
author: DJ Adams
date: DD MMM YYYY
paging: "%d / %d"
---

# Talk

Level up your SAP BTP resource management skills with command line tools and APIs.

# Speaker

DJ Adams, Developer Advocate at SAP

# Topics

* Migrating to the Cloud
* Skills, Methodologies and Ways of Working

# What we'll learn together

In this session we'll learn what resources there are to manage on the SAP Business Technology Platform (SAP BTP), and how to manage them with tools and APIs you can build into automated mechanisms. We'll discover what tools are available, what they're used for and how to use them.

---

# Flow

```
~~~graph-easy --as=boxart
[Overview] -> [Resources] -> [CLI tools] -> [APIs] -> [Wrap up] -> [More info]
[CLI tools] -> [Demo 1]
[APIs] -> [Demo 2]
~~~
```

## Content

* The entire content of this talk is available online in a GitHub repository: [qmacro/mastering-sap-2022][1]
* As well as these slides (which are in very readable Markdown) there are also detailed [notes][2]

[1]: https://github.com/qmacro/mastering-sap-2022/
[2]: https://github.com/qmacro/mastering-sap-2022/blob/main/notes.md

👉 `https://github.com/qmacro/mastering-sap-2022`
---

# Setting the scene

- A cloud platform since 2012, known today as SAP BTP
- Multiple functions and purposes
- The go-to platform for everything beyond core products
  - Integrations, extensions
  - Messaging, connectivity
  - Cloud native developments (apps & services)
  - ... and lots more
- Large array of offerings - "resources"
- Our first experience is via the SAP BTP Cockpit

👉 `https://cockpit.hanatrial.ondemand.com/trial/#/globalaccount`

## Takeaway

- The Cockpit is great for HUMANS 👱

---

# The next level

- Hyperscaler platforms, where resources co-exist
- Cattle not pets - see [Monday morning thoughts: a cloud native smell][1]
- Automation is key for the next stages in your cloud journey
- Facilities beyond the Cockpit for managing SAP BTP resources

👉 `https://cockpit.hanatrial.ondemand.com/trial/#/home/trial`

## Takeaway

- CLI tools and APIs are great for MACHINES 🤖

[1]: https://blogs.sap.com/2018/04/09/monday-morning-thoughts-a-cloud-native-smell/
---

# Resources

(Not an exhaustive list!)

## Structure

* Directories, subaccounts, namespaces, orgs, spaces

## Runtime

* Environments, apps, services, subscriptions

## Security

* Users, roles, role collections

## Connectivity
* Cloud connectors, destinations

---

# CLI tools

Platform and runtime specific tools, plus tools to manipulate and explore data

## Platform and runtime

- btp     [for managing SAP BTP resources][1]
- smctl   [to interact with the SAP Service Manager][2]
- kubectl [the main Kubernetes command line tool][3]
- cf      [the Cloud Foundry command line tool][4]

## Data manipulation and exploration

- jq      [the command line JSON processor][5]
- yq      [the equivalent of jq for YAML][6]
- ijq     [interactive jq][7]
- fx      [terminal JSON viewer][8]
- fzf     [a command line fuzzy finder][9]

[1]: https://help.sap.com/products/BTP/65de2977205c403bbc107264b8eccf4b/7c6df2db6332419ea7a862191525377c.html
[2]: https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/0107f3f8c1954a4e96802f556fc807e3.html
[3]: https://kubernetes.io/docs/reference/kubectl/
[4]: https://docs.cloudfoundry.org/cf-cli/
[5]: https://stedolan.github.io/jq/
[6]: https://github.com/mikefarah/yq
[7]: https://sr.ht/~gpanders/ijq/
[8]: https://github.com/antonmedv/fx
[9]: https://github.com/junegunn/fzf

---

# APIs

These are all available in the SAP API Business Hub package [Core Services for SAP BTP][1]

* Accounts Service
* Entitlements Service
* Events Service
* Provisioning Service
* Resource Consumption
* SaaS Provisioning Service

And also:

* Service Manager

[1]: https://api.sap.com/package/SAPCloudPlatformCoreServices/overview

---

# btp CLI

* Available for all platforms
* See also the [BTP Setup Automator][1]
* Super comfortable and convenient
* Usage: `btp [OPTIONS] ACTION [GROUP/OBJECT] [PARAMS]`

## Command structure

Like many CLI tools that support a wide array of resources, the btp CLI command structure takes a "verb/noun" approach, where _actions_ are performed on _objects_, which are arranged into _groups_.

### btp CLI actions

```bash
btp help all | grep -E '^\s+[a-z]+' | awk '{print $1}' | sort -u | paste -sd' ' | fold -s -w $(tput cols)
```

[1]: https://github.com/SAP-samples/btp-setup-automator

---

# btp CLI groups and objects

Actions _login_, _logout_, _help_ and _target_ are general.

The other actions (_create_, _delete_, _list_, _get_ etc) are performed on objects (this info from `btp help all`).

## Accounts group

* available-environment, available-region, directory, entitlement, environment-instance, global-account, label, resource-provider, subaccount, subscription

## Security group

* app, role, role-collection, user

## Services group

* binding, broker, instance, offering, plan, platform

---

# Exploring the btp CLI

## General overview

* `btp get accounts/global-account`
* `btp get accounts/global-account --show-hierarchy` (aliased as `btphier`)

## Selecting a specific subaccount

* `btp target --help` (GUID needed)
* `btp list accounts/subaccount`
* `btp --format json list accounts/subaccount`
* `btp --format json list accounts/subaccount | jq .`
* `btp --format json list accounts/subaccount | jq -C . | less -R`
* `btp --format json list accounts/subaccount | ijq`
* `btpsubselguid` using `jq` and `fzf`
* `btp target --subaccount $(btpsubselguid)`

---

# Exploring the btp CLI (cntd)

## Users and role collections

* `btp list security/user`
* `btp get security/user qmacro+blue@gmail.com`
* `btp --format json get security/user qmacro+blue@gmail.com`
* `btp list security/role-collection`
* `btp --format json list security/role-collection`
* `btp --format json list security/role-collection | fx`
* `btp --format json list security/role-collection | jq -r .[].name | fzf`

### Assigning and unassigning a role collection

`btp assign security/role-collection "$(btp --format json list security/role-collection | jq -r .[].name | fzf)" --to-user qmacro+blue@gmail.com`

`btp unassign security/role-collection "$(btp --format json get security/user qmacro+blue@gmail.com | jq -r .roleCollections[] | fzf)" --from-user qmacro+blue@gmail.com`

---

# Connection between layers

```
~~~graph-easy --as=boxart
[Global Account] -> [Subaccounts] -> [Environment Instances]
~~~
```

```bash
btp --format json list accounts/environment-instance # (from getcfapiendpoint)
```

```json
{
  "environmentInstances": [
    {
      "labels": "{\"Org Name:\":\"8fe7efd4trial\",\"API Endpoint:\":\"https://api.cf.eu10.hana.ondemand.com\",\"Org ID:\":\"d7706871-d9db-450d-9b99-dfbe23358dc4\"}",
      "environmentType": "cloudfoundry",
      ...
    }
  ]
}
```

---

# API Packages on SAP API Business Hub

```
~~~graph-easy --as=boxart
[SAP API Business Hub] -> [API Package] -> [APIs]
[API Package] -> [Overview]
[API Package] -> [Documents]
~~~
```

---

# API Package : Core Services for SAP BTP

```
~~~graph-easy --as=boxart
[API Package] -> [Accounts Service API]
[API Package] -> [Entitlements Service API]
[API Package] -> [Events Service API]
[API Package] -> [...]
~~~
```

---

# API Package : Core Services for SAP BTP : API : Accounts Service

```
~~~graph-easy --as=boxart
[Accounts Service API] -> [Overview]
[Accounts Service API] -> [API Reference]
[Accounts Service API] -> [Model View]
[Accounts Service API] -> [SAP Cloud SDK]
[Accounts Service API] -> [Try Out]
~~~
```

Grouped endpoints are described in the [API Reference][1]

👉 `https://api.sap.com/package/SAPCloudPlatformCoreServices/rest`

[1]: https://api.sap.com/api/APIAccountsService/resource

---

# Calling an API protected with OAuth

Service instance binding contains OAuth details. Use those to request an access token, to authenticate the call.

```
┌──────────┐     ┌──────────┐    ┌──────────┐
│ Service  ├──┬─►│ Instance ├───►│ Binding  │
└──────────┘  │  └──────────┘    └────┬─────┘
              │                       │
┌──────────┐  │                       │
│ Plan     ├──┘                       │
└──────────┘                          │
     ┌────────────────────────────────┘
     ▼
┌──────────┐     ┌──────────┐
│  Token   ├────►│ API call │
└──────────┘     └──────────┘
```

---

# Example API call to /accounts/v1/subaccounts

## Requirements

* Use **SAP Cloud Management Service** with **central** plan - see [documentation][3]

## Tools

* `cf create-service cis central cis-central`
* `cf create-service-key cis-central cis-central-key`
* `cf service-key cis-central cis-central-key | sed 1,2d > binding.json` # ugh
* `requesttoken > token.json` # use appropriate grant type
* `callaccountsserviceapiendpoint /accounts/v1/subaccounts`

## Results

```json
{"value":[{"guid":"cd76fdef-16f8-47a3-954b-cab6678cc24d","technicalName":"cd76fdef-16f8-47a3-954b-cab6678cc24d","displayName":"testsubaccount","globalAccountGUID":"fdce9323-d6e6-42e6-8df0-5e501c90a2be","parentGUID":"fdce9323-d6e6-42e6-8df0-5e501c90a2be",...}]}
```

[1]: https://api.sap.com/api/APIAccountsService/resource
[2]: https://help.sap.com/products/BTP/65de2977205c403bbc107264b8eccf4b/3670474a58c24ac2b082e76cbbd9dc19.html
[3]: https://help.sap.com/products/BTP/65de2977205c403bbc107264b8eccf4b/a508b724bf6d457ca7ac024b8e4b8457.html

---

# SAP Service Manager - overview

Facilitates brokering services across runtimes, based on the open source [Peripli][1]

```
┌────────────────┐                                  ┌──────────┐
│ Service Broker ├───┐                          ┌───┤ Platform │
└────────────────┘   │                          │   └──────────┘
                     │                          │
┌────────────────┐   │   ┌─────────────────┐    │   ┌──────────┐
│ Service Broker ├───┼───┤ Service Manager ├────┼───┤ Platform │
└────────────────┘   │   └─────────────────┘    │   └──────────┘
                     │                          │
┌────────────────┐   │                          │   ┌──────────┐
│ Service Broker ├───┘                          └───┤ Platform │
└────────────────┘                                  └──────────┘
```

Think `cf`'s services facility, but cross-environment, allowing consumption of services from anywhere, anywhere (Cloud Foundry, Kyma / Kubernetes, etc).

[1]: https://peripli.github.io/

---

# SAP Service Manager - interaction

This is itself a service for viewing & managing service offerings, plans, etc.

## Via the smctl CLI

* Install available from the open source project and [referenced in SAP Help Portal][1]
* Commands such as `list-platforms`, `register-broker`, `provision`, `update-instance`

## Via the btp CLI

* Service management group objects: binding, broker, instance, offering, plan, platform

## Via the API

* Described in SAP API Business Hub - [Service Manager][2]
* Documented in SAP Help Portal - [Working with SAP Service Manager APIs][3]
* Protected via OAuth, requires SAP Service Manager instance with [appropriate plan][4]


[1]: https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/93532bd72117429bb823a46ef2ddd003.html
[2]: https://api.sap.com/api/APIServiceManagment/overview
[3]: https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/4e19b11211fe4ca2a266d3fdd4a72188.html
[4]: https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/917a8a7c926444cf99d0230c82db1831.html
---

# Wrapping up

* The SAP BTP Cockpit provides a great human-centric interface for viewing & managing resources
* For the next step in cloud, automation, unattended operations, analysis and more, we need more direct access
* This is provided by tools such as `btp` and augmented by general command line tools like `jq`
* The API surface area of SAP BTP is wide and rich, offering programmatic access

---

# Further reading

Blog posts

* [SAP Tech Bytes: btp CLI – installation](https://blogs.sap.com/2021/09/01/sap-tech-bytes-btp-cli-installation/)
* [SAP Tech Bytes: btp CLI - logging in](https://blogs.sap.com/2021/09/07/sap-tech-bytes-btp-cli-logging-in/)
* [SAP Tech Bytes: btp CLI - managing configuration](https://blogs.sap.com/2021/09/14/sap-tech-bytes-btp-cli-managing-configuration/)
* [SAP Tech Bytes: btp CLI - autocompletion](https://blogs.sap.com/2021/09/21/sap-tech-bytes-btp-cli-autocompletion/)
* [SAP Tech Bytes: btp CLI - new home for configuration](https://blogs.sap.com/2022/02/17/sap-tech-bytes-btp-cli-new-home-for-configuration/)
* [Getting BTP resource GUIDs with the btp CLI - part 1](https://blogs.sap.com/2021/11/24/getting-btp-resource-guids-with-the-btp-cli-part-1/)
* [Getting BTP resource GUIDs with the btp CLI - part 2 - JSON and jq](https://blogs.sap.com/2021/12/01/getting-btp-resource-guids-with-the-btp-cli-part-2-json-and-jq/)
* [Exploring service brokers and service consumption on SAP Business Technology Platform](https://blogs.sap.com/2022/03/08/exploring-service-brokers-and-cf-on-sap-business-technology-platform/)
* [Embracing JSON and jq](https://qmacro.org/2021/10/29/embracing-jq-and-json/)

---

# Further viewing

Videos from the `Hands-on SAP Dev` [show][1]

* [Getting started with btp, the SAP Business Technology Platform CLI](https://www.youtube.com/watch?v=mhk6Kot-Ays&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=9)
* [Logging in and configuration with btp, the SAP Business Technology Platform CLI](https://www.youtube.com/watch?v=jN_4J8tmmTo&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=8)
* [Scripting and JSON output with btp, the SAP Business Technology Platform CLI](https://www.youtube.com/watch?v=xRmHZGk4QCU&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=7)
* [Let's write a script together with bash, jq, and the btp CLI](https://www.youtube.com/watch?v=mPngw5yZyf8&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=6)
* [Booting our 2022 live stream series with a review of Developer Keynote btp CLI scripting](https://www.youtube.com/watch?v=1jekfZJ3fTk&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=5)
* [Developer Keynote btp CLI scripting part 2 - obtaining and parsing JSON output with jq](https://www.youtube.com/watch?v=_tQWo2bzFzE&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=4)
* [Fun with SAP BTP Cloud Management Service and Core Services APIs](https://www.youtube.com/watch?v=KiPJJHmEbgc&list=PL6RpkC85SLQABOpzhd7WI-hMpy99PxUo0&index=3)

[1]: https://blogs.sap.com/2020/11/09/an-overview-of-sap-developers-video-content/#shows
