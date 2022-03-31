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

[1]: https://api.sap.com/package/SAPCloudPlatformCoreServices/rest

---

# btp CLI group / object structure

```
```
