---
title: Azure Kubernetes ネットワーク ポリシー | Microsoft Docs
description: Kubernetes クラスターをセキュリティで保護するための Kubernetes ネットワーク ポリシーについて説明します。
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: 5a6da7e65a9a3e962a2df37b062792fbb990d04d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73159683"
---
# <a name="azure-kubernetes-network-policies-overview"></a>Azure Kubernetes ネットワーク ポリシーの概要

ネットワーク ポリシーは、ネットワーク セキュリティ グループ (NSG) が VM にマイクロセグメンテーションを提供するのと同じように、ポッドにマイクロセグメンテーションを提供します。 Azure ネットワーク ポリシーの実装では、標準の Kubernetes ネットワーク ポリシーの仕様がサポートされます。 ラベルを使用してポッドのグループを選択し、これらのポッドとの間で許可されるトラフィックの種類を指定する、イングレスとエグレスの規則の一覧を定義できます。 Kubernetes ネットワーク ポリシーの詳細については、[Kubernetes のドキュメント](https://kubernetes.io/docs/concepts/services-networking/network-policies/)を参照してください。

![Kubernetes ネットワーク ポリシーの概要](./media/kubernetes-network-policies/kubernetes-network-policies-overview.png)

Azure ネットワーク ポリシーは、VNet 統合をコンテナーに提供する Azure CNI と連携して動作します。 現在、このポリシーは Linux ノードでのみサポートされています。 実装によって Linux IP テーブルの規則が定義済みポリシーに基づいて構成され、トラフィックのフィルター処理が強制されます。

## <a name="planning-security-for-your-kubernetes-cluster"></a>Kubernetes クラスターのセキュリティの計画
ご自身のクラスターにセキュリティを実装する場合は、ネットワーク セキュリティ グループ (NSG) を使用して、南北のトラフィック、つまりクラスター サブネットを出入りするトラフィックをフィルター処理します。また、Kubernetes ネットワーク ポリシーを使用して、東西のトラフィック、つまりクラスター内のポッド間のトラフィックをフィルター処理します。

## <a name="using-azure-kubernetes-network-policies"></a>Azure Kubernetes ネットワーク ポリシーの使用
次の方法で Azure ネットワーク ポリシーを使用して、ポッドにマイクロセグメンテーションを提供できます。

### <a name="acs-engine"></a>ACS エンジン
ACS エンジンは、Azure での Kubernetes クラスター デプロイ用 Azure Resource Manager テンプレートを生成するツールです。 クラスターの構成は、テンプレートを生成するときにツールに渡される JSON ファイルで指定されます。 サポートされているクラスター設定とその説明の完全な一覧については、Microsoft Azure Container Service エンジン - クラスター定義に関するページをご覧ください。

ACS エンジンを使用してデプロイされたクラスターでポリシーを有効にするには、クラスター定義ファイルで networkPolicy 設定の値が "azure" になるように指定します。

#### <a name="example-configuration"></a>構成例

次の JSON 構成の例では、新しい仮想ネットワークとサブネットを作成し、Azure CNI を使用して、そこに Kubernetes クラスターをデプロイします。 JSON ファイルの編集には "メモ帳" を使用することをお勧めします。 
```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
         "networkPolicy": "azure"
       }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "<specify a cluster name>",
      "vmSize": "Standard_D2s_v3"
    },
    "agentPoolProfiles": [
      {
        "name": "agentpool",
        "count": 2,
        "vmSize": "Standard_D2s_v3",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
   "linuxProfile": {
      "adminUsername": "<specify admin username>",
      "ssh": {
        "publicKeys": [
          {
            "keyData": "<cut and paste your ssh key here>"
          }
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "<enter the client ID of your service principal here >",
      "secret": "<enter the password of your service principal here>"
    }
  }
}

```
### <a name="creating-your-own-kubernetes-cluster-in-azure"></a>Azure でのご自身の Kubernetes クラスターの作成
実装を使用すると、ACS エンジンなどのツールを使用せずに、ご自身でデプロイした Kubernetes クラスターのポッドにネットワーク ポリシーを提供できます。 この場合は、最初に CNI プラグインをインストールし、クラスター内のすべての仮想マシンで有効にします。 詳細については、[自身でデプロイした Kubernetes クラスター用プラグインのデプロイ](deploy-container-networking.md#deploy-plug-in-for-a-kubernetes-cluster)に関するページをご覧ください。

クラスターがデプロイされたら、次の `kubectl` コマンドを実行して、Azure ネットワーク ポリシー *daemonset* をダウンロードして、クラスターに適用します。

  ```
  kubectl apply -f https://raw.githubusercontent.com/Azure/acs-engine/master/parts/k8s/addons/kubernetesmasteraddons-azure-npm-daemonset.yaml

  ```
また、ソリューションはオープン ソースで、コードは [Azure コンテナー ネットワーク リポジトリ](https://github.com/Azure/azure-container-networking/tree/master/npm)から入手できます。



## <a name="next-steps"></a>次のステップ
- [Azure Kubernetes Service](../aks/intro-kubernetes.md) について確認します。
-  [コンテナー ネットワーク](container-networking-overview.md)について確認します。
- Kubernetes クラスターまたは Docker コンテナー用の[プラグインをデプロイ](deploy-container-networking.md)します。
