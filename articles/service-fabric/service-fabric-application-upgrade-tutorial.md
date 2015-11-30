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
   ms.date="07/17/2015"
   ms.author="subramar"/>



# Visual Studio による Service Fabric アプリケーションのアップグレード チュートリアル

Service Fabric では、変更されたサービスのみをアップグレードし、アップグレード プロセス全体を通じてそのアプリケーションの正常性を監視して、問題が発生した場合はアプリケーションを以前のバージョンに自動的にロールバックすることによって、クラウド アプリケーションのアップグレードのプロセスを簡略化します。Service Fabric アプリケーションのアップグレードは、*ゼロ ダウンタイム*です。アプリケーションをダウンタイムなしでアップグレードできます。このチュートリアルでは、Visual Studio から単純なローリング アップグレードを行う方法について説明します。


## 手順 1: ビジュアル オブジェクト サンプルのビルドと発行

この手順を行うには、Github からアプリケーションをダウンロードし、サンプルの readme ファイルで説明されているように、**webgl-utils.js** および **gl-matrix-min.js** ファイルをプロジェクトに追加します。そうしないと、アプリケーションは動作しません。これらのファイルをプロジェクトに追加した後、アプリケーション プロジェクト **[VisualObjectsApplication]** を右クリックし、次の図のように [Service Fabric] メニュー項目の発行コマンドを選択して、アプリケーションをビルドおよび発行します。

![Service Fabric アプリケーションのコンテキスト メニュー][image1]

別のポップアップが表示されるので、**[接続エンドポイント]** を **[ローカル クラスター]** に設定します。**[発行]** をクリックする前に、ウィンドウを次のように設定します。

![Service Fabric アプリケーションの発行][image2]

これで、ダイアログ ボックスで [発行] をクリックできるようになりました。[クラスターおよびアプリケーションを表示する Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) を使用できます。Visual Objects アプリケーションには、ブラウザーのアドレス バーに「[http://localhost:8081/visualobjects](http://localhost:8081/visualobjects)」と入力すると移動する Web サービスが設けられています。画面上を動く 10 個のフローティング ビジュアル オブジェクトが表示されます。

## 手順 2: ビジュアル オブジェクト サンプルの更新

手順 1. でデプロイされたバージョンでは、ビジュアル オブジェクトが回転しないことにお気付きになったことでしょう。アプリケーションをアップグレードして、ビジュアル オブジェクトも回転させてみましょう。

VisualObjects ソリューション内の VisualObjects.ActorService プロジェクトを選択し、StatefulVisualObjectActor.cs ファイルを開きます。そのファイル内で `MoveObject` メソッドに移動し、`this.State.Move()` をコメント アウトし、`this.State.Move(true)` をコメント解除します。この変更によって、サービスのアップグレード後、オブジェクトは回転するようになります。これで、修正されたプロジェクトをビルドするソリューションをビルドできます (リビルドではなく。[すべてリビルド] を選択すると、すべてのプロジェクトのバージョンを更新しなければならなくなります)。

アプリケーションのバージョン管理も行う必要があります。バージョンを変更するには、ソリューションを右クリックしてから、Visual Studio の **[マニフェスト ファイルの編集]** オプションを使用します。このオプションでは、次のようなバージョン編集用のダイアログ ボックスが表示されます。

![[バージョン管理] ダイアログ ボックス][image3]

**[マニフェスト バージョンの編集]** タブを選択し、アプリケーションと、編集したプロジェクトおよびそのコード パッケージのバージョンを 2.0.0 に更新します。変更後、マニフェストは次のようになります (太字部分は変更個所を示します)。

![バージョンの更新][image4]

[SemVer](http://www.semver.org) を使用している場合、Visual Studio ツールはバージョンを自動ロールアップできます (**[アプリケーションとサービスのバージョンを自動的に更新する]** を選択)。このオプションが選択されている場合は、コードおよび構成パッケージのバージョンだけを更新する必要があります。変更を保存し、**[アプリケーションのアップグレード]** ボックスをオンにします。


## 手順 3: アプリケーションのアップグレード

[アプリケーション アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)と[アップグレード プロセス](service-fabric-application-upgrade.md)に精通し、アップグレードのさまざまなパラメーター、タイムアウト、および適用されている正常性条件の理解を深めましょう。このチュートリアルでは、サービスの正常性評価の条件は、既定値 (UnMonitored Mode) のままにします。これらを設定するには、**[アップグレードの設定の構成]** を選択し、必要に応じてパラメーターを変更します。

アプリケーションのアップグレードを開始する準備がすべて完了しました。開始するには **[発行]** を選択します。これで、アプリケーションがバージョン 2.0.0 にアップグレードされ、オブジェクトが回転するようになります。Service Fabric によって、アップグレード ドメインが一度に 1 つずつアップグレードされます (いくつかのオブジェクトが最初に更新され、他のオブジェクトが続きます)。その間も、クライアント (ブラウザー) を通じて、サービスにアクセスできます。


アプリケーションのアップグレードの処理を、Service Fabric Explorer を使用して監視できます (アプリケーションの下の **[進行中のアップグレード]** タブ)。

数分後に、すべてのアップグレード ドメインがアップグレードされ (完了し)、Visual Studio の出力ウィンドウにもアップグレードの完了が表示されます。ブラウザー ウィンドウの*すべての*ビジュアル オブジェクトが回転しているはずです。

練習のために、バージョンをバージョン 2.0.0 からバージョン 3.0.0 へ、あるいは、バージョン 2.0.0 からバージョン 1.0.0 に変更および移行してみることもできます。タイムアウトと正常性ポリシーをいろいろ試して、これらに精通してください。Azure クラスターにデプロイする場合、ローカル クラスターにデプロイするときに使用するパラメーターと使用されるパラメーターが異なる場合があります。タイムアウトを控えめに設定することをお勧めします。


## 次のステップ

「[Powershell によるアプリケーションのアップグレード](service-fabric-application-upgrade-tutorial-powershell.md)」では、PowerShell を使用してアプリケーションをアップグレードする方法について説明します。

[アップグレード パラメーター](service-fabric-application-upgrade-parameters.md)を使用して、アプリケーションのアップグレード方法を制御します。

[データのシリアル化](service-fabric-application-upgrade-data-serialization.md)の方法を学ぶことで、アプリケーションのアップグレードに互換性を持たせます。

「[高度なトピック](service-fabric-application-upgrade-advanced.md)」を参照して、アプリケーションをアップグレードするときの高度な機能の使用方法を学習します。

「[アプリケーションのアップグレードのトラブルシューティング](service-fabric-application-upgrade-troubleshooting.md)」の手順を参照して、アプリケーションのアップグレードでの一般的な問題を修正します。
 


[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png

<!---HONumber=Nov15_HO4-->