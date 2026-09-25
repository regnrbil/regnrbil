<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/branding/cardataone-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="./assets/branding/cardataone-light.png">
  <img alt="CarDataOne" src="./assets/branding/cardataone-light.png" width="760">
</picture>

# European Vehicle Data Infrastructure

**Vehicle data · Reporting · Analytics · APIs · AI · Digital Twin · Enterprise Services**

[cardataone.com](https://cardataone.com)

</div>

---

## Architecture at a glance

<table>
<tr>
<td align="center" width="46%">
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/branding/cardataone-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="./assets/branding/cardataone-light.png">
  <img alt="CarDataOne" src="./assets/branding/cardataone-light.png" width="390">
</picture>
<br><strong>CarDataOne</strong><br>
International vehicle-data platform
</td>
<td align="center" width="38%">
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/branding/regnrbil-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="./assets/branding/regnrbil-light.png">
  <img alt="Regnrbil Norway" src="./assets/branding/regnrbil-light.png" width="320">
</picture>
<br><strong>Norway</strong><br>
Country-isolated vehicle-data platform
</td>
<td align="center" width="16%">
<img src="https://avatars.githubusercontent.com/u/314800851?s=256&v=4" alt="Security governance" width="96">
<br><strong>Security</strong><br>
Governance & control plane
</td>
</tr>
</table>

CarDataOne is a modular vehicle-data platform built around isolated country environments, shared dashboard and analytics services, private data services, and centralized security governance.

The current platform structure covers **Norway, Sweden, Denmark and Finland**. Each country is treated as an independent production boundary with its own runtime configuration, data sources, credentials, storage and authorization scope.

---

## Verified customer and dashboard flow

The profile architecture follows the flow defined in the current country, Dashboard+ and infrastructure repositories:

~~~mermaid
flowchart LR
    USER[Customer / Partner / Enterprise user]
    ENTRY[CarDataOne public entry]
    COUNTRY{Country context}
    NO[NO · Regnrbil]
    SE[SE · Sweden]
    DK[DK · Denmark]
    FI[FI · Finland]
    GATE[Server-side login / entitlement gate]
    HANDOFF[Short-lived signed handoff<br/>country · tenant · plan · scope]
    DASH[Dashboard+ / Enterprise]
    BIND[Country-bound Cloudflare<br/>service binding]
    DATA[(Country-isolated APIs<br/>and approved data sources)]

    USER --> ENTRY --> COUNTRY
    COUNTRY --> NO
    COUNTRY --> SE
    COUNTRY --> DK
    COUNTRY --> FI

    NO --> GATE
    SE --> GATE
    DK --> GATE
    FI --> GATE

    GATE --> HANDOFF --> DASH --> BIND --> DATA
~~~

The browser does not determine country access by itself. Dashboard sessions are designed to be server-validated and bound to the correct country, tenant, plan and scope. The current Dashboard+ Worker configuration declares separate service bindings for Norway, Sweden, Denmark and Finland.

---

## Administration, analytics and platform services

The administrative and analytics planes are separate from the customer authorization path.

~~~mermaid
flowchart TB
    APPS[NO / SE / DK / FI<br/>Dashboard+ / Enterprise]
    TAG[CarDataOne Tag<br/>first-party analytics]
    AE[(Cloudflare Analytics Engine)]
    CC[CarDataOne Control Center]
    SUPERSET[Apache Superset<br/>BI & embedded analytics]
    OFBIZ[Apache OFBiz<br/>CRM / ERP integration]
    UNOMI[Apache Unomi<br/>profiles & segmentation]
    NIFI[Apache NiFi<br/>integration / ETL / provenance]

    SYNC[Scheduled Analytics Sync]
    MARKET[(PostgreSQL / PostGIS<br/>market_no · market_se · market_dk · market_fi)]
    AI[DB-GPT / Local AI<br/>controlled data analysis]
    VSS[VSS / Vehicle Data Broker]
    TWIN[3D / Digital Twin<br/>glTF vehicle assets]

    SEC[Security Governance<br/>Security Control Plane]

    APPS --> TAG --> AE
    AE --> CC
    AE --> SUPERSET

    SYNC --> MARKET
    MARKET --> SUPERSET
    MARKET --> AI

    CC --> SUPERSET
    CC --> OFBIZ
    CC --> UNOMI
    CC --> NIFI

    APPS --> VSS
    APPS --> TWIN

    SEC -. governance .-> APPS
    SEC -. governance .-> CC
    SEC -. governance .-> MARKET
~~~

---

## Core platform layers

| Layer | Role |
| --- | --- |
| **Country platforms** | Isolated vehicle-data and reporting environments for Norway, Sweden, Denmark and Finland |
| **Dashboard+ / Enterprise** | Shared dashboard application with explicit country-specific service bindings |
| **Control Center** | Central administration and orchestration surface |
| **Analytics** | First-party event collection, Cloudflare analytics and PostgreSQL reporting pipelines |
| **Business intelligence** | Apache Superset integration for BI and embedded analytical dashboards |
| **CRM / ERP** | Apache OFBiz integration layer |
| **Customer intelligence** | Apache Unomi and Unomi Tracker integration for profiles, events and segmentation |
| **Data integration** | Apache NiFi integration for ETL, synchronization and provenance |
| **AI / data analysis** | DB-GPT and local-model tooling for controlled analytical workflows |
| **Vehicle-data standards** | COVESA VSS and vehicle-data broker components |
| **3D / digital twin** | glTF-based vehicle assets and browser-oriented digital-twin capabilities |
| **Security** | Governance, authorization gates, audit controls and security control plane |

---

## Country isolation

CarDataOne uses a strict country-isolation model. Each country environment maintains its own:

- Worker/runtime configuration
- data sources and approved provider integrations
- customer and report data
- database and storage bindings
- API and authority credentials
- payment and entitlement configuration
- analytics identifiers
- authorization and audit boundary

Source code and generic UI components may be reused across markets. Production credentials, customer data, payment state, database bindings and authority integrations are not shared merely because the applications share a common design.

---

## Cloud and deployment model

~~~text
Private GitHub repository
        ↓
Cloudflare Workers Builds
        ↓
Build validation
        ↓
Wrangler deployment
        ↓
Country / platform Worker
        ↓
Public or protected route
~~~

The active application stack is based on **Cloudflare Workers, Wrangler, React Router, TypeScript and Vite**, with Cloudflare Analytics Engine, Durable Objects, PostgreSQL/PostGIS and Hyperdrive used where required.

GitHub is the source of truth for application code. Cloudflare is the active runtime and deployment platform for the web applications represented here.

---

## Data and analytics model

CarDataOne separates customer-facing operational traffic from analytics and reporting infrastructure.

~~~text
Country platforms / Dashboard+ / Enterprise
                 ↓
         First-party analytics
                 ↓
      Cloudflare Analytics Engine
                 ↓
       Control Center / Superset

Cloudflare analytics sources
                 ↓
      Scheduled analytics sync
                 ↓
 market_no / market_se / market_dk / market_fi
                 ↓
        PostgreSQL / PostGIS
                 ↓
       BI / analytical services
~~~

The four market-data paths remain country-separated.

---

## Security architecture

<div align="center">
<img src="https://avatars.githubusercontent.com/u/314800851?s=256&v=4" alt="CarDataOne security governance" width="120">
</div>

Security is a cross-cutting platform layer. The documented model includes centralized governance, explicit authorization gates, country-specific security boundaries, server-side authentication and authorization, least-privilege runtime bindings, audit controls, and separation of source code, runtime secrets and production data.

Privileged or paid operations are designed to fail closed when required authorization, entitlement or configuration is missing.

---

## Vehicle data, AI and digital twin

The technical foundation includes:

- structured vehicle-data contracts and country adapters
- COVESA Vehicle Signal Specification (VSS)
- vehicle-data broker components
- controlled DB-GPT / local-AI analytical workflows
- PostgreSQL/PostGIS analysis
- glTF-based 3D vehicle assets
- browser-based vehicle visualization
- digital-twin and simulation-oriented dashboard components

AI and automation operate inside the same authorization and country-isolation boundaries; they do not replace those controls.

---

## Repository model

CarDataOne / Regnrbil uses a multi-repository architecture. The public profile repository is intentionally limited to company presentation and architecture.

Production repositories remain private and are separated by function, including country applications, infrastructure policy, security governance, dashboard and enterprise applications, Control Center, analytics ingestion, BI, CRM/ERP integration, customer intelligence, data integration, AI/data analysis, vehicle-data standards, vehicle-data brokering and 3D/digital-twin assets.

This public profile does **not** expose credentials, secrets, private database identifiers, internal network topology, customer data or private deployment endpoints.

---

## Technology foundation

**Application & edge**  
Cloudflare Workers · Wrangler · React Router · TypeScript · Vite

**Data**  
PostgreSQL · PostGIS · Cloudflare Hyperdrive · Cloudflare Analytics Engine

**Analytics & enterprise integration**  
Apache Superset · Apache NiFi · Apache OFBiz · Apache Unomi

**AI**  
DB-GPT · Ollama · local-model workflows

**Vehicle data**  
COVESA VSS · vehicle-data broker components

**3D**  
glTF · browser-based vehicle visualization · digital-twin components

---

## Public presentation scope

This profile represents the **CarDataOne / Regnrbil vehicle-data infrastructure only**. Unrelated projects, non-vehicle platforms and separate business initiatives are intentionally excluded.

<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="./assets/branding/cardataone-dark.png">
  <source media="(prefers-color-scheme: light)" srcset="./assets/branding/cardataone-light.png">
  <img alt="CarDataOne" src="./assets/branding/cardataone-light.png" width="620">
</picture>

**European vehicle data infrastructure, analytics, APIs and automotive intelligence.**

[cardataone.com](https://cardataone.com)

<sub>Profile Architecture README · V1.2.0 · 2026-09-25</sub>

</div>
