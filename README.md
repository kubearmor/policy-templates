# Hardening policies

How to get hardening policies for workloads that are deployed in k8s cluster?

[kubearmor-client](https://github.com/kubearmor/kubearmor-client) has this recommendation feature. `karmor recommend` will scan the workloads deployed in our cluster and recommend policies based on container image, k8s manifest or the actual runtime environment. 

1. [Getting hardening policies for general workloads](https://github.com/kubearmor/policy-templates/tree/release#try-it-out-by-yourself)
2. [Getting hardening polices for 5G workloads](https://github.com/kubearmor/policy-templates/tree/release#5g-policies)

## Try it out by yourself
> Assuming k8s cluster is configured, this can be verified via using `kubectl config current-context` command. If not follow [this](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl)

### Step: 1
- Install `kubearmor-client` via `curl -sfL http://get.kubearmor.io/ | sudo sh -s -- -b /usr/local/bin`
- Then run `karmor install` and it'll install kubearmor in your cluster.

### Step: 2
- Deploy the sample wordpress-mysql deployment.
```console
$ kubectl apply -f https://raw.githubusercontent.com/kubearmor/KubeArmor/main/examples/wordpress-mysql/wordpress-mysql-deployment.yaml
```

### Step: 3
Get MITRE based policies for our wordpress-mysql deployment. To see the usage of recommend run this `karmor recommend --help`
```console
➜  ~ karmor recommend --help                                         
Recommend policies based on container image, k8s manifest or the actual runtime env

Usage:
  karmor recommend [flags]
  karmor recommend [command]

Available Commands:
  update      Updates policy-template cache

Flags:
  -c, --config string      absolute path to image registry configuration file (default "/home/yasin/.docker/config.json")
  -h, --help               help for recommend
  -i, --image strings      Container image list (comma separated)
  -l, --labels strings     User defined labels for policy (comma separated)
  -n, --namespace string   User defined namespace value for policies
  -o, --outdir string      output folder to write policies (default "out")
  -r, --report string      report file (default "report.txt")
  -t, --tag strings        tags (comma-separated) to apply. Eg. PCI-DSS, MITRE

Global Flags:
      --context string      Name of the kubeconfig context to use
      --kubeconfig string   Path to the kubeconfig file to use

Use "karmor recommend [command] --help" for more information about a command.
```
### Step: 4
For example, We want MITRE hardening policies for wordpress-mysql.
```bash
$ karmor recommend -n wordpress-mysql -t MITRE
```
Output:
```console
➜  ~ karmor recommend -n wordpress-mysql -t MITRE
INFO[0000] pulling image                                 image="mysql:5.6"
5.6: Pulling from library/mysql
Digest: sha256:20575ecebe6216036d25dab5903808211f1e9ba63dc7825ac20cb975e34cfcae
Status: Image is up to date for mysql:5.6
INFO[0013] dumped image to tar                           tar=/tmp/karmor1058791538/XzNanyxB.tar
Distribution debian
INFO[0020] No runtime policy generated for wordpress-mysql/mysql/mysql:5.6 
created policy out/wordpress-mysql-mysql/mysql-5-6-maint-tools-access.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-trusted-cert-mod.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-system-owner-discovery.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-write-under-bin-dir.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-write-under-dev-dir.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-k8s-client-tool-exec.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-remote-file-copy.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-write-in-shm-dir.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-write-etc-dir.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-shell-history-mod.yaml ...
created policy out/wordpress-mysql-mysql/mysql-5-6-file-integrity-monitoring.yaml ...
INFO[0026] pulling image                                 image="wordpress:4.8-apache"
4.8-apache: Pulling from library/wordpress
Digest: sha256:6216f64ab88fc51d311e38c7f69ca3f9aaba621492b4f1fa93ddf63093768845
Status: Image is up to date for wordpress:4.8-apache
INFO[0048] dumped image to tar                           tar=/tmp/karmor1440787614/typeSnOk.tar
Distribution debian
INFO[0065] No runtime policy generated for wordpress-mysql/wordpress/wordpress:4.8-apache 
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-maint-tools-access.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-trusted-cert-mod.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-system-owner-discovery.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-write-under-bin-dir.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-write-under-dev-dir.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-k8s-client-tool-exec.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-remote-file-copy.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-write-in-shm-dir.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-write-etc-dir.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-shell-history-mod.yaml ...
created policy out/wordpress-mysql-wordpress/wordpress-4-8-apache-file-integrity-monitoring.yaml ...
output report in out/report.txt ...
  Deployment              | wordpress-mysql/mysql      
  Container               | mysql:5.6                  
  OS                      | linux                      
  Arch                    | amd64                      
  Distro                  | debian                     
  Output Directory        | out/wordpress-mysql-mysql  
  policy-template version | v0.1.9                     
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
|               POLICY                |           SHORT DESC           | SEVERITY | ACTION |                       TAGS                        |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-maint-tools-access.yaml   | Restrict access to maintenance | 1        | Audit  | PCI_DSS                                           |
|                                     | tools (apk, mii-tool, ...)     |          |        | MITRE                                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-trusted-cert-mod.yaml     | Restrict access to trusted     | 1        | Block  | MITRE                                             |
|                                     | certificated bundles in the OS |          |        | MITRE_T1552_unsecured_credentials                 |
|                                     | image                          |          |        |                                                   |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-system-owner-             | System Information Discovery   | 3        | Block  | MITRE                                             |
| discovery.yaml                      | - block system owner discovery |          |        | MITRE_T1082_system_information_discovery          |
|                                     | commands                       |          |        |                                                   |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-write-under-bin-dir.yaml  | System and Information         | 5        | Block  | NIST NIST_800-53_AU-2                             |
|                                     | Integrity - System Monitoring  |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     | make directory under /bin/     |          |        | MITRE_T1036_masquerading                          |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-write-under-dev-dir.yaml  | System and Information         | 5        | Audit  | NIST NIST_800-53_AU-2                             |
|                                     | Integrity - System Monitoring  |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     | make files under /dev/         |          |        | MITRE_T1036_masquerading                          |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-k8s-client-tool-exec.yaml | Adversaries may abuse a        | 5        | Block  | MITRE_T1609_container_administration_command      |
|                                     | container administration       |          |        | MITRE_TA0002_execution                            |
|                                     | service to execute commands    |          |        | MITRE_T1610_deploy_container                      |
|                                     | within a container.            |          |        | MITRE NIST_800-53 NIST_800-53_AU-2                |
|                                     |                                |          |        | NIST_800-53_SI-4 NIST                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-remote-file-copy.yaml     | The adversary is trying to     | 5        | Block  | MITRE                                             |
|                                     | steal data.                    |          |        | MITRE_TA0008_lateral_movement                     |
|                                     |                                |          |        | MITRE_TA0010_exfiltration                         |
|                                     |                                |          |        | MITRE_TA0006_credential_access                    |
|                                     |                                |          |        | MITRE_T1552_unsecured_credentials                 |
|                                     |                                |          |        | NIST_800-53_SI-4(18) NIST                         |
|                                     |                                |          |        | NIST_800-53 NIST_800-53_SC-4                      |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-write-in-shm-dir.yaml     | The adversary is trying to     | 5        | Block  | MITRE_execution                                   |
|                                     | write under shm folder         |          |        | MITRE                                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-write-etc-dir.yaml        | The adversary is trying to     | 5        | Block  | NIST_800-53_SI-7 NIST                             |
|                                     | avoid being detected.          |          |        | NIST_800-53_SI-4 NIST_800-53                      |
|                                     |                                |          |        | MITRE_T1562.001_disable_or_modify_tools           |
|                                     |                                |          |        | MITRE_T1036.005_match_legitimate_name_or_location |
|                                     |                                |          |        | MITRE_TA0003_persistence                          |
|                                     |                                |          |        | MITRE MITRE_T1036_masquerading                    |
|                                     |                                |          |        | MITRE_TA0005_defense_evasion                      |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-shell-history-mod.yaml    | Adversaries may delete or      | 5        | Block  | NIST NIST_800-53 NIST_800-53_CM-5                 |
|                                     | modify artifacts generated     |          |        | NIST_800-53_AU-6(8)                               |
|                                     | within systems to remove       |          |        | MITRE_T1070_indicator_removal_on_host             |
|                                     | evidence.                      |          |        | MITRE MITRE_T1036_masquerading                    |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| mysql-5-6-file-integrity-           | File Integrity Monitoring      | 1        | Block  | NIST NIST_800-53_AU-2                             |
| monitoring.yaml                     |                                |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     |                                |          |        | MITRE_T1036_masquerading                          |
|                                     |                                |          |        | MITRE_T1565_data_manipulation                     |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+

  Deployment              | wordpress-mysql/wordpress      
  Container               | wordpress:4.8-apache           
  OS                      | linux                          
  Arch                    | amd64                          
  Distro                  | debian                         
  Output Directory        | out/wordpress-mysql-wordpress  
  policy-template version | v0.1.9                         
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
|               POLICY                |           SHORT DESC           | SEVERITY | ACTION |                       TAGS                        |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-maint-tools-   | Restrict access to maintenance | 1        | Audit  | PCI_DSS                                           |
| access.yaml                         | tools (apk, mii-tool, ...)     |          |        | MITRE                                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-trusted-cert-  | Restrict access to trusted     | 1        | Block  | MITRE                                             |
| mod.yaml                            | certificated bundles in the OS |          |        | MITRE_T1552_unsecured_credentials                 |
|                                     | image                          |          |        |                                                   |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-system-owner-  | System Information Discovery   | 3        | Block  | MITRE                                             |
| discovery.yaml                      | - block system owner discovery |          |        | MITRE_T1082_system_information_discovery          |
|                                     | commands                       |          |        |                                                   |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-write-under-   | System and Information         | 5        | Block  | NIST NIST_800-53_AU-2                             |
| bin-dir.yaml                        | Integrity - System Monitoring  |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     | make directory under /bin/     |          |        | MITRE_T1036_masquerading                          |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-write-under-   | System and Information         | 5        | Audit  | NIST NIST_800-53_AU-2                             |
| dev-dir.yaml                        | Integrity - System Monitoring  |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     | make files under /dev/         |          |        | MITRE_T1036_masquerading                          |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-k8s-client-    | Adversaries may abuse a        | 5        | Block  | MITRE_T1609_container_administration_command      |
| tool-exec.yaml                      | container administration       |          |        | MITRE_TA0002_execution                            |
|                                     | service to execute commands    |          |        | MITRE_T1610_deploy_container                      |
|                                     | within a container.            |          |        | MITRE NIST_800-53 NIST_800-53_AU-2                |
|                                     |                                |          |        | NIST_800-53_SI-4 NIST                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-remote-file-   | The adversary is trying to     | 5        | Block  | MITRE                                             |
| copy.yaml                           | steal data.                    |          |        | MITRE_TA0008_lateral_movement                     |
|                                     |                                |          |        | MITRE_TA0010_exfiltration                         |
|                                     |                                |          |        | MITRE_TA0006_credential_access                    |
|                                     |                                |          |        | MITRE_T1552_unsecured_credentials                 |
|                                     |                                |          |        | NIST_800-53_SI-4(18) NIST                         |
|                                     |                                |          |        | NIST_800-53 NIST_800-53_SC-4                      |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-write-in-shm-  | The adversary is trying to     | 5        | Block  | MITRE_execution                                   |
| dir.yaml                            | write under shm folder         |          |        | MITRE                                             |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-write-etc-     | The adversary is trying to     | 5        | Block  | NIST_800-53_SI-7 NIST                             |
| dir.yaml                            | avoid being detected.          |          |        | NIST_800-53_SI-4 NIST_800-53                      |
|                                     |                                |          |        | MITRE_T1562.001_disable_or_modify_tools           |
|                                     |                                |          |        | MITRE_T1036.005_match_legitimate_name_or_location |
|                                     |                                |          |        | MITRE_TA0003_persistence                          |
|                                     |                                |          |        | MITRE MITRE_T1036_masquerading                    |
|                                     |                                |          |        | MITRE_TA0005_defense_evasion                      |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-shell-history- | Adversaries may delete or      | 5        | Block  | NIST NIST_800-53 NIST_800-53_CM-5                 |
| mod.yaml                            | modify artifacts generated     |          |        | NIST_800-53_AU-6(8)                               |
|                                     | within systems to remove       |          |        | MITRE_T1070_indicator_removal_on_host             |
|                                     | evidence.                      |          |        | MITRE MITRE_T1036_masquerading                    |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+
| wordpress-4-8-apache-file-          | File Integrity Monitoring      | 1        | Block  | NIST NIST_800-53_AU-2                             |
| integrity-monitoring.yaml           |                                |          |        | NIST_800-53_SI-4 MITRE                            |
|                                     |                                |          |        | MITRE_T1036_masquerading                          |
|                                     |                                |          |        | MITRE_T1565_data_manipulation                     |
+-------------------------------------+--------------------------------+----------+--------+---------------------------------------------------+

```

## Step: 5
Applying the recommended policies in k8s cluster. All of the generated policies are in your `/out/` directory.
```bash
➜  ~ cd out/wordpress-mysql-mysql
➜  wordpress-mysql-mysql ls
mysql-5-6-access-ctrl-permission-mod.yaml      mysql-5-6-k8s-client-tool-exec.yaml  mysql-5-6-shell-history-mod.yaml       mysql-5-6-write-etc-dir.yaml
mysql-5-6-cis-commandline-warning-banner.yaml  mysql-5-6-maint-tools-access.yaml    mysql-5-6-system-network-env-mod.yaml  mysql-5-6-write-in-shm-dir.yaml
mysql-5-6-cronjob-cfg.yaml                     mysql-5-6-mysqldump-bin-exec.yaml    mysql-5-6-system-owner-discovery.yaml  mysql-5-6-write-under-bin-dir.yaml
mysql-5-6-file-integrity-monitoring.yaml       mysql-5-6-pkg-mngr-exec.yaml         mysql-5-6-trusted-cert-mod.yaml        mysql-5-6-write-under-dev-dir.yaml
mysql-5-6-file-system-mounts.yaml              mysql-5-6-remote-file-copy.yaml      mysql-5-6-user-grp-mod.yaml
```
To apply all the policies that are recommended. Simple navigate to the directory `cd /out/wordpress-mysql-mysql/` and run `kubectl apply -f .` 
```bash
➜  wordpress-mysql-mysql kubectl apply -f .                          
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-access-ctrl-permission-mod created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-cis-commandline-warning-banner created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-cronjob-cfg created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-file-integrity-monitoring created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-file-system-mounts created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-k8s-client-tool-exec created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-maint-tools-access created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-mysqldump-bin-exec created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-pkg-mngr-exec created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-remote-file-copy created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-shell-history-mod created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-system-network-env-mod created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-system-owner-discovery created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-trusted-cert-mod created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-user-grp-mod created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-write-etc-dir created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-write-in-shm-dir created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-write-under-bin-dir created
kubearmorpolicy.security.kubearmor.com/mysql-mysql-5-6-write-under-dev-dir created
```

**Now we are successfully applied all the hardening policies.**

## FiGHT Policies

To get hardening policies for FiGHT workloads use `karmor recommend` with the tag filter

```bash
$ karmor recommend -n default -t FIGHT
```

```console
karmor recommend -t FIGHT -n default                                                                    (summary|✚3)
INFO[0000] Found outdated version of policy-templates    Current Version=v0.2.3
INFO[0000] Downloading latest version [v0.2.4]          
INFO[0002] policy-templates updated                      Updated Version=v0.2.4
INFO[0003] pulling image                                 image=knoxuser/5g-sample
latest: Pulling from knoxuser/5g-sample
Digest: sha256:6b06964cdbbc517102ce5e0cef95152f3c6a7ef703e4057cb574539de91f72e6
Status: Image is up to date for knoxuser/5g-sample:latest
INFO[0008] dumped image to tar                           tar=/tmp/karmor464060076/PeVhlFGJ.tar
Distribution debian
INFO[0008] No runtime policy generated for default/5g-sample/knoxuser/5g-sample 
created policy out/default-5g-sample/knoxuser-5g-sample-latest-trusted-cert-mod.yaml ...
created policy out/default-5g-sample/knoxuser-5g-sample-latest-5g-tactic-credentials-from-password-stores.yaml ...
created policy out/default-5g-sample/knoxuser-5g-sample-latest-impair-defense.yaml ...
created policy out/default-5g-sample/knoxuser-5g-sample-latest-network-service-scanning.yaml ...
created policy out/default-5g-sample/knoxuser-5g-sample-latest-remote-services.yaml ...
local port to be used for port forwarding discovery-engine-56b9499696-m9z2r: 32778 
INFO[0010] Connected to discovery engine                
output report in out/report.txt ...
  Deployment              | default/5g-sample          
  Container               | knoxuser/5g-sample:latest  
  OS                      | linux                      
  Arch                    | amd64                      
  Distro                  | debian                     
  Output Directory        | out/default-5g-sample      
  policy-template version | v0.2.4                     
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
|               POLICY               |           SHORT DESC           | SEVERITY | ACTION |               TAGS                |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
| knoxuser-5g-sample-latest-trusted- | Restrict access to trusted     | 1        | Block  | MITRE                             |
| cert-mod.yaml                      | certificated bundles in the OS |          |        | MITRE_T1552_unsecured_credentials |
|                                    | image                          |          |        | FGT1555 FIGHT                     |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
| knoxuser-5g-sample-latest-5g-      | Adversaries may search for     | 1        | Block  | MITRE                             |
| tactic-credentials-from-password-  | common password storage        |          |        | MITRE_T1552_unsecured_credentials |
| stores.yaml                        | locations to obtain user       |          |        | FGT1555 FIGHT                     |
|                                    | credentials.                   |          |        |                                   |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
| knoxuser-5g-sample-latest-impair-  | Adversaries may maliciously    | 6        | Audit  | MITRE                             |
| defense.yaml                       | modify components of a victim  |          |        | FGT1562                           |
|                                    | environment in order to        |          |        | FIGHT                             |
|                                    | hinder or disable defensive    |          |        |                                   |
|                                    | mechanisms.                    |          |        |                                   |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
| knoxuser-5g-sample-latest-network- | Adversaries may attempt to     | 5        | Audit  | MITRE                             |
| service-scanning.yaml              | get a listing of services      |          |        | FGT1046                           |
|                                    | running on remote hosts,       |          |        | FIGHT                             |
|                                    | including those that may be    |          |        |                                   |
|                                    | vulnerable to remote software  |          |        |                                   |
|                                    | exploitation.                  |          |        |                                   |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
| knoxuser-5g-sample-latest-remote-  | Adversaries may use Valid      | 3        | Audit  | MITRE                             |
| services.yaml                      | Accounts to log into a service |          |        | FIGHT                             |
|                                    | specifically designed to       |          |        | FGT1021                           |
|                                    | accept remote connections,     |          |        |                                   |
|                                    | such as telnet, SSH, and VNC.  |          |        |                                   |
+------------------------------------+--------------------------------+----------+--------+-----------------------------------+
```
