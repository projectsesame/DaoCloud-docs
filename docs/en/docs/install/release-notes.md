---
MTPE: todo
Date: 2023-04-12
---

# Installer Release Notes

This page lists the Release Notes of the installer, so that you can understand the evolution path and feature changes of each version.

## 2022-4-11

### v0.6.1

#### Optimization

- **Optimized** Upgraded Kpanda to v0.16.1
- **Optimized** Upgraded Skoala to v0.19.4

## 2022-4-06

### v0.6.0

#### Features

- **NEW** Added support for one-click upgrade of Gproduct components
- **NEW** Adapted operating system: UOS V20 1020a / Ubuntu 20.04
- **NEW** Support OCP (OpenShift Container Platform) to install DCE5.0
- **NEW** CLI supports generating clusterConfig templates
- **NEW** All in one mode starts the minimal installation mode by default
- **NEW** Added Kcollie component in Gproduct component
- **NEW** Support community version to sync image to external repository

#### Optimization

- **Optimize** Decouple the code for generating offline packages and the code required for the installation process
- **Optimize** Optimize tinder node inotify parameters
- **Optimize** Optimize the full-mode online installation experience
- **Optimize** optimize clusterConfig structure and configuration
- **Optimized** Community Edition allows not to check clusterConfig format and parameters
- **Optimize** Optimize installer execution scheduler log output

#### fix

- **FIX** Removed dependency on wget
- **Fix** Fix the problem of installation failure after repeated decompression of offline packages
- **FIX** Fix MinIo non-reentrant issue
- **Fix** Fix redis pvc that continues to be left behind when middleware Redis CR is removed
- **Fix** Fix the problem of sequence dependency when Amamba and Amamba-jenkins are installed concurrently
- **Fix** Fix the problem that the installer command line -j parameter parsing fails

## 2022-2-28

### v0.5.0

#### Features

- **NEW** Offline package separation osPackage, needs to define `osPackagePath` in the cluster configuration file
- **NEW** Support addon offline, you need to define `addonOfflinePackagePath` in the cluster configuration file
- **NEW** Offline installation supports operating systems REHL 8.4, REHL 7.9

#### Optimization

- **Optimization** Upgraded the version of pre-dependent tools

#### fix

- **Fix** installer command line `-j` parameter validity detection problem
- **Fix** The installation path problem of pre-dependent tools
- **Fix** the problem that the host list password is invalid for pure numbers
- **Fix** When the runtime is Docker, the built-in warehouse image cannot be pulled

#### Known Issues

- The installer installation fails due to the pre-installed runc on REHL8 with non-minimal installation. Temporary solution: execute `rpm -qa | grep runc && yum remove -y runc` on each of the above nodes before installation
- Illegal kernel parameter settings on REHL8 with non-minimal installation, temporary solution; execute on each of the above nodes before installation
   `eval $(grep -i 'vm.maxmapcount' /etc/sysctl.conf -r /etc/sysctl.d | xargs -L1 | awk -F ':' '{printf("sed -i -r \"s /(%s)/#\\1/\" %s; ", $2, $1)}') && sysctl --system`
- There are potential risks in the concurrent installation of helm, and the installation cannot continue after failure

## 2022-12-30

### v0.4.0

#### New features

- **NEW** The syntax of clusterConfig has been upgraded from v1alpha1 to v1alpha2, the syntax has incompatible changes, you can check the documentation
- **NEW** No longer install permanent Harbor and permanent MinIO on the global service cluster
- **NEW** bootstrapping nodes need to exist permanently, users install minio, chart museum, registry
- **NEW** Added installation of contour as default ingress-controller for commercial version
- **NEW** New installation of cert-manager in commercial version
- **NEW** Support cluster deployment in private key mode
- **NEW** supports external image registry for deployment

#### Optimized

- **Optimization** The offline package no longer includes the ISO of the operating system, which needs to be downloaded separately. In the case of pure offline, the absolute path of the ISO needs to be defined in the clusterConfig file
- **Optimized** Commercial version uses Contour as default ingress-controller
- **Optimized** MinIO supports using VIP
- **Optimize** coredns automatically inject registry VIP analysis
- **Optimization** Optimize the offline package production process and speed up the packaging of Docker images
- **Optimized** Optimized the offline package size
- **Optimized** infrastructure support 1.25: upgrade redis-operator, eck-operator, hwameiStor
- **optimized** upgrade to keycloakX
- **Optimize** istio version upgrade v1.16.1

#### Known Issues

- The default installation mode does not support unpartitioned SSD disks. If you want to support it, you need to manually intervene.
- Pure offline environment, no app store by default. Please manually connect the chart-museum of the bootstrapping node to the global cluster, registry address: http://{Tinder IP}:8081, username rootuser, password rootpass123
- There is a known problem in the metallb community. There is a dadfailed IPV6 loopback address in the main network card, and metallb cannot work. Before installing, you need to ensure that the main network card is not dadfailed
- If the machine is too stuck during the startup of insight-api-server, the initialization (migrate) of the database cannot be completed during the Liveness health check cycle, resulting in the need for manual intervention
- The iso path in the clusterConfig configuration file must be an absolute path, relative paths are not supported
- The default k8s version of kubean and the offline package are still limited to k8s 1.24 version, which has not been updated to 1.25 (PG does not support it yet)
- If the external-Registry is Harbor, the Project will not be automatically created for the time being, and it needs to be created manually in advance
- When Docker is running, the built-in registry cannot be pulled, it will be fixed in the next version
- After disabling IPV6, podman cannot start the kind cluster of bootstrapping nodes

## 2022-11-30

### v0.3.29

#### New features

- **NEW** ARM64 support: build arm64 offline packages.
- **NEW** Added support for kylin v10 sp2 offline package.
- **NEW** Infrastructure Support 1.25: Upgrade redis-operator, eck-operator, hwameiStor and other components.
- **NEW** Added support for cluster deployment in private key mode.
- **New** The workload is elastically scaled based on custom indicators, which is closer to the user's actual business elastic expansion and contraction needs.

#### Optimized

- **Optimize** Create permanent harbor with operator, enable HTTPS, and use Postgressql operator.
- **Optimized** Commercial version uses contour as default ingress-controller.
- **Optimized** MinIO supports using VIP.
- **Optimized** coredns is automatically injected into registry VIP resolution.
- **Optimization** Optimize the offline package production process and speed up the packaging of docker images.

#### Bug fixes

- **FIX** Fixed issues with fair cloud service.
- **FIX** Fixed issues with image and helm for various submodules.
- **FIXED** Bug fixes for offline package loading.

#### Known issues

- Because some operators need to be upgraded to support 1.25, DCE5 does not support 1.20 downwards.
- The default k8s version of kubean and the offline package are still limited to k8s 1.24 version, which has not been updated to 1.25 (postgres-operator is not supported yet).
- In the case of Image Load, the istio-ingressgateway imagePullPolicy is always.
- For the ARM version, step 16 (harbor) cannot be performed, because harbor does not support ARM for the time being. The mainfest.yaml file needs to be modified, the postgressql operator is fasle, and -j 1,2,3,4,5,6,7,8,9,10,11,12,13,14,15 should be added when executing the installation command