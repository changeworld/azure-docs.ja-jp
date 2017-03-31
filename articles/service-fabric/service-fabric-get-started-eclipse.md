---
title: "Azure Service Fabric での Eclipse プラグインの概要 | Microsoft Docs"
description: "Azure Service Fabric での Eclipse プラグインの概要を説明します。"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: bf84458f-4b87-4de1-9844-19909e368deb
ms.service: service-fabric
ms.devlang: java
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/27/2016
ms.author: saysa
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: b612259b97bf238bac28cb77bf26f684128dd882
ms.lasthandoff: 03/21/2017


---

# <a name="getting-started-with-eclipse-plugin-for-service-fabric-java-application-development"></a>Service Fabric Java アプリケーション開発での Eclipse プラグインの概要
Eclipse は、Java 開発者の間で最も使用されている IDE の&1; つです。 この記事では、Eclipse 開発環境をセットアップして Service Fabric を操作する方法について説明します。 この記事は、プラグインのインストール、Service Fabric アプリケーションの作成、ローカルまたはリモートの Service Fabric クラスターへの Service Fabric アプリケーションのデプロイを行う際に役に立ちます。

## <a name="install-or-update-service-fabric-plugin-on-eclipse-neon"></a>Eclipse Neon への Service Fabric プラグインのインストールまたは更新
Service Fabric には、Java サービスのビルドとデプロイのプロセスを簡略化できる、**Eclipse IDE for Java Developers** 用のプラグインが用意されています。

1. 最新の Eclipse **Neon** と Buildship バージョン (1.0.17 以降) がインストールされていることを確認します。 **[Help (ヘルプ)]、[Installation Details (インストールの詳細)]** の順に選択して、インストールされたコンポーネントのバージョンを確認できます。 Buildship は、[こちら][buildship-update]の手順に従って更新できます。 Eclipse Neon が最新バージョンであるかどうかを確認して更新するには、**[Help (ヘルプ)]、[Check for Updates (更新プログラムの確認)]** の順に移動します。

2. **[Help (ヘルプ)]、[Install New Software (新しいソフトウェアのインストール)]** の順に選択して、Service Fabric プラグインをインストールします。
  1. [Work with (作業対象)] テキストボックスに、次のように入力します。``http://dl.windowsazure.com/eclipse/servicefabric``
  2. [追加] をクリックします。

  ![Eclipse Neon plugin for Service Fabric][sf-eclipse-plugin-install]

  3. Service Fabric プラグインを選択し、[Next (次へ)] をクリックします。
  4. 指示に従ってインストールを実行し、使用許諾契約書に同意します。

Service Fabric Eclipse プラグインを既にインストールしてある場合は、最新バージョンを使用していることを確認してください。 **[Help (ヘルプ)]、[Installation Details (インストールの詳細)]** の順に移動して、より新しいバージョンに更新できるかどうかを確認できます。 次に、インストールされているプラグインの一覧で Service Fabric を探し、クリックして更新します。 保留中の更新があったら、取り込みとインストールが行われます。

> [!NOTE]
> Eclipse で Service Fabric Eclipse プラグインのインストールまたは更新に長い時間がかかる場合があります。これは、Eclipse が必ず、Eclipse インスタンスに登録されているすべての更新サイトに加えられた、すべての新しい変更のメタデータを取り込もうとすることが原因です。 そのため、時間を短縮するには、**[Available Software Sites (利用できるソフトウェア サイト)]** に移動して、Service Fabric プラグインの場所 `http://dl.windowsazure.com/eclipse/servicefabric` を示すサイトを除き、すべてのサイトをオフにするという小技を使用できます。
>

## <a name="create-service-fabric-application-using-eclipse"></a>Eclipse を使用した Service Fabric アプリケーションの作成

1. **[File (ファイル)]、[New (新規)]、[Other (その他)]** の順に移動します。 **Service Fabric プロジェクト**を選択します。 **[次へ]**をクリックします。

    ![新しい Service Fabric プロジェクトのページ 1][create-application/p1]

2. プロジェクトに名前を付けます。 **[次へ]**をクリックします。

    ![新しい Service Fabric プロジェクトのページ 2][create-application/p2]

3. 使用できる一連のテンプレート (アクター、ステートレス、コンテナー、またはゲスト実行可能) から、サービス テンプレートを選択します。 **[次へ]**をクリックします。

    ![新しい Service Fabric プロジェクトのページ 3][create-application/p3]

4. このページでサービス名や関連するサービスの詳細を入力して、**[Finish (完了)]** をクリックします。

    ![新しい Service Fabric プロジェクトのページ 4][create-application/p4]

5. Service Fabric プロジェクトを初めて作成する場合、Service Fabric の観点を設定するかどうかを問われます。**[yes (はい)]** を選択して続行してください。

    ![新しい Service Fabric プロジェクトのページ 5][create-application/p5]

6. 作成が正常に完了したら、プロジェクトは次のようになります。

    ![新しい Service Fabric プロジェクトのページ 6][create-application/p6]

## <a name="build-and-deploy-the-service-fabric-application-using-eclipse"></a>Eclipse を使用した Service Fabric アプリケーションのビルドとデプロイ

* 先ほど作成した Service Fabric アプリケーションを右クリックします。 コンテキスト メニューで **[Service Fabric]** オプションを選択します。 すると、複数のオプションが含まれたサブメニューが表示されます。 これは次のようになります。

    ![Service Fabric の右クリック メニュー][publish/RightClick]

  ビルド、リビルド、クリーンの各オプションをクリックすると、目的の操作が実行されます。
  - **[Build Application (アプリケーションのビルド)]** を選択すると、クリーニングなしでアプリケーションがビルドされます
  - **[Rebuild Application (アプリケーションのリビルド)]** を選択すると、アプリケーションのクリーンビルドが実行されます
  - **[Clean Application (アプリケーションのクリーン)]** を選択すると、ビルド アーティファクトのアプリケーションがクリーンされます


* このメニューから、アプリケーションのデプロイ、デプロイ解除、発行も選択できます。
  - **[Deploy Application (アプリケーションのデプロイ)]** を選択すると、ローカル クラスターへのデプロイが行われます
  - **[Publish Application (アプリケーションの発行)]** を選択すると、``Local.json`` と ``Cloud.json`` のどちらかの発行プロファイルを選択するよう求められます。 これらの JSON ファイルは、ローカルまたはクラウド (Azure) のクラスターに接続するために必要な情報 (接続エンドポイントやセキュリティ情報など) を格納するために使用されます。

  ![Service Fabric の右クリック メニュー][publish/Publish]

* Eclipse 実行構成を使用して Service Fabric アプリケーションをデプロイするという方法もあります。

  1. **[Run (実行)]、[Run Configurations (構成の実行)]** の順に選択します。 **[Grade Project (Grade プロジェクト)]** で **[ServiceFabricDeployer]** 実行構成を選択します。
  2. 右側のウィンドウの **[Arguments (引数)]** タブで、**[publishProfile]** として **[local (ローカル)]** または **[cloud (クラウド)]** を指定します。 既定の設定は **[local]** です。 リモート/クラウド クラスターにデプロイする場合は、**[cloud (クラウド)]** を選択します。
  3. 発行プロファイルに正しい情報が入力されていることを確認し、必要に応じて `Local.json` または `Cloud.json` を編集します。このとき、あればエンドポイントの詳細とセキュリティ資格情報を使用します。
  4. **[Grade Project (Grade プロジェクト)]** で、右側のウィンドウの **[Working Directory (作業ディレクトリ)]** が、デプロイするアプリケーションを指していることを確認します。 デプロイするアプリケーションでない場合、**[Workspace (ワークスペース)]** ボタンをクリックし、目的のアプリケーションを選択します。
  5. **[Apply (適用)]** をクリックし、**[Run (実行)]** をクリックします。

すぐにアプリケーションのビルドとデプロイが行われます。 Service Fabric Explorer で、その状況を監視できます。  

## <a name="add-new-service-fabric-service-to-your-service-fabric-application"></a>Service Fabric アプリケーションへの新しい Service Fabric サービスの追加

既存の Service Fabric アプリケーションへの新しい Service Fabric サービスの追加は、次の手順を使用して実行できます。

1. サービスを追加するプロジェクトを右クリックしてコンテキスト メニューを開き、**[Service Fabric]** オプションを選択します。 すると、複数のオプションが含まれたサブメニューが表示されます。

    ![Service Fabric のサービス追加のページ 1][add-service/p1]

2. **[Add ServiceFabric Service (ServiceFabric サービスの追加)]** オプションを選択すると、サービスをプロジェクトに追加する次の一連の手順に移ります。
3. プロジェクトに追加するサービス テンプレートを選択して、**[Next (次へ)]** をクリックします。

    ![Service Fabric のサービス追加のページ 2][add-service/p2]

4. (必要に応じて他の詳細と共に) サービス名を入力し、下の **[Add Service (サービスの追加)]** ボタンをクリックします。  

    ![Service Fabric のサービス追加のページ 3][add-service/p3]

5. サービスが正常に追加されたら、プロジェクト構造の全体は次のようになります。

    ![Service Fabric のサービス追加のページ 4][add-service/p4]

## <a name="upgrade-your-service-fabric-java-application"></a>Service Fabric Java アプリケーションのアップグレード

Service Fabric Eclipse プラグインを使用して ``App1`` プロジェクトの作成とデプロイを行い、アプリケーションの種類が ``App1AppicationType`` でアプリケーションのバージョンが 1.0 の ``fabric:/App1Application`` という名前のアプリケーションを作成してある場合に、 そのアプリケーションを停止させることなくアップグレードする必要が生じたとします。

アプリケーションに変更を加え、変更されたサービスをリビルドします。  変更されたサービスのマニフェスト ファイル (``ServiceManifest.xml``) を、サービスの更新されたバージョン (と必要に応じて Code、Config、Data) で更新します。 さらに、アプリケーションのマニフェスト (``ApplicationManifest.xml``) を、アプリケーションの更新されたバージョン番号と変更されたサービスで変更します。  

Eclipse を使用してアプリケーションをアップグレードする場合、実行構成の複製を作成し、必要に応じてそれを使用してアップグレードできます。このとき、次の手順を使用します。
1. **[Run (実行)]、[Run Configurations (構成の実行)]** の順に選択します。 左側のウィンドウにある **[Grade Project (Grade プロジェクト)]** の左側の小さな矢印をクリックします。
2. **[ServiceFabricDeployer]** を右クリックし、**[Duplicate (複製)]** を選択します。 この構成に新しい名前を付けます (**ServiceFabricUpgrader** など)。
3. 右側のパネルの **[Arguments (引数)]** タブで、**-Pconfig='deploy'** を **-Pconfig=upgrade** に変更し、**[Apply (適用)]** をクリックします。
4. これで、アプリケーションをアップグレードするための実行構成の作成と保存が完了しました。必要に応じてこの実行構成を **Run (実行)** できます。 これを実行すると、アプリケーション マニフェスト ファイルから、アプリケーションの種類の更新後の最新バージョンが取得されます。

Service Fabric Explorer を使用してアプリケーションのアップグレードを監視できるようになりました。 数分で、アプリケーションが更新されました。

<!-- Images -->

[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png

[create-application/p1]:./media/service-fabric-get-started-eclipse/create-application/p1.png
[create-application/p2]:./media/service-fabric-get-started-eclipse/create-application/p2.png
[create-application/p3]:./media/service-fabric-get-started-eclipse/create-application/p3.png
[create-application/p4]:./media/service-fabric-get-started-eclipse/create-application/p4.png
[create-application/p5]:./media/service-fabric-get-started-eclipse/create-application/p5.png
[create-application/p6]:./media/service-fabric-get-started-eclipse/create-application/p6.png

[publish/Publish]: ./media/service-fabric-get-started-eclipse/publish/Publish.png
[publish/RightClick]: ./media/service-fabric-get-started-eclipse/publish/RightClick.png

[add-service/p1]: ./media/service-fabric-get-started-eclipse/add-service/p1.png
[add-service/p2]: ./media/service-fabric-get-started-eclipse/add-service/p2.png
[add-service/p3]: ./media/service-fabric-get-started-eclipse/add-service/p3.png
[add-service/p4]: ./media/service-fabric-get-started-eclipse/add-service/p4.png

<!-- Links -->
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

