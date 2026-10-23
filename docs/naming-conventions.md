# Naming conventions

How repositories, applications and BTP service instances are named in the `joris-ide`
organisation. **MUST** and **SHOULD** have their usual meaning: a MUST is not optional, a
SHOULD needs a reason to deviate.

Everything starts from one name: the **service name**, or **slug**.

## 1. Repositories

### 1.1 The service name (slug)

A repository that is an **app or a microservice MUST** have a service name, the *slug*:

- lower-case **kebab-case**: the letters `a-z`, the digits `0-9` and single hyphens;
- it **starts with a letter**, never with a digit;
- it is CLI-friendly: no spaces, underscores, dots or capitals.

```text
^[a-z][a-z0-9]*(-[a-z0-9]+)*$
```

| Slug | |
| --- | --- |
| `my-example-service` | ✅ |
| `sales-order-checks` | ✅ |
| `2nd-gateway` | ❌ starts with a digit |
| `Sales_Order_Checks` | ❌ capitals and underscores |
| `sales--order` | ❌ double hyphen |

The JI service scaffold also limits the slug to **30 characters**, so that the names built
from it (section 2) stay short.

The slug is the name of the service everywhere: in the repository name, in the Cloud
Foundry application and in every service instance (section 2).

### 1.2 The repository name

A repository name has this structure:

```text
[CAP_]<LANGUAGE>_<slug>
```

| Part | Rule |
| --- | --- |
| `CAP_` | Added when the repository is an **SAP CAP** service. Left out otherwise. |
| `<LANGUAGE>` | The **dominant programming language** of the repository, upper case (table below). For now it is chosen at the author's discretion. |
| `<slug>` | The service name from 1.1, unchanged. |

The parts are separated by an underscore. Because the slug never contains an underscore,
the slug is always everything after the last `_`.

| `<LANGUAGE>` | Used for |
| --- | --- |
| `TS` | TypeScript. Typical for CAP services. |
| `JS` | JavaScript. Also common for CAP services. |
| `PY` | A Python-based microservice. |
| `CDS` | A repository in which CDS files are dominant. |
| `MTA` | A repository with only an MTA at its centre, which creates BTP resources such as HDI containers. |
| `JA` | Java (legacy). |

**Frontends.** When an application also packages a frontend app, the **backend's**
dominant language takes precedence. The frontend technology (React, UI5, …) is not part
of the name.

### 1.3 Examples

| Repository | What it is |
| --- | --- |
| `CAP_TS_sales-order-checks` | SAP CAP service in TypeScript, slug `sales-order-checks` |
| `CAP_JS_my-example-service` | SAP CAP service in JavaScript, slug `my-example-service` |
| `CAP_TS_supplier-portal` | SAP CAP service in TypeScript that also ships a UI5 app — the UI5 part is not in the name |
| `PY_invoice-ocr` | Python microservice, not CAP, slug `invoice-ocr` |
| `MTA_shared-hdi` | MTA-only repository that creates an HDI container, slug `shared-hdi` |

## 2. Applications and service instances

All BTP names of a service are **derived from its slug**. Each one is the slug, or the
slug plus a suffix that says what it is. The JI service scaffold generates them this way.

| Thing | Name | Example (`sales-order-checks`) |
| --- | --- | --- |
| MTA `ID` (`mta.yaml`) | `<slug>` | `sales-order-checks` |
| `xsappname` (`xs-security.json`) | `<slug>` | `sales-order-checks` |
| npm package name (`package.json`) | `<slug>` | `sales-order-checks` |
| Cloud Foundry app — the service | `<slug>-srv` | `sales-order-checks-srv` |
| Cloud Foundry app — the HDI deployer | `<slug>-db-deployer` | `sales-order-checks-db-deployer` |
| HDI container (`hana`, plan `hdi-shared`) | `<slug>-hdi` | `sales-order-checks-hdi` |
| XSUAA instance (`xsuaa`, plan `application`) | `<slug>-xsuaa` | `sales-order-checks-xsuaa` |
| Secrets — user-provided instance | `<slug>-secrets` | `sales-order-checks-secrets` |

- In `mta.yaml`, a resource's MTA name is the same as the name of its service instance.
- The names are the **same in every BTP space** (DEV, QAS, PRD). The space tells the
  environments apart, not the name.
- The `<slug>-secrets` instance is not created by a deploy. It is created once per space,
  before the first deploy, and its credentials are filled in the BTP cockpit. Its name
  is a MUST of the JI programming standards (JI-SEC-01).

## 3. Putting it together

```text
Service:     Sales Order Checks          (human-readable name)
Slug:        sales-order-checks
Repository:  CAP_TS_sales-order-checks
MTA ID:      sales-order-checks
CF apps:     sales-order-checks-srv, sales-order-checks-db-deployer
Instances:   sales-order-checks-hdi, sales-order-checks-xsuaa, sales-order-checks-secrets
```
