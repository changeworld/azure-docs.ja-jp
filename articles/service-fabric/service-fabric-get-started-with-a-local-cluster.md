<properties
   pageTitle="ローカル クラスターでアプリケーションのデプロイおよびアップグレードを開始する | Microsoft Azure"
   description="Service Fabric のローカル クラスターをセットアップし、それに既存のアプリケーションをデプロイし、そのアプリケーションをアップグレードします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="ryanwi;mikhegn"/>

# ローカル クラスターでアプリケーションのデプロイおよびアップグレードを開始する
Azure Service Fabric SDK には、完全なローカル開発環境が含まれています。これを使用して、ローカル クラスター上でアプリケーションをデプロイおよび管理する作業をすぐに開始することができます。この記事では、ローカル クラスターを作成し、既存のアプリケーションをローカル クラスターにデプロイして、そのアプリケーションを新しいバージョンにアップグレードします。これらの作業はすべて Windows PowerShell で行います。

> [AZURE.NOTE] この記事では、既に [ 開発環境がセットアップ](service-fabric-get-started.md)されていると仮定します。

## ローカル クラスターを作成する
Service Fabric クラスターとは、アプリケーションをデプロイすることができるハードウェア リソース セットです。通常、クラスターは 5 台から数千台のコンピューターで構成されます。ただし、Service Fabric SDK には、1 台のコンピューター上で実行できるクラスター構成が含まれています。

Service Fabric ローカル クラスターがエミュレーターでもシミュレーターでもないことを理解しておくことは重要です。これは、複数のコンピューターから成るクラスターで検出されるのと同じプラットフォーム コードを実行します。唯一の違いは、通常 5 台のコンピューターに分散して実行されるプラットフォーム プロセスを 1 台のコンピューター上で実行するということです。

SDK では、2 つの方法でローカル クラスターをセットアップすることができます。1 つは Windows PowerShell スクリプトを使用する方法、もう 1 つはローカル クラスター マネージャーのシステム トレイ アプリを使用する方法です。このチュートリアルでは PowerShell スクリプトを使用します。

> [AZURE.NOTE] Visual Studio からアプリケーションをデプロイするという方法でローカル クラスターを既に作成している場合は、このセクションを省略できます。


1. 管理者として、新しい PowerShell ウィンドウを起動します。

2. SDK フォルダーからクラスター セットアップ スクリプトを実行します。

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1"
	```

    クラスターのセットアップには、しばらく時間がかかります。セットアップが完了すると、次のような出力が表示されます。

    ![クラスターのセットアップに関する出力][cluster-setup-success]

    これでクラスターにアプリケーションをデプロイする準備が整いました。

## アプリケーションをデプロイする
Service Fabric SDK には、アプリケーションを作成するための豊富なフレームワークと開発者向ツールが含まれています。Visual Studio でアプリケーションを作成する方法について理解を深めるには、「[Visual Studio で最初の Service Fabric アプリケーションを作成する](service-fabric-create-your-first-application-in-visual-studio.md)」を参照してください。

このチュートリアルでは、プラットフォームの管理面 (デプロイメント、監視、およびアップグレードなど) に集中できるように、既存のサンプル アプリケーション (名前は WordCount) を使用します。


1. 管理者として、新しい PowerShell ウィンドウを起動します。

2. Service Fabric SDK PowerShell モジュールをインポートします。

    ```powershell
    Import-Module "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\Tools\PSModule\ServiceFabricSDK\ServiceFabricSDK.psm1"
    ```

3. C:\\ServiceFabric のようなディレクトリを作成し、そこにダウンロードしてデプロイするアプリケーションを格納します。

    ```powershell
    mkdir c:\ServiceFabric\
    cd c:\ServiceFabric\
    ```

4. 作成した場所に、[WordCount アプリケーションをダウンロード](http://aka.ms/servicefabric-wordcountapp)します。注: Microsoft Edge ブラウザーでは、ファイルが *.zip* 拡張子付きで保存されます。ファイル拡張子を *.sfpkg* に変更します。

5. 次のように、ローカル クラスターに接続します。

    ```powershell
    Connect-ServiceFabricCluster localhost:19000
    ```

6. SDK のデプロイ コマンドを使って、名前とアプリケーション パッケージのパスを指定し、新しいアプリケーションを作成します。

    ```powershell  
  Publish-NewServiceFabricApplication -ApplicationPackagePath c:\ServiceFabric\WordCountV1.sfpkg -ApplicationName "fabric:/WordCount"
    ```

    すべて成功すると、出力は次のように表示されます。

    ![アプリケーションをローカル クラスターにデプロイする][deploy-app-to-local-cluster]

7. アプリケーションの動作を確認するには、ブラウザーを起動し、[http://localhost:8081/wordcount/index.html](http://localhost:8081/wordcount/index.html) に移動します。次のような結果が表示されます。

    ![デプロイされたアプリケーション UI][deployed-app-ui]

    WordCount アプリケーションは非常に単純です。これには、ランダムな 5 文字の "単語" を生成するクライアント側の JavaScript コードが含まれます。生成された単語は ASP.NET Web API 経由でアプリケーションに中継されます。ステートフル サービスは、単語のカウントを追跡します。単語の最初の文字に基づいてパーティション分割されます。[入門サンプル](https://azure.microsoft.com/documentation/samples/service-fabric-dotnet-getting-started/)で WordCount アプリケーションのソース コードを見つけることができます。

    デプロイしたアプリケーションには 4 つのパーティションがあります。このため、A ～ G で始まる単語は最初のパーティションに格納され、H ～ N で始まる単語は 2 番目のパーティションに格納されるといった具合になります。

## アプリケーションの詳細と状態を表示する
これでアプリケーションがデプロイされました。アプリケーションの詳細の一部を PowerShell で見てみましょう。

1. クラスター上にあるデプロイされたすべてのアプリケーションのクエリを実行します。

    ```powershell
    Get-ServiceFabricApplication
    ```

    WordCount アプリケーションのみがデプロイされているとすると、次のような結果が表示されます。

    ![PowerShell で、デプロイされたすべてのアプリケーションのクエリを実行する][ps-getsfapp]

2. WordCount アプリケーションに含まれているサービス セットのクエリを実行することにより、次のレベルに移動します。

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![PowerShell でアプリケーションのサービスを一覧表示する][ps-getsfsvc]

    アプリケーションは 2 つのサービス (Web フロント エンドと、単語を管理するステートフル サービス) から構成されています。

3. 最後に、WordCountService のパーティションの一覧を見てみましょう。

    ```powershell
    Get-ServiceFabricPartition 'fabric:/WordCount/WordCountService'
    ```

    ![PowerShell でサービスのパーティションを表示する][ps-getsfpartitions]

    使用した一連のコマンドは、すべての Service Fabric PowerShell コマンドと同様に、ローカルまたはリモートで接続する可能性のあるどのクラスターでも利用できます。

    より視覚的な方法でクラスターと対話するには、Web ベースの Service Fabric Explorer ツールを使用します。そのためには、ブラウザーで [http://localhost:19080/Explorer](http://localhost:19080/Explorer) に移動してください。

    ![Service Fabric Explorer でアプリケーションの詳細を表示する][sfx-service-overview]

    > [AZURE.NOTE] Service Fabric Explorer の詳細については、「[Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)」を参照してください。

## アプリケーションのアップグレード
Service Fabric では、クラスター全体にロールアウトされるときにアプリケーションの正常性を監視することにより、ダウンタイムのないアップグレードを実現します。WordCount アプリケーションの簡単なアップグレードを実行してみましょう。

アプリケーションの新しいバージョンでは、母音で始まる単語のみをカウントするようになります。アップグレードがロールアウトされると、アプリケーションの動作に 2 つの変化が現れます。第 1 に、カウント数の増加のスピードが低下します。前よりもカウント対象の単語が少なくなっているためです。第 2 に、1 番目のパーティションには 2 つの母音 (A と E) が含められ、他のすべてのパーティションにはそれぞれ 1 つの母音が含められるので、1 番目のパーティションのカウント数は最終的に、他のパーティションの値を上回るようになります。

1. v1 パッケージをダウンロードしたのと同じ場所に、[WordCount v2 パッケージをダウンロード](http://aka.ms/servicefabric-wordcountappv2)します。

2. PowerShell ウィンドウに戻り、SDK のアップグレード コマンドを使用して、新しいバージョンをクラスターに登録します。その後、fabric:/WordCount アプリケーションのアップグレードを開始します。

    ```powershell
    Publish-UpgradedServiceFabricApplication -ApplicationPackagePath C:\ServiceFabric\WordCountV2.sfpkg -ApplicationName "fabric:/WordCount" -UpgradeParameters @{"FailureAction"="Rollback"; "UpgradeReplicaSetCheckTimeout"=1; "Monitored"=$true; "Force"=$true}
    ```

    アップグレードが始まると、PowerShell に次のような出力が表示されます。

    ![PowerShell でのアップグレードの進行状況][ps-appupgradeprogress]

3. アップグレードの進行中は、Service Fabric Explorer でその状態を監視するのが簡単な場合もあります。ブラウザー ウィンドウを起動して、[http://localhost:19080/Explorer](http://localhost:19080/Explorer) に移動します。左側のツリーで **[アプリケーション]** を展開し、**[WordCount]** を選択して、最後に **[fabric:/WordCount]** を選択します。[基本] タブには、クラスターのアップグレード ドメインごとにアップグレードの進行状況が表示されます。

    ![Service Fabric Explorer でのアップグレードの進行状況][sfx-upgradeprogress]

    各ドメインでのアップグレードが進行すると、正常性チェックが行われ、アプリケーションが正常に動作していることが確認されます。

4. fabric:/WordCount アプリケーションのサービス セットに対する以前のクエリを再実行すると、WordCountService のバージョンが変化している一方、WordCountWebService のバージョンは変化していないことがわかります。

    ```powershell
    Get-ServiceFabricService -ApplicationName 'fabric:/WordCount'
    ```

    ![アップグレード後にアプリケーション サービスのクエリを実行する][ps-getsfsvc-postupgrade]

    これは、Service Fabric のアプリケーションのアップグレードの管理方法に焦点を当てています。この方法では変更されたサービス セット (またはそれらのサービス内のコード/構成パッケージ) のみを対象とし、アップグレード処理にかかる時間を短縮し、アップグレード処理の信頼性を高めます。

5. 最後に、ブラウザーに戻り、アプリケーションの新しいバージョンの動作を確認します。予想通り、カウント数の増加のスピードは緩やかで、最終的に 1 番目のパーティションのカウント数がやや大きくなります。

    ![ブラウザーでアプリケーションの新しいバージョンを表示する][deployed-app-ui-v2]

## クリーンアップしています

まとめに入る前に、ローカル クラスターが現実のものであることを思い出してください。アプリケーションは削除されるまで、バックグラウンドで実行され続けます。アプリの性質によっては、実行中のアプリがコンピューター上の大量のリソースを占有する場合があります。アプリケーションとクラスターは、いくつかの方法で管理できます。

1. 個々のアプリケーションとそのデータすべてを削除するには、次の手順を実行します。

    ```powershell
    Unpublish-ServiceFabricApplication -ApplicationName "fabric:/WordCount"
    ```

    または、Service Fabric Explorer の **[アクション]** メニューか、左側のウィンドウにあるアプリケーション リスト ビューのコンテキスト メニューからアプリケーションを削除します。

    ![Delete an application is Service Fabric Explorer][sfe-delete-application]

2. クラスターからアプリケーションを削除すると、WordCount アプリケーションの種類のバージョン 1.0.0 と 2.0.0 の登録を解除できます。削除を実行すると、コードと構成を含むアプリケーション パッケージがクラスターのイメージ ストアから削除されます。

    ```powershell
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 2.0.0
    Remove-ServiceFabricApplicationType -ApplicationTypeName WordCount -ApplicationTypeVersion 1.0.0
    ```

    または、Service Fabric Explorer で、そのアプリケーションについて **[Unprovision Type (種類のプロビジョニング解除)]** を選択します。

3. クラスターをシャットダウンしても、アプリケーションのデータとトレースは保持するという場合は、システム トレイ アプリで **[Stop Local Cluster (ローカル クラスターの停止)]** をクリックします。

4. クラスターを完全に削除するには、システム トレイ アプリで **[Remove Local Cluster (ローカル クラスターの削除)]** をクリックします。このオプションを使用すると、次回 Visual Studio で F5 キーを押したときにも、デプロイが遅くなります。しばらく使用しない場合や、リソースを解放する必要がある場合にのみ、ローカル クラスターを削除してください。

## 1 ノードと 5 ノードのクラスター モード

ローカル クラスターを操作してアプリケーションを開発している場合、コードを記述してデバッグし、コードを変更してデバッグするという繰り返しが頻繁に発生します。このプロセスを最適化するために、ローカル クラスターは 2 つのモードで実行できます。1 ノード モードと 5 ノード モードです。各モードに、それぞれの利点があります。5 ノード クラスター モードでは、実際のクラスターを操作できます。フェールオーバーのシナリオをテストし、サービスのより多くのインスタンスとレプリカを操作できます。1 ノード クラスター モードは、サービスをすばやくデプロイおよび登録できるように最適化されています。Service Fabric ランタイムを使用して、すばやくコードを検証できます。

1 ノード クラスター モードも 5 ノード クラスター モードも、エミュレーターやシミュレーターではありません。これは、複数のコンピューターから成るクラスターで検出されるのと同じプラットフォーム コードを実行します。

> [AZURE.NOTE] この機能は、SDK version 5.2 以降で使用できます。

クラスター モードを 1 ノード クラスターに変更するには、Service Fabric ローカル クラスター マネージャーを使用するか、次のように PowerShell を使用します。

1. 管理者として、新しい PowerShell ウィンドウを起動します。

2. SDK フォルダーからクラスター セットアップ スクリプトを実行します。

	```powershell
	& "$ENV:ProgramFiles\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateOneNodeCluster
	```

    クラスターのセットアップには、しばらく時間がかかります。セットアップが完了すると、次のような出力が表示されます。
    
    ![クラスターのセットアップに関する出力][cluster-setup-success-1-node]

Service Fabric ローカル クラスター マネージャーを使用している場合:

![クラスター モードを切り替える][switch-cluster-mode]

> [AZURE.WARNING] クラスター モードを変更すると、現在のクラスターがシステムから削除され、新しいクラスターが作成されます。クラスターに保存したデータは、クラスター モードを変更すると、削除されます。

## 次のステップ
- いくつかのビルド済みのアプリケーションをデプロイし、アップグレードしたので、今度は [Visual Studio で自身のアプリケーションをビルドしてみます](service-fabric-create-your-first-application-in-visual-studio.md)。
- この記事の中でローカル クラスターに対して実行したアクションはすべて、[Azure クラスター](service-fabric-cluster-creation-via-portal.md)に対しても実行することができます。
- この記事で実行したアップグレードは基本的なものでした。Service Fabric アップグレードの機能と柔軟性の詳細については、「[アップグレードに関するドキュメント](service-fabric-application-upgrade.md)」を参照してください。

<!-- Images -->

[cluster-setup-success]: ./media/service-fabric-get-started-with-a-local-cluster/LocalClusterSetup.png
[extracted-app-package]: ./media/service-fabric-get-started-with-a-local-cluster/ExtractedAppPackage.png
[deploy-app-to-local-cluster]: ./media/service-fabric-get-started-with-a-local-cluster/DeployAppToLocalCluster.png
[deployed-app-ui]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-v1.png
[deployed-app-ui-v2]: ./media/service-fabric-get-started-with-a-local-cluster/DeployedAppUI-PostUpgrade.png
[sfx-app-instance]: ./media/service-fabric-get-started-with-a-local-cluster/SfxAppInstance.png
[sfx-two-app-instances-different-partitions]: ./media/service-fabric-get-started-with-a-local-cluster/SfxTwoAppInstances-DifferentPartitionCount.png
[ps-getsfapp]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFApp.png
[ps-getsfsvc]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc.png
[ps-getsfpartitions]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFPartitions.png
[ps-appupgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/PS-AppUpgradeProgress.png
[ps-getsfsvc-postupgrade]: ./media/service-fabric-get-started-with-a-local-cluster/PS-GetSFSvc-PostUpgrade.png
[sfx-upgradeprogress]: ./media/service-fabric-get-started-with-a-local-cluster/SfxUpgradeOverview.png
[sfx-service-overview]: ./media/service-fabric-get-started-with-a-local-cluster/sfx-service-overview.png
[sfe-delete-application]: ./media/service-fabric-get-started-with-a-local-cluster/sfe-delete-application.png
[cluster-setup-success-1-node]: ./media/service-fabric-get-started-with-a-local-cluster/cluster-setup-success-1-node.png
[switch-cluster-mode]: ./media/service-fabric-get-started-with-a-local-cluster/switch-cluster-mode.png

<!---HONumber=AcomDC_0921_2016-->