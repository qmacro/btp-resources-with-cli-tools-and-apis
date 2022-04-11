# Slide notes

These are some notes I made for myself while preparing the slides. You may find them useful so I'm including them in this repository too.

## Flow

Resources - what "things" the BTP offers - services, runtime environments, and more

## Setting the scene

The platform has been around for a decade, plenty of time for the number of features, services and offerings to grow. That's also more than enough time for the industry as a whole to move a little closer to maturity (will that ever happen?) and for the landscapes, technologies and approaches to change with that progression. So these days we're looking at multiple runtimes, for example. This means that we have a large array of offerings which I'm going to refer to as "resources" to keep things simple.

## The next level

Cattle not pets - many resources are either ephemeral, some short lived, some long lived. Many co-exist with other resources in a multi-tenant architecture, meaning that you don't get to pick the names, and are often generated on-demand, meaning that generated names are likely to be GUIDs - unique and with less meaning for (and compatibility with) human brains.

The two facilities beyond the Cockpit have been sitting there ready for you, just below the big blue button that you probably normally press when you're on the Cockpit landing page.

## Resources

You'll want to interact with all these resources to maintaining and managing them, and you'll certainly also want to report on them too, as well as have an overview of what you've got and how things relate.

## CLI tools

I've listed `cf` and `kubectl` here of course, as the key CLI tools for the Cloud Foundry and Kyma environments. Given the short time we have for this talk, we won't get to them in detail (altho I do have a quick invocation of `cf` to show you, in relation to resources higher up). But it's important to record them in the notes for this talk.

The data manipulation tools are not SAP specific, nor are they even runtime specific, but my goodness me they're useful in the context of managing resources in an automated fashion, so it's important I cover them, however briefly, in this talk.

`fx` is named for "function execution".

`fzf` is included because as well as automating the reporting, management and manipulation of resources, there are circumstances where you want to interact in that context too, and `fzf` provides a great way to allow selection of one or more items from lists of things.

## btp CLI

Two other examples of CLI tools that support "a wide array of resources" are Azure's `az` and GitHub's `gh`.

I've included this gratuitous UNIX pipeline just because. Some notes on it:

* `btp help all` gives the entirety of the help for the btp CLI, which includes a list of objects and actions, where the actions are indented a little
* the regular expression used in the `grep` invocation (used with the `-E` option to turn on support for extended regular expression syntax) searches for at one or more alphabetic characters, preceded by one or more spaces
* the simple `awk` invocation effectively trims any whitespace around the matched strings
* the `-u` option to sort will also weed out any duplicates
* with `paste` we can join lines together into a single line, with each line value separated with a space (`-d' '`)
* finally, `fold` is used to wrap the output nicely, to the current width of the terminal

There are plenty of improvements to make, and more ways to achieve the same thing.

## btp CLI groups and objects

It should be fairly easy to see that the the objects that the btp CLI operates upon, and the groups they're contained in, generally reflect the organisation of BTP resources we saw on an earlier slide.

## Exploring the btp CLI

Try each of the commands in turn, get a feel for them, what they output, how they work. Make sure you're logged in and have also enabled the autocomplete feature (see the blog post on this referenced in the "Further reading" slide at the end).

### General overview

The `btp get accounts/global-account` output isn't that exciting or useful to me, but I do find the option `--show-hierarchy` very helpful, so much so that [I created an alias for it](https://github.com/qmacro/dotfiles/commit/5ae49d9ff230e8193b928255a7715da137c65324#diff-8dbd2c3fb402ff682d15be45984f4b84a0468000316ff37bcb7febb5ec321382R57) (`btphier`).

The output from `btp get accounts/global-account --show-hierarchy` is very useful; parsing it programatically can be done, but there's a better way - see the two blog posts (part 1 and 2) "Getting BTP resource GUIDs with the btp CLI" referenced in the "Further reading" slide at the end.

### Selecting a specific subaccount

The `btp target` command is a convenience, allowing you to have a pre-determined subaccount specified and used where commands require it. The slight challenge is that to set a target you need to specify the subaccount's GUID, not its name.

While `btp list accounts/subaccount` will give you the GUID-to-name relations, it's not output that we want to parse (too brittle). This is where one of the btp CLI's superpowers comes in - JSON output. This is far more predictable and designed to be stable.

JSON is not the easiest to read, even when displayed in uncompressed form (i.e. with some whitespace included for indentation). So tools like `jq`, `less` and `ijq` are very useful here.

In particular, `ijq` lets us to explore the `jq` language, with which we can use expressions such as this for finding the GUID of a given subaccount name (for example):

```jq
.value[] | select(.displayName == "trial") | .guid
```

We can see another example of some `jq` expressions that are used to extract subaccount details and present them in tab-separated format, in the `btpsubselguid` script. This output is then fed to `fzf` which presents the values for a selection, and then finally `cut` is used to select the third field in the output from that.

## Exploring the btp CLI (cntd)

As we can see, `fx` is also a nice tool to explore JSON output. And with it we can see that the structure of the JSON for the "security/role-collection" resource is an array of objects, one for each role-collection. So we can use a bit of `jq` to extract the role collection names from that structure: `.[].name` (the `-r` option tells `jq` not to try to output JSON, but just raw data). The flat list then can be nicely fed into `fzf`.

The assignment and unassignment of role-collections to and from users can also be done with the btp CLI. Using `btp assign` and hitting Tab for autocompletion, we can see that the "assign" action works with role-collections in the "security" group, and entitlements in the "accounts" group. Similarly, we can see via autocompletion that the "unassign" action works exclusively with role-collections in the "security" group. For each of the actions there's a corresponding option `--to-user` or `--from-user`, depending on the direction.

## API Packages on SAP API Business Hub

It's important to understand how to find one's way around APIs on the hub, so this slide and the next one just explain the '10,000 feet view', zooming down into the "Core Services for SAP BTP" API package.

## API Package : Core Services for SAP BTP : API : Accounts Service

The title of this slide reflects the breadcrumbs that lead us to a specific API, the "Accounts Service" API. Note that an API can and usually does have multiple endpoints (URL paths) here, so don't confuse a specific URL with an API.

The URL indicated with the 👉 pointer will take us to the "Core Services for SAP BTP" level in the SAP API Business Hub, from where we can descend a level into the details for the specific "Accounts Service" API.

## Calling an API protected with OAuth

Most APIs are protected with OAuth, specifically OAuth 2.0. There's a general flow which is as follows:

1. Create an instance of a service, with a service plan that is appropriate for the requirement
1. From this instance, a service key, aka "binding", can be created
1. Using information in the binding, an access token can be requested
1. The access token obtained can be used to authenticate the API call

Which service is required depends on the API you want to call, and is usually described in the corresponding documentation. For example, there is an SAP Help Portal document linked from the Accounts Service API overview page. The title of this document is [Account Administration Using APIs of the SAP Cloud Management Service [Feature Set B]](https://help.sap.com/products/BTP/65de2977205c403bbc107264b8eccf4b/17b6a171552544a6804f12ea83112a3f.html) and the first sentence tells us what service is required:

> Provides information about using the APIs of the SAP Cloud Management service for SAP BTP (technical name: cis) to manage some of the administrative operations in your accounts.

Note also that different service plans will provide different levels of access (scope), so the right one needs to be used in the first step. This information is also usually found in the same documentation.
