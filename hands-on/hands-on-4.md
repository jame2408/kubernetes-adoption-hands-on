# Install Helm

```bash
$ https://raw.githubusercontent.com/helm/helm/master/scripts/get-helm-3 | bash
```

# Create Helm Chart Skeleton

```bash
$ helm create <name>
```

# Install Chart

```bash
$ helm install --name-template <name> --namespace <ns> <chart-directory>
```
