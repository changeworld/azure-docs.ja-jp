---
title: Azure Arc 対応データ サービスのデプロイを計画する
description: この記事では、Azure Arc 対応データ サービスのデプロイを計画する際の考慮事項について説明します。
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 15688b89b319b884707e9e6d8faa9a13de3233e0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563342"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Azure Arc 対応データ サービスのデプロイを計画する

この記事では、Azure Arc 対応データ サービスのデプロイを計画する方法について説明します。

Azure Arc 対応データ サービスのデプロイを計画するにあたっては、データベース ワークロードとそれらのワークロードのビジネス要件を正しく理解することが重要です。 たとえば、可用性、ビジネス継続性、これらのワークロードに対するメモリ、CPU、ストレージの容量要件などを検討する必要があります。 また、ビジネス要件に基づいて、これらのデータベース ワークロードをサポートするインフラストラクチャを慎重に準備する必要があります。

## <a name="prerequisites"></a>前提条件

Azure Arc 対応データ サービスをデプロイする前に、前提条件を満たしていること、必要な背景や情報が準備できていることを確認してください。 デプロイを成功させるには、インフラストラクチャ環境を適切なレベルのアクセスと、ストレージ、CPU、メモリの適切な容量で適切に構成する必要があります。 

以下の記事を確認してください。

- [サイズ設定のガイダンス](sizing-guidance.md)
- [ストレージの構成](storage-configuration.md)
- [接続モードとその要件に関するページ](connectivity.md)

次のことを確認します。

- [`arcdata` CLI 拡張子](install-arcdata-extension.md) がインストールされている。
- 他の[クライアント ツール](install-client-tools.md)がインストールされている。
- Kubernetes クラスターにアクセスできる。
- *kubeconfig* ファイルが構成されている。 デプロイ先の Kubernetes クラスターがポイントされている必要があります。 クラスターの現在のコンテキストを確認するには、次のコマンドを実行します。
   ```console
   kubectl cluster-info
   ```  
- Azure Arc データ コントローラー、Azure Arc 対応 SQL Managed Instance、Azure Arc 対応 PostgreSQL Hyperscale サーバーなどのリソースが予測され、請求される Azure サブスクリプションがある。

インフラストラクチャの準備ができたら、次の方法で Azure Arc 対応データ サービスをデプロイします。
1. Kubernetes クラスターの検証済みディストリビューションのいずれかで Azure Arc 対応データ コントローラーを作成します。
1. Azure Arc 対応 SQL Managed Instance または Azure Arc 対応 PostgreSQL Hyperscale サーバー グループ、あるいはその両方を作成します。

> [!CAUTION]
> 一部のデータ サービス レベルとモードは[一般提供 (GA)](release-notes.md)段階であり、一部はプレビュー段階です。 同じデータ コントローラーで GA サービスとプレビュー サービスを混在させないことをお勧めします。 同じデータ コントローラーで GA サービスとプレビュー サービスを混在させている場合、インプレース アップグレードはできません。 このシナリオでは、アップグレードするときに、データ コントローラーとデータ サービスをいったん削除してから再作成する必要があります。

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>概要: Azure Arc 対応データ コントローラーを作成する

Azure Arc 対応データ サービスは、さまざまな種類の Kubernetes クラスターで作成でき、マネージド Kubernetes サービスは、さまざまな方法を使用して作成できます。

現時点で検証されている Kubernetes サービスとディストリビューションの一覧は次のとおりです。

- Amazon Elastic Kubernetes Service (Amazon EKS)
- Azure Kubernetes Service (AKS)
- Azure Stack HCI の Azure Kubernetes Service
- Azure Red Hat OpenShift
- Google Kubernetes Engine (GKE)
- オープンソースのアップストリーム Kubernetes (通常、Kubeadm を使用してデプロイされる)
- OpenShift Container Platform (OCP)

> [!IMPORTANT]
> * Kubernetes のサポートされている最小バージョンは 1.19 です。 詳細については、「[リリース ノート&nbsp; - Azure Arc 対応データ サービス](./release-notes.md#known-issues)」の「既知の問題」セクションを参照してください。
> * サポートされる OCP の最小バージョンは 4.7 です。
> * Azure Kubernetes Service を使用している場合は、クラスターのワーカー ノードの仮想マシン (VM) サイズが少なくとも Standard_D8s_v3 であり、Premium ディスクを使用している必要があります。 
> * クラスターは複数の可用性ゾーンにまたがることはできません。 
> * 詳細については、「[リリース ノート&nbsp; - Azure Arc 対応データ サービス](./release-notes.md#known-issues)」の「既知の問題」セクションを参照してください。

Azure Arc 対応データ サービスを作成する場合、選択したサービスまたはディストリビューション オプションに関係なく、次の情報を提供する必要があります。

- **データ コントローラー名**: データ コントローラーのわかりやすい名前 (例: *production-dc*、*seattle-dc* など)。 名前は [Kubernetes 名前付け標準](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)を満たしている必要があります。

- **ユーザー名**: Kibana または Grafana 管理者ユーザーのユーザー名。
- **パスワード**: Kibana または Grafana 管理者ユーザーのパスワード。
- **Kubernetes 名前空間の名前**: データ コントローラーを作成する Kubernetes 名前空間の名前。
- **接続モード**: Azure Arc 対応データ サービス環境から Azure への接続の程度が決定されます。 *間接* 接続モードは一般提供されており、*直接* 接続モードはプレビュー段階です。 選択した接続モードによって、デプロイ方法のオプションが決まります。 詳細については、「[接続モードと要件](./connectivity.md)」を参照してください。
- **Azure サブスクリプション ID**: Azure 内のデータ コントローラー リソースを作成する場所の Azure サブスクリプション GUID。 すべての Azure Arc 対応 SQL Managed Instance および Azure Database for PostgreSQL Hyperscale サーバー グループも、このサブスクリプションで作成され、そのサブスクリプションに課金されます。
- **Azure リソース グループ名**: Azure 内のデータ コントローラー リソースを作成するリソース グループの名前。 すべての Azure Arc 対応 SQL Managed Instance および Azure Database for PostgreSQL Hyperscale サーバー グループも、このリソース グループで作成されます。
- **Azure の場所**: Azure でデータ コントローラー リソースのメタデータが格納される Azure の場所。 利用可能なリージョンの一覧については、Azure グローバル インフラストラクチャの「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)」ページを参照してください。 デプロイされたデータ コントローラーによって管理される Azure リソースに関するメタデータと課金情報は、場所パラメーターとして指定した Azure 内の場所だけに保存されます。 直接接続モードでデプロイしている場合、データ コントローラーの場所パラメーターは、対象とするカスタム場所リソースの場所と同じになります。
- **サービス プリンシパル情報**: 
   - **間接** 接続モードでデプロイする場合は、使用状況とメトリック データをアップロードするためにサービス プリンシパル情報が必要です。 詳細については、「[使用状況データ、メトリック、およびログを Azure にアップロードする](upload-metrics-and-logs-to-azure-monitor.md)」の「サービス プリンシパルにロールを割り当てる」セクションを参照してください。 

- **インフラストラクチャ**: 課金のために、Azure Arc 対応データ サービスを実行しているインフラストラクチャを示す必要があります。 オプションは次のとおりです。
- `alibaba`
- `aws`
- `azure`
- `gcp`
- `onpremises`
- `other`

## <a name="additional-concepts-for-direct-connectivity-mode"></a>直接接続モードの追加の概念

「[接続モードと要件](./connectivity.md)」で説明されているように、Azure Arc データ コントローラーは **直接** または **間接** の接続モードでデプロイできます。 直接接続モードで Azure Arc データ サービスをデプロイするには、さらにいくつかの概念と考慮事項を理解している必要があります。

* まず、Azure Arc 対応データ サービスがデプロイされる Kubernetes クラスターは、[Azure Arc 対応 Kubernetes クラスター](../kubernetes/overview.md)である必要があります。 Kubernetes クラスターを Azure に接続することで、Azure portal から直接クラスターに Azure Arc データ サービスをデプロイして管理したり、使用状況、ログ、およびメトリックを Azure に自動的にアップロードしたり、その他の Azure のメリットを活用したりすることができます。 方法については、[Azure へのクラスターの接続](../kubernetes/quickstart-connect-cluster.md)に関するページを参照してください。

* Kubernetes クラスターで Azure Arc が有効になったら、以下を実行して、Azure Arc 対応のデータ サービスをデプロイします。
   1. Azure Arc データ サービス拡張機能を作成します。 方法については、「[Azure Arc 対応 Kubernetes のクラスター拡張機能](../kubernetes/conceptual-extensions.md)」を参照してください。
   1. カスタムの場所を作成する。 方法については、「[Azure Arc 対応 Kubernetes でのカスタムの場所](../kubernetes/conceptual-custom-locations.md)」を参照してください。
   1. Azure Arc データ コントローラーを作成します。

   これら 3 つの手順はすべて、Azure portal の Azure Arc データ コントローラー作成ウィザードを使用して 1 回の手順で実行できます。

Azure Arc データ コントローラーをインストールしたら、Azure Arc 対応 SQL Managed Instance や Azure Arc 対応 PostgreSQL Hyperscale などのデータ サービスを作成して、アクセスできます。


## <a name="next-steps"></a>次のステップ

Azure Arc データ コントローラーを作成するための追加オプションがいくつかあります。

> **試してみたい場合**
> AKS、Amazon EKS、または GKE 上、または Azure VM 内で [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) をすぐに開始できます。
>
- [Azure portal を使用して、直接接続モードでデータ コントローラーを作成する](create-data-controller-direct-prerequisites.md)
- [CLI を使用して、間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-cli.md)
- [Azure Data Studio を使用して、間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio で Jupyter ノートブックを使用して、Azure portal から間接接続モードでデータ コントローラーを作成する](create-data-controller-indirect-azure-portal.md)
- [kubectl や oc などの Kubernetes ツールを使用して、間接接続モードでデータ コントローラーを作成する](create-data-controller-using-kubernetes-native-tools.md)
