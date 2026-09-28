# PLAT-ADR-0001: Foundational Platform Architecture

## Status

Accepted

## Context

This repository is the foundation of a greenfield SecOps platform. The platform consists of infrastructure, services, automation, detections, and supporting tooling that are expected to evolve together as a cohesive system.

The platform needs consistent assumptions about how components are organized, built, deployed, connected, authenticated, and reproduced. These assumptions are intentionally coupled: changing one of them may require substantial changes across the repository and deployed environment.

In particular, the platform should minimize dependence on repository boundaries, codeforge-specific automation, network location, service-specific authentication, heavyweight runtime environments, and unconstrained dependency resolution.

Individual services and infrastructure components may make more specific decisions, but those decisions must operate within the platform's foundational model.

## Decision

The SecOps platform will use a monorepo-based, identity-oriented, automation-driven architecture with the following foundational properties:

 - **Monorepo**: All platform infrastructure, services, and supporting components will reside within a single repository. Components remain independently buildable and deployable where practical.

 - **Make as the canonical automation interface**: Make provides the common interface for build, test, validation, packaging, deployment, and other automation. The repository root Makefile dispatches to namespaced project targets, while projects expose local targets when operating from within their own directories. For example:

```bash
make bootstrap-talos-build
```

from the repository root corresponds to:


```bash
make build
```

within bootstrap/talos.

 - **Codeforge CI as a thin wrapper**: Codeforge-specific CI/CD configuration will primarily invoke repository-provided Make targets. CI/CD logic should remain executable outside of the codeforge wherever practical.

 - **Identity-based networking**: Service connectivity will use an identity-based overlay network rather than treating traditional network segmentation and network location as the primary security boundary. Network identity does not replace application-level authentication or authorization.

 - **SSO for user-facing services**: Services that provide user-facing interfaces must support OAuth or OIDC-based SSO. Backchannel logout is preferred where supported, but is not mandatory due to incomplete support across open-source applications.

 - **Minimal runtime environments**: Services should be readily deployable using distroless containers. Where practical, services should produce self-contained runtime artifacts, such as Go applications compiling to a single executable.

 - **Locked dependencies**: Every buildable project must maintain a dependency lock mechanism appropriate to its ecosystem. Builds must resolve dependencies from the locked dependency graph. Byte-for-byte reproducibility is not a platform requirement.

These properties collectively define the baseline engineering model for the platform. Deviations require explicit architectural consideration rather than being treated as ordinary implementation choices.

Detailed implementations, component-specific architecture, and contributor practices will be documented separately in the repository's design and guideline documentation.

## Consequences

The platform gains a consistent set of assumptions across infrastructure and services, allowing tooling, deployment, security controls, and development workflows to be designed around a common model.

Cross-component changes can be made atomically within the monorepo, and the same automation interfaces can be used by developers and CI/CD systems.

Identity becomes a primary abstraction for both network access and user authentication, reducing dependence on network location and individually managed service credentials.

Minimal container runtimes and locked dependencies reduce unnecessary runtime and dependency variability, while avoiding the stronger requirements and complexity of mandatory byte-for-byte reproducible builds.

The primary consequence is architectural coupling. These decisions intentionally establish constraints across the entire platform. Changing a foundational property may require refactoring repository structure, automation, networking, authentication, containerization, and deployment infrastructure together.

Services that cannot naturally conform to these constraints may require additional integration or an explicit architectural exception.
