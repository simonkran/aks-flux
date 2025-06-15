# Flux GitOps Repository

This repository contains the GitOps configuration for managing Kubernetes deployments across multiple environments and regions using Flux.

## Structure

```
├── clusters/                  # Environment-specific configurations
│   ├── eastasia/
│   │   ├── dev/              # Development environment
│   │   ├── stage/            # Staging environment
│   │   ├── uat/              # User Acceptance Testing
│   │   └── prod/             # Production environment
│   └── westeurope/
│       ├── dev/
│       ├── stage/
│       ├── uat/
│       └── prod/
├── apps/                     # Application manifests
└── infrastructure/           # Infrastructure components
```

## Environments

- **dev**: Development environment for testing features
- **stage**: Pre-production staging environment
- **uat**: User Acceptance Testing environment
- **prod**: Production environment

## Regions

- **eastasia**: East Asia region (Azure)
- **westeurope**: Western Europe region (Azure)

## Getting Started

1. Initialize Flux in your cluster pointing to the appropriate environment path
2. Flux will automatically sync infrastructure first, then applications
3. Add your applications to the `apps/` directory
4. Add infrastructure components to the `infrastructure/` directory

## Bootstrap Command Example

```bash
flux bootstrap git \
  --url=ssh://git@github.com/your-org/your-repo \
  --branch=main \
  --path=clusters/eastasia/dev
```
