<properties
   pageTitle="Visual Studio を使用した Service Fabric クラスターのセットアップ | Microsoft Azure"
   description="Visual Studio の Azure リソース グループ プロジェクトで作成した Azure リソース マネージャー (ARM) テンプレートを使用して Service Fabric クラスターをセットアップする方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/11/2016"
   ms.author="karolz@microsoft.com"/>

# Visual Studio による Service Fabric クラスターのセットアップ
この記事では、Visual Studio と Azure リソース マネージャー (ARM) テンプレートを使用して Azure Service Fabric クラスターをセットアップする方法について説明します。テンプレートを作成するために、Visual Studio Azure リソース グループ プロジェクトを使用します。作成したテンプレートは、Visual Studio から Azure に直接デプロイできますが、スクリプトから使用したり、継続的インテグレーション (CI) 機能の一部として使用したりすることもできます。

## Azure リソース グループ プロジェクトを使用して Service Fabric クラスター テンプレートを作成する
まず、Visual Studio を開き、Azure リソース グループ プロジェクトを作成します (**[クラウド]** フォルダーにあります)。

![[Azure リソース グループ] プロジェクトが選択されている [新しいプロジェクト] ダイアログ][1]

このプロジェクト用の新しい Visual Studio ソリューションを作成することも、既存のソリューションに追加することもできます。

>[AZURE.NOTE] [クラウド] ノードの下に [Azure リソース グループ] プロジェクトがない場合は、Azure SDK がインストールされていません。Web Platform Installer を起動し (まだインストールしていない場合は、[ここでインストールしてください](http://www.microsoft.com/web/downloads/platform.aspx))、"Azure SDK for .NET" を検索して、お使いの Visual Studio のバージョンと互換性があるバージョンをインストールしてください。

[OK] ボタンをクリックすると、次のように、作成するリソース マネージャー テンプレートを選択するよう求められます。

![Service Fabric クラスター テンプレートが選択されている [Azure テンプレートの選択] ダイアログ][2]

[Service Fabric クラスター] テンプレートを選択し、もう一度 [OK] ボタンをクリックします。これで、プロジェクトおよびリソース マネージャー テンプレートが作成されました。

## デプロイ用のテンプレートを準備する
テンプレートをデプロイしてクラスターを作成する前に、必要なテンプレート パラメーターの値を指定する必要があります。これらのパラメーター値は、`ServiceFabricCluster.parameters.json` ファイルから読み取られます。このファイルは、リソース グループ プロジェクトの `Templates` フォルダーにあります。ファイルを開き、次の値を指定します。

|パラメーター名 |説明|
|-----------------------  |--------------------------|
|clusterLocation |Service Fabric クラスターを配置する **Azure リージョン**の名前。たとえば、"East US" などとします。|
|certificateThumbprint |クラスターをセキュリティで保護する証明書の拇印。|
|sourceVaultResourceId |クラスターをセキュリティで保護する証明書が格納されている Key Vault の*リソース ID*。|
|certificateUrlValue |クラスターのセキュリティ証明書の URL。|

Visual Studio Service Fabric リソース マネージャー テンプレートは、証明書によって保護されている、安全なクラスターを作成します。この証明書は、最後の 3 つのテンプレート パラメーター (`certificateThumbprint`、`sourceVaultValue`、`certificateUrlValue`) によって識別され、**Azure Key Vault** 内に存在する必要があります。クラスター セキュリティ証明書を作成する方法の詳細については、「[Service Fabric クラスターのセキュリティ保護](service-fabric-cluster-security.md#secure-a-service-fabric-cluster-by-using-certificates)」を参照してください。

## 省略可能: クラスター名を変更する
Service Fabric クラスターにはそれぞれ名前が与えられています。Fabric クラスターが Azure で作成されると、クラスター名により、(Azure リージョンと共に) クラスターの DNS (ドメイン ネーム システム) が決定されます。たとえば、クラスターに `myBigCluster` という名前を付けたとき、`clusterLocation` パラメーターが米国東部に設定されている場合、クラスターの DNS 名は `myBigCluster.eastus.cloudapp.azure.com` になります。

既定では、クラスター名は自動生成されます。"cluster" プレフィックスにランダムなサフィックスを付けることで一意に作成されます。これにより、**継続的インテグレーション** (CI) システムの一部としてテンプレートを使用することが非常に簡単になります。自分にとってわかりやすい名前をクラスターに使用する場合、リソース マネージャー テンプレート ファイル (`ServiceFabricCluster.json`) の `clusterName` 変数の値を選択した名前に設定します。そのファイルに定義されている最初の変数です。

## 省略可能: パブリック アプリケーション ポートを追加する
デプロイする前に、クラスターのパブリック アプリケーション ポートを変更することもできます。既定では、テンプレートが開くパブリック TCP ポートは 2 つだけです (80 と 8081)。アプリケーションにさらに多くのポートが必要な場合は、テンプレート内の Azure Load Balancer の定義を変更します。定義は、メイン テンプレート ファイル (`SecureFabricCluster.json`) に保存されています。そのファイルを開き、`loadBalancedAppPort` を検索します。各ポートが、次の 3 つのアーティファクトに関連付けられていることがわかります。

1. ポートの TCP ポート値を定義するテンプレート変数:

	```json
	"loadBalancedAppPort1": "80"
	```

2. Azure ロード バランサーが特定の Service Fabric ノードの使用を試行する頻度と期間を定義する*プローブ*。この期間を過ぎると、別のノードにフェールオーバーします。プローブは、Load Balancer リソースの一部です。最初の既定のアプリケーション ポートのプローブ定義は、次のようになります。

	```json
	{
        "name": "AppPortProbe1",
        "properties": {
            "intervalInSeconds": 5,
            "numberOfProbes": 2,
            "port": "[variables('loadBalancedAppPort1')]",
            "protocol": "Tcp"
        }
    }
	```

3. ポートとプローブを関連付け、一連の Service Fabric クラスター ノード間で負荷を分散する*負荷分散ルール*:

    ```json
	{
	    "name": "AppPortLBRule1",
	    "properties": {
	        "backendAddressPool": {
	            "id": "[variables('lbPoolID0')]"
	        },
	        "backendPort": "[variables('loadBalancedAppPort1')]",
	        "enableFloatingIP": false,
	        "frontendIPConfiguration": {
	            "id": "[variables('lbIPConfig0')]"
	        },
	        "frontendPort": "[variables('loadBalancedAppPort1')]",
	        "idleTimeoutInMinutes": 5,
	        "probe": {
	            "id": "[concat(variables('lbID0'),'/probes/AppPortProbe1')]"
	        },
	        "protocol": "Tcp"
	    }
	}
    ```
クラスターにデプロイする予定のアプリケーションで追加のポートが必要になる場合、プローブと負荷分散ルール定義を追加作成することで、ポートを追加できます。リソース マネージャー テンプレートを通じて Azure Load Balancer を操作する方法の詳細については、「[Get started creating an internal load balancer using a template (テンプレートを使用した内部 Load Balancer の作成の開始)](../load-balancer/load-balancer-get-started-ilb-arm-template.md)」を参照してください。

## Visual Studio を使用してテンプレートをデプロイする
必要なパラメーター値すべてを `ServiceFabricCluster.param.dev.json` ファイルに保存したら、テンプレートをデプロイして Service Fabric クラスターを作成できます。Visual Studio ソリューション エクスプローラーでリソース グループ プロジェクトを右クリックし、**[デプロイ]** をクリックします。必要に応じて、Azure に対する認証を求める **[リソース グループに配置する]** ダイアログ ボックスが表示されます。

![[リソース グループに配置する] ダイアログ ボックス][3]

このダイアログ ボックスでは、クラスターの既存のリソース マネージャー リソース グループを選択したり、新しいグループを作成したりすることができます。通常は、Service Fabric クラスター用に別のリソース グループを使用することには意味があります。

[配置] ボタンをクリックすると、テンプレート パラメーター値の確認を求めるメッセージが表示されます。**[保存]** ボタンをクリックします。永続的な値を持たないパラメーターが 1 つあります。それは、クラスターの管理者アカウント パスワードです。パスワードの入力を求められたときに、パスワード値を指定する必要があります。

>[AZURE.NOTE] 現在使用しているコンピューターから Azure を管理するために PowerShell を使用したことがない場合は、維持管理作業を少し行う必要があります。
>1. [`Set-ExecutionPolicy`](https://technet.microsoft.com/library/hh849812.aspx) コマンドを実行して、PowerShell スクリプトを有効にします。開発用コンピューターでは、通常、"Unrestricted" ポリシーでかまいません。
>2. Azure PowerShell コマンドからの診断データの収集を許可するかどうかを決定し、必要に応じて [`Enable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619303.aspx) または [`Disable-AzureRmDataCollection`](https://msdn.microsoft.com/library/mt619236.aspx) を実行します。こうすることで、テンプレートのデプロイ中に不要なメッセージが表示されないようにすることができます。

Visual Studio の出力ウィンドウで、デプロイ プロセスの進行状況を監視することができます。テンプレートのデプロイが完了したら、新しいクラスターを使用できます。

エラーが発生した場合、[Azure ポータル](https://portal.azure.com/)に進み、デプロイしたリソース グループを開きます。**[すべての設定]** をクリックし、[設定] ブレードで **[デプロイ]** をクリックします。リソース グループのデプロイが失敗すると、詳細な診断情報がここに表示されます。

>[AZURE.NOTE] Service Fabric クラスターが可用性を維持し、状態を保持するには、一定数のノードが常にアップしている必要があります。これは、「維持クォーラム」と呼ばれます。そのため、先に[状態の完全なバックアップ](service-fabric-reliable-services-backup-restore.md)を実行しない限り、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。

## 次のステップ
- [Azure ポータルによる Service Fabric クラスターのセットアップについて学習する](service-fabric-cluster-creation-via-portal.md)
- [Visual Studio による Service Fabric アプリケーションの管理とデプロイの方法について学習する](service-fabric-manage-application-in-visual-studio.md)

<!--Image references-->
[1]: ./media/service-fabric-cluster-creation-via-visual-studio/azure-resource-group-project-creation.png
[2]: ./media/service-fabric-cluster-creation-via-visual-studio/selecting-azure-template.png
[3]: ./media/service-fabric-cluster-creation-via-visual-studio/deploy-to-azure.png

<!---HONumber=AcomDC_0316_2016-->