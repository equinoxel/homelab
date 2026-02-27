# MySQL Connection Component

This Kustomize component provides a standardized way for applications to connect to the shared MySQL database cluster.

## Features

- Injects standard database connection environment variables.
- Supports `Deployment`, `StatefulSet`, and `HelmRelease` (standard `bjw-s` app-template structure).
- Configurable via a local `mysql-secret`.

## Environment Variables

The following variables are injected into the application containers:

| Variable | Source / Value | Description |
|----------|----------------|-------------|
| `DB_HOST` | `mysql-innodbcluster-router.database-system.svc.cluster.local` | The internal DNS name for the MySQL router. |
| `DB_PORT` | `3306` | The default MySQL port. |
| `DB_USER` | Secret `mysql-secret`, key `DB_USER` | Database username. |
| `DB_PASSWORD` | Secret `mysql-secret`, key `DB_PASSWORD` | Database password. |
| `DB_NAME` | Secret `mysql-secret`, key `DB_NAME` | Name of the database. |

## Usage

### 1. Add the component to `kustomization.yaml`

In your application's `kustomization.yaml`, add the component:

```yaml
components:
  - ../../components/connect/mysql
```

### 2. Create the required secret

Ensure a secret named `mysql-secret` exists in the application's namespace with the following keys:

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
stringData:
  DB_USER: "your-user"
  DB_PASSWORD: "your-password"
  DB_NAME: "your-database"
```

### 3. Overriding defaults

If you need to use a different secret name or override specific variables, you can define them directly in your resource's `env` section. Kustomize will prioritize or merge these depending on the patch type.

For `HelmRelease` using the `bjw-s` template, ensure your controller is named `main` and container is named `main` for the patch to apply automatically.

## Resource Compatibility

- **Deployments**: Targets containers named `app`.
- **StatefulSets**: Targets containers named `app`.
- **HelmReleases**: Targets `spec.values.controllers.main.containers.main`.
