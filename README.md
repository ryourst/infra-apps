# infra-apps
ArgoCD ApplicationSet installs for common infrastructure apps, with settings for either public cloud or on-prem baremetal clusters.

Included so far:
- **ingress-nginx** - Installs as a deployment with cloud LB, or daemonset with host networking for on-prem clusters. If you intend to reach ArgoCD via ingress, you will need to temporarily use kube-proxy to reach ArgoCD and get the ApplicationSet deployed:
    ```
    kubectl port-forward svc/argocd-server -n argocd 8080:443 &
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
    argocd login 127.0.0.1:8080
    argocd app create infra-apps --repo https://github.com/ryourst/infra-apps.git --path ./onprem \
     --dest-server https://kubernetes.default.svc --dest-namespace argocd
    ```
