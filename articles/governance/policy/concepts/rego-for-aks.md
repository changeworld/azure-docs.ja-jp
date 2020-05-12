---
title: Azure Kubernetes Service に対する Azure Policy について学習する
description: Azure Policy が Rego および Open Policy Agent を使用して、Azure Kubernetes Service 上でクラスターを管理する方法について説明します。
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: f6c70d676914cf861ecc378efc4ec23a78879f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187717"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Azure Kubernetes Service に対する Azure Policy の理解

Azure Policy は [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) と統合することにより、一元的な一貫性のある方法でクラスターを大規模に適用して保護できます。
[Gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2 ([Open Policy Agent](https://www.openpolicyagent.org/) (OPA) のための_アドミッション コントローラー Webhook_) の使用を拡張することにより、Azure Policy では Azure リソースおよび AKS クラスターのコンプライアンスの状態を 1 つの場所から管理および報告することが可能になります。

> [!NOTE]
> AKS 用の Azure Policy は限定プレビューで、組み込みのポリシー定義のみをサポートします。

## <a name="overview"></a>概要

AKS クラスターで AKS 用の Azure Policy を有効にして使用するには、次の操作を実行します。

- [プレビュー機能のためのオプトイン](#opt-in-for-preview)
- [Azure Policy アドオンをインストールする](#installation-steps)
- [AKS のポリシー定義を割り当てる](#built-in-policies)
- [検証を待機する](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>プレビューのためのオプトイン

Azure Policy アドオンをインストールするか、いずれかのサービス機能を有効にする前に、サブスクリプションは **Microsoft.ContainerService** リソース プロバイダーと **Microsoft.PolicyInsights** リソース プロバイダーを有効にする必要があり、その後プレビューへの参加が承認されるようにする必要があります。 プレビューに参加するには、Azure portal または Azure CLI のいずれかで次の手順を実行します。

- Azure portal:

  1. **Microsoft.ContainerService** と **Microsoft.PolicyInsights** リソース プロバイダーに登録します。 手順については、「[リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)」を参照してください。

  1. Azure portal 上で **[すべてのサービス]** をクリックし、 **[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="[すべてのサービス] で [ポリシー] を検索する" border="false":::

  1. Azure Policy ページの左側にある **[プレビューに参加する]** を選択します。

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="AKS プレビューのポリシーに参加する" border="false":::

  1. プレビューに追加するサブスクリプションの行を選択します。

  1. サブスクリプションの一覧の上部にある **[オプトイン]** ボタンを選択します。

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Azure Policy アドオン

Kubernetes 用の _Azure Policy アドオン_ を使って、Azure Policy サービスを Gatekeeper アドミッション コントローラーに接続します。 _azure-policy_ 名前空間にインストールされるこのアドオンは、次の機能を実行します。

- Azure Policy で AKS クラスターへの割り当てをチェックします
- _rego_ ポリシー定義などのポリシー詳細をダウンロードし、**configmaps** としてキャッシュします
- AKS クラスターでフル スキャンのコンプライアンス チェックを実行します
- 監査およびコンプライアンスの詳細を Azure Policy に報告します

### <a name="installing-the-add-on"></a>アドオンのインストール

#### <a name="prerequisites"></a>前提条件

AKS クラスターにアドオンをインストールする前に、プレビュー拡張機能をインストールする必要があります。 この手順は Azure CLI で実行されます。

1. Azure CLI バージョン 2.0.62 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

1. AKS クラスターは、バージョン _1.10_ 以上である必要があります。 AKS クラスターのバージョンを検証するには、次のスクリプトを使用します。

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. AKS 用の Azure CLI プレビュー拡張機能 `aks-preview` のバージョン _0.4.0_ をインストールします。

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > 以前に _aks-preview_ 拡張機能をインストール済みの場合は、`az extension update --name aks-preview` コマンドを使用して、すべての更新プログラムをインストールします。

#### <a name="installation-steps"></a>インストール手順

前提条件が完了したら、管理する AKS クラスターに Azure Policy アドオンをインストールします。

- Azure portal

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. お使いのいずれかの AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="AKS クラスターからのポリシー" border="false":::

  1. メイン ページで、 **[アドオンを有効にする]** ボタンを選択します。

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="AKS 用の Azure Policy アドオンを有効にする" border="false":::

     > [!NOTE]
     > **[アドオンを有効にする]** ボタンが淡色表示されている場合、サブスクリプションはまだプレビューに追加されていません。 必要な手順については「[プレビューのためのオプトイン](#opt-in-for-preview)」を参照してください。

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>検証および報告の頻度

アドオンは5 分おきに、ポリシーの割り当ての変更について Azure Policy でチェックインします。 この更新サイクル中に、アドオンによって _azure-policy_ 名前空間のすべての _configmaps_ が削除され、Gatekeeper で使用される _configmaps_ が再作成されます。

> [!NOTE]
> _クラスター管理者_は _azure-policy_ 名前空間に対する権限を持つ場合がありますが、名前空間を変更することは推奨されず、サポートもされません。 手動で行ったすべての変更は、更新サイクル中に失われます。

アドオンは 5 分ごとにクラスターのフル スキャンを呼び出します。 アドオンを使うと、フル スキャンの詳細情報と、クラスターに試行された変更についての Gatekeeper によるすべてのリアルタイム評価が収集された後、すべての Azure Policy 割り当てと同様に、[[ポリシー準拠状況の詳細]](../how-to/get-compliance-data.md) に含めるために、結果が Azure Policy に報告されます。 アクティブなポリシー割り当ての結果のみが監査サイクル中に返されます。

## <a name="policy-language"></a>ポリシーの言語

AKS を管理するための Azure Policy 言語構造は、既存のポリシーのものに従います。 _EnforceRegoPolicy_ 効果が AKS クラスターを管理するために使用され、OPA および Gatekeeper v2 の操作に固有の _details_ プロパティが使用されます。 詳細と例については、[EnforceRegoPolicy](effects.md#enforceregopolicy) 効果を参照してください。

ポリシー定義の _details.policy_ プロパティの一部として、Azure Policy は rego ポリシーの URI をアドオンに渡します。 Rego は、Kubernetes クラスターへの要求を検証または変更するために、OPA および Gatekeeper でサポートされる言語です。 Azure Policy は、Kubernetes 管理のための既存の標準をサポートすることによって、既存の規則を再利用し、これらを Azure Policy とペアにすることで、統合されたクラウド コンプライアンス レポート体験を実現します。 詳しくは、「[Rego とは](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)」を参照してください。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure portal を使用して AKS を管理するための組み込みのポリシーを検索するには、次の手順に従います。

1. Azure portal で Azure Policy サービスを開始します。 左側のウィンドウで **[すべてのサービス]** を選択し、 **[ポリシー]** を検索して選択します。

1. Azure Policy ページの左側のウィンドウで、 **[定義]** を選択します。

1. カテゴリ ドロップダウン リスト ボックスから、 **[すべて選択]** を使用してフィルターをクリアし、 **[Kubernetes サービス]** を選択します。

1. ポリシー定義を選択し、 **[割り当てる]** ボタンを選択します。

> [!NOTE]
> AKS 用の Azure Policy 定義を割り当てるとき、 **[スコープ]** に AKS クラスター リソースを含める必要があります。

あるいは、「[ポリシーの割り当て - ポータル](../assign-policy-portal.md)」クイックスタートを使用して、AKS ポリシーを見つけて割り当てます。 サンプルの 'audit vms' ではなく、Kubernetes ポリシー定義を検索します。

> [!IMPORTANT]
> **Kubernetes サービス** カテゴリの組み込みポリシーは、AKS でのみ使用できます。

## <a name="logging"></a>ログ記録

### <a name="azure-policy-add-on-logs"></a>Azure Policy アドオンのログ

Azure Policy アドオンは、Kubernetes のコント ローラー/コンテナーとして、AKS クラスター内にログを保持します。 このログは AKS クラスターの **[Insights]** ページに公開されます。 詳しくは、「[コンテナーの Azure Monitor を使用して AKS クラスターのパフォーマンスを把握する](../../../azure-monitor/insights/container-insights-analyze.md)」をご覧ください。

### <a name="gatekeeper-logs"></a>Gatekeeper のログ

新規リソース要求についての Gatekeeper ログを有効にするには、「[Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー](../../../aks/view-master-logs.md)」の手順に従います。
新規リソース要求に関して拒否されたイベントを表示するためのクエリの例を以下に示します。

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Gatekeeper コンテナーからログを表示するには、「[Azure Kubernetes Service (AKS) での Kubernetes マスター ノード ログの有効化とレビュー](../../../aks/view-master-logs.md)」の手順に従い、 **[診断設定]** ウィンドウで _kube-apiserver_ オプションを確認します。

## <a name="remove-the-add-on"></a>アドオンを削除する

AKS クラスターから Azure Policy アドオンを削除するには、Azure portal または Azure CLI のいずれかを使用します。

- Azure portal

  1. Azure portal で **[すべてのサービス]** をクリックし、 **[Kubernetes サービス]** を検索して選択することによって AKS サービスを起動します。

  1. Azure Policy アドオンを無効にする AKS クラスターを選択します。

  1. [Kubernetes サービス] ページの左側の **[ポリシー (プレビュー)]** を選択します。

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="AKS クラスターからのポリシー" border="false":::

  1. メイン ページで、 **[アドオンを無効にする]** ボタンを選択します。

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="AKS 用の Azure Policy アドオンを無効にする" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Azure Policy アドオンによって収集される診断データ

Kubernetes の Azure Policy アドオンを使うと、制限されたクラスター診断データが収集されます。 この診断データは、ソフトウェアとパフォーマンスに関連する重要な技術データです。 これは、次の方法で使用されます。

- Azure Policy アドオンを最新の状態に維持する
- Azure Policy アドオンをセキュリティで保護し、信頼性が高く、パフォーマンスに優れた状態に維持する
- アドオンの使用の集計分析を通じて Azure Policy アドオンを改善する

アドオンによって収集された情報は、個人データではありません。 現在、次の詳細情報が収集されています。

- Azure Policy アドオン エージェントのバージョン
- クラスターの種類
- クラスターのリージョン
- クラスターのリソース グループ
- クラスターのリソース ID
- クラスターのサブスクリプション ID
- クラスターの OS (例:Linux)
- クラスターの市区町村 (例:シアトル)
- クラスターの州または都道府県 (例:ワシントン)
- クラスターの国または地域 (例:米国)
- ポリシー評価でのエージェントのインストール中に Azure Policy アドオンによって発生した例外/エラー
- Azure Policy アドオンによってインストールされていない Gatekeeper ポリシーの数

## <a name="next-steps"></a>次のステップ

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- [ポリシー定義の構造](definition-structure.md)を確認します。
- 「[Policy の効果について](effects.md)」を確認します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。