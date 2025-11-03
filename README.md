# Ollama on Kubernetes

Kubernetes and Argo CD manifests for running an Ollama backend together with the Open WebUI frontend. The manifests default to deploying the stack into an `ai` namespace and exposing the UI through an ingress.

## Repository layout

- `ai/` – Kustomize base that provisions the Ollama DaemonSet, Open WebUI deployment, service, ingress, and the `ai` namespace.
- `ingress-lb/` – Kustomize base for a cluster `LoadBalancer` service that fronts an existing NGINX ingress controller (handy on MicroK8s).
- `app-ai-stack.yaml` / `app-ingress-lb.yaml` – Argo CD `Application` resources that point at the `ai` and `ingress-lb` folders in this repository.

## Using Kustomize directly

```bash
kubectl apply -k ai
kubectl apply -k ingress-lb
```

Update hostnames or other defaults in the manifests before applying if they differ from your environment (for example the `ai/home.lan` host in `ai/openwebui-ingress.yaml`).

## Deploying with Argo CD

1. Commit this repository to your own Git remote and update the `spec.source.repoURL` values in both `app-*.yaml` files.
2. Apply the Argo CD applications: `kubectl apply -f app-ai-stack.yaml` and `kubectl apply -f app-ingress-lb.yaml`.
3. Sync the applications in the Argo CD UI/CLI; automated sync and self-heal are already enabled.

## Model storage

The Ollama DaemonSet uses a hostPath volume at `/var/lib/ollama` to persist downloaded models on each node. Ensure that path exists (or change it) on every node where the DaemonSet should run.

## Next steps

- Adjust resource requests/limits in `ai/ollama-daemonset.yaml` to match your cluster capacity.
- Configure TLS or external DNS for the Open WebUI ingress if exposing it beyond a trusted network.
