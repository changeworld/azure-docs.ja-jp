<properties
   pageTitle="Azure ポータルからの Service Fabric クラスターのセットアップ | Microsoft Azure"
   description="Azure ポータルからの Service Fabric クラスターのセットアップ。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/19/2015"
   ms.author="chackdan"/>

# Azure ポータルからの Service Fabric クラスターのセットアップ

このページでは、Service Fabric クラスターのセットアップについて説明します。サブスクリプションに、このクラスターを構成する IaaS VM をデプロイできるだけのコア数が割り当てられていることが前提となります。


## クラスターの作成

1. Azure ポータル ([http://aka.ms/servicefabricportal](http://aka.ms/servicefabricportal)) にログオンします。

2. **[新規]** をクリックして、新しいリソース テンプレートを追加します。Marketplace の [すべて] で **Service Fabric Cluster** という名前のテンプレートを探します。最上位レベル カテゴリ [Marketplace] をクリックして **[すべて]** に移動し、[すべて] で "Fabric" を探し、Enter を押します。自動フィルターが働かないことがあるので、必ず **Enter を押します**。![SearchforServiceFabricClusterTemplate][SearchforServiceFabricClusterTemplate]

3. 一覧から "Service Fabric Cluster" を選択します
4. Service Fabric Cluster ブレードに移動し、**[作成]** をクリックしてクラスターの詳細を入力します。
5. **新しいリソース グループ (RG)** を作成します。クラスター名と同じにします。後で探すときに楽です。デプロイメントを変更したりクラスターを削除するときに特に便利です。

  	注 - 既存のリソース グループを使用することもできますが、新しいリソース グループを作成することをお勧めします。そうすれば、必要のないクラスターの削除がとても簡単です。

 	 ![CreateRG][CreateRG]


6. クラスターをデプロイする**サブスクリプション**を選択します (特に、複数のサブスクリプションがある場合)。

7. ドロップダウンから**場所**を選択します (既定の米国西部以外の場所に作成する場合)。

8. **ノードの種類**を構成します。ノードの種類は、Cloud Services の「ロール」と同等のものと見なすことができます。VM のサイズ、VM の数、プロパティが定義されています。1 つのクラスターで複数のノードの種類を使用できます。唯一の制約は、少なくとも 1 つのノードの種類 (プライマリ、つまりポータルで定義する最初のノード) を 5 VM 以上にすることです。
	1. 必要な VM サイズと価格レベルを選択します (既定は D4 Standard、クラスターをアプリケーションのテストだけに使用する場合は D2 以下の VM サイズを選択)。	
	2. VM の数を選択します。後でノードの種類の VM 数をスケールアップまたはスケールダウンできます。ただし、プライマリつまり最初のノードの種類は 5 VM 以上にする必要があります。
	3. ノードの種類の名前を選択します (1 ～ 12 文字で、英数字のみ)。	
	4. VM リモート デスクトップ ユーザーの名前とパスワードを選択します。
	5. **複数のノード種類がある場合の考慮事項**。単一ノード種類のクラスターをデプロイする場合は、次の手順を省略します。

		- 例を使って概念を説明します。「フロントエンド」サービスと「バックエンド」サービスのあるアプリケーションをデプロイし、「フロントエンド」サービスはインターネットに開いたポートのある小さい VM (VM サイズ A2、D2 など) に配置し、計算量の多い「バックエンド」サービスはインターネットに接続していない大きい VM (VM サイズ D4、D6、D12 など) に配置します。
		- 両方のサービスを 1 つのノード種類に配置することもできますが、2 ノード種類のクラスターに配置することをお勧めします。各ノード種類は、プロパティが異なっていてもよく (インターネット接続など)、VM のサイズと数は独立して変更できます。
		- 最初に、5 VM 以上にするノードの種類を定義します。他のノードの種類は、最少 1 個の VM でかまいません。
					

  	![CreateNodeType][CreateNodeType]

9. **アプリケーション ポート** - クラスターにアプリケーションをすぐにデプロイする場合は、そのノード種類 (または作成したノード種類) でアプリケーションに対して開くポートを追加します。このノード種類に関連付けられているロード バランサーを変更することにより、後でノード種類にポートを追加できます (プローブを追加した後、ロード バランサー ルールにプローブを追加する必要があります)。今すぐ行うと、ポータルの Automation が必要なプローブとルールを LB に追加するので少し楽です。
	1. アプリケーション パッケージの一部であるサービス マニフェストでアプリケーション ポートを検索できます。各アプリケーションに移動し、サービス マニフェストを開き、アプリケーションが外界と通信するために必要なすべての "input" エンドポイントを記録します。
	2. [アプリケーション入力エンドポイント] フィールドにすべてのポートをコンマで区切って追加します。TCP クライアント接続エンドポイント - 既定で 19000 なので、指定する必要はありません。たとえば、アプリケーションではポート "83" を開く必要があります。これは、アプリケーション パッケージの servicemanifest.xml にあります (複数の servicemanifest.xml が存在することがあります)。

  ほとんどのサンプル アプリケーションはポート 80 と 8081 を使用するので、このクラスターにサンプルをデプロイする場合はそれらを追加します。

  ![ポート][Ports]



10. **オプション: 配置プロパティ** - ここでは構成を追加する必要はありません。"NodeTypeName" の既定の配置プロパティがシステムによって追加されます。アプリケーションで必要な場合はさらに追加できます。 

  
## セキュリティの構成

現時点では、Service Fabric は X509 証明書によるクラスターの保護のみをサポートします。このプロセスを開始する前に、KeyVault に証明書をアップロードする必要があります。方法の詳細については、「[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)」を参照してください。

クラスターのセキュリティ保護はオプションですが、強くお勧めします。クラスターをセキュリティで保護しない場合は、セキュリティ モードを「なし」に切り替える必要があります。

セキュリティの考慮事項と方法の詳細については、「[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)」を参照してください。

![SecurityConfigs][SecurityConfigs]



## オプション: 診断の構成

既定では、問題のトラブルシューティングのためクラスターで診断が有効になります。診断を無効にする場合:

1. [診断構成] ブレードに移動します。

2. [状態] を [オフ] に変更します。

## オプション: ファブリックの設定

これは高度なオプションであり、Service Fabric クラスターの既定の設定を変更できます。アプリケーションまたはクラスターで必要であることが確実でない限り、既定は**変更しない**ことをお勧めします。

## クラスター作成の完了
**[概要]** をクリックして、提供した構成を確認するか、またはクラスターのデプロイに使用する ARM テンプレートをダウンロードできます。必須の設定を指定すると、[作成] ボタンが有効になり、クラスター作成プロセスを開始できます。
 

[通知] で進行状況を確認できます (画面右側のステータス バーの近くにある「ベル」アイコンをクリック)。クラスターの作成中に [スタート画面にピン留めする] をクリックした場合、[Service Fabric クラスターのデプロイ] がスタート画面にピン留めされます。

![通知][Notifications]

## クラスターの状態の表示

デプロイメントが完了した後、ポータルでクラスターを検査できます。

1. **[参照]** に移動し、リソース **[Service Fabric クラスター]** をクリックします。

2. クラスターを探してクリックします。

  ![BrowseCluster][BrowseCluster]

3. これにより、クラスターのパブリック IP アドレスなどのクラスターに関する詳細が、ダッシュボードに表示されます。**[クラスター パブリック IP アドレス]** の上にポインターを移動するとクリップボードが開き、クリックしてコピーできます。

  ![ClusterDashboard][ClusterDashboard]

  クラスター ダッシュボード ブレードのノード モニター部分では、正常な VM と異常な VM の数が示されます。正常性状態の詳細は、「[Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)」を参照してください。


## クラスターへの接続とアプリケーションのデプロイ

クラスターをセットアップした後は、接続してアプリケーションのデプロイを開始できます。

1. Service Fabric SDK がインストールされているコンピューターでは、Windows PowerShell を起動します。

2. 作成したクラスターがセキュリティで保護されているかどうかに応じて、次の PS コマンド セットのいずれかを実行します。
 

- オプション 1: **セキュリティ保護されていないクラスターへの接続**。 

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10 
```

次のコマンドを実行してアプリケーションをデプロイします。パスは実際のコンピューターのものに置き換えます。

 - オプション 2: **セキュリティで保護されたクラスターへの接続**

次のコマンドを実行し、"Connect-serviceFabricCluster" PS コマンドの実行に使用するコンピューターに証明書を設定します。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

次の PS を実行してセキュアなクラスターに接続します。証明書の詳細はポータルで指定したものと同じです。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
            -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
            -StoreLocation CurrentUser -StoreName My
```
たとえば、上の PS コマンドの結果は次のようになります。

```powershell
Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
            -KeepAliveIntervalInSec 10 `
            -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
            -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
            -StoreLocation CurrentUser -StoreName My
```


次のコマンドを実行してアプリケーションをデプロイします。パスは実際のコンピューターのものに置き換えます。次の例は、単語数サンプル アプリケーションをデプロイします。

前の手順で接続したクラスターにパッケージをコピーします。


```powershell
$applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
```

```powershell
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
````
アプリケーションの種類を Service Fabric に登録します。

```powershell
Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
````

登録したアプリケーションの種類で新しいインスタンスを作成します。

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
````

選択したブラウザーが開き、アプリケーションがリッスンしているエンドポイントに接続します。このサンプル アプリケーションの場合、URL は次のようになります。

http://sfcluster4doc.westus.cloudapp.azure.com:31000



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)

<!--Image references-->
[SearchforServiceFabricClusterTemplate]: ./media/service-fabric-cluster-creation-via-portal/SearchforServiceFabricClusterTemplate.png
[CreateRG]: ./media/service-fabric-cluster-creation-via-portal/CreateRG.png
[CreateNodeType]: ./media/service-fabric-cluster-creation-via-portal/NodeType.png
[Ports]: ./media/service-fabric-cluster-creation-via-portal/ports.png
[SFConfigurations]: ./media/service-fabric-cluster-creation-via-portal/SFConfigurations.png
[SecurityConfigs]: ./media/service-fabric-cluster-creation-via-portal/SecurityConfigs.png
[Notifications]: ./media/service-fabric-cluster-creation-via-portal/notifications.png
[BrowseCluster]: ./media/service-fabric-cluster-creation-via-portal/browse.png
[ClusterDashboard]: ./media/service-fabric-cluster-creation-via-portal/ClusterDashboard.png
[SecureConnection]: ./media/service-fabric-cluster-creation-via-portal/SecureConnection.png

<!-----HONumber=AcomDC_1203_2015-->