# ams.project Stacks for ReadyStackGo — Stable Channel

Stack definitions for [ams.project](https://www.ams-erp.com/) by [ams.Solution AG](https://www.ams-erp.com/) — an enterprise project management system built on microservices.

> **Stable channel.** This repository holds the **released** manifests of ams.project
> (`X.Y.Z`, images tagged `linux-vX.Y.Z`) for production use. Every version here was
> first delivered and tested as a preview in the
> [preview channel](https://github.com/Wiesenwischer/rsgo-ams-project-preview)
> and then approved for release.

## Usage

Add this repository to [ReadyStackGo](https://github.com/Wiesenwischer/ReadyStackGo) as a stack source:
**Settings → Stack Sources → Add → Git Repository**, using this repository's URL

```
https://github.com/Wiesenwischer/rsgo-ams-project-stable.git
```

(branch `main`). ReadyStackGo lists the products below in the **Stack Catalog**.

## Products

| Product | Description | Version | Directory |
|---------|-------------|---------|-----------|
| ams.project | Enterprise Project Management System | 4.0.0 | [ams-project/4.0.0](ams-project/4.0.0/) |
| ams.identityaccess | Standalone Identity Provider | 3.4.1 | [ams-identityaccess/3.4.1](ams-identityaccess/3.4.1/) |

ams.identityaccess is versioned separately from ams.project. ams.project 4.0.0 needs
ams.identityaccess **3.4.0 or later**.

### ams.project

Multi-stack product with Infrastructure, Platform and Business Services organised by bounded context:

- **Infrastructure** — EventStore, Redis
- **Platform** — Web BFF, Desktop Gateways, Admin Portal, Customer Documentation Portal, Client Installer
- **Business Services** — ProjectManagement, Discussions, Memo, SalesOrders, Export, ERP Integration, Capacity Planning, Production Planning, Reporting, Project Exporting, Project Import, Public API, Analytics, TaskContext

### ams.identityaccess

Standalone identity provider (IdentityServer / AdminPortal / ClientHub / EmailSender) that can be deployed independently of ams.project.

## Layout

Each released version has its own directory (`ams-project/<version>/`, `ams-identityaccess/<version>/`)
with a `stack.yaml` in the [RSGO Manifest Format](https://github.com/Wiesenwischer/ReadyStackGo).
ReadyStackGo identifies a product by `productId` and `productVersion`, so several versions
can be offered side by side. A published version directory is never changed afterwards;
a correction is released as a new version.
