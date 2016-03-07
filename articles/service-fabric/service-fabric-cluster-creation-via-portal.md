<properties
   pageTitle="Azure ポータルから Service Fabric クラスターを作成する | Microsoft Azure"
   description="Azure ポータルから Service Fabric クラスターを作成します。"
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
   ms.date="02/12/2016"
   ms.author="chackdan"/>


# Azure ポータルから Service Fabric クラスターを作成する

ここでは、Azure Service Fabric クラスターをセットアップする方法について説明します。サブスクリプションに、このクラスターを構成する IaaS VM をデプロイできるだけのコア数が割り当てられている必要があります。


## クラスターを作成する

1. [Azure ポータル](https://portal.azure.com/)にサインインします。

2. **[新規]** をクリックして、新しいリソース テンプレートを追加します。**[Marketplace]** の **[すべて]** で、テンプレートを検索します。これは **Service Fabric クラスター**と呼ばれます。

    a.最上位にある **[Marketplace]** をクリックします。

    b.**[すべて]** の **[ファブリック]** をクリックし、Enter キーを押します。オート フィルターが動作しないことがあるので、Enter キーを押してください。![Azure ポータルで Service Fabric クラスター テンプレートを検索するスクリーン ショット。][SearchforServiceFabricClusterTemplate]

3. 一覧から **[Service Fabric クラスター]** を選択します。

4. **[Service Fabric クラスター]** ブレードに移動し、**[作成]** をクリックしてクラスターの詳細を入力します。

5. **[新しいリソース グループの作成]** で、リソース グループにクラスターと同じ名前を付けます。同じ名前にすると、デプロイメントを変更する場合や、クラスターを削除する場合など、後で検索するときに便利です。

    >[AZURE.NOTE] 既存のリソース グループを使用することもできますが、新しいリソース グループを作成することをお勧めします。こうすることで、不要になったクラスターを簡単に削除できます。

 	![新しいリソース グループを作成するスクリーン ショット。][CreateRG]

6. クラスターをデプロイする**サブスクリプション**を選択します (特に、複数のサブスクリプションがある場合)。

7. ドロップダウン リストから **[場所]** を選択します。既定値は **[米国西部]** です。

8. **[ノードのタイプ]**を構成します。ノードのタイプは、Cloud Services のロールと同等のものと見なすことができます。ノードのタイプには、VM のサイズ、VM の数、プロパティが定義されています。クラスターには複数のノードのタイプを指定できますが、プライマリ ノードのタイプ (ポータルに最初に定義したノード) には、少なくとも 5 つの VM が必要です。ノードのタイプを構成するには:

	a.必要な VM のサイズと価格レベルを選択します。既定値は D4 Standard ですが、アプリケーションのテストにのみこのクラスターを使用する場合は、D2 以下の小さな VM を選択できます。

	b.VM の数を選択します。後でノードのタイプの VM 数を増減できますが、最初のノードのタイプには、5 個以上の VM が必要です。

	c.ノードのタイプの名前を選択します (英字と数字のみを含む 1 ～ 12 文字)。

	d.VM リモート デスクトップの**ユーザー名**と**パスワード**を選択します。

	e.クラスターに複数のノードのタイプが必要な場合は、次の問題について検討してください(ノードのタイプが 1 つのクラスターをデプロイする予定の場合は、手順 9 に進みます)。

	* たとえば、フロントエンド サービスとバックエンド サービスを含むアプリケーションをデプロイするとします。インターネットに対してポートを開いている小さい VM (A2、D2 などの VM サイズ) にフロントエンド サービスをデプロイする予定です。一方で、インターネットに接続していない大きな VM (D4、D6、D12 などの VM サイズ) に計算負荷が高いバックエンド サービスをデプロイする予定です。

	* 1 つのノードのタイプに両方のサービスをデプロイすることはできますが、2 つのノードのタイプを持つクラスターにデプロイすることをお勧めします。ノードのタイプごとに、インターネット接続、VM サイズ、VM 数など、異なるプロパティを指定し、個別に拡張することができます。

	* 少なくとも 5 個の VM を含めるノードのタイプを最初に定義します。他のノードのタイプは、最少 1 個の VM でかまいません。

  	![ノードのタイプのスクリーン ショット。][CreateNodeType]

9. クラスターにアプリケーションをすぐにデプロイする場合は、**[アプリケーション ポート]** のノード種類 (または作成したノード種類) のアプリケーションに対して開くポートを追加します。後でノードのタイプにポートを追加するには、そのノードのタイプに関連付けられているロード バランサーを変更します(プローブを追加し、そのプローブをロード バランサー ルールに追加します)。 今すぐ行うと、ポータルの Automation が必要なプローブとルールをロード バランサーに追加するので少し楽です。

	a.アプリケーション パッケージの一部であるサービス マニフェストでアプリケーション ポートを検索できます。各アプリケーションに移動し、サービス マニフェストを開き、アプリケーションが外界と通信するために必要なすべての入力エンドポイントを記録します。

	b.**[アプリケーション入力エンドポイント]** フィールドにすべてのポートをコンマで区切って追加します。TCP クライアント接続エンドポイント - 既定で 19000 なので、指定する必要はありません。たとえば、サンプル アプリケーション WordCount の場合、ポート 83 を開く必要があります。この設定は、アプリケーション パッケージの servicemanifest.xml ファイルにあります(複数の servicemanifest.xml ファイルが存在する場合があります)。

    c.ほとんどのサンプル アプリケーションはポート 80 と 8081 を使用するので、このクラスターにサンプルをデプロイする場合はそれらを追加します。![ポート][Ports]

10. "NodeTypeName" の既定の配置プロパティはシステムによって追加されるので、**[配置プロパティ]** を構成する必要はありません。アプリケーションに必要な場合は、追加することもできます。

## セキュリティを構成する

現時点では、Service Fabric は X509 証明書によるクラスターの保護のみをサポートします。このプロセスを開始する前に、Key Vault に証明書をアップロードする必要があります。この方法の詳細については、「[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)」を参照してください。

クラスターのセキュリティ保護はオプションですが、強くお勧めします。クラスターをセキュリティで保護しない場合は、**[セキュリティ モード]** を **[なし]** に切り替える必要があります。

セキュリティの考慮事項と手順については、「[Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)」を参照してください。

![Azure ポータルのセキュリティ構成のスクリーン ショット。][SecurityConfigs]

## 省略可能: 診断を構成する

既定では、問題のトラブルシューティングのためクラスターで診断が有効になります。診断を無効にするには:

1. **[診断構成]** ブレードに移動します。

2. **[状態]** を **[オフ]** に変更します。

## 省略可能: Service Fabric クラスター設定を変更する

これは詳細オプションなので、Service Fabric クラスターの既定の設定を変更できます。アプリケーションまたはクラスターに必要な場合を除き、既定の設定を変更しないことをお勧めします。

## クラスターの作成を完了する

クラスターの作成を完了するには、**[概要]** をクリックして設定した構成を確認するか、クラスターのデプロイに使用する Azure リソース マネージャー テンプレートをダウンロードします。必須の設定を指定すると、**[作成]** ボタンが有効になります。このボタンをクリックして、クラスター作成プロセスを開始できます。

通知には作成の進行状況が表示されます(画面の右上にあるステータス バーの近くの "ベル" アイコンをクリックします)。 クラスターの作成中に **[スタート画面にピン留めする]** をクリックした場合、**[Service Fabric クラスターのデプロイ]** が**スタート**画面にピン留めされます。

!["Service Fabric クラスターのデプロイ中" が表示されているスタート画面のスクリーン ショット。][Notifications]

## クラスターの状態を表示する

デプロイメントが完了した後、ポータルでクラスターを検査できます。

1. **[参照]** に移動し、**[Service Fabric クラスター]** をクリックします。

2. クラスターを探してクリックします。![ポータルでクラスターを検索するスクリーン ショット。][BrowseCluster]

3. これにより、クラスターのパブリック IP アドレスなどのクラスターに関する詳細が、ダッシュボードに表示されます。**[クラスター パブリック IP アドレス]** の上にポインターを移動するとクリップボードが開き、アドレスをクリックしてコピーできます。![ダッシュボードのクラスターの詳細のスクリーン ショット。][ClusterDashboard]

  クラスターのダッシュボード ブレードの **[ノード モニター]** セクションには、正常な VM 数と正常ではない VM 数が表示されます。クラスターの正常性の詳細については、「[Service Fabric の正常性モニタリングの概要](service-fabric-health-introduction.md)」を参照してください。

>[AZURE.NOTE] Service Fabric クラスターが可用性を維持し、状態を保持するには、一定数のノードが常にアップしている必要があります。これは、「維持クォーラム」と呼ばれます。そのため、先に[状態の完全なバックアップ](service-fabric-reliable-services-backup-restore.md)を実行しない限り、クラスター内のすべてのコンピューターをシャットダウンするのは一般に安全ではありません。

## クラスターに接続してアプリケーションをデプロイする

クラスターをセットアップした後は、接続してアプリケーションのデプロイを開始できます。まず、Service Fabric SDK がインストールされているコンピューターで Windows PowerShell を起動します。次に、作成したクラスターがセキュリティで保護されているかどうかに応じて、次の PowerShell コマンド セットのいずれかを実行して、クラスターに接続します。

- オプション 1: セキュリティで保護されていないクラスターへの接続。

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 -KeepAliveIntervalInSec 10
    ```

- オプション 2: セキュリティで保護されたクラスターへの接続。

    1. 次のコマンドを実行し、"Connect-serviceFabricCluster" PowerShell コマンドの実行に使用するコンピューターに証明書を設定します。

        ```powershell
        Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
                -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
                -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
        ```

    2. 次の PowerShell コマンドを実行して、セキュリティで保護されたクラスターに接続します。証明書の詳細はポータルで指定したものと同じです。

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
                  -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
                  -StoreLocation CurrentUser -StoreName My
        ```

        たとえば、上の PowerShell コマンドの結果は次のようになります。

        ```powershell
        Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
                  -KeepAliveIntervalInSec 10 `
                  -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
                  -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
                  -StoreLocation CurrentUser -StoreName My
        ```

接続が完了するので、次のコマンドを実行してアプリケーションをデプロイします。パスは実際のコンピューターのものに置き換えます。次の例は、単語数サンプル アプリケーションをデプロイします。

1. 前の手順で接続したクラスターにパッケージをコピーします。

    ```powershell
    $applicationPath = "C:\VS2015\WordCount\WordCount\pkg\Debug"
    ```

    ```powershell
    Copy-ServiceFabricApplicationPackage -ApplicationPackagePath $applicationPath -ApplicationPackagePathInImageStore "WordCount" -ImageStoreConnectionString fabric:ImageStore
    ```
2. アプリケーションの種類を Service Fabric に登録します。

    ```powershell
    Register-ServiceFabricApplicationType -ApplicationPathInImageStore "WordCount"
    ```

3. 登録したアプリケーションの種類で新しいインスタンスを作成します。

    ```powershell
    New-ServiceFabricApplication -ApplicationName fabric:/WordCount -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0.0
    ```

4. 選択したブラウザーが開き、アプリケーションがリッスンしているエンドポイントに接続します。サンプル アプリケーション WordCount の場合、URL は次のようになります。

    http://sfcluster4doc.westus.cloudapp.azure.com:31000

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## 仮想マシン スケール セット (VMSS) インスタンスまたはクラスター ノードへの RDP 接続 

クラスターで指定する NodeType ごとに、VMSS がセットアップされます。詳細については、「[How to RDP into your VMSS instance (VMSS インスタンスに RDP 接続する方法)](service-fabric-cluster-nodetypes.md)」を参照してください。

## 次のステップ

- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)
- [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)
- [How to RDP into your VMSS instance (VMSS インスタンスに RDP 接続する方法)](service-fabric-cluster-nodetypes.md)

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

<!---HONumber=AcomDC_0224_2016-->