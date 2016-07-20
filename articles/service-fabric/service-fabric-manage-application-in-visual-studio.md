<properties
   pageTitle="Visual Studio でアプリケーションを管理する | Microsoft Azure"
   description="Visual Studio を利用し、Service Fabric のアプリケーションとサービスを作成、開発、パッケージ化、デプロイ、デバッグします。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/02/2016"
   ms.author="seanmck"/>

# Visual Studio を利用すれば、Service Fabric アプリケーションの記述と管理が簡単になりまする

Azure Service Fabric アプリケーションとサービスを Visual Studio で管理できます。[開発環境のセットアップ](service-fabric-get-started.md)が済んだら、Visual Studio を使用して、Service Fabric アプリケーションの作成、サービスの追加、ローカル開発クラスターでのアプリケーションのパッケージ化、登録、およびデプロイができます。

アプリケーションを管理するには、ソリューション エクスプローラーで、アプリケーション プロジェクトを右クリックします。

![アプリケーション プロジェクトを右クリックし、Service Fabric アプリケーションを管理する][manageservicefabric]

## Service Fabric アプリケーションのデプロイ

アプリケーションのデプロイでは、次の手順が 1 つの単純な操作にまとめられています。

1. アプリケーション パッケージの作成
2. アプリケーション パッケージのイメージ ストアへのアップロード
3. アプリケーションの種類の登録
4. 実行している任意のアプリケーション インスタンスの削除
5. 新しいアプリケーション インスタンスの作成

Visual Studio では、**[F5]** キーを押してもアプリケーションはデプロイされ、すべてのアプリケーション インスタンスにデバッガーがアタッチされます。**[Ctrl + F5]** キーを押してデバッグなしでアプリケーションをデプロイするか、発行プロファイルを使用してローカルまたはリモート クラスターにアプリケーションを発行できます。詳細については、「[Visual Studio を使用してリモート クラスターにアプリケーションを発行する](service-fabric-publish-app-remote-cluster.md)」を参照してください。

### アプリケーション デバッグ モード

サービスをローカルでデバッグしている間、既存のアプリケーションとデータを維持したい場合があります。Visual Studio Service Fabric ツールには、**Application Debug Mode (アプリケーション デバッグ モード)** というプロパティが用意されていて、**F5** キーを押したときにアプリケーションをアンインストールするか、デバッグ セッションの終了後もアプリケーションを維持するかを制御できます。

#### Application Debug Mode (アプリケーション デバッグ モード) プロパティを設定するには

1. アプリケーション プロジェクトのショートカット メニューで、**[プロパティ]** を選択します (または **[F4]** キーを押します)。
2. **[プロパティ]** ウィンドウの **[Application Debug Mode (アプリケーション デバッグ モード)]** プロパティを **[削除]** または **[Auto Upgrade (自動アップグレード)]** に設定します。

![Set Application Debug Mode Property][debugmodeproperty]

このプロパティの値を **[Auto Upgrade (自動アップグレード)]** に設定すると、ローカル クラスターで実行されているアプリケーションが維持されます。次回 **F5** キーを押すと、そのデプロイは、監視なしの自動モードを使用してアップグレードとして扱われ、アプリケーションは、日付文字列が追加された新しいバージョンにアップグレードされます。アップグレード プロセスでは、前のデバッグ セッションで入力したすべてのデータが保持されます。

![Example of new application version with date1 appended][preservedate]

データは、Service Fabric プラットフォームのアップグレード機能を活用して保持されます。アプリケーションのアップグレードの詳細については、「[Service Fabric アプリケーションのアップグレード](service-fabric-application-upgrade.md)」を参照してください。

**注:** Service Fabric Tools for Visual Studio バージョン 1.1 未満には、このプロパティが存在しません。バージョン 1.1 未満の場合は、**[開始時にデータを保存]** プロパティで同じ動作が得られます。
## Service Fabric アプリケーションにサービスを追加します。

新しい Fabric サービスをアプリケーションに追加して、機能を拡張することができます。サービスがアプリケーション パッケージに含まれているかどうかを確認するには、**[新規 Fabric サービス]** メニュー項目からサービスを追加します。

![新しい Fabric サービスをアプリケーションに追加する][newservice]

アプリケーションに追加する Service Fabric プロジェクトの種類を選択し、サービスの名前を指定します。使用するサービスの種類を決定するのに役立つ、「[サービスのフレームワークの選択](service-fabric-choose-framework.md)」を参照してください。

![アプリケーションに追加する Fabric サービス プロジェクトの種類を選択する][addserviceproject]

新しいサービスは、ソリューションおよび既存のアプリケーション パッケージに追加されます。サービス参照と既定のサービス インスタンスは、アプリケーション マニフェストに追加されます。サービスが作成され、次回、アプリケーションをデプロイする際に起動します。

![新しいサービスは、アプリケーション マニフェストに追加されます][newserviceapplicationmanifest]

## Service Fabric アプリケーションのパッケージ化

アプリケーションとそのサービスをクラスターにデプロイするには、アプリケーション パッケージを作成する必要があります。パッケージは、アプリケーション マニフェスト、サービス マニフェスト、および特定のレイアウトで必要なその他のファイルを整理します。Visual Studio は、パッケージを 'pkg' ディレクトリのアプリケーション プロジェクト フォルダーに設定し、管理します。**[アプリケーション]** コンテキスト メニューから **[パッケージ]** をクリックすると、アプリケーション パッケージが作成または更新されます。カスタム Powershell スクリプトを使用してアプリケーションをデプロイする場合に、これを実行できます。

## アプリケーションの削除

Service Fabric Explorer を使用して、ローカル クラスターからアプリケーション タイプのプロビジョニングを解除できます。クラスター エクスプローラーは、http://localhost:19080/Explorer などのクラスターの HTTP ゲートウェイ エンドポイント (通常 19080 または 19007) からアクセスできます。これで、次のように、上で説明したデプロイの手順を元に戻すことができます。

1. 実行している任意のアプリケーション インスタンスの削除
2. アプリケーションの種類の登録解除

![アプリケーションの削除](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

- [Service Fabric のアプリケーション モデル](service-fabric-application-model.md)
- [Service Fabric アプリケーションのデプロイ](service-fabric-deploy-remove-applications.md)
- [複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)
- [Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)
- [Service Fabric Explorer を使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[preservedata]: ./media/service-fabric-manage-application-in-visual-studio/preservedata.png
[preservedate]: ./media/service-fabric-manage-application-in-visual-studio/preservedate.png
[debugmodeproperty]: ./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png

<!---HONumber=AcomDC_0706_2016-->