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
