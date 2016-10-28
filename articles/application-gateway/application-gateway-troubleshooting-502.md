<properties
   pageTitle="Application Gateway での無効なゲートウェイによる (502) エラーのトラブルシューティング | Microsoft Azure"
   description="Application Gateway の 502 エラーに関するトラブルシューティングの方法を説明します"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/02/2016"
   ms.author="amitsriva" />

# Application Gateway での無効なゲートウェイによるエラーのトラブルシューティング

## Overview

Azure Application Gateway の構成後に発生する可能性があるエラーの 1 つに、"サーバー エラー: 502 - Web サーバーがゲートウェイまたはプロキシ サーバーとして動作しているときに、無効な応答を受信しました。" というエラーがあります。このエラーが発生する主な理由としては、次のことが考えられます。

- Azure Application Gateway のバックエンド プールが構成されていないか、空である。
- VM スケール セット内に正常な VM またはインスタンスがない。
- VM スケール セットのバックエンド VM またはインスタンスが既定の正常性プローブに応答していない。
- カスタムの正常性プローブの構成が無効または不適切である。
- 要求がタイムアウトしたか、ユーザー要求に関して接続の問題がある。

## 空の BackendAddressPool

### 原因

バックエンド アドレス プールに構成済みの VM または VM スケール セットがない場合、Application Gateway は顧客の要求をルーティングできず、無効なゲートウェイによるエラーをスローします。

### 解決策

バックエンド アドレス プールを空でない状態にしてください。これには、PowerShell、CLI、ポータルのいずれかを使用できます。

	Get-AzureRmApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"

前のコマンドレットから取得した出力に、空でないバックエンド アドレス プールが含まれている必要があります。次の例では、バックエンド VM の FQDN または IP アドレスが構成された 2 つのプールが返されます。BackendAddressPool のプロビジョニング状態は "Succeeded" である必要があります。
	
	BackendAddressPoolsText: 
			[{
				"BackendAddresses": [{
					"ipAddress": "10.0.0.10",
					"ipAddress": "10.0.0.11"
				}],
				"BackendIpConfigurations": [],
				"ProvisioningState": "Succeeded",
				"Name": "Pool01",
				"Etag": "W/"00000000-0000-0000-0000-000000000000"",
				"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
			}, {
				"BackendAddresses": [{
					"Fqdn": "xyx.cloudapp.net",
					"Fqdn": "abc.cloudapp.net"
				}],
				"BackendIpConfigurations": [],
				"ProvisioningState": "Succeeded",
				"Name": "Pool02",
				"Etag": "W/"00000000-0000-0000-0000-000000000000"",
				"Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
			}]


## BackendAddressPool の異常なインスタンス

### 原因

BackendAddressPool のインスタンスがすべて異常である場合、Application Gateway にユーザー要求のルーティング先となるバックエンドがありません。これは、バックエンド インスタンスは正常であるものの、必要なアプリケーションがデプロイされていない場合にも生じることがあります。

### 解決策

インスタンスが正常であり、アプリケーションが正しく構成されていることを確認してください。バックエンド インスタンスが同じ VNet 内に存在する他の VM からの ping に応答できることをチェックします。パブリック エンドポイントを構成している場合は、Web アプリケーションに対するブラウザーの要求を処理できるようにします。

## 既定の正常性プローブに関する問題

### 原因

502 エラーは、既定の正常性プローブがバックエンド VM に到達できないことを示している場合もよくあります。Application Gateway インスタンスがプロビジョニングされると、BackendHttpSetting のプロパティを使用して BackendAddressPool ごとに既定の正常性プローブが自動的に構成されます。このプローブの設定にはユーザーの入力は必要ありません。具体的には、負荷分散規則を構成する際に、BackendHttpSetting と BackendAddressPool の間で関連付けが行われます。既定のプローブがこれらの関連付けごとに構成されます。Application Gateway は、BackendHttpSetting 要素で指定されたポートで BackendAddressPool 内の各インスタンスに対して定期的な正常性チェック接続を開始します。次の表は、既定の正常性プローブに関連する値の一覧です。


|プローブのプロパティ | 値 | Description|
|---|---|---|
| プローブの URL| http://127.0.0.1/ | URL パス |
| 間隔 | 30 | プローブの間隔 (秒) |
| タイムアウト | 30 | プローブのタイムアウト (秒) |
| 異常のしきい値 | 3 | プローブの再試行回数。プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |

### 解決策

- 既定のサイトが構成されており、127.0.0.1 でリッスンしていることを確認します。
- BackendHttpSetting で 80 以外のポートが指定されている場合、既定のサイトはポート 80 でリッスンするように構成する必要があります。
- http://127.0.0.1:port を呼び出したときに、HTTP 結果コード 200 が30 秒のタイムアウト期間内に返されるようにする必要があります。
- 構成済みのポートを開き、構成済みのポートでの送受信トラフィックをブロックするファイアウォール規則または Azure ネットワーク セキュリティ グループが存在しないようにします。
- FQDN またはパブリック IP と共に Azure クラシック VM またはクラウド サービスを使用する場合、対応する[エンドポイント](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md)を必ず開いてください。
- Azure Resource Manager を介して VM を構成しており、Application Gateway がデプロイされた VNet の外側に VM がある場合、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)は、目的のポートにアクセスできるように構成する必要があります。


## カスタムの正常性プローブに関する問題

### 原因

カスタムの正常性プローブを使用すれば、既定のプローブ動作の柔軟性を高めることができます。カスタム プローブを使用する場合、ユーザーは、プローブの間隔、テスト対象の URL とパス、バックエンド プール インスタンスを "異常" とマークするまでの応答の失敗回数を構成することができます。次のプロパティが追加されます。

|プローブのプロパティ| Description|
|---|---|
| Name | プローブの名前。この名前は、バックエンドの HTTP 設定でプローブを参照するために使用されます。 |
| プロトコル | プローブを送信するために使用するプロトコル。唯一の有効なプロトコルは HTTP です。 |
| Host | プローブを送信するホスト名。Application Gateway でマルチサイトを構成する場合にのみ適用可能です。これは VM ホスト名とは異なります。 |
| パス | プローブの相対パス。パスは、先頭が '/' である必要があります。プローブの送信先は <protocol>://<host>:<port><path> になります。 |
| 間隔 | プローブの間隔 (秒)。2 つの連続するプローブの時間間隔。|
| タイムアウト | プローブのタイムアウト (秒)。プローブは、このタイムアウト期間内に正常な応答を受信しない場合に「失敗」とマークされます。 |
| 異常のしきい値 | プローブの再試行回数。プローブの連続失敗回数が異常のしきい値に達すると、バックエンド サーバーは「ダウン」とマークされます。 |


### 解決策

カスタムの正常性プローブが前の表のとおりに正しく構成されていることを確認します。前のトラブルシューティングの手順を実行したうえで、次のことも必ず行ってください。

- プロトコルが HTTP のみに設定されていることを確認する。現在 HTTPS はサポートされていません。
- プローブが[ガイド](application-gateway-create-probe-ps.md)のとおりに正しく指定されていることを確認する。
- Application Gateway を単一のサイトで構成する場合、既定ではホスト名は "127.0.0.1" と指定する必要があります (カスタム プローブで構成する場合は除く)。
- http://\<host>:<port><path> を呼び出したときに HTTP 結果コード 200 が返されることを確認する。
- 間隔、タイムアウト、異常のしきい値が許容される範囲内であることを確認する。

## 要求のタイムアウト

### 原因

ユーザー要求の受信時に、Application Gateway は構成済みの規則をその要求に適用し、要求をバックエンド プール インスタンスにルーティングします。Application Gateway は一定時間バックエンド インスタンスからの応答を待ちます。この時間間隔は構成できます。既定ではこの間隔は **30** 秒です。この間隔の間に Application Gateway がバックエンド アプリケーションから応答を受信しない場合、ユーザー要求の結果として 502 エラーが表示されます。

### 解決策

Application Gateway では、ユーザーは BackendHttpSetting でこの設定を構成し、別のプールに適用できます。バックエンド プールごとに異なる BackendHttpSetting を構成できるため、異なる要求のタイムアウトを構成できます。

	New-AzureRmApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60

## 次のステップ

前の手順で問題を解決できない場合は、[サポート チケット](https://azure.microsoft.com/support/options/)を開きます。

<!---HONumber=AcomDC_0907_2016-->