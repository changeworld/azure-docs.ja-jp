<properties
   pageTitle="Visual Studio での Service Fabric アプリケーションの管理"
   description="Microsoft Azure Service Fabric アプリケーションとサービスを Visual Studio で管理することができます。"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="jesseb"/>

# Visual Studio での Service Fabric アプリケーションの管理

Microsoft Azure Service Fabric アプリケーションとサービスを Visual Studio で管理することができます。[開発環境のセットアップ](../service-fabric-setup-your-development-environment)が済んだら、Visual Studio を使用して、Service Fabric アプリケーションの作成、サービスの追加、ローカル開発クラスターでのアプリケーションのパッケージ化、登録、およびデプロイができます。

Service Fabric アプリケーションを管理するには、ソリューション エクスプローラーで、アプリケーション プロジェクトを右クリックします。

![アプリケーション プロジェクトを右クリックし、Service Fabric アプリケーションを管理する][manageservicefabric]

## Service Fabric アプリケーションのデプロイ

Service Fabric アプリケーションのデプロイでは、次の手順が 1 つの単純な操作にまとめられています。

1. アプリケーション パッケージの作成
2. アプリケーション パッケージのイメージ ストアへのアップロード
3. アプリケーションの種類の登録
4. 実行している任意のアプリケーション インスタンスの削除
5. 新しいアプリケーション インスタンスの作成

Visual Studio で、[ビルド] メニューから、[ソリューションの配置] を選択してアプリケーションをデプロイできます。**[F5]** キーを押してもアプリケーションはデプロイされ、すべてのアプリケーション インスタンスにデバッガーがアタッチされます。


## Service Fabric アプリケーションへのサービスの追加

新しい Fabric サービスをアプリケーションに追加して、機能を拡張することができます。サービスがアプリケーション パッケージに含まれているかどうかを確認するには、**[新規 Fabric サービス]** メニュー項目からサービスを追加します。

![新しい Fabric サービスをアプリケーションに追加する][newservice]

アプリケーションに追加する Service Fabric プロジェクトの種類を選択し、サービスの名前を指定します。使用するサービスの種類を決定するのに役立つ、「[サービスのフレームワークの選択](service-fabric-choose-framework.md)」を参照してください。

![アプリケーションに追加する Fabric サービス プロジェクトの種類を選択する][addserviceproject]

新しいサービスは、ソリューションおよび既存のアプリケーション パッケージに追加されます。サービス参照と既定のサービス インスタンスは、アプリケーション マニフェストに追加されます。サービスが作成され、次回、アプリケーションをデプロイする際に起動します。

![新しいサービスは、アプリケーション マニフェストに追加されます][newserviceapplicationmanifest]

## Service Fabric アプリケーションのパッケージ化

アプリケーション パッケージは、アプリケーションとそのサービスをクラスターにデプロイするために作成する必要があります。パッケージは、アプリケーション マニフェスト、サービス マニフェスト、および特定のレイアウトで必要なその他のファイルを整理します。Visual Studio は、パッケージを 'pkg' ディレクトリのアプリケーション プロジェクト フォルダーに設定し、管理します。**[パッケージ]** をクリックすると、アプリケーション パッケージが作成もしくは更新されます。カスタム Powershell スクリプトを使用してアプリケーションをデプロイする場合に、これを実行できます。

## アプリケーションの削除

サーバー エクスプローラーを使用して、ローカルのクラスターからアプリケーションを削除できます。これで、次のように、上で説明したデプロイの手順を元に戻すことができます。

1. 実行している任意のアプリケーション インスタンスの削除
2. アプリケーションの種類の登録解除
3. イメージ ストアからのアプリケーション パッケージの削除

![アプリケーションの削除](./media/service-fabric-manage-application-in-visual-studio/removeapplication.png)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

- [Service Fabric のアプリケーション モデル](service-fabric-application-model.md)
- [Service Fabric アプリケーションの展開](service-fabric-deploy-remove-applications.md)
- [Service Fabric アプリケーションのデバッグ](service-fabric-debugging-your-application.md)
- [Service Fabric エクスプローラーを使用したクラスターの視覚化](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]: ./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]: ./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]: ./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png

<!---HONumber=Oct15_HO3-->