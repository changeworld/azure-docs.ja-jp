---
title: Azure Red Hat OpenShift でセキュリティ コンテンツ制約を管理する | Microsoft Docs
description: Azure Red Hat OpenShift クラスター管理者によるセキュリティ コンテンツ制約の管理
services: container-service
author: troy0820
ms.author: jzim
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 8e85ac98683487c6b18be7f502f28cad9a0c2251
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2019
ms.locfileid: "71712842"
---
# <a name="overview"></a>概要 

セキュリティ コンテキスト制約を利用することで、管理者はポッドのアクセス許可を制御できます。 この API タイプの詳細については、[セキュリティ コンテキスト制約](https://https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html) (SCC) アーキテクチャ ドキュメントを参照してください。 インスタンス内の SCC は、CLI を使用して通常の API オブジェクトとして管理できます。

## <a name="listing-security-context-constraints"></a>セキュリティ コンテキスト制約の一覧表示

SCC の現在の一覧を取得するには 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examining-a-security-context-constraints-object"></a>セキュリティ コンテキスト制約オブジェクトの調査

特定の SCC を調べるには、`oc get`、`oc describe`、または `oc edit` を使用します。  たとえば、**制約付き** SCC は次のように確認します。
```bash
$ oc describe scc restricted
Name:                   restricted
Priority:               <none>
Access:
  Users:                <none>
  Groups:               system:authenticated
Settings:
  Allow Privileged:         false
  Default Add Capabilities:     <none>
  Required Drop Capabilities:       KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:         <none>
  Allowed Seccomp Profiles:     <none>
  Allowed Volume Types:         configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:           false
  Allow Host Ports:         false
  Allow Host PID:           false
  Allow Host IPC:           false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:          <none>
    UID Range Max:          <none>
  SELinux Context Strategy: MustRunAs
    User:               <none>
    Role:               <none>
    Type:               <none>
    Level:              <none>
  FSGroup Strategy: MustRunAs
    Ranges:             <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:             <none>
```
## <a name="next-steps"></a>次の手順
osa-customer-admin ロールの構成方法:
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift の Azure Active Directory 統合](howto-aad-app-configuration.md) 