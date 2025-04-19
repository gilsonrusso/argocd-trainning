# Helm - App Masterizando o tailwind

## Commands

- Create a helm

```bash
helm create
```

- Testing Helm values - Visualize output

```bash
helm install masterizando . --debug --dry-run > debug.yaml
```

- Create | Upgrade | Delete | history | rollback helm

```bash
# Create
helm install masterizando .
# Upgrade and set a new values or a file
helm upgrade masterizando . --values=setup.values.yaml
# Delete
helm uninstall masterizando . -n default
# History
helm history masterizando
# Rollback
helm rollback masterizando 1
```

### Port foward

```
kubectl port-forward pods/frontend-778979c958-cvfws  9000:5000 -n default
```
