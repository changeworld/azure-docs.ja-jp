---
title: Azure Arc 対応 Kubernetes に関してよく寄せられる質問
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: この記事には、Azure Arc 対応 Kubernetes に関してよく寄せられる質問の一覧が記載されています
keywords: Kubernetes, Arc, Azure, コンテナー, 構成, GitOps, faq
ms.openlocfilehash: 750b783d3234bb5ea61ed12dc4cf0471b7b231e4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215056"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>よく寄せられる質問 - Azure Arc 対応 Kubernetes

この記事では、Azure Arc 対応 Kubernetes についてよく寄せられる質問を記載します。

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Azure Arc 対応 Kubernetes と Azure Kubernetes Service (AKS) の違いは何ですか?

AKS は、Azure によるマネージド Kubernetes オファリングです。 AKS は、複雑さと運用上のオーバーヘッドの多くを Azure にオフロードすることで、Azure でのマネージド Kubernetes クラスターのデプロイを簡素化します。 Kubernetes マスターは Azure によって管理されるので、お客様はエージェント ノードの管理と保守のみを行います。

Azure Arc 対応 Kubernetes を使用すると、Kubernetes クラスターを Azure に接続することで、Azure の管理機能 (Azure Monitor や Azure Policy など) を拡張できます。 ユーザーは、基になる Kubernetes クラスター自体を管理します。

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Azure で実行されている AKS クラスターを Azure Arc に接続する必要はありますか?

Azure Kubernetes Service (AKS) クラスターを Azure Arc に接続する必要があるのは、クラスター上で App Services や Data Services のような Azure Arc 対応サービスを実行する場合のみです。 これは、Azure Arc 対応 Kubernetes の[カスタムの場所](custom-locations.md)の機能を使って行うことができます。 これは、クラスター拡張とカスタムの場所が AKS クラスター上にネイティブに導入されるまでの制限事項です。

カスタムの場所を使用せず、Azure Monitor や Azure Policy (Gatekeeper) などの管理機能のみを使用する場合は、AKS でネイティブに使用できるため、Azure Arc への接続は必要ありません。
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-edge-to-azure-arc"></a>Azure Stack Edge 上の AKS-HCI クラスターと Kubernetes クラスターを Azure Arc に接続する必要がありますか?

はい。Azure Stack Edge 上の AKS-HCI クラスターまたは Kubernetes クラスターを Azure Arc に接続することで、Azure Resource Manager のリソース表現がクラスターに提供されます。 このリソースの表現により、クラスターの構成、Azure Monitor、Azure Policy (Gatekeeper) などの機能が、接続された Kubernetes クラスターに拡張されます。

Azure Arc 対応 Kubernetes クラスターが Azure Stack Edge、AKS on Azure Stack HCI (2021 年 4 月以降の更新プログラム)、または AKS on Windows Server 2019 Datacenter (2021 年 4 月以降の更新プログラム) 上にある場合、Kubernetes の構成は無料で提供されます。

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>期限切れの Azure Arc 対応 Kubernetes リソースの処理方法は?

Azure Arc 対応の Kubernetes のクラスターに関連付けられているシステム割り当てのマネージド ID は、Azure Arc サービスと通信するために Azure Arc エージェントによってのみ使用されます。 このシステム割り当てのマネージド ID に関連付けられている証明書の有効期限は 90 日間で、エージェントはこの証明書の更新を 46 日目から 90 日目まで試行します。 この証明書の有効期限が切れると、リソースが考慮され、`Expired` とすべての機能 (構成、監視、ポリシーなど) がこのクラスターで動作しなくなります。その後、もう一度クラスターを削除して Azure Arc に接続する必要があります。 そのため、マネージド ID 証明書を確実に更新するには、46 日目から 90 日目までの間、クラスターを少なくとも 1 回はオンラインにすることをお勧めします。

任意のクラスターについて証明書の有効期限が近づいていることを確認するには、次のコマンドを実行します

```console
az connectedk8s show -n <name> -g <resource-group>
```

この出力では、`managedIdentityCertificateExpirationTime` の値は、マネージド ID 証明書がいつ期限切れになるか (その証明書の 90 日目のマーク) を示しています。 

`managedIdentityCertificateExpirationTime` の値が過去のタイムスタンプを示している場合、上記の出力における `connectivityStatus` フィールドは `Expired` に設定されます。 そのような場合に、Kubernetes クラスターが Azure Arc でもう一度機能するようにするには

1. クラスター上の Azure Arc 対応 Kubernetes リソースとエージェントを削除します。 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. クラスターにエージェントをデプロイして、Azure Arc 対応 Kubernetes リソースを再作成します。
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` では、クラスター上の構成とクラスター拡張も削除されます。 `az connectedk8s connect` を実行した後に、手動で、または Azure Policy を使用して、クラスター上で構成とクラスター拡張機能を再作成します。

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>CI/CD パイプラインを既に使用している場合でも、Azure Arc 対応 Kubernetes と構成を使用できますか?

はい。その場合でも CI/CD パイプラインを介してデプロイを受け取るクラスターで構成を使用できます。 従来の CI/CD パイプラインと比較すると、構成で 2 つの追加メリットが得られます。

**ドリフト調整**

CI/CD パイプラインでは、パイプラインの実行中に変更が 1 回だけ適用されます。 ただし、クラスター上の Kubernetes リソースの望ましい状態を取得するために、クラスター上の GitOps オペレーターによって Git リポジトリが継続的にポーリングされます。 GitOps オペレーターによって、リソースの望ましい状態がクラスター上のリソースの実際の状態とは異なることが検出されると、このドリフトが調整されます。

**GitOps が全体的に適用される**

CI/CD パイプラインは、Kubernetes クラスターへのイベント ドリブン デプロイ (Git リポジトリへのプッシュなど) に役立ちます。 ただし、すべての Kubernetes クラスターに同じ構成をデプロイする場合は、各 Kubernetes クラスターの資格情報を CI/CD パイプラインに手動で構成する必要があります。 

Azure Arc 対応 Kubernetes の場合、Azure Resource Manager によって構成が管理されるので、Azure Policy を使用して、サブスクリプションまたはリソース グループのスコープ内のすべての Azure Arc 対応 Kubernetes リソースで同じ構成の作成を自動化することができます。 この機能は、ポリシーの割り当て後に作成された Azure Arc 対応 Kubernetes リソースにも適用できます。

この機能は、コンプライアンスとガバナンスの要件を満たすために、Kubernetes クラスターのインベントリ全体でベースライン構成 (ネットワーク ポリシー、ロール バインド、ポッド セキュリティ ポリシーなど) に適用されます。

## <a name="does-azure-arc-enabled-kubernetes-store-any-customer-data-outside-of-the-clusters-region"></a>Azure Arc 対応 Kubernetes によって、クラスターのリージョン外に格納される顧客データはありますか?

現在、顧客データを 1 つのリージョンに格納できるようにする機能は、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[セキュリティ センター](https://azure.microsoft.com/global-infrastructure/data-residency/)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* クイックスタートを利用して、[Kubernetes クラスターを Azure Arc に接続](./quickstart-connect-cluster.md)します。
* Kubernetes クラスターが既に Azure Arc に接続されていますか? [Azure Arc 対応 Kubernetes クラスターで構成を作成する](./tutorial-use-gitops-connected-cluster.md)。
* [Azure Policy を使用して構成を大規模に適用する](./use-azure-policy.md)方法について学ぶ。
