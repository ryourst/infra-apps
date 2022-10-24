# infra-apps/other
ArgoCD Applications or App-of-Apps sets that can't use the ApplicationSet generator due to use of sync waves.

## Health checks

You need to update the `argocd-cm` ConfigMap as [stated here](https://argo-cd.readthedocs.io/en/stable/operator-manual/health/#argocd-app) first. 

> **Edit manually if you don't want to do the patch**

```shell
kubectl patch cm/argocd-cm -n argocd --type=merge \
-p='{"data":{"resource.customizations.health.argoproj.io_Application":"hs = {}\nhs.status = \"Progressing\"\nhs.message = \"\"\nif obj.status ~= nil then\n  if obj.status.health ~= nil then\n    hs.status = obj.status.health.status\n    if obj.status.health.message ~= nil then\n      hs.message = obj.status.health.message\n    end\n  end\nend\nreturn hs\n"}}'
```
This step is needed so ArgoCD will wait for apps to become "Ready" before progressing to the next sync wave.


Included so far:
- **rook-ceph** - Deployed as 3 applications using waves, as follows:
    - Wave 0: Deploy CRDs and operator
    - Wave 1: Deploy cluster
    - Wave 2: Deploy toolbox and storage classes
