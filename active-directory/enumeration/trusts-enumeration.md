---
description: AD Trusts Enumeration
---

# Trusts enumeration

A trust, represented as TDOs (Trusted Domain Objects), is a relationship between two domains/forests which allows users of one domain/forest to access resources in the other domain/forest.

Can be of two types:

* **Automatic**: Parent-child.
* **Established**: Forest-Forest (external)

Trust Directions:

* **One way (unidirectional)**: Users in the trusted domain can access resources in the trusting domain, but not the other way.
* **Two way (bi-directional)**: Users of both domains can access resources in the other domain.

Trust transitivity:

* **Transitive**: Can be extended to establish trust relationships with other domains (A<->B and B<->C, then A<->C). For trusts between two domains in different forests it is non-transitive by default.

Default trusts:

* **Parent-Child (unidirectional)**.
* **Tree-root trust (unidirectional)**: All domains in a tree have trusts. Note: confirm this!!!!

Shortcuts:

* Used to reduce access times in complex trust scenarios.
* Can be unidirectional or bi-directional.

External Trusts:

* Between two domains in different forests when forests doesn't have trust relationships.
* Can be unidirectional or bi-directional and transitive or non-transitive.

Forests trust:

* Between forests root domains.
* Can be unidirectional or bi-directional and transitive or non-transitive.
* Need to establish manually.

## Mapping

### Get a list of all domain trusts for the current domain

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetDomainTrust #List all the domain trusts
Get-NetDomainTrust -Domain $DOMAIN_NAME #List all the domain trusts for a trusted domain
```
{% endcode %}

### Get details about the current forest

```bash
Get-NetForest #Details about current forest
Get-NetForest -Forest $FOREST_NAME #Details about other forests which we have trusts
```

### Get all domains in the current forest

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetForestDomain #List all domains in the current forest
Get-NetForestDomain -Forest $FOREST_NAME #List all domains in the forest we have trust
```
{% endcode %}

### Get all global catalogs (DCs) for the current forest

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetForestCatalog #List all global catalogs on the current forest
Get-NetForestCatalog -Forest $FOREST_NAME #List all global catalogs on a forest we have trust
```
{% endcode %}

### Map trusts of a forest (not the trusts inside the forest)

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetForestTrust #List all trusts of the forest to another forest
Get-NetForestTrust -Forest $FOREST_NAME #List all trusts of the forests to another forest on a forest we have trust
```
{% endcode %}

### Map all trust of the Forest (including all domains)

```bash
#PowerView:
Get-NetForestDomain -Verbose | Get-NetDomainTrust
```
