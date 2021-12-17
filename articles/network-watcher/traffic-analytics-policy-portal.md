---
title: Azure Policy を使用して Traffic Analytics をデプロイして管理する
titleSuffix: Azure Network Watcher
description: この記事では、組み込みのポリシーを使用して、Traffic Analytics のデプロイを管理する方法について説明します
services: network-watcher
author: moagra
ms.service: network-watcher
ms.topic: article
ms.workload: infrastructure-services
ms.date: 07/11/2021
ms.author: moagra
ms.openlocfilehash: 9d916122714b1dddab7d1d9b6d13e6fd58d14d46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619365"
---
# <a name="deploy-and-manage-traffic-analytics-using-azure-policy"></a>Azure Policy を使用して Traffic Analytics をデプロイして管理する 

Azure Policy は、組織の標準を適用し、コンプライアンスを大規模に評価するのに役立ちます。 Azure Policy の一般的なユースケースには、リソースの整合性、規制コンプライアンス、セキュリティ、コスト、管理のガバナンスの実装が含まれています。 この記事では、セットアップを管理するために [Traffic Analytics](./traffic-analytics.md) で使用できる 3 つの組み込みポリシーについて説明します。

Azure Policy 定義を初めて作成する場合は、次を参照してください。 
- [Azure Policy の概要](../governance/policy/overview.md) 
- [Azure Policy の割り当てを作成するためのチュートリアル](../governance/policy/assign-policy-portal.md#create-a-policy-assignment)。


## <a name="locate-the-policies"></a>ポリシーを見つける
1. Azure ポータル ([portal.azure.com](https://portal.azure.com)) に移動します。 

![ポリシーのホームページ](./media/network-watcher-builtin-policy/1_policy-search.png)上部の検索バーでポリシーを検索して Azure Policy ページに移動する

2. 左側ペインの **[Assignments (割り当て)]** タブに移動します。

![［割り当て］タブ](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. **[Assign Policy (ポリシーの割り当て)]** ボタンをクリックします。 

![[Assign Policy (ポリシーの割り当て)] ボタン](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. [Policy Definitions (ポリシーの定義)] の下にある 3 つのドット メニューをクリックして、使用可能なポリシーを確認します。

5. 型フィルターを使用して、[Built-in (組み込み)] を選択します。 その後、"traffic analytics" を検索します

3 つの組み込みポリシーが表示されるはずです ![トラフィック分析用のポリシー リスト](./media/traffic-analytics/policy-filtered-view.png)

6. 割り当てるポリシーを選択します。

- "*Network Watcher ログでトラフィック分析を有効にする必要がある*" は、準拠していないフロー ログ (トラフィック分析が有効になっていないフロー ログ) にフラグを設定する監査ポリシーです
- "*トラフィック分析に特定のワークスペースを使用するようにネットワーク セキュリティ グループを構成する*" と "*Traffic Analytics を有効にするためにネットワーク セキュリティ グループを構成する*" は、デプロイ アクションを含むポリシーです。 有効になっているポリシーに応じて、既に構成されている設定を上書きする、または上書きしないすべての NSG でトラフィック分析を有効にします。

各ポリシーには、次のような個別の手順があります。  

## <a name="audit-policy"></a>監査ポリシー 

### <a name="network-watcher-flow-logs-should-have-traffic-analytics-enabled"></a>Network Watcher のフロー ログではトラフィック分析を有効にする必要がある

このポリシーでは、"Microsoft.Network/networkWatchers/flowLogs" という種類の既存のすべての Azure Resource Manager オブジェクトを監査し、フロー ログ リソースの "networkWatcherFlowAnalyticsConfiguration.enabled" プロパティを使用して Traffic Analytics が有効にされているかどうかを確認します。 プロパティが false に設定されているフロー ログ リソースにフラグを設定します。

ポリシーの完全な定義を表示する場合は、[[定義] タブ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)にアクセスし、"traffic analytics" を検索してポリシーを見つけることができます

### <a name="assignment"></a>割り当て

1. ポリシーの詳細を入力します

- スコープ: サブスクリプションまたはリソース グループにすることができます。 後者の場合は、(ネットワーク セキュリティ グループではなく) フロー ログ リソースを含むリソース グループを選択します
- ポリシー定義:「Locate the policies (ポリシーの検索)」セクションで示されているように選択する必要があります。
- AssignmentName:わかりやすい名前を使用します。 

2. [確認および作成] をクリックして、割り当て内容を確認します。

追加のパラメーターは必要ありません。 監査ポリシーを割り当てるときは、[Remediation (修復)] タブで詳細を入力する必要はありません。  

![監査ポリシーの確認の Traffic Analytics](./media/traffic-analytics/policy-one-assign.png)

### <a name="results"></a>結果

結果を確認するには、[Compliance (コンプライアンス)] タブを開き、割り当ての名前を検索します。
ポリシーを実行すると、次のスクリーンショットに似た内容が表示されます。 ポリシーが実行されていない場合は、しばらく待ってください。 

![監査ポリシーの結果のトラフィック分析](./media/traffic-analytics/policy-one-results.png)

## <a name="deploy-if-not-exists-policy"></a>Deploy-If-not-exists ポリシー 

### <a name="configure-network-security-groups-to-use-specific-workspace-for-traffic-analytics"></a>トラフィック分析に特定のワークスペースを使用するようにネットワーク セキュリティ グループを構成する 

Traffic Analytics が有効になっていない NSG にフラグが設定されます。 これは、フラグが設定された NSG では、対応するフロー ログ リソースが存在しないか、フロー ログ リソースは存在するが、それに対してトラフィック分析が有効になっていないことを意味します。 ポリシーが既存のリソースに影響を与える場合は、修復タスクを作成できます。
Network Watcher はリージョン サービスであるため、このポリシーは、選択されたスコープ内の特定のリージョンに属する NSG にのみ適用されます (異なるリージョンには、別のポリシー割り当てを作成してください)。
 
修復は、ポリシーの割り当て中またはポリシーが割り当てられて評価された後に割り当てることができます。 修復により、指定されたパラメーターを使用してフラグが設定されたすべてのリソースで Traffic Analytics が有効になります。 NSG で特定のストレージ ID へのフロー ログが既に有効になっているが、Traffic Analytics が有効になっていない場合は、修復により、指定されたパラメーターを使用して、この NSG に対して Traffic Analytics が有効にされることにご注意ください。 フラグが設定された NSG の場合、パラメーターに指定されているストレージ ID がフロー ログに対して既に有効になっているものと異なる場合、後者は修復タスクで指定されたストレージ ID で上書きされます。 上書きしない場合は、以下に説明されているポリシー "*Traffic Analytics を有効にするためにネットワーク セキュリティ グループを構成する*" を使用します。

ポリシーの完全な定義を表示する場合は、[[定義] タブ](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions)にアクセスし、"Traffic Analytics" を検索してポリシーを見つけることができます。 

### <a name="configure-network-security-groups-to-enable-traffic-analytics"></a>Traffic Analytics を有効にするためにネットワーク セキュリティ グループを構成する

これは上記のポリシーと同じです。ただし、修復中は、フロー ログが有効になっているが、ポリシー割り当てで指定されたパラメーターで Traffic Analytics が無効にされているフラグが付き NSG のフロー ログ設定は上書きされません。

### <a name="assignment"></a>割り当て

1. ポリシーの詳細を入力します

- スコープ: サブスクリプションまたはリソース グループにすることができます  
- ポリシー定義:「Locate the policies (ポリシーの検索)」セクションで示されているように選択する必要があります。
- AssignmentName:わかりやすい名前を使用します。 

2. ポリシーのパラメーターを追加します。 

- NSG リージョン:ポリシーの対象となる Azure リージョン
- Storage ID:ストレージ アカウントのフル リソース ID。 ストレージ アカウントは、NSG と同じリージョンに存在する必要があります。
- Network Watchers RG:ご利用の Network Watcher リソースを含むリソース グループの名前。 名前を変更していない場合は、既定の "NetworkWatcherRG" を入力できます。
- Network Watcher 名:リージョンの Network Watcher サービスの名前。 形式:NetworkWatcher_RegionName。 例:NetworkWatcher_centralus.
- ワークスペース リソース ID: ワークスペースのリソース ID。Traffic Analytics が有効になっている必要があります。 形式は `/subscriptions/<SubscriptionID>/resourceGroups/<ResouceGroupName>/providers/Microsoft.Storage/storageAccounts/<StorageAccountName>`
- WorkspaceID: ワークスペース GUID
- WorkspaceRegion: ワークスペースのリージョン (NSG のリージョンと同じである必要はないことにご注意ください)
- TimeInterval: 処理されたログがワークスペースにプッシュされる頻度。 現在、使用できる値は 60 分と 10 分です。 既定値は 60 分です。
- Effect: DeployIfNotExists (既に割り当てられている値)

3. 修復の詳細の追加

- ポリシーが既存のリソースに影響を与えるようにする場合は、 *[修復タスクの作成]* のチェックボックスをオンにします
- *[マネージド ID の作成]* は既にオンになっているはずです
- マネージド ID に対して前回と同じ場所を選択しました。
- このポリシーを使用するには、共同作成者または所有者のアクセス許可が必要です。 これらのアクセス許可がある場合、エラーは表示されません。

4. [Review + Create (レビュー + 作成)] をクリックして割り当てを確認すると、次のスクリーンショットのような内容が表示されます。

![DINE ポリシーの確認のトラフィック分析](./media/traffic-analytics/policy-two-review.png) 


### <a name="results"></a>結果

結果を確認するには、[Compliance (コンプライアンス)] タブを開き、割り当ての名前を検索します。
ポリシーができると、次のようなスクリーンショットが表示されます。 ポリシーが実行されていない場合は、しばらく待ってください。

![DINE ポリシーの結果のトラフィック分析](./media/traffic-analytics/policy-two-results.png)  

### <a name="remediation"></a>修復

手動で修復するには、上記の [コンプライアンス] タブで *[修復タスクの作成]* を選択します

![DINE ポリシーの修復のトラフィック分析](./media/traffic-analytics/policy-two-remediate.png) 


## <a name="troubleshooting"></a>トラブルシューティング

### <a name="remediation-task-fails-with-policyauthorizationfailed-error-code"></a>"PolicyAuthorizationFailed" エラー コードで修復タスクが失敗する。

サンプル エラー例 "ポリシー割り当て '/subscriptions/123ds-fdf3657-fdjjjskms638/resourceGroups/DummyRG/providers/Microsoft.Authorization/policyAssignments/b67334e8770a4afc92e7a929/' のリソース ID に、デプロイを作成するために必要なアクセス許可がありません。"

このようなシナリオでは、割り当てのマネージド ID に手動でアクセスを許可する必要があります。 適切なサブスクリプションまたはリソース グループ (ポリシー パラメーターで指定されたリソースを含む) に移動し、ポリシーによって作成されたマネージド ID へのアクセスを共同作成者に許可します。 上の例では、"b67334e8770a4afc92e7a929" は共同作成者である必要があります。


## <a name="next-steps"></a>次のステップ 

-   [NSG フロー ログの組み込みポリシー](./nsg-flow-logs-policy-portal.md)について学習する
-   [Traffic Analytics ](./traffic-analytics.md) についてさらに学習する
-   [Network Watcher ](./index.yml)の詳細
