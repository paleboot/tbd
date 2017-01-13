---
date: 2016-03-09T20:08:11+01:00
title: Feature Flags
weight: 20
---

Feature Flags are a time honored way to control the capabilitities os an application or service in a large decisive way. 

### An Example

Say you have 
a application or service that launches from the command line, and has a `main` method or function. Your feature flag 
could be `--withOneClickPurchase` to turn on code in the app to do with Amazon's patented one-click purchasing 
experience.  Without that command line argument, the application would run with a shopping cart component. At least
that's the way the developers coded that application. The 'One Click Purchase' and 'Shopping Cart' alternates are 
probably also the same language that the business people associated with the project use. It gets complicated in 
that flags need not be implicity on/off or new/old, they could be additive. In our case here, there could also be a
`--allowUsersToUserShoppingCartInsteadOfOneClick` capability. It can be additive, you see.

{{< note title="Flags Are Toggles" >}}
Industry Luminary, Martin Fowler, calls these Feature Toggles, and wrote a foundational definition (see refs below). 
Feature Flags is in wider use by the industry, though, so we're going with that.
{{< /note >}}

## Granularity

If could be that the flag controls something large like a component. In our case above we could say that 
`OneClickPurchasing` and `ShoppingCart` are the names of a compoents.  It could be that the granularity of the flag
is much smaller - Say Americans want to see temperatures in degrees Fahrenheit and other nationalities would 
prefer degrees Centigrade/Celcuius. We could have a flag `--temp=F` and `--temp=C`. For fun, the developers also added
`--temp=K` (Kelvins).

## Implementation

For the `OneClickPurchasing` and `ShoppingCart` alternates, it culd be that an abstraction `PurchasingCompleting` 
abstraction was created. Then at the most primordial boot place that's code controlled, the `--withOneClickPurchase` flag
is acted upon:

Java, by hand:

```java
if args.contains("--withOneClickPurchase") {
  purchasingCompleting = new OneClickPurchasing();
}
```

Java Dependency Injection by config:

```java
bootContainer.addComponent(classFromName(config.get("purchasingCompleting")));

```

There are many more ways of passing flag intentions (or any config) to a runtime.  If you at all can, you want to 
 avoid if/else conditions in the code where a path choice would be made. Hence our emphasis on an abstraction.

## Continuous Integration

It is important to have CI guard your reasonable expected permutations of toggle. That means tests that happen on an
application or service after launching it, should be adaptable too and test what is meaninful for those toggle 
permutations. It also means that in terms of CI pipelines there's a fan-out **after** unit tests, for each meaningful
flag permutation.

## Runtime switchable

Sometimes Flags/Toggles set at app launch time, isn't enough. Say you're an Airline selling tickets for flights online.
You might also rental cars in conjunction with a partner - say 'Really Cool Rental Cars' (RCRC). The connection to 
any partner or their up/down status is outside your control, so you might want a switch in the software that works 
without relaunch, to turn RCRC on or off, and allow the 24/7 support team to flip it if certain 'Runbook' conditions
have been met.  In this case, the end users may not notice if Hertz, Avis, Enterprise, etc are all still amongst
the offerings for that airport at the flight arrival time.

Key for Runtime switchable flags is the need for the state to persist. A restart of the application or service should
not set that flag choice back to default - it should retain the previous choice. It gets complicated when you think
about the need for the toggle to permeate multiple nodes in a cluster of horizontally scaled sibling processes. For
that last, then holding the flag state in Consul![](/images/ext.png)](https://www.consul.io/), 
Etcd![](/images/ext.png)](https://github.com/coreos/etcd) (or equiv) is a the modern way.

## Build Flags

Build flags affect the application or service as it is being built. With respect to the OneClickPurchase flag again,
the application would be incapable at runtime of having that capability if the build were not invoked with the suitable
flag somehow.

# References Elsewhere

<a id="showHideRefs" href="javascript:toggleRefs();">show references</a>

Date    | Type  | Article
--------|-------|--------
29 Oct 2010 | MartinFowler.com artcile | [Feature Toggle](https://martinfowler.com/bliki/FeatureToggle.html)
10 Oct 2014 | Conference Talk | [Trunk Based Development in the Enterprise - Its Relevance and Economics](https://www.perforce.com/merge/2014-sessions/trunk-based-development-enterprise-its-relevance-economics)
08 Feb 2016 | MartinFowler.com article | [Feature Toggles](https://martinfowler.com/articles/feature-toggles.html)

 