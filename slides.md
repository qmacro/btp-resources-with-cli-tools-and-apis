---
theme: ./themes/theme.json
author: DJ Adams
date: DD MMM YYYY
paging: "%d / %d"
---

# Title

Level up your SAP BTP resource management skills with command line tools and APIs.

# Speaker

DJ Adams, Developer Advocate at SAP

# Topics

* Migrating to the Cloud
* Skills, Methodologies and Ways of Working

# What we'll learn together

In this session we'll learn what resources there are to manage on the SAP Business Technology Platform (SAP BTP), and how to manage them with tools and APIs you can build into automated mechanisms. We'll discover what tools are available, what they're used for and how to use them.

---

# Setting the scene

- A cloud platform since 2012, known today as SAP BTP
- Multiple functions and purposes
- The go-to platform for everything beyond core products
  - Integrations
  - Extensions
  - Messaging
  - Connectivity
  - Cloud native developments
  - Services
  - ... and lots more
- Large array of offerings - "resources"
- Our first experience is via the SAP BTP Cockpit

## Takeaway

- The Cockpit is great for HUMANS 👱

---

# The next level

- Hyperscaler platforms, where resources co-exist
- Cattle not pets - see [Monday morning thoughts: a cloud native smell][1]
- Automation is key for the next stages in your cloud journey
- Facilities beyond the Cockpit for managing SAP BTP resources

## Takeaway

- CLI tools and APIs are great for MACHINES 🤖

[1]: https://blogs.sap.com/2018/04/09/monday-morning-thoughts-a-cloud-native-smell/
---

# What we're going to look at

- A general overview of resources
- Essential CLI tools
- APIs that cover SAP BTP core services

---

# Resources

|Category     |Examples                                             |
|-------------|-----------------------------------------------------|
|Structure    |Directories, subaccounts, namespaces, orgs, spaces   |
|Runtime      |Environments, apps, services, subscriptions          |
|Security     |Users, roles, role collections                       |
|Connectivity | Cloud connectors, destinations                      |

(Not an exhaustive list!)

---

# CLI tools

## Platform and runtime

- btp     [for managing SAP BTP resources][1]
- smctl   [to interact with the SAP Service Manager][2]
- kubectl [the main Kubernetes command line tool][3]
- cf      [the Cloud Foundry command line tool][4]

## Exploratory tools

- jq      [the command line JSON processor][5]
- ijq     [interactive jq][6]
- fx      [terminal JSON viewer][7]
- fzf     [a command line fuzzy finder][8]

[1]: https://help.sap.com/products/BTP/65de2977205c403bbc107264b8eccf4b/7c6df2db6332419ea7a862191525377c.html
[2]: https://help.sap.com/viewer/09cc82baadc542a688176dce601398de/Cloud/en-US/0107f3f8c1954a4e96802f556fc807e3.html
[3]: https://kubernetes.io/docs/reference/kubectl/
[4]: https://docs.cloudfoundry.org/cf-cli/
[5]: https://stedolan.github.io/jq/
[6]: https://sr.ht/~gpanders/ijq/
[7]: https://github.com/antonmedv/fx
[8]: https://github.com/junegunn/fzf

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
btp help all 2>/dev/null | grep -E '^\s+[a-z]+' | awk '{print $1}' \
  | sort -u | paste -sd' ' | fold -s -w $(tput cols)
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

* `btp target --help`
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
* `btp list security/role-collection`
* `btp --format json list security/role-collection`
* `btp --format json list security/role-collection | fx`
* `btp --format json list security/role-collection | jq -r .[].name`
* `btp --format json list security/role-collection | jq -r .[].name | fzf`

## Putting things together

* `btp assign security/role-collection "$(btp --format json list security/role-collection | jq -r .[].name | fzf)" --to-user qmacro+blue@gmail.com`

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

# API Package : Core Services for SAP BTP

```
~~~graph-easy --as=boxart
[SAP API Business Hub] -> [API Package] -> [APIs]
[API Package] -> [Overview]
[API Package] -> [Documents]
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
_Diagram powered by [ASCIIFlow][1]_

[1]: https://github.com/lewish/asciiflow

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
