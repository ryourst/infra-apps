# infra-apps
ArgoCD ApplicationSet installs for common infrastructure apps, with settings for either public cloud or on-prem baremetal clusters.

Included so far:
- **ingress-nginx** - Installs as a deployment with cloud LB, or daemonset with host networking for on-prem clusters. If you intend to reach ArgoCD via ingress, you will need to temporarily use kube-proxy to reach ArgoCD and get the ApplicationSet deployed:
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```
