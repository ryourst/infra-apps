# infra-apps
ArgoCD ApplicationSet installs for common infrastructure apps, with settings for either public cloud or on-prem baremetal clusters.

Included so far:
- **ingress-nginx** - Installs as a deployment with cloud LB, or daemonset with host networking for on-prem clusters. If you intend to reach ArgoCD via ingress, you will need to temporarily use kube-proxy to reach ArgoCD and get the ApplicationSet deployed:
    ```
    kubectl port-forward svc/argocd-server -n argocd 8080:443 >/dev/null &
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
    argocd login 127.0.0.1:8080
    argocd app create infra-apps --repo https://github.com/ryourst/infra-apps.git --path ./onprem \
     --dest-server https://kubernetes.default.svc --dest-namespace argocd
    argocd app sync infra-apps
    # deploy your ingress resource and re-login to argocd
    kill %1
    argocd login argocd-server.example.com
    ```
- **kube-prometheus** - Installs Prometheus, Grafana, and Alertmanager with cloud LB, or with NGINX ingress resources for on-prem. Ingress resource yaml files need to be updated for correct host name.

    Note: The prometheus CRD will fail to install because the file is too long for ArgoCD (as of 2.4.15) to apply. Instead, after it fails, sync the failed CRD manually in the UI and check `Replace` option.
