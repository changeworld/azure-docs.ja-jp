---
title: プライベート リンクを構成する
description: Private Link を構成する
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 99e8830b1b4b667d68bb8db1243d1c0ddaeab15a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006774"
---
# <a name="configure-your-private-link"></a>プライベート リンクを構成する
プライベート リンクを構成するには、いくつかの手順を実行する必要があります。 
* リソースでのプライベート リンク スコープの作成
* ネットワークでのプライベート エンドポイントの作成と、スコープへのその接続
* Azure Monitor リソースでの必要なアクセス権の構成。

この記事では、Azure portal でそれを行う方法を説明し、プロセスを自動化するための Azure Resource Manager (ARM) テンプレートの例を示します。 

## <a name="create-a-private-link-connection-through-the-azure-portal"></a>Azure portal を使用して Private Link 接続を作成する
このセクションでは、Azure portal を介してプライベート リンクを設定するプロセスを、ステップごとに確認します。 コマンド ラインまたは Azure Resource Manager テンプレート (ARM テンプレート) を使用して Private Link を作成および管理する方法については、「[API とコマンド ラインの使用](#use-apis-and-command-line)」を参照してください。

### <a name="create-an-azure-monitor-private-link-scope"></a>Azure Monitor Private Link スコープを作成する

1. Azure portal で **[リソースの作成]** に移動して、「**Azure Monitor Private Link Scope**」を検索します。

   ![Azure Monitor Private Link スコープを検索する](./media/private-link-security/ampls-find-1c.png)

2. **[作成]** を選択します。
3. サブスクリプションとリソース グループを選択します。
4. AMPLS に名前を付けます。 "AppServerProdTelem" など、意味のある明確な名前を使用することをお勧めします。
5. **[確認および作成]** を選択します。 

   ![Azure Monitor Private Link スコープを作成する](./media/private-link-security/ampls-create-1d.png)

6. 検証をパスしたら、 **[作成]** を選択します。

### <a name="connect-azure-monitor-resources"></a>Azure Monitor リソースの接続

Azure Monitor リソース (Log Analytics ワークスペースと Application Insights コンポーネント) を AMPLS に接続します。

1. Azure Monitor Private Link スコープで、左側のメニューの **[Azure Monitor リソース]** を選択します。 **[追加]** ボタンを選びます。
2. ワークスペースまたはコンポーネントを追加します。 **[追加]** ボタンを選択するとダイアログが表示され、Azure Monitor リソースを選択できます。 サブスクリプションとリソース グループを参照するか、名前を入力してフィルターを適用できます。 ワークスペースまたはコンポーネントを選択し、 **[適用]** を選択してスコープに追加します。

    ![スコープの選択 UX のスクリーンショット](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> Azure Monitor リソースを削除するには、まず接続先の AMPLS オブジェクトから切断する必要があります。 AMPLS に接続されているリソースを削除することはできません。

### <a name="connect-to-a-private-endpoint"></a>プライベート エンドポイントへの接続

これで、AMPLS にリソースを接続できたので、ネットワークを接続するためのプライベート エンドポイントを作成します。 このタスクは、この例のように、[Azure portal Private Link センター](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints)、または Azure Monitor Private Link Scope 内で実行できます。

1. スコープ リソースで、左側のリソース メニューの **[プライベート エンドポイント接続]** を選択します。 **[プライベート エンドポイント]** を選択して、エンドポイント作成プロセスを開始します。 Private Link センターで開始された接続をここで承認するには、そのリンクを選択し、 **[承認]** を選択します。

    ![プライベート エンドポイント接続 UX のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. サブスクリプション、リソース グループ、およびエンドポイントの名前と、それを有効にするリージョンを選択します。 リージョンは、接続する VNet のリージョンと同じにする必要があります。

3. **[Next:リソース]** を選択します。 

4. リソース画面で、

   a. Azure Monitor プライベート スコープ リソースを含む **[サブスクリプション]** を選択します。 

   b. **[リソースの種類]** には、**Microsoft.insights/privateLinkScopes** を選択してください。 

   c. **[リソース]** ドロップダウンから、前に作成した Private Link スコープを選択します。 

   d. **Next:構成 >** をクリックします。
      ![[プライベート エンドポイントを作成する] の選択のスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. [構成] ウィンドウで、

   a.    Azure Monitor リソースに接続する **[仮想ネットワーク]** と **[サブネット]** を選択します。 
 
   b.    **[プライベート DNS ゾーンとの統合]** で **[はい]** を選択して、新しいプライベート DNS ゾーンを自動で作成します。 実際の DNS ゾーンは、下のスクリーンショットに示されているものとは異なる場合があります。 
   > [!NOTE]
   > **[いいえ]** を選択して DNS レコードを手動で管理する場合は、最初に、このプライベート エンドポイントと AMPLS 構成を含め、プライベート リンクの設定を完了させます。 次に、「[Azure プライベート エンドポイントの DNS 構成](../../private-link/private-endpoint-dns.md)」の手順に従って、DNS を構成します。 プライベート リンクの設定の準備で、空のレコードを作成しないようにしてください。 作成する DNS レコードによって、既存の設定がオーバーライドされ、Azure Monitor との接続が影響を受ける可能性があります。
 
   c.    **[Review + create]\(レビュー + 作成\)** を選択します。
 
   d.    検証をパスします。 
 
   e.    **［作成］** を選択します 

    ![プライベート エンドポイントの詳細の選択に関するスクリーンショット](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

これで、この AMPLS に接続された新しいプライベート エンドポイントが作成されました。


## <a name="configure-access-to-your-resources"></a>リソースへのアクセスを構成する
ここまでは、ネットワークの構成について説明しましたが、監視対象リソース (Log Analytics ワークスペースと Application Insights コンポーネント) へのネットワーク アクセスを構成する方法についても検討する必要があります。

Azure Portal にアクセスします。 リソースのメニューの左側に、 **[ネットワークの分離]** というメニュー項目があります。 このページで、プライベート リンクを介してリソースに接続できるネットワークと、他のネットワークがそれに接続できるかどうかの両方を制御します。


> [!NOTE]
> 2021 年 9 月より、ネットワークの分離が厳密に適用されるようになります。 パブリック ネットワークからのクエリをブロックするように設定されていて、(AMPLS を介して) どのプライベート ネットワークにも接続されていないリソースは、すべてのネットワークからのクエリを受け付けなくなります。

![LA ネットワークの分離](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>接続されている Azure Monitor プライベート リンク スコープ
ここでは、Azure Monitor のプライベート リンク スコープへのリソースの接続を確認して構成できます。 スコープ (AMPLS) に接続すると、各 AMPLS に接続されている仮想ネットワークからのトラフィックがリソースに到達できます。 それには、[Azure Monitor リソースの接続](#connect-azure-monitor-resources)の場合と同じように、スコープから接続するのと同じ効果があります。 

新しい接続を追加するには、 **[追加]** を選択し、Azure Monitor Private Link スコープを選択します。 **[適用]** を選択して接続します。 「[AMPLS の制限を考慮する](./private-link-design.md#consider-ampls-limits)」で説明されているように、リソースは 5 つの AMPLS オブジェクトに接続できます。

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>仮想ネットワークのアクセスの構成 - プライベート リンク スコープの外部からのアクセスの管理
このページの下部にある設定では、パブリック ネットワーク (つまり示されたスコープ (AMPLS) には接続されていないネットワーク) からのアクセスを制御します。

**[Allow public network access for ingestion]\(取り込みにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、接続されているスコープの外側のクライアント (コンピューター、SDK など) は、リソースにデータをアップロードしたり、ログを送信したりできません。

**[Allow public network access for queries]\(クエリにパブリック ネットワークを許可する\)** を **[いいえ]** に設定した場合、接続されているスコープの外側のクライアント (コンピューター、SDK など) は、リソースのデータのクエリを実行できません。 このデータには、ログ、メトリック、ライブ メトリック ストリームへのアクセスと、ブック、ダッシュボード、クエリ API ベースのクライアント エクスペリエンス、Azure portal の分析情報などの上に構築されたエクスペリエンスが含まれます。 Azure portal の外部で実行され、Log Analytics データにクエリを発行するエクスペリエンスも、プライベート リンク VNET 内で実行する必要があります。


## <a name="use-apis-and-command-line"></a>API とコマンド ラインの使用

前に説明したプロセスは、Azure Resource Manager テンプレート、REST、コマンド ライン インターフェイスを使用して自動化できます。

### <a name="create-and-manage-azure-monitor-private-link-scopes-ampls"></a>Azure Monitor プライベート リンク スコープ (AMPLS) を作成および管理する
プライベート リンク スコープを作成して管理するには、[REST API](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) または [Azure CLI (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope) を使用します。

#### <a name="create-ampls-with-open-access-modes---cli-example"></a>オープン アクセス モードで AMPLS を作成する - CLI の例
次の CLI コマンドを実行すると、クエリとインジェストのアクセス モードの両方が Open に設定された、"my-scope" という名前の新しい AMPLS リソースが作成されます。
```
az resource create -g "my-resource-group" --name "my-scope" --api-version "2021-07-01-preview" --resource-type Microsoft.Insights/privateLinkScopes --properties "{\"accessModeSettings\":{\"queryAccessMode\":\"Open\", \"ingestionAccessMode\":\"Open\"}}"
```

#### <a name="create-ampls-with-mixed-access-modes---powershell-example"></a>混合アクセス モードで AMPLS を作成する - PowerShell の例
次の PowerShell スクリプトを実行すると、クエリ アクセス モードが Open で、インジェスト アクセス モードが PrivateOnly に設定されている (つまり、AMPLS 内のリソースへのインジェストのみを許可する)、"my-scope" という名前の新しい AMPLS リソースが作成されます。

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group"
$scopeName = "my-scope"
$scopeProperties = @{
    accessModeSettings = @{
        queryAccessMode     = "Open"; 
        ingestionAccessMode = "PrivateOnly"
    } 
}

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# create private link scope resource
$scope = New-AzResource -Location "Global" -Properties $scopeProperties -ResourceName $scopeName -ResourceType "Microsoft.Insights/privateLinkScopes" -ResourceGroupName $scopeResourceGroup -ApiVersion "2021-07-01-preview" -Force
```

#### <a name="create-ampls---azure-resource-manager-template-arm-template"></a>AMPLS の作成 - Azure Resource Manager テンプレート (ARM テンプレート)
次の Azure Resource Manager テンプレートでは、以下のものが作成されます。
* クエリとインジェストのアクセス モードが [開く] に設定された、"my-scope" という名前のプライベート リンク スコープ (AMPLS)。
* "my-workspace" という名前の Log Analytics ワークスペース
* "my-workspace-connection" という名前のスコープ付きリソースの "my-scope" AMPLS への追加

> [!NOTE]
> Private Link Scope オブジェクトの作成には、新しい API バージョン (2021-07-01-preview 以降) を使用してください (下記の type 'microsoft.insights/privatelinkscopes')。 過去に説明した ARM テンプレートでは古い API バージョンが使用され、その結果、AMPLS が QueryAccessMode="Open" と IngestionAccessMode="PrivateOnly" に設定されていました。

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2021-07-01-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {
                "accessModeSettings":{
                    "queryAccessMode":"Open",
                    "ingestionAccessMode":"Open"
                }
            }
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

### <a name="set-ampls-access-modes---powershell-example"></a>AMPLS のアクセス モードの設定 - PowerShell の例
AMPLS でアクセス モード フラグを設定するには、次の PowerShell スクリプトを使用できます。 次のスクリプトを実行すると、フラグが Open に設定されます。 プライベート専用モードを使用するには、値 "PrivateOnly" を使用します。

AMPLS アクセス モードの更新が有効になるまでに、最大 10 分かかります。

```
# scope details
$scopeSubscriptionId = "ab1800bd-ceac-48cd-...-..."
$scopeResourceGroup = "my-resource-group-name"
$scopeName = "my-scope"

# login
Connect-AzAccount

# select subscription
Select-AzSubscription -SubscriptionId $scopeSubscriptionId

# get private link scope resource
$scope = Get-AzResource -ResourceType Microsoft.Insights/privateLinkScopes -ResourceGroupName $scopeResourceGroup -ResourceName $scopeName -ApiVersion "2021-07-01-preview"

# set access mode settings
$scope.Properties.AccessModeSettings.QueryAccessMode = "Open";
$scope.Properties.AccessModeSettings.IngestionAccessMode = "Open";
$scope | Set-AzResource -Force
```

### <a name="set-resource-access-flags"></a>リソース アクセス フラグを設定する
ワークスペースまたはコンポーネント アクセス フラグを管理するには、[Log Analytics ワークスペース](/cli/azure/monitor/log-analytics/workspace)または [Application Insights コンポーネント](/cli/azure/ext/application-insights/monitor/app-insights/component)でフラグ `[--ingestion-access {Disabled, Enabled}]` と `[--query-access {Disabled, Enabled}]` を使用します。


## <a name="review-and-validate-your-private-link-setup"></a>自分の Private Link セットアップを確認および検証する

### <a name="reviewing-your-endpoints-dns-settings"></a>エンドポイントの DNS 設定の確認
作成したプライベート エンドポイントには今、次の 5 つの DNS ゾーンが構成されているはずです。

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> これらのゾーンのそれぞれで、特定の Azure Monitor エンドポイントが VNet の IP プールからのプライベート IP にマップされます。 以下の画像に示されている IP アドレスは単なる例です。 実際の構成では、代わりに、独自のネットワークからのプライベート IP が表示されるはずです。

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
このゾーンでカバーされるのは、Azure Monitor によって使用されるグローバル エンドポイントです。つまり、これらのエンドポイントによって、すべてのリソース (特定のものではなく) を考慮して要求が処理されます。 このゾーンには、次のようにマップされたエンドポイントが必要です。
* `in.ai` - Application Insights インジェスト エンドポイント (グローバルおよびリージョン両方のエントリ)
* `api` - Application Insights および Log Analytics API エンドポイント
* `live` - Application Insights ライブ メトリック エンドポイント
* `profiler` - Application Insights プロファイラー エンドポイント
* `snapshot` - Application Insights スナップショット エンドポイント[![プライベート DNS ゾーン monitor-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
このゾーンでカバーされるのは、OMS エンドポイントへのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン oms-opinsights-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
このゾーンでカバーされるのは、ODS エンドポイント (Log Analytics のインジェスト エンドポイント) へのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン ods-opinsights-azure-com のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
このゾーンでカバーされるのは、エージェント サービス オートメーション エンドポイントへのワークスペース固有のマッピングです。 このプライベート エンドポイントに接続されている AMPLS にリンクされた各ワークスペースのエントリが表示されます。
[![プライベート DNS ゾーン エージェント svc-azure-automation-net。](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
このゾーンには、グローバル エージェントのソリューション パック ストレージ アカウントへの接続性が構成されます。 これにより、エージェントは、新規または更新されたソリューション パック (管理パックとも呼ばれます) をダウンロードできます。 使用するワークスペースの数に関係なく、Log Analytics エージェントを処理するのに必要なエントリは 1 つだけです。
[![プライベート DNS ゾーン blob-core-windows-net のスクリーンショット。](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> このエントリは、2021 年 4 月 19 日以降 (または、Azure ソブリン クラウドでは、2021 年 6 月から) 作成された Private Link セットアップにのみ追加されます。


### <a name="validating-you-are-communicating-over-a-private-link"></a>プライベート リンクを介して通信が行われていることの検証
* 自分の要求がプライベート エンドポイントを介して送信されたことを検証するために、ネットワーク追跡ツール (またはご利用のブラウザー) を使用してそれらを確認できます。 たとえば、ご利用のワークスペースまたはアプリケーションに対してクエリを試みる場合は、API エンドポイントにマップされたプライベート IP に要求が送信されることを確認します。この例では、*172.17.0.9* です。

    注: ブラウザーによっては、他の DNS 設定が使用されます (「[ブラウザーの DNS 設定](./private-link-design.md#browser-dns-settings)」を参照してください)。 目的の DNS 設定が適用されていることを確認します。

* (AMPLS 経由で接続されていない) パブリック ネットワークからの要求がワークスペースまたはコンポーネントによって受信されていないことを確認するには、「[リソースへのアクセスを構成する](#configure-access-to-your-resources)」で説明されているように、リソースのパブリック インジェストとクエリ フラグを *[いいえ]* に設定します。

* ご利用の保護されたネットワーク上のクライアントから、`nslookup` を、ご利用の DNS ゾーンに一覧表示されている任意のエンドポイントに対して使用します。 それは、既定で使用されるパブリック IP ではなく、マップされたプライベート IP に、DNS サーバーによって解決される必要があります。


## <a name="next-steps"></a>次のステップ

- カスタム ログとカスタマー マネージド キー (CMK) の[プライベート ストレージ](private-storage.md)について確認する
- [Automation 用の Private Link](../../automation/how-to/private-link-security.md) について確認します