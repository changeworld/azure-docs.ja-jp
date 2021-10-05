---
title: Azure Arc 対応データ サービスのデプロイを計画する
description: Azure Arc 対応データ サービスのデプロイを計画する際の考慮事項について説明します
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d9ae624ddc0a4e5a2f5d9ac38428f4f3c10a01e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832412"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>Azure Arc 対応データ サービスのデプロイを計画する

この記事では、Azure Arc 対応データ サービスのデプロイを計画する方法について説明します。


まず、Azure Arc データ サービスをデプロイするには、データベース ワークロードとそれらのワークロードのビジネス要件を正しく理解する必要があります。 たとえば、可用性、ビジネス継続性、これらのワークロードに対するメモリ、CPU、ストレージの容量の要件などを検討します。 次に、これらのデータベース ワークロードをサポートするインフラストラクチャを、ビジネス要件に基づいて準備する必要があります。

## <a name="prerequisites"></a>前提条件

Azure Arc 対応データ サービスをデプロイする前に、前提条件を理解する、必要な情報をすべて準備する、適切なアクセス レベルでインフラストラクチャ環境を正しく構成する、ストレージ、CPU、メモリに適切な容量を設定することが重要です。 こうすることで、デプロイを正常に終わらせることができます。

以下のセクションをご覧ください。
- [サイズ設定のガイダンス](sizing-guidance.md)
- [ストレージの構成](storage-configuration.md)
- [接続モードとその要件に関するページ](connectivity.md)

以下について確認します。
- [`arcdata` CLI 拡張機能](install-arcdata-extension.md)がインストールされている。
- 他の[クライアント ツール](install-client-tools.md)がインストールされている。
- Kubernetes クラスターにアクセスできる。
- `kubeconfig` ファイルが構成されている。 デプロイ先の Kubernetes クラスターがポイントされている必要があります。 次のコマンドを実行して、デプロイ先のクラスターの現在のコンテキストを確認します。
   ```console
   kubectl cluster-info
   ```
- Azure Arc データ コントローラー、Azure Arc 対応 SQL Managed Instance、Azure Arc 対応 PostgreSQL Hyperscale サーバーなどのリソースが射影され請求される Azure サブスクリプション。

インフラストラクチャの準備ができたら、次の方法で Azure Arc 対応データ サービスをデプロイします。
1. Kubernetes クラスターの検証済みディストリビューションのいずれかで Azure Arc 対応データ コントローラーを作成します
1. Azure Arc 対応 SQL Managed Instance または Azure Arc 対応 PostgreSQL Hyperscale サーバー グループ、あるいはその両方を作成します。

> [!CAUTION]
> 一部のデータ サービス レベルとモードは[一般提供](release-notes.md)段階であり、一部はプレビュー段階です。 同じデータ コントローラーで GA サービスとプレビュー サービスを混在させないことをお勧めします。 同じデータ コントローラーで GA サービスとプレビュー サービスを混在させている場合、インプレース アップグレードはできません。 このシナリオでは、アップグレードするときに、データ コントローラーとデータ サービスをいったん削除してから再作成する必要があります。

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>概要: Azure Arc 対応データ コントローラーを作成する

Azure Arc 対応データ サービスは、複数の異なる種類の Kubernetes クラスター、および複数の異なる方法を使用してマネージド Kubernetes サービスに作成できます。

現時点で検証されている Kubernetes サービスとディストリビューションの一覧は次のとおりです。


- AWS Elastic Kubernetes Service (EKS)
- Azure Kubernetes Service (AKS)
- Azure Stack HCI の Azure Kubernetes Service
- Azure RedHat OpenShift (ARO)
- Google Kubernetes Engine (GKE)
- Kubeadm を使用して通常デプロイされるオープンソースのアップストリーム Kubernetes
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * Kubernetes のサポートされている最小バージョンは 1.19 です。 追加情報については、[既知の問題](./release-notes.md#known-issues)を参照してください。
> * サポートされる OCP の最小バージョンは 4.7 です。
> * Azure Kubernetes Service を使用している場合は、クラスターのワーカー ノードの VM サイズが少なくとも **Standard_D8s_v3** であり、**Premium ディスク** を使用している必要があります。 
> * クラスターは複数の可用性ゾーンにまたがることはできません。 
> * 追加情報については、[既知の問題](./release-notes.md#known-issues)を参照してください。

選択するオプションに関わりなく、作成プロセス中に次の情報を指定する必要があります。

- **データ コントローラー名** - データ コントローラーのわかりやすい名前 (例:「production-dc」、「seattle-dc」など)。 名前は [Kubernetes 名前付け標準](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)を満たしている必要があります。
- **ユーザー名** - Kibana/Grafana 管理者ユーザーのユーザー名。
- **パスワード** - Kibana/Grafana 管理者ユーザーのパスワード。
- **Kubernetes 名前空間の名前** - データ コントローラーを作成する Kubernetes 名前空間の名前。
- **接続モード** - 接続モードによって、Azure Arc 対応データ サービス環境から Azure への接続の程度が決定されます。 関節接続モードが一般提供されています。 直接接続モードはプレビュー段階です。  選択した接続モードによって、デプロイ方法のオプションが決まります。  詳細については、[接続モード](./connectivity.md)に関するページを参照してください。
- **Azure サブスクリプション ID** - Azure 内のデータ コントローラー リソースを作成する場所の Azure サブスクリプション GUID。  すべての Azure Arc 対応 SQL Managed Instance および PostgreSQL Hyperscale サーバー グループもこのサブスクリプションで作成され、そのサブスクリプションに課金されます。
- **Azure リソース グループ名** - Azure 内のデータ コントローラー リソースを作成するリソース グループの名前。  すべての Azure Arc 対応 SQL Managed Instance および PostgreSQL Hyperscale サーバー グループもこのリソース グループで作成されます。
- **Azure の場所** - Azure でデータ コントローラー リソース メタデータが格納される Azure の場所。 利用可能なリージョンの一覧については、「[Azure グローバル インフラストラクチャ/リージョン別の製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)」を参照してください。 デプロイしているデータ コントローラーにより管理される Azure リソースに関するメタデータと課金情報は、場所パラメーターとして指定した Azure 内の場所だけに保存されます。 直接接続モードでデプロイしている場合、データ コントローラーの場所パラメーターは、対象とするカスタム場所リソースの場所と同じになります。
- **サービス プリンシパル情報** - [アップロードの前提条件](upload-metrics-and-logs-to-azure-monitor.md)に関する記事で説明されているように、"*直接*" 接続モードでデプロイする場合は、Azure Arc データ コントローラーの作成時にサービス プリンシパル情報が必要になります。 "*間接*" 接続モードでは、Azure Arc データ コントローラーを作成した後で、手動でエクスポートしてアップロードするためにサービス プリンシパルが必要になります。
- **インフラストラクチャ** - 課金のために、Azure Arc 対応データ サービスを実行しているインフラストラクチャを示す必要があります。  オプションには、`alibaba`、`aws`、`azure`、`gcp`、`onpremises`、または `other` があります。

## <a name="additional-concepts-for-direct-connected-mode"></a>直接接続モードの追加の概念

[接続モード](./connectivity.md)に関するページで説明されているように、Azure Arc データ コントローラーは **直接** または **間接** の接続モードでデプロイできます。 **直接** 接続モードで Azure Arc データ サービスをデプロイするには、いくつかの追加の概念と考慮事項を理解している必要があります。
まず、Azure Arc 対応データ サービスがデプロイされる Kubernetes クラスターは、[Azure Arc 対応 Kubernetes クラスター](../kubernetes/overview.md)である必要があります。 Kubernetes クラスターを Azure Arc にオンボードすると、使用状況情報、ログ、メトリックなどの自動アップロードといった機能に利用できる Azure 接続が提供されます。Kubernetes クラスターを Azure に接続することで、Azure portal からクラスターに直接 Azure Arc データ サービスをデプロイして管理することもできます。

Kubernetes クラスターを Azure に接続するには、次の手順を実行します。
- [お使いのクラスターを Azure に接続する](../kubernetes/quickstart-connect-cluster.md)

次に、Kubernetes クラスターが Azure Arc にオンボードされたら、次の手順を実行して、Azure Arc 対応データ サービスを Azure Arc 対応 Kubernetes クラスターにデプロイします。
- Arc データ サービス拡張機能を作成する。[クラスター拡張機能の詳細情報](../kubernetes/conceptual-extensions.md)
- カスタムの場所を作成する。[カスタムの場所の詳細情報](../kubernetes/conceptual-custom-locations.md)
- Azure Arc データ コントローラーを作成する

これら 3 つの手順はすべて、Azure portal の Azure Arc データ コントローラー作成ウィザードを使用して 1 回の手順で実行できます。

Azure Arc データ コントローラーをインストールしたら、Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale サーバー グループなどのデータ サービスを作成できます。


## <a name="next-steps"></a>次の手順

次のように、Azure Arc データ コントローラーを作成するためのオプションは複数あります。

> **試してみたい場合**
> Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。
>
- [Azure portal を使用して、直接接続モードでデータ コントローラーを作成する](create-data-controller-direct-prerequisites.md)
- [CLI を使用して間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-cli.md)
- [Azure Data Studio を使用して間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio で Jupyter ノートブックを使用して、データ コントローラーを Azure portal から間接接続モードで作成する](create-data-controller-indirect-azure-portal.md)
- [kubectl や oc などの Kubernetes ツールを使用して、間接接続モードでデータ コントローラーを作成する](create-data-controller-using-kubernetes-native-tools.md)
