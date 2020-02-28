---
title: AKS エンジン用 Azure Policy の概要
description: Azure Policy に Gatekeeper v3 の CustomResourceDefinitions と Open Policy Agent と AKS エンジンを使用してクラスターを管理する方法について説明します。
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75436434"
---
# <a name="understand-azure-policy-for-aks-engine"></a>AKS エンジン用 Azure Policy の概要

Azure Policy は、[AKS エンジン](https://github.com/Azure/aks-engine/blob/master/docs/README.md)と統合されています。これは、Azure 上のセルフマネージド Kubernetes クラスターをすばやくブートストラップする便利なツールが用意されているシステムです。 この統合により、AKS エンジンのセルフマネージド クラスターを使った一貫した一元的な方法で、大規模な適用と保護を実現できます。 Azure Policy では、Kubernetes 用の _アドミッション コントローラー Webhook_ である [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (ベータ) を拡張することで、Azure リソースと AKS エンジンのセルフマネージド クラスターのコンプライアンス状態を 1 か所から管理および報告できるようになります。

> [!NOTE]
> AKS エンジン用 Azure Policy はパブリック プレビュー段階であり、SLA はありません。 Gatekeeper v3 はベータ版であり、オープン ソース コミュニティによってサポートされています。 このサービスは、組み込みのポリシー定義と、サービス プリンシパルを使って構成されたリソース グループごとに 1 つの AKS エンジン クラスターのみをサポートします。

> [!IMPORTANT]
> AKS エンジン用 Azure Policy、AKS エンジン、Gatekeeper v3 のサポートを受けるには、AKS エンジンの GitHub リポジトリで[新しい問題](https://github.com/Azure/aks-engine/issues/new/choose)を作成します。

## <a name="overview"></a>概要

Azure 上のセルフマネージド Kubernetes クラスターで AKS エンジン用 Azure Policy を有効にして使用するには、次の操作を実行します。

- [前提条件](#prerequisites)
- [Azure Policy アドオンをインストールする](#installing-the-add-on)
- [AKS エンジン用のポリシー定義を割り当てる](#built-in-policies)
- [検証を待機する](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>前提条件

Azure Policy アドオンをインストールするか、このサービスの機能のいずれかを有効にする前に、お客様のサブスクリプションで **Microsoft.PolicyInsights** リソース プロバイダーを有効にし、クラスター サービス プリンシパルのロール割り当てを作成する必要があります。 

1. リソース プロバイダーを有効にするには、[リソース プロバイダーと種類](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)に関する記事の手順を実行するか、Azure CLI または Azure PowerShell コマンドを実行します。

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. クラスター サービス プリンシパルのロールの割り当てを作成する

   - クラスター サービス プリンシパル アプリ ID がわからない場合は、次のコマンドを使用して確認します。

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Azure CLI を使用して、'Policy Insights Data Writer (Preview)' ロールの割り当てをクラスター サービス プリンシパル アプリ ID (前の手順の値 _aadClientID_) に割り当てます。 `<subscriptionId>` をお使いのサブスクリプション ID に、`<aks engine cluster resource group>` を AKS エンジンのセルフマネージド Kubernetes クラスターが属するリソース グループに置き換えます。

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure Policy アドオン

Kubernetes 用の _Azure Policy アドオン_ を使って、Azure Policy サービスを Gatekeeper アドミッション コントローラーに接続します。 このアドオンは _kube-system_ 名前空間にインストールされ、次の機能が実行されます。

- AKS エンジン クラスターへの割り当てを Azure ポリシーで確認します
- ポリシーの詳細、制約テンプレート、および制約をダウンロードしてインストールします
- AKS エンジン クラスター上でフル スキャンのコンプライアンス チェックを実行します
- 監査およびコンプライアンスの詳細を Azure Policy に報告します

### <a name="installing-the-add-on"></a>アドオンのインストール

前提条件が満たすと、Azure Policy アドオンをインストールできるようになります。 インストールは、AKS エンジンの作成時または更新サイクル時に行うか、既存のクラスターに対して独立した操作として行うことができます。

- 作成または更新サイクル時にインストールする

  新しいセルフマネージド クラスターの作成時、または既存のクラスターに対する更新として Azure Policy アドオンを有効にするには、AKS エンジン用の **addons** プロパティ クラスター定義を含めます。

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  詳細については、外部ガイドの [AKS エンジン クラスター定義](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)を参照してください。

- Helm Chart を含む既存のクラスターにインストールする

  次の手順でクラスターを準備し、アドオンをインストールします。

  1. _gatekeeper-system_ 名前空間に対して Gatekeeper をインストールします。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. _kube-system_ に _control-plane_ ラベルを追加します。 このラベルには、Gatekeeper および Azure Policy アドオンによる _kube-system_ ポッドおよびサービスの監査は含まれません。

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Kubernetes データ (名前空間、ポッド、イングレス、サービス) を OPA と同期します。

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     詳細については、[OPA の「Replicating data (データのレプリケート)」](https://github.com/open-policy-agent/gatekeeper#replicating-data)を参照してください。

  1. Azure Policy リポジトリを Helm に追加します。

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     詳細については、[Helm Chart のクイックスタート ガイド](https://helm.sh/docs/using_helm/#quickstart-guide)のページを参照してください。

  1. Helm Chart を使用してアドオンをインストールします。 `<subscriptionId>` をお使いのサブスクリプション ID に、`<aks engine cluster resource group>` を AKS エンジンのセルフマネージド Kubernetes クラスターが属するリソース グループに置き換えます。

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Helm Chart によってインストールされるアドオンの詳細については、GitHub 上の [Azure Policy アドオンの Helm Chart 定義](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts)に関する記事を参照してください。

     > [!NOTE]
     > Azure Policy アドオンとリソース グループ ID の関係があるため、Azure Policy は各リソース グループに対して 1 つの AKS エンジン クラスターのみをサポートします。

アドオンが正常にインストールされていることと、_azure-policy_ ポッドが実行されていることを確認するには、次のコマンドを実行します。

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>検証および報告の頻度

アドオンは5 分おきに、ポリシーの割り当ての変更について Azure Policy でチェックインします。 この更新サイクル中に、アドオンによって変更が確認されます。 これらの変更によって、制約テンプレートと制約の作成、更新、または削除がトリガーされます。

> [!NOTE]
> _クラスター管理者_が制約テンプレートや制約を変更するアクセス許可を持っているとしても、Azure Policy によって作成された制約テンプレートや制約を変更することは推奨されません。またサポートされていません。 手動で行ったすべての変更は、更新サイクル中に失われます。

アドオンは 5 分ごとにクラスターのフル スキャンを呼び出します。 アドオンを使うと、フル スキャンの詳細情報と、クラスターに試行された変更についての Gatekeeper によるすべてのリアルタイム評価が収集された後、すべての Azure Policy 割り当てと同様に、[[ポリシー準拠状況の詳細]](../how-to/get-compliance-data.md) に含めるために、結果が Azure Policy に報告されます。 アクティブなポリシー割り当ての結果のみが監査サイクル中に返されます。 監査結果は、失敗した制約の状態フィールドに違反と示されることもあります。

## <a name="policy-language"></a>ポリシーの言語

AKS エンジンを管理するための Azure Policy 言語構造は、既存のポリシーのものに従います。 効果 _EnforceOPAConstraint_ は、AKS エンジン クラスターの管理に使用されます。また、[OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) と Gatekeeper v3 での作業に固有の _details_ プロパティを受け取ります。 詳細と例については、[EnforceOPAConstraint](effects.md#enforceopaconstraint) 効果に関する記事を参照してください。

Azure Policy からはアドオンに対して、ポリシー定義の _details.constraintTemplate_ および _details.constraint_ プロパティの一部として [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) の URI が渡されます。 Rego は、Kubernetes クラスターへの要求を検証するために OPA および Gatekeeper がサポートする言語です。 Azure Policy は、Kubernetes 管理のための既存の標準をサポートすることによって、既存の規則を再利用し、これらを Azure Policy とペアにすることで、統合されたクラウド コンプライアンス レポート体験を実現します。 詳しくは、「[Rego とは](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)」を参照してください。

## <a name="built-in-policies"></a>組み込みのポリシー

Azure portal を使用して AKS エンジン クラスターを管理する組み込みのポリシーを確認するには、次の手順を実行します。

1. Azure portal で Azure Policy サービスを開始します。 左側のウィンドウで **[すべてのサービス]** を選択し、 **[ポリシー]** を検索して選択します。

1. Azure Policy ページの左側のウィンドウで、 **[定義]** を選択します。

1. カテゴリ ドロップダウン リスト ボックスから、 **[すべて選択]** を使用してフィルターをクリアし、 **[Kubernetes]** を選択します。

1. ポリシー定義を選択し、 **[割り当てる]** ボタンを選択します。

> [!NOTE]
> AKS エンジン用 Azure Policy の定義を割り当てる場合、 **[スコープ]** は、AKS エンジン クラスターのリソース グループにする必要があります。

あるいは、「[ポリシーの割り当て - ポータル](../assign-policy-portal.md)」クイックスタートを使用して、AKS エンジン ポリシーを見つけて割り当てます。 サンプルの 'audit vm' ではなく、AKS エンジンポリシー定義を検索します。

> [!IMPORTANT]
> **Kubernetes** カテゴリの組み込みポリシーは、AKS エンジンでのみ使用できます。

## <a name="logging"></a>ログ記録

### <a name="azure-policy-add-on-logs"></a>Azure Policy アドオンのログ

Azure Policy アドオンは、Kubernetes のコントローラー/コンテナーとして、AKS エンジン クラスター内にログを保持します。

Azure Policy アドオンのログを表示するには、`kubectl` を使用します。

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gatekeeper のログ

Gatekeeper ポッドである _gatekeeper-controller-manager-0_ は、通常、`gatekeeper-system` または `kube-system` 名前空間に属しますが、デプロイ方法によっては別の名前空間に属する可能性があります。

Gatekeeper のログを表示するには、`kubectl` を使用します。

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

詳細については、OPA ドキュメントの [Gatekeeper のデバッグ](https://github.com/open-policy-agent/gatekeeper#debugging)に関する記事を参照してください。

## <a name="remove-the-add-on"></a>アドオンを削除する

AKS エンジン クラスターから Azure Policy アドオンと Gatekeeper を削除するには、アドオンのインストール方法に合わせたメソッドを使用します。

- AKS エンジンのクラスター定義で **addons** プロパティを設定してインストールした場合:

  _azure-policy_ の **addons** プロパティを false に変更した後で、クラスター定義を AKS エンジンに再デプロイします。


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Helm Chart と共にインストールした場合:

  1. 古い制約を削除する

     現在のアンインストール メカニズムでは、Gatekeeper システムのみが削除されます。ユーザーが作成した _ConstraintTemplate_、_Constraint_、または _Config_ リソースは削除されません。また、付随する _CRD_ を削除されません。

     Gatekeeper を実行中の場合は、次の方法で不要な制約を削除できます。

     - 制約リソースのすべてのインスタンスの削除
     - _ConstraintTemplate_ リソースを削除すると、_CRD_ は自動的にクリーンアップされます
     - _Config_ リソースを削除すると、同期対象のリソース上のファイナライザーが削除されます

  1. Azure Policy アドオンをアンインストールする
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Gatekeeper をアンインストールする
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
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