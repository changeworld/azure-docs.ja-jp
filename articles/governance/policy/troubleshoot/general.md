---
title: 一般的なエラーのトラブルシューティング
description: ポリシー定義、さまざまな SDK、および Kubernetes のアドオンの作成に関する問題をトラブルシューティングする方法について説明します。
ms.date: 12/01/2020
ms.topic: troubleshooting
ms.openlocfilehash: b88d00575adb571c59b562d25067c4a1716fb50f
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882978"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Azure Policy を使用してエラーを解決する

ポリシー定義を作成したり、SDK を使用したり、[Azure Policy for Kubernetes](../concepts/policy-for-kubernetes.md) を設定したりすると、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまな一般的なエラーと、その解決方法について説明します。

## <a name="finding-error-details"></a>エラー詳細の検索

エラーの詳細の場所は、エラーの原因となったアクションによって異なります。

- カスタム ポリシーを使用する場合は、Azure portal で試して、リンティングによるスキーマに関するフィードバックを取得するか、結果の[コンプライアンス データ](../how-to/get-compliance-data.md)を確認して、リソースがどのように評価されたかを確認してください。
- さまざまな SDK を使用する場合、SDK では、関数が失敗した理由に関する詳細が提供されます。
- Kubernetes のアドオンを使用する場合は、クラスター内の[ログ](../concepts/policy-for-kubernetes.md#logging)から開始します。

## <a name="general-errors"></a>一般エラー

### <a name="scenario-alias-not-found"></a>シナリオ:エイリアスが見つからない

#### <a name="issue"></a>問題

Azure Policy が[エイリアス](../concepts/definition-structure.md#aliases)を使用して、Azure Resource Manager のプロパティにマップしています。

#### <a name="cause"></a>原因

無効または存在しないエイリアスがポリシー定義で使用されています。

#### <a name="resolution"></a>解決策

まず、Resource Manager プロパティにエイリアスがあることを確認します。 [Visual Studio Code 用の Azure Policy 拡張機能](../how-to/extension-for-vscode.md)、または SDK を使用して、使用可能なエイリアスを検索します。 Resource Manager プロパティのエイリアスが存在しない場合は、サポート チケットを作成します。

### <a name="scenario-evaluation-details-not-up-to-date"></a>シナリオ:評価の詳細が最新ではない

#### <a name="issue"></a>問題

リソースが "未開始" 状態であるか、またはコンプライアンスの詳細が最新ではありません。

#### <a name="cause"></a>原因

新しいポリシーまたはイニシアチブの割り当ては、適用されるまでに約 30 分かかります。 既存の割り当てのスコープ内の新規または更新されたリソースは、15 分後に使用できるようになります。 標準のコンプライアンス スキャンは、24 時間ごとに行われます。 詳細については、「[評価のトリガー](../how-to/get-compliance-data.md#evaluation-triggers)」を参照してください。

#### <a name="resolution"></a>解決策

まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。

### <a name="scenario-compliance-not-as-expected"></a>シナリオ:コンプライアンスが想定どおりでない

#### <a name="issue"></a>問題

リソースの評価状態が、そのリソースに対して想定されている _Compliant_ または _Not-Compliant_ のいずれかではありません。

#### <a name="cause"></a>原因

リソースがポリシー割り当ての正しいスコープに含まれていないか、ポリシー定義が意図したとおりに動作していません。

#### <a name="resolution"></a>解像度

ポリシー定義をトラブルシューティングするには、次の手順を実行します。

1. まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。
1. 割り当てパラメーターと割り当てスコープが正しく設定されていることを確認します。
1. [ポリシー定義モード](../concepts/definition-structure.md#mode)を確認します。
   - すべてのリソースの種類の場合は モード "すべて"。
   - ポリシー定義によってタグまたは場所を確認する場合はモード "インデックス付き"。
1. リソースのスコープが[除外対象](../concepts/assignment-structure.md#excluded-scopes)または[適用除外対象](../concepts/exemption-structure.md)でないことを確認します。
1. ポリシー割り当ての準拠に `0/0` リソースが表示されている場合は、割り当てスコープ内で適用できると判断されたリソースはありません。 ポリシー定義と割り当てスコープの両方を確認してください。
1. 対応していることが予期されていた非対応のリソースについては、[非準拠である理由の特定](../how-to/determine-non-compliance.md)に関するページを参照してください。 定義と評価されたプロパティ値を比較することで、リソースが非準拠である理由がわかります。
   - **対象の値** が間違っている場合は、ポリシー定義を修正します。
   - **現在の値** が間違っている場合は、`resources.azure.com` を介してリソース ペイロードを検証します。
1. その他の一般的な問題と解決策については、[「適用が想定どおりでない」のトラブルシューティング](#scenario-enforcement-not-as-expected)を参照してください。

複製してカスタマイズした組み込みのポリシー定義またはカスタム定義で引き続き問題が発生する場合は、問題が適切に転送されるように、"**ポリシーの作成**" についてサポート チケットを作成してください。

### <a name="scenario-enforcement-not-as-expected"></a>シナリオ:適用が想定どおりでない

#### <a name="issue"></a>問題

Azure Policy によって処理される予定のリソースが処理されず、[Azure アクティビティ ログ](../../../azure-monitor/platform/platform-logs-overview.md)にエントリがありません。

#### <a name="cause"></a>原因

ポリシー割り当ては _[Disabled]_ の [enforcementMode](../concepts/assignment-structure.md#enforcement-mode) に対して構成されています。 強制モードが無効になっていると、ポリシー効果は適用されず、アクティビティ ログにエントリはありません。

#### <a name="resolution"></a>解決策

ポリシー割り当ての適用をトラブルシューティングするには、次の手順を実行します。

1. まず、評価が完了して Azure portal または SDK でコンプライアンスの結果を利用できるようになるまで、適切な時間待機します。 Azure PowerShell または REST API を使用して新しい評価スキャンを開始するには「[オンデマンドの評価スキャン](../how-to/get-compliance-data.md#on-demand-evaluation-scan)」を参照してください。
1. 割り当てパラメーターと割り当てスコープが正しく設定されていること、**enforcementMode** が _Enabled_ であることを確認します。
1. [ポリシー定義モード](../concepts/definition-structure.md#mode)を確認します。
   - すべてのリソースの種類の場合は モード "すべて"。
   - ポリシー定義によってタグまたは場所を確認する場合はモード "インデックス付き"。
1. リソースのスコープが[除外対象](../concepts/assignment-structure.md#excluded-scopes)または[適用除外対象](../concepts/exemption-structure.md)でないことを確認します。
1. リソースのペイロードがポリシー ロジックと一致することを確認します。 これを行うには、[HAR トレース](../../../azure-portal/capture-browser-trace.md)をキャプチャするか、ARM テンプレートのプロパティを確認します。
1. その他の一般的な問題と解決策については、[「コンプライアンスが想定どおりでない」のトラブルシューティング](#scenario-compliance-not-as-expected)を参照してください。

複製してカスタマイズした組み込みのポリシー定義またはカスタム定義で引き続き問題が発生する場合は、問題が適切に転送されるように、"**ポリシーの作成**" についてサポート チケットを作成してください。

### <a name="scenario-denied-by-azure-policy"></a>シナリオ:Azure Policy による拒否

#### <a name="issue"></a>問題

リソースの作成または更新が拒否されました。

#### <a name="cause"></a>原因

新しいリソースまたは更新されたリソースが存在するスコープへのポリシー割り当てが、[Deny](../concepts/effects.md#deny) 効果が適用されているポリシー定義の条件を満たしています。 これらの定義を満たしているリソースは、作成または更新できません。

#### <a name="resolution"></a>解決策

拒否されたポリシー割り当てのエラー メッセージには、ポリシー定義とポリシー割り当て ID が含まれています。 メッセージ内のエラー情報が欠落している場合は、[アクティビティ ログ](../../../azure-monitor/platform/activity-log.md#view-the-activity-log)でも確認できます。 この情報を使用して、リソースの制限を理解し、許可された値に一致するように要求のリソース プロパティを調整します。

## <a name="template-errors"></a>テンプレート エラー

### <a name="scenario-policy-supported-functions-processed-by-template"></a>シナリオ:ポリシーでサポートされる関数がテンプレートによって処理される

#### <a name="issue"></a>問題

Azure Policy は、Azure Resource Manager テンプレート (ARM テンプレート) の関数とポリシー定義でのみ使用できる関数を多数サポートしています。 Resource Manager は、ポリシー定義の一部としてではなく、デプロイの一部としてこれらの機能を処理します。

#### <a name="cause"></a>原因

`parameter()` や `resourceGroup()` などのサポートされている関数を使用すると、ポリシー定義および Azure Policy エンジンが処理する関数を残す代わりに、デプロイメント時に関数の処理結果が得られます。

#### <a name="resolution"></a>解決策

関数をポリシー定義の一部として渡すには、プロパティが `[[resourceGroup().tags.myTag]` のようになるように `[` で文字列全体をエスケープします。 エスケープ文字により、Resource Manager によってテンプレートが処理されるときに値が文字列として扱われます。 その後 Azure Policy は関数をポリシー定義に配置し、予期したとおりに動的になるようにします。 詳細については、「[Azure Resource Manager テンプレートの構文と式](../../../azure-resource-manager/templates/template-expressions.md)」を参照してください。

## <a name="add-on-for-kubernetes-installation-errors"></a>Kubernetes インストール エラー用アドオン

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>シナリオ:パスワードによる Helm Chart を使用したインストールの失敗

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが、次のいずれかのメッセージで失敗します。

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>原因

生成されたパスワードに、Helm Chart では分割されるコンマ (`,`) が含まれています。

#### <a name="resolution"></a>解決策

`helm install azure-policy-addon` を実行する場合は、バックスラッシュ (`\`) を使用してパスワード値のコンマ (`,`) をエスケープします。

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>シナリオ:名前が既に存在するため、Helm Chart を使用したインストールが失敗する

#### <a name="issue"></a>問題

`helm install azure-policy-addon` コマンドが失敗し、次のメッセージが表示されます。

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>原因

`azure-policy-addon` という名前の Helm Chart は、既にインストールされているか、部分的にインストールされています。

#### <a name="resolution"></a>解決策

[Kubernetes アドオンの Azure Policy を削除](../concepts/policy-for-kubernetes.md#remove-the-add-on)する指示に従ってから、`helm install azure-policy-addon` コマンドを再実行します。

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>シナリオ:Azure 仮想マシンのユーザー割り当て ID がシステム割り当てマネージド ID に置き換えられる

#### <a name="issue"></a>問題

マシン内の監査設定にゲスト構成ポリシーのイニシアチブを割り当てると、マシンに割り当てられていたユーザー割り当てマネージド ID は割り当てられなくなります。 システム割り当てマネージド ID のみが割り当てられます。

#### <a name="cause"></a>原因

以前にゲスト構成の DeployIfNotExists 定義で使用されていたポリシー定義により、システム割り当て ID がマシンに割り当てられるだけでなく、ユーザー割り当て ID の割り当ても削除されました。

#### <a name="resolution"></a>解像度

以前にこの問題の原因となった定義は、"\[非推奨\]" と表示され、ユーザー割り当てマネージド ID は削除されず、前提条件を管理するポリシー定義に置き換えられます。 手動操作が必要です。 "\[非推奨\]" とマークされている既存のポリシー割り当てをすべて削除し、元のポリシーと同じ名前の更新された前提条件ポリシー イニシアチブとポリシー定義に置き換えてください。

詳細については、次のブログ投稿を参照してください。

[ゲスト構成の監査ポリシーに関してリリースされた重要な変更](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Kubernetes 一般的エラー用アドオン

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>シナリオ:エグレス制限のため、アドオンは Azure Policy サービス エンドポイントに接続できません

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できず、次のいずれかのエラーが返されます。

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>原因

この問題は、クラスター エグレスがロックダウンされている場合に発生します。

#### <a name="resolution"></a>解決方法

次の記事に記載されているドメインとポートが開いていることを確認します。

- [AKS クラスターに必要な送信ネットワーク規則と FQDN](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Azure Arc 対応 Kubernetes 用の Azure Policy アドオン (プレビュー) をインストールする](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>シナリオ:aad-pod-identity の構成のため、アドオンは Azure Policy サービス エンドポイントに接続できません

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できず、次のいずれかのエラーが返されます。

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>原因

このエラーは _add-pod-identity_ がクラスターにインストールされていて、_kube-system_ ポッドが _aad-pod-identity_ で除外されていない場合に発生します。

Azure Instance Metadata エンドポイントの呼び出しをインターセプトするよう、_aad-pod-identity_ コンポーネントの Node Managed Identity (NMI) ポッドによりノードの iptables が変更されます。 この設定の場合、Metadata エンドポイントに要求が行われると、ポッドで _aad-pod-identity_ が使用されていない場合でも NMI により要求がインターセプトされます。
CRD に定義されているラベルに一致するポッドから Metadata エンドポイントに要求が行われた場合、NMI で何も処理することなく、その要求をプロキシ処理することを _aad-pod-identity_ に通知するよう、**AzurePodIdentityException CRD** を構成できます。

#### <a name="resolution"></a>解決方法

_aad-pod-identity_ の _kube-system_ 名前空間の `kubernetes.azure.com/managedby: aks` ラベルを持つシステム ポッドを、**AzurePodIdentityException** CRD を構成することで除外します。

詳細については、「[特定のポッドまたはアプリケーションの AAD Pod Identity を無効にする](https://azure.github.io/aad-pod-identity/docs/configure/application_exception)」を参照してください。

例外を構成するには、次の例を参照してください。

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>シナリオ:リソース プロバイダーが未登録

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できますが、アドオン ログに次のいずれかのエラーが見られます。

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
https://aka.ms/policy-register-subscription for how to register subscriptions.
```

または

```
policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
Code="InternalServerError" Message="Encountered an internal server error."
```

#### <a name="cause"></a>原因

`Microsoft.PolicyInsights` リソースプロバイダーは登録されていないため、ポリシー定義を取得してコンプライアンス データを返すには、アドオン用に登録する必要があります。

#### <a name="resolution"></a>解決方法

クラスター サブスクリプションに `Microsoft.PolicyInsights` リソース プロバイダーを登録します。 手順については、[リソースプロバイダーの登録](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)に関するページを参照してください。

### <a name="scenario-the-subscription-is-disabled"></a>シナリオ:サブスクリプションが無効

#### <a name="issue"></a>問題

アドオンは Azure Policy サービス エンドポイントに接続できませんが、次のエラーが見られます。

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>原因

このエラーは、サブスクリプションが問題であると判断され、サブスクリプションをブロックするための機能フラグ `Microsoft.PolicyInsights/DataPlaneBlocked` が追加されたことを示します。

#### <a name="resolution"></a>解決方法

この問題を調査して解決するには、機能チーム `azuredg@microsoft.com` にお問い合わせください。

## <a name="next-steps"></a>次のステップ

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Microsoft Q&A](/answers/topics/azure-policy.html) を通じて、専門家からの回答を得る。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
- さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
