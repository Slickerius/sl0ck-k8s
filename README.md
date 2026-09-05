# sl0ck's Kubernetes Infra

Flux reconciles `main` into the sl0ck k3s cluster every minute. Drift is
corrected automatically and resources removed from Git are pruned.

## Secrets

Secret values are encrypted with SOPS and age. The recovery identity is kept
outside this public repository at:

```text
~/.config/sops/age/keys.txt
```

Back up that file securely. Without it, encrypted secrets cannot be recovered
after a total cluster loss.

Edit an encrypted secret with:

```sh
SOPS_AGE_KEY_FILE="$HOME/.config/sops/age/keys.txt" sops path/to/secret.yaml
```

## Deployment ownership

Flux owns all declared fields except application image fields. App repositories
build and deploy immutable git-SHA images through their own CI/CD workflows;
Flux ignore rules preserve those live image pins.

To pause or resume reconciliation:

```sh
kubectl -n flux-system patch kustomization flux-system \
  --type=merge -p '{"spec":{"suspend":true}}'
kubectl -n flux-system patch kustomization flux-system \
  --type=merge -p '{"spec":{"suspend":false}}'
```
