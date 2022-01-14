{
  "title": "SaaS Fundamentals",
  "summary": "Notes on SaaS Basics",
  "date": "2022-01-13T12:54:48Z",
  "lastmod": "2022-01-13T12:54:48Z",
  "tags": [
    "saas"
  ],
  "categories": [
    "saas"
  ],
  "draft": false
}

## Attributes of a SaaS Environment

* All tenants deployed by a single automated process
* All tenants running the same version
* Customisations are global and available to any tenant
* Operations have a single pane of glass for all tenants
* Logging and metrics have tenant context
* Tenants can be deployed with different multi-tenancy
* DevOps supports frequent and regular updates
* Tiers used to distinguish different tenant experiences
* Operations, architecture, product management, and business all have access to rich metrics data

## SaaS Mindset

### SaaS is a business model

* Selling a service, not a product
* Service-centric approach

### Always prepare for growth

* How can SaaS grow the business?
* How can we innovate faster?
* How to drive greater customer loyalty?
* Visibility into customer activity

### Tenant Profiles

* Don't constrain tenant profiles based on current requirements
* How to meet needs of varying tenants?
* Can you align tenant experience with cost?
* Are there tenant profiles in the market which aren't addressed today?

### Move the business out of siloes

* Bring all parties together:
{{< resize-image src="business-siloes.png" alt="Business Siloes" >}}

## SaaS Architecture Fundamentals

* A mindset and approach to serve and manage customers in a unified experience
  * Trade-off of customisation for agility and efficiency
* Success measured on the ability to maximise agility, innovation, growth, and cost efficiency

### The only SaaS blueprint

{{< resize-image src="saas-components.png" alt="Saas Components" >}}

### Factors that may shape architecture

* Compliance
* Noisy neighbour
* Tiering strategy
* Global reach
* Tenant isolation

### Common architecture models

* Reality is a mix of models for different tiers
  * Same version in all tiers
  * Often silo for premium tier

#### Silo

{{< resize-image src="model-silo.png" alt="Silo Model" >}}

#### Bridge

{{< resize-image src="model-bridge.png" alt="Bridge Model" >}}

#### Pool

{{< resize-image src="model-pool.png" alt="Pool Model" >}}

### Key areas that influence SaaS architecture

* Variable tenant load
* Seamless tenant identity
* Variable data footprint
* Operational agility

### Core architectural concepts

* Data partitioning
* Tenant isolation
* SaaS identity
* Onboarding
* Tiering
* Metering and billing

## SaaS Identity

### SaaS Identity = User Identity + Tenant Context

* JSON Web Token (JWT) with embedded tenant context
  * OpenID Connect (OIDC) framework defines JWT
    * Allows for ```ID Token``` and ```Access Token```
  * Additional attribute for ```tenant_id```

### Amazon Cognito Dilemma

* Independent User Pool per Tenant
  * Separate policies
  * Better isolation
  * Mapping required
  * Scale issues
* Shared User Pool across Tenants
  * Better OAuth flow
  * Better scale
  * No custom policies
  * Isolation challenges

### User Mapping

{{< resize-image src="user_mapping.png" alt="User Mapping" >}}

### Hybrid Identity

* Just-in-time identity migration creates on-prem users in Cognito when they first login

{{< resize-image src="hybrid_identity.png" alt="Hybrid Identity" >}}

## SaaS Onboarding

{{< resize-image src="onboarding.png" alt="Onboarding" >}}
