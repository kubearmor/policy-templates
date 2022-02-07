# AccuKnox Policies Library
A community-owned library of Kubernetes System and Network policies

[![Build Status](https://travis-ci.com/accuknox/KubeArmor.svg?branch=master)](https://travis-ci.com/accuknox/KubeArmor)
[![Slack](https://kubearmor.herokuapp.com/badge.svg)](https://kubearmor.herokuapp.com)
[![Discussions](https://img.shields.io/badge/Got%20Questions%3F-Chat-Violet)](https://github.com/kubearmor/KubeArmor/discussions)
[![Contributions](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/kubearmor/policy-templates/issues)

----
## AccuKnox Templates overview

Please follow the hierarchy while contribution

```bash
.
├── mitre
│   ├── network
│   │   └── cnp-firewall-world-block.yaml
│   ├── system
│   │   └── ksp-postgres-allow.yaml
│   │   └── ksp-privilage-pod-block.yaml
├── pci-dss
│   ├── network
│   │   └── cnp-cardholder-data-block.yaml
│   ├── system
│   │   └── ksp-protect-cardholder-data-audit.yaml
├── nist
│   ├── network
│   │   └── cnp-account-management-block.yaml
│   ├── system
│   │   └── ksp-remote-file-copy-block.yaml
│   │   └── ksp-active-directory-audit.yaml
├── cves
│   ├── network
│   │   └── cnp-CVE-2009-0932.yaml
│   ├── system
│   │   └── ksp-CVE-2021-29156.yaml
│   │   └── ksp-CVE-2021-29442.yaml
├── cis
│   ├── system
│   │   └── hsp-scheduler-pod-block.yaml
...
```

📖 Documentation
-----

Please navigate to https://kubearmor.gitbook.io for detailed documentation to **deploy** KubeArmor and create own **custom** templates.
We have also added a set of templates to help you understand how things work.

💪 Contributions
-----

Policy-templates is powered by major contributions from the community and an initiative from AccuKnox.
Refer [Contribution](https://github.com/kubearmor/KubeArmor/blob/main/contribution/contribution_guide.md) for more info 

💬 Discussion
-----

Got questions / doubts / ideas to discuss?
Feel free to open a discussion on [Github discussions](https://github.com/kubearmor/KubeArmor/discussions) board.

👨‍💻 Test it yourself
-----

> Assuming cluster is configured, this can be verified via using `kubectl config current-context` command. If not follow [this](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl)

**Step #1:** Download and install `karmor` CLI binary on your local machine
```sh
curl -sfL https://raw.githubusercontent.com/kubearmor/kubearmor-client/main/install.sh | sudo sh -s -- -b /usr/local/bin
```

**Step #2:** Install [KubeArmor](https://github.com/kubearmor/KubeArmor) using `karmor` CLI tool
```sh
karmor install
```

**Step #3:** Deploy sample application on configured cluster, we'll use `nginx` as deployment here
```sh
kubectl apply -f https://k8s.io/examples/application/deployment.yaml
kubectl get pods -l app=nginx
```

**Step #4:** Applying MITRE Policy to block system owner discovery command
```sh
kubectl apply -f https://raw.githubusercontent.com/kubearmor/policy-templates/main/mitre/system/ksp-mitre-system-owner-user-discovery.yaml
```

**Step #05:** Checking if system owner command is Blocked or not
```sh
kubectl exec -it --namespace default nginx-deployment-xxxxxxxxxx-xxxxx -- bash
root@nginx-deployment-687d8556b7-8wjmj:/# whoami
bash: /usr/bin/whoami: Permission denied
```
> Replace `nginx-deployment-xxxxxxxxxx-xxxxx` with pod name from Step #3. <br>
> We can see the command didn't executed and instead we got Permission denied


**Step #6:** Getting telemetry/alerts for KubeArmor
```sh
kubectl port-forward -n kube-system svc/kubearmor 32767:32767
```
> Keep this terminal open, and in another terminal type
```sh
karmor log
```



<!---
```
- recommended-policies
   - mitre (compliance type)
     - host/workload
       - mysql/generic/postgres/ (mention appropriate workload here)
         - system/network-ingress/network-egress (policy type)
           - policy-name.yaml
```
-->
