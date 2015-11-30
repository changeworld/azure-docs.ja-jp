<properties
   pageTitle="Azure リソース マネージャーによる Traffic Manager プレビューのサポート | Microsoft Azure"
   description="Azure リソース マネージャー (ARM) での Traffic Manager に対する Powershell の使用 (プレビュー)"
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/12/2015"
   ms.author="joaoma" />



# Azure リソース マネージャーによる Azure Traffic Manager プレビューのサポート
Azure リソース マネージャー (ARM) は、Azure のサービスの新しい管理フレームワークです。Azure リソース マネージャー ベースの API とツールを使用して、Azure Traffic Manager プロファイルを管理できるようになりました。Azure リソース マネージャーの詳細については、[リソース グループを使用した Azure のリソースの管理](../azure-preview-portal-using-resource-groups.md)に関するページをご覧ください。

>[AZURE.NOTE]REST API、Azure PowerShell、Azure CLI、.NET SDK など、ARM による Traffic Manager のサポートは現在プレビュー段階です。



## リソース モデル

Azure Traffic Manager は、Traffic Manager プロファイルと呼ばれる設定のコレクションを使用して構成されます。これには、DNS 設定、トラフィック ルーティング設定、エンドポイント監視設定、トラフィックのルーティング先となるサービス エンドポイントのリストが含まれます。

ARM では、各 Traffic Manager プロファイルは、Microsoft.Network リソース プロバイダーによって管理される、TrafficManagerProfiles 型の ARM リソースで表されます。REST API レベルでの各プロファイルの URI は次のとおりです。

	https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## Azure Traffic Manager サービス管理 API との比較

ARM を使用して Traffic Manager プロファイルを構成すると、下記のプレビューの制限事項を除き、(ARM 以外の) サービス管理 API と同じ Traffic Manager の機能セットにアクセスできます。

ただし、機能はこれまでと同じですが、一部の用語が変更されました。

- Traffic Manager が特定の DNS 要求に応答するときに、トラフィックのルーティング先のエンドポイントを選択する方法を決定する "負荷分散方法" が、"トラフィック ルーティング方法" という名前に変更されました。

- トラフィック ルーティング方法の "ラウンド ロビン" が、"重み付け" という名前に変更されました。

- トラフィック ルーティング方法の "フェールオーバー" が、"優先度" という名前に変更されました。

## プレビューの制限事項
Azure リソース マネージャーによる Traffic Manager のサポートはプレビュー サービスであるため、現在は制限事項がいくつかあります。

- 既存の (ARM 以外の) サービス管理 API、ツール、ポータルを使用して作成された Traffic Manager プロファイルは ARM では使用できません。逆の場合も同様です。ARM 以外の API から ARM API へのプロファイルの移行は現在サポートされていません。

- 	REST API では、Traffic Manager プロファイルの修正プログラムの適用をサポートしていません。プロファイルのプロパティを更新するには、プロファイルを取得し、変更後のプロファイルを配置する必要があります。
- 	サポートされているのは "外部" エンドポイントだけです。これらは Azure ベースのサービスで Traffic Manager を使用する際にも使用できますが、その場合、これらのエンドポイントには、内部エンドポイントの料金で課金されます (外部エンドポイントを使用することによる唯一の影響は、基になる Azure サービスを無効化または削除しても、外部エンドポイントが自動的には無効化または削除されないことです。代わりに、エンドポイントを手動で無効化または削除する必要があります)。
-	Azure Traffic Manager は、Azure ポータルではまだ使用できません。クラシック ポータルでのみ使用できます。

## Azure PowerShell の設定

各手順では、Microsoft Azure PowerShell を使用します。次の手順を使用して、Microsoft Azure PowerShell を構成する必要があります。

PowerShell 以外のユーザーは、他のインターフェイスを使用して同じ操作を実行することもできます。

### 手順 1
Azure のダウンロード ページから入手できる、最新の Azure PowerShell をインストールします。
### 手順 2
ARM コマンドレットを使用するように PowerShell モードを切り替えます。詳細については、「リソース マネージャーでの Windows PowerShell の使用」をご覧ください。

	PS C:\> Switch-AzureMode -Name AzureResourceManager
### 手順 3
Azure アカウントにログインします。

	PS C:\> Add-AzureAccount

資格情報を使用して認証を行うよう求められます。

### 手順 4
使用する Azure サブスクリプションを選択します。

	PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

使用できるサブスクリプションのリストを表示するには、Get-AzureSubscription コマンドレットを使用します。

### 手順 5

 Traffic Manager サービスは、Microsoft.Network リソース プロバイダーによって管理されています。ARM で Traffic Manager を使用するには、このリソース プロバイダーを使用するように Azure サブスクリプションを登録する必要があります。この操作は、サブスクリプションごとに 1 回だけ実行します。

	PS C:\> Register-AzureProvider –ProviderNamespace Microsoft.Network

### 手順 6
リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

	PS C:\> New-AzureResourceGroup -Name MyAzureResourceGroup -location "West US"

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。ただし、Traffic Manager プロファイル リソースはすべてグローバルであり、リージョンの違いがないため、リソース グループの場所を選択しても、Azure Traffic Manager には影響しません。

## Traffic Manager プロファイルの作成

Traffic Manager プロファイルを作成するには、New-AzureTrafficManagerProfile コマンドレットを使用します。

	PS C:\> $profile = New-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"

パラメーターは、次のとおりです。

- Name: Traffic Manager プロファイル リソースの ARM リソース名。同じリソース グループ内のプロファイルには、一意の名前が必要です。この名前は、DNS クエリに使用される DNS 名とは別のものです。

-	ResourceGroupName: プロファイル リソースが属する ARM リソース グループの名前。

-	TrafficRoutingMethod: 受信 DNS クエリへの応答で返されるエンドポイントを特定するために使用する、トラフィック ルーティング方法を指定します。指定できる値は、"Performance"、"Weighted"、または "Priority" です。

-	RelativeDnsName: この Traffic Manager プロファイルで提供される相対 DNS 名を指定します。Azure Traffic Manager が使用する DNS ドメイン名とこの値を組み合わせて、プロファイルの完全修飾ドメイン名 (FQDN) が形成されます。たとえば、値 "contoso" を指定すると、Traffic Manager プロファイルの完全修飾ドメイン名は "contoso.trafficmanager.net" になります。

-	TTL: DNS の TTL (Time-to-Live) を秒単位で指定します。このパラメーターにより、ローカル DNS リゾルバーと DNS クライアントに、この Traffic Manager プロファイルで提供される DNS 応答をキャッシュする時間を通知します。

-	MonitorProtocol: エンドポイントの正常性の監視に使用するプロトコルを指定します。指定できる値は、"HTTP" と "HTTPS" です。

-	MonitorPort: エンドポイントの正常性の監視に使用する TCP ポートを指定します。

-	MonitorPath: エンドポイントの正常性のプローブに使用する、エンドポイントのドメイン名の相対パスを指定します。

このコマンドレットは、Azure Traffic Manager 内に Traffic Manager プロファイルを作成し、対応するプロファイル オブジェクトを返します。この時点では、プロファイルにはエンドポイントは含まれていません。Traffic Manager プロファイルにエンドポイントを追加する方法の詳細については、「[Traffic Manager プロファイルの更新](#update-a-traffic-manager-profile)」をご覧ください。

## Traffic Manager プロファイルの取得

既存の Traffic Manager プロファイル オブジェクトを取得するには、Get-AzureTrafficManagerProfle コマンドレットを使用します。

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup

このコマンドレットは、Traffic Manager プロファイル オブジェクトを返します。

## Traffic Manager プロファイルの更新[](#update-traffic-manager-profile)

Traffic Manager プロファイルを変更して、エンドポイントを追加または削除したり、プロファイル設定を変更したりするときは、次の 3 段階のプロセスに従います。

1.	Get-AzureTrafficManagerProfile を使用してプロファイルを取得します (または、New-AzureTrafficManagerProfile から返されるプロファイルを使用します)。

2.	エンドポイントの追加、エンドポイントの削除、エンドポイントのパラメーターの変更、またはプロファイルのパラメーターの変更を行って、プロファイルを変更します。これらの変更はオフラインで行われます。つまり、プロファイルを表すローカル オブジェクトだけが変更されます。

3.	Set-AzureTrafficManagerProfile コマンドレットを使用して、変更をコミットします。これにより、Azure Traffic Manager 内の既存のプロファイルが、提供されたプロファイルに置き換えられます。

詳細については、以下の例をご覧ください。

### プロファイルへのエンドポイントの追加

Add-AzureTrafficManagerEndpointConfig コマンドレットを使用して、Traffic Manager プロファイルにエンドポイントを追加できます。

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Add-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile –Type ExternalEndpoints –Target site1.contoso.com –EndpointStatus Enabled –Weight 10 –Priority 1 –EndpointLocation “West US”
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

Add-AzureTrafficManagerEndpointConfig のパラメーターは次のとおりです。

- EndpointName: エンドポイントの名前。同じプロファイル内のエンドポイントには、個別の名前が必要です。この名前は、サービス管理操作でエンドポイントを参照する際に使用されます。これは、エンドポイントの DNS 名ではありません。

-	TrafficManagerProfile: エンドポイントの追加先となる Traffic Manager プロファイル オブジェクト。

-	Type: Traffic Manager エンドポイントの型。現在、ARM API でサポートされているのは ExternalEndpoint 型だけです (「[プレビューの制限事項](#preview-limitations)」をご覧ください)。

-	Target: エンドポイントの完全修飾 DNS 名。Traffic Manager は、トラフィックをこのエンドポイントに送信するために、DNS 応答でこの値を返します。

-	EndpointStatus: エンドポイントの状態を指定します。エンドポイントが Enabled の場合、エンドポイントの正常性がプローブされ、エンドポイントがトラフィック ルーティング方法に含まれます。指定できる値は、"Enabled" または "Disabled" です。

-	Weight: エンドポイントに割り当てられた重みを指定します。これは、Traffic Manager プロファイルが、トラフィック ルーティング方法として "重み付け" を使用するように構成されている場合にのみ使用します。指定できる値は 1 ～ 1000 です。

-	Priority: トラフィック ルーティング方法として "優先度" を使用しているときに、このエンドポイントの優先度を指定します。Priority は、1 ～ 1000 の範囲内で指定する必要があります。値が小さいほど、優先度が高くなります。

-	EndpointLocation: "パフォーマンス" トラフィック ルーティング方法と共に使用する際に、外部エンドポイントの場所を指定します。指定できる場所のリストについては、Get-AzureLocation をご覧ください。

EndpointStatus、Weight、Priority の各パラメーターは省略可能です。省略した場合、これらのパラメーターは PowerShell から渡されず、サーバー側の既定値が適用されます。

### プロファイルからのエンドポイントの削除

プロファイルからエンドポイントを削除するには、削除するエンドポイントの名前を指定して、Remove-AzureTrafficmanagerEndpointConfig を使用します。

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 –TrafficManagerProfile $profile
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

プロファイル オブジェクトをパラメーターとして渡すのではなく、パイプを使用して渡すことによって、エンドポイントを追加または削除する操作のシーケンスを "パイプ" することもできます。次に例を示します。

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerEndpointConfig –EndpointName site1 | Set-AzureTrafficManagerProfile

### プロファイルまたはエンドポイントの設定の変更

プロファイルとエンドポイントのパラメーターは、オフラインで変更することができ、Set-AzureTrafficManagerProfile を使用して変更をコミットできます。唯一の例外として、プロファイルの変更後に、プロファイルの RelativeDnsName を変更することはできません (この値を変更するには、プロファイルを削除して再作成します)。たとえば、プロファイルの TTL と最初のエンドポイントの状態を変更するには、次のように入力します。

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> $profile.Ttl = 300
	PS C:\> $profile.Endpoints[0].EndpointStatus = "Disabled"
	PS C:\> Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile

### Traffic Manager プロファイルの削除
Traffic Manager プロファイルを削除するには、プロファイル名とリソース グループ名を指定して、Remove-AzureTrafficManagerProfile コマンドレットを使用します。

	PS C:\> Remove-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup [-Force]

このコマンドレットでは、確認のメッセージが表示されます。オプションの "-Force" スイッチを使用すると、このメッセージが表示されないようにすることができます。プロファイル オブジェクトを使用して、削除するプロファイルを指定することもできます。

	PS C:\> $profile = Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup
	PS C:\> Remove-AzureTrafficManagerProfile –TrafficManagerProfile $profile [-Force]

また、このシーケンスをパイプすることもできます。

	PS C:\> Get-AzureTrafficManagerProfile –Name MyProfile -ResourceGroupName MyAzureResourceGroup | Remove-AzureTrafficManagerProfile [-Force]


## 次のステップ

[Traffic Manager の監視](traffic-manager-monitoring.md)

[Traffic Manager のパフォーマンスに関する考慮事項](traffic-manager-performance-considerations.md)
 

<!---HONumber=Nov15_HO4-->