<properties
   pageTitle="Azure 診断でログを収集する方法 | Microsoft Azure"
   description="この記事では、Azure で実行されている Service Fabric クラスターのログを収集するように Azure 診断を設定する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ms-toddabel"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/20/2016"
   ms.author="toddabel"/>


# Azure 診断でログを収集する方法

Azure Service Fabric クラスターを実行している場合、1 か所ですべてのノードのログを収集することをお勧めします。1 か所でログを収集すると、クラスターと、そのクラスターで実行されているアプリケーションやサービスで発生する問題の分析と解決が簡単になります。ログのアップロードと収集には、ログを Azure Storage にアップロードする Azure 診断拡張機能を使用する方法があります。実際には、ログはストレージで直接的にはそれほど役立ちませんが、外部プロセスを使用してストレージからイベントを読み込み、[Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) などの製品や別のログ解析ソリューションに配置できます。

## 前提条件
これらのツールは、このドキュメントの操作の一部を実行するために使用されます。

* [Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics.md) (Azure Cloud Services と関連性はありますが、お勧めの情報と例が掲載されています)
* [Azure リソース マネージャー](../resource-group-overview.md)
* [Azure PowerShell](../powershell-install-configure.md)
* [Azure Resource Manager クライアント](https://github.com/projectkudu/ARMClient)

## 収集することができるさまざまなログ ソース
1. **Service Fabric ログ:** プラットフォームから標準の ETW と EventSource チャネルに対して生成されます。次のような種類のログがあります。
  - 操作イベント: Service Fabric プラットフォームで実行される操作のログです。たとえば、アプリケーションとサービスの作成、ノードの状態変化、アップグレード情報などです。
  - [Actor プログラミング モデル イベント](service-fabric-reliable-actors-diagnostics.md)
  - [Reliable Services プログラミング モデル イベント](service-fabric-reliable-services-diagnostics.md)
2. **アプリケーション イベント**: サービス コードから生成され、Visual Studio テンプレートで指定された EventSource ヘルパー クラスを使用して出力されるイベントです。アプリケーションからログを出力する方法については、[ローカル コンピューターの設定でサービスを監視および診断する方法に関する記事](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)を参照してください。


## 診断拡張機能のデプロイ
ログ収集の最初の手順は、Service Fabric クラスター内の各 VM に診断拡張機能をデプロイすることです。診断拡張機能を使用すると、各 VM のログが収集され、指定したストレージ アカウントにアップロードされます。Azure ポータルを使用するか Azure リソース マネージャーを使用するかによって、またクラスター作成の一環としてのデプロイか、既に存在するクラスターの場合かによって、一部の手順が変わります。各シナリオの手順を見てみましょう。

### ポータルを使用してクラスター作成の一環で診断拡張機能をデプロイする
クラスター作成の一環としてクラスター内の VM に診断拡張機能をデプロイするには、下図の [診断設定] パネルを使用します。アクターまたは Reliable Service イベントの収集を有効にするには、[診断] が既定の **[オン]** に設定されていることを確認してください。クラスターの作成後、これらの設定をポータルで変更することはできません。

![ポータルでのクラスター作成のための Azure 診断設定](./media/service-fabric-diagnostics-how-to-setup-wad/portal-cluster-creation-diagnostics-setting.png)

サポート要求の登録後、Azure サポート チームがそれを解決するためにサポート ログが**必要**になります。これらのログはリアルタイムで収集され、リソース グループで作成されたストレージ アカウントの 1 つに保存されます。診断設定により、[アクター](service-fabric-reliable-actors-diagnostics.md) イベントや [Reliable Service](service-fabric-reliable-services-diagnostics.md) イベントなどのアプリケーション レベル イベントと一部のシステム レベルの Service Fabric イベントは、Azure Storage に保存されるよう構成されます。[Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) などの製品や独自のプロセスでストレージ アカウントからイベントを選択できます。現在のところ、テーブルに送信されるイベントを絞り込む方法はありません。テーブルからイベントを削除するプロセスが実装されていない場合、テーブルは増加を続けます。ポータルを利用してクラスターを作成する場合、デプロイの完了後にテンプレートをエクスポートすることが推奨されます。テンプレートは次の方法でポータルからエクスポートできます。

1. リソース グループを開きます。
2. [設定] を選択し、[設定] パネルを表示します。
3. [デプロイ] を選択し、[デプロイ履歴] パネルを表示します。
4. デプロイを選択し、そのデプロイの詳細を表示します。
5. [テンプレートのエクスポート] を選択し、[テンプレート] パネルを表示します。
6. [保存] を選択し、テンプレート、パラメーター、PowerShell ファイルを含む .zip ファイルをエクスポートします。

ファイルのエクスポート後、変更が必要になります。**parameters.json** ファイルを編集し、**adminPassword** 要素を削除します。これでデプロイ スクリプトの実行時にパスワードが要求されます。

### Azure リソース マネージャー を使用してクラスター作成の一環で診断拡張機能をデプロイする
リソース マネージャーを使用してクラスターを作成するには、診断の構成 JSON を完全なクラスターのリソース マネージャー テンプレートに追加してから、クラスターを作成します。ここでは、リソース マネージャー テンプレート サンプルの一部として、診断構成を追加した five-VM クラスター リソース マネージャー テンプレートのサンプルを用意しました。このサンプルは、Azure サンプル ギャラリーの「[Five-node cluster with Diagnostics Resource Manager template sample](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad)」で参照できます。Resource Manager テンプレートの診断設定を確認するには、**azuredeploy.json** ファイルを開き、**IaaSDiagnostics** を検索します。このテンプレートを使用してクラスターを作成するには、上のリンクにある **[Azure にデプロイ]** ボタンをクリックしてください。

または、リソース マネージャー サンプルをダウンロードし、変更を加え、Azure PowerShell ウィンドウで `New-AzureRmResourceGroupDeployment` コマンドを使用して、変更したテンプレートでクラスターを作成する方法もあります。コマンドで渡す必要があるパラメーターについては、後述します。PowerShell を利用してリソース グループをデプロイする方法については、「[Azure Resource Manager のテンプレートを使用したリソース グループのデプロイ](../resource-group-template-deploy.md)」を参照してください。

```powershell

New-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName -Name $deploymentName -TemplateFile $pathToARMConfigJsonFile -TemplateParameterFile $pathToParameterFile –Verbose
```

### 既存のクラスターに診断拡張機能をデプロイする
まだ診断がデプロイされていない既存のクラスターがある場合、次の手順で診断を追加できます。既存クラスターに作成に使用された ARM テンプレートを変更するか、上記の説明に基づき、ポータルからテンプレートをダウンロードします。次のタスクを実行し、**template.json** ファイルを変更します。

リソース セクションを増やすことで新しいストレージ リソースをテンプレートに追加します。

##### resources セクションを更新する
```json
{
  "apiVersion": "2015-05-01-preview",
  "type": "Microsoft.Storage/storageAccounts",
  "name": "[parameters('applicationDiagnosticsStorageAccountName')]",
  "location": "[parameters('computeLocation')]",
  "properties": {
    "accountType": "[parameters('applicationDiagnosticsStorageAccountType')]"
  },
  "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
  }
},
```

 次に、ストレージ アカウント定義の直後の、"supportLogStorageAccountName" と "vmNodeType0Name" の間でパラメーター セクションを増やします。プレースホルダー テキストの *storage account name goes here* を任意のストレージ アカウントの名前に置き換えます。

##### parameters セクションを更新する
```json
    "applicationDiagnosticsStorageAccountType": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "Replication option for the application diagnostics storage account"
      }
    },
    "applicationDiagnosticsStorageAccountName": {
      "type": "string",
      "defaultValue": "storage account name goes here",
      "metadata": {
        "description": "Name for the storage account that contains application diagnostics data from the cluster"
      }
    },
```
"extensions" 配列内に次のコードを追加し、**template.json** の *VirtualMachineProfile* セクションを更新します。挿入箇所に合わせ、始めまたは終わりにコンマを追加します。

##### VirtualMachineProfile の拡張子配列を増やす
```json
{
	"name": "[concat(parameters('vmNodeType0Name'),'_Microsoft.Insights.VMDiagnosticsSettings')]",
	"properties": {
		"type": "IaaSDiagnostics",
		"autoUpgradeMinorVersion": true,
		"protectedSettings": {
		"storageAccountName": "[parameters('applicationDiagnosticsStorageAccountName')]",
		"storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('applicationDiagnosticsStorageAccountName')),'2015-05-01-preview').key1]",
		"storageAccountEndPoint": "https://core.windows.net/"
		},
		"publisher": "Microsoft.Azure.Diagnostics",
		"settings": {
		"WadCfg": {
			"DiagnosticMonitorConfiguration": {
			"overallQuotaInMB": "50000",
			"EtwProviders": {
				"EtwEventSourceProviderConfiguration": [
				{
					"provider": "Microsoft-ServiceFabric-Actors",
					"scheduledTransferKeywordFilter": "1",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricReliableActorEventTable"
					}
				},
				{
					"provider": "Microsoft-ServiceFabric-Services",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricReliableServiceEventTable"
					}
				}
				],
				"EtwManifestProviderConfiguration": [
				{
					"provider": "cbd93bc2-71e5-4566-b3a7-595d8eeca6e8",
					"scheduledTransferLogLevelFilter": "Information",
					"scheduledTransferKeywordFilter": "4611686018427387904",
					"scheduledTransferPeriod": "PT5M",
					"DefaultEvents": {
					"eventDestination": "ServiceFabricSystemEventTable"
					}
				}
				]
			}
			}
		},
		"StorageAccount": "[parameters('applicationDiagnosticsStorageAccountName')]"
		},
		"typeHandlerVersion": "1.5"
	}
}
```

説明したように **template.json** ファイルを変更したら、ARM テンプレートを再発行します。テンプレートのエクスポート後、**deploy.ps1** ファイルを実行すると、テンプレートが再発行されます。デプロイ後、*[ProvisioningState]* が *[成功]* になります。


## 新しい EventSource チャネルのログを収集およびアップロードするように診断を更新する
デプロイする予定の新しいアプリケーションを示す新しい EventSource チャネルのログを収集するように診断を更新するには、既存のクラスターに対する診断の設定について説明した[前述のセクション](#deploywadarm)と同じ手順を実行する必要があります。**template.json** の *EtwEventSourceProviderConfiguration* セクションを更新し、新しい EventSource のエントリを追加してから、*New-AzureRmResourceGroupDeployment* PowerShell コマンドで構成の更新を適用する必要があります。


## 次のステップ
問題を解決する際に確認する必要があるイベントの詳細については、[Reliable Actors](service-fabric-reliable-actors-diagnostics.md) と [Reliable Services](service-fabric-reliable-services-diagnostics.md) で生成される診断イベントを参照してください。

<!---HONumber=AcomDC_0525_2016-->