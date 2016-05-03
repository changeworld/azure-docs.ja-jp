 <properties
   pageTitle="Service Fabric アプリケーションのアップグレード チュートリアル | Microsoft Azure"
   description="この記事では、Visual Studio による Service Fabric アプリケーションのデプロイ、コードの変更、アップグレードのロールアウトを段階的に説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/14/2016"
   ms.author="subramar"/>



# Visual Studio による Service Fabric アプリケーションのアップグレード チュートリアル

Azure Service Fabric では、変更されたサービスのみをアップグレードし、アップグレード プロセス全体を通じてそのアプリケーションの正常性を監視することで、クラウド アプリケーションのアップグレードのプロセスを簡略化します。また、問題が発生した場合は、アプリケーションが自動的に以前のバージョンにロールバックされます。Service Fabric アプリケーションのアップグレードは、*ゼロ ダウンタイム*です。アプリケーションをダウンタイムなしでアップグレードできます。このチュートリアルでは、Visual Studio から単純なローリング アップグレードを行う方法について説明します。


## 手順 1: ビジュアル オブジェクト サンプルのビルドと発行

この手順を行うには、GitHub からアプリケーションをダウンロードし、サンプルの readme ファイルで説明されているように、**webgl-utils.js** ファイルと **gl-matrix-min.js** ファイルをプロジェクトに追加します。そうしないと、アプリケーションは動作しません。これらのファイルをプロジェクトに追加した後、**VisualObjectsApplication** アプリケーション プロジェクトを右クリックし、次の図のように [Service Fabric] メニュー項目の **[発行]** コマンドを選択して、アプリケーションをビルドして発行します。

![Context menu for a Service Fabric application][image1]

別のポップアップが表示されるので、**[接続エンドポイント]** を **[ローカル クラスター]** に設定します。**[発行]** をクリックする前に、ウィンドウを次のように設定します。

![Publishing a Service Fabric application][image2]

これで、ダイアログ ボックスの **[発行]** をクリックできるようになりました。[クラスターおよびアプリケーションを表示する Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用できます。Visual Objects アプリケーションには、ブラウザーのアドレス バーに「[http://localhost:8081/visualobjects](http://localhost:8081/visualobjects)」と入力すると移動できる Web サービスがあります。画面上を動く 10 個のフローティング ビジュアル オブジェクトが表示されます。

## 手順 2: ビジュアル オブジェクト サンプルの更新

手順 1. でデプロイされたバージョンでは、ビジュアル オブジェクトが回転しないことに気付くかもしれません。アプリケーションをアップグレードして、ビジュアル オブジェクトも回転させてみましょう。

VisualObjects ソリューション内の VisualObjects.ActorService プロジェクトを選択し、**StatefulVisualObjectActor.cs** ファイルを開きます。そのファイル内で `MoveObject` メソッドに移動して、`this.State.Move()` をコメント アウトし、`this.State.Move(true)` をコメント解除します。この変更によって、サービスのアップグレード後、オブジェクトは回転するようになります。これで、ソリューションを (リビルドではなく) ビルドできるようになりました。これによって、変更したプロジェクトがビルドされます。**[すべてリビルド]** を選択した場合、すべてのプロジェクトのバージョンを更新する必要があります。

アプリケーションのバージョン管理も行う必要があります。バージョンを変更するには、ソリューションを右クリックしてから、Visual Studio の **[マニフェスト ファイルの編集]** オプションを使用します。このオプションでは、次のようなバージョン編集用のダイアログ ボックスが表示されます。

![Versioning dialog box][image3]

**[マニフェスト バージョンの編集]** タブを選択し、アプリケーションと、編集したプロジェクトおよびそのコード パッケージのバージョンを 2.0.0 に更新します。変更後、マニフェストは次のようになります (太字部分は変更個所を示します)。

![Updating versions][image4]

**[アプリケーションとサービスのバージョンを自動的に更新する]** チェック ボックスをオンにした場合、Visual Studio ツールはバージョンの自動ロールアップを実行できます。[SemVer](http://www.semver.org) を使用すると、そのチェック ボックスがオンになっている場合は、コードまたは構成パッケージ (あるいはその両方) のバージョンのみを更新する必要があります。

変更を保存し、**[アプリケーションのアップグレード]** ボックスをオンにします。


## 手順 3: アプリケーションのアップグレード

[アプリケーション アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)と[アップグレード プロセス](service-fabric-application-upgrade.md)に精通し、アップグレードのさまざまなパラメーター、タイムアウト、および適用できる正常性条件の理解を深めましょう。このチュートリアルでは、サービスの正常性評価の条件は、既定値 (監視なしのモード) のままにします。これらの設定を構成するには、**[アップグレードの設定の構成]** を選択し、必要に応じてパラメーターを変更します。

アプリケーションのアップグレードを開始する準備がすべて完了しました。開始するには **[発行]** を選択します。これで、アプリケーションがバージョン 2.0.0 にアップグレードされ、オブジェクトが回転するようになります。Service Fabric によって、更新ドメインが一度に 1 つアップグレードされます (いくつかのオブジェクトが最初に更新された後、他のオブジェクトが続きます)。その間も、クライアント (ブラウザー) を通じてサービスにアクセスできます。


アプリケーションのアップグレードが進むと、Service Fabric Explorer で、アプリケーションの下にある **[進行中のアップグレード]** タブを使用して監視できます。

数分後に、すべての更新ドメインがアップグレードされ (完了し)、Visual Studio の出力ウィンドウにもアップグレードの完了が表示されます。さらに、ブラウザー ウィンドウの*すべて*のビジュアル オブジェクトが回転しています。

練習のために、バージョンをバージョン 2.0.0 からバージョン 3.0.0 へ、または、バージョン 2.0.0 からバージョン 1.0.0 に変更および移行してみることもできます。タイムアウトと正常性ポリシーをいろいろ試して、これらに精通してください。Azure クラスターにデプロイする場合、使用するパラメーターは、ローカル クラスターにデプロイするときに使用するパラメーターと異なるため、タイムアウトを控えめに設定することをお勧めします。


## 次のステップ

「[PowerShell を使用した Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)」では、PowerShell を使用したアプリケーションのアップグレードについて説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「[高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションのアップグレード中に高度な機能を使用する方法を学習します。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を解決します。



[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=AcomDC_0420_2016-->