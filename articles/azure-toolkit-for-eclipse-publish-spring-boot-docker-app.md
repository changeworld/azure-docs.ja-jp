---
title: "Azure Toolkit for Eclipse を使用して Spring Boot アプリを Docker コンテナーとして発行する | Microsoft Docs"
description: "Azure Toolkit for Eclipse を使用して、Web アプリを Docker コンテナーとして Microsoft Azure に発行する方法について説明します。"
services: 
documentationcenter: java
author: rmcmurray
manager: erikre
editor: 
ms.assetid: 
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: multiple
ms.devlang: Java
ms.topic: article
ms.date: 06/21/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 8be2bcb9179e9af0957fcee69680ac803fd3d918
ms.openlocfilehash: 36d8233b64144d87501cd4d4a9cac539ab143d38
ms.contentlocale: ja-jp
ms.lasthandoff: 06/23/2017


---

# <a name="publish-a-spring-boot-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse を使用して Spring Boot アプリを Docker コンテナーとして発行する

**[Spring Framework]** は Java 開発者のエンタープライズ レベルのアプリケーション作成を支援するオープンソース ソリューションです。 このプラットフォームで構築される特に知られたプロジェクトの 1 つが [Spring Boot] です。これによって、スタンドアロンの Java アプリケーションの作成方法が簡略化されます。

**[Docker]** は、開発者が、コンテナーで実行されるアプリケーションのデプロイ、スケーリング、および管理を自動化することを支援するオープン ソース ソリューションです。

このチュートリアルでは、Azure Toolkit for Eclipse を使用して Spring Boot アプリケーションを Docker コンテナーとして Microsoft Azure にデプロイする手順について説明します。

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="cloning-the-default-spring-boot-docker-app-repository"></a>既定の Spring Boot Docker アプリ リポジトリの複製

### <a name="importing-the-public-repository"></a>パブリック リポジトリのインポート

次の手順では、IntelliJ を使用して、ローカル コンピューターに Spring Boot Docker リポジトリを複製する方法について説明します。 コマンド ラインの使用が適している場合は、「[Azure Container Service で Spring Boot アプリケーションを Linux にデプロイする][Deploy Spring Boot on Linux in ACS]」を参照してください。

1. Eclipse を開きます。

1. **[ファイル]**、**[インポート]** の順にクリックします。

   ![[ファイル] の [インポート] メニュー][CL01]

1. **[インポート]** ダイアログ ボックスが開いたら、次の手順を実行します。

   a. [**Git**] を展開します。

   b. **[Projects from Git]\(Git のプロジェクト\)** を選択します。
   
   c. **[次へ]** をクリックします。

   ![[インポート] ダイアログ ボックス][CL02]

1. **[Select Repository Source]\(リポジトリ ソースの選択\)** ページで次の手順を実行します。

   a. **[Clone URI]\(URI の複製\)** を選択します。
   
   b. **[次へ]** をクリックします。

   ![リポジトリ ソースの選択][CL03]

1. **[Source Git Repository]\(ソース Git リポジトリ\)** ページで次の手順を実行します。

   a. リポジトリの **[URI]** に「`https://github.com/spring-guides/gs-spring-boot-docker.git`」と入力します。これで **[Host]\(ホスト\)** と **[Repository path]\(リポジトリ パス\)** の各フィールドに正しい値が設定されます。
   
   b. Spring Boot リポジトリは公開されるため、Git のユーザー名とパスワードを入力する必要はありません。
   
   c. **[次へ]** をクリックします。

   ![[Source Git Repository]\(ソース Git リポジトリ\)][CL04]

1. **[Branch]\(ブランチ\)** ページで **[Next]\(次へ\)** をクリックします。

   ![[Branch]\(ブランチ\)][CL05]

1. **[Local Destination]\(ローカルの保存先\)** ページで次の手順を実行します。

   a. ローカル リポジトリを保存するローカル フォルダーを指定します。
   
   b. **[次へ]** をクリックします。

   ![[Local Destination]\(ローカルの保存先\)][CL06]

1. **[Select a wizard to use for importing projects]\(プロジェクトのインポートに使用するウィザードの選択\)** ページで次の手順を実行します。

   a. **[Import as a general project]\(一般的なプロジェクトのインポート\)** を選択します。
   
   b. **[次へ]** をクリックします。

   ![[Local Destination]\(ローカルの保存先\)][CL07]

1. **[Import Projects]\(プロジェクトのインポート\)** ページで次の手順を実行します。

   a. **[Project name]\(プロジェクト名\)** を指定します。
   
   b. **[完了]**をクリックします。

   ![[Import Projects]\(プロジェクトのインポート\)][CL08]

1. リポジトリの複製が完了すると、Eclipse にすべてのファイルが表示されます。

   ![ローカル リポジトリ][CL09]

### <a name="creating-a-maven-project-from-your-local-repository"></a>ローカル リポジトリから Maven プロジェクトを作成する

Spring Boot Docker リポジトリには、このチュートリアルで使用する完成した Maven プロジェクトが含まれています。 

1. **[ファイル]**、**[インポート]** の順にクリックします。

   ![[ファイル] の [インポート] メニュー][CL01]

1. **[インポート]** ダイアログ ボックスが開いたら、次の手順を実行します。

   a. **[Maven]** を展開します。
   
   b. **[Existing Maven Projects]\(既存の Maven プロジェクト\)** を選択します。
   
   c. **[次へ]** をクリックします。

   ![[インポート] ダイアログ ボックス][MV01]

1. **[Maven Projects]\(Maven プロジェクト\)** ページで次の手順を実行します。

   a. ローカル リポジトリの **[Root Directory]\(ルート ディレクトリ\)** 内にある `complete` フォルダーを指定します。
   
   b. **[Advanced]\(詳細\)** セクションを展開し、**[Name template]\(テンプレートの名前\)** にカスタムの名前を入力します。
   
   c. プロジェクト内の `pom.xml` ファイルのチェック ボックスをオンにします。
   
   d. **[完了]**をクリックします。

   ![[Maven Projects]\(Maven プロジェクト\)][MV02]

1. Maven プロジェクトが正常に開くと、Eclipse に 2 つ目のプロジェクトが表示されます。

   ![ローカルの Maven プロジェクト][MV03]

## <a name="building-your-spring-boot-app-with-maven"></a>Maven による Spring Boot アプリのビルド

1. Eclipse Project Explorer で、Maven プロジェクトを選択します。

1. **[Run]\(実行\)**、**[Run As]\(プログラム名を指定して実行\)**、**[Maven build]\(Maven ビルド\)** の順にクリックします。

   ![[Run As]\(プログラム名を指定して実行\) の [Maven build]\(Maven ビルド\)][BU01]

1. アプリケーションが正常にビルドされると、コンソール ウィンドウに状態が表示されます。

   ![Maven ビルドの成功][BU02]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Docker コンテナーを使用して Web アプリを Azure に発行する

1. Eclipse Project Explorer で、Maven プロジェクトを選択します。

1. Azure の **[Publish]\(発行\)** メニューをクリックし、**[Publish as Docker container]\(Docker コンテナーとして発行\)** をクリックします。

   ![[Publish as Docker Container]\(Docker コンテナーとして発行\)][PU01]

1. **[Deploy Docker Container on Azure]\(Azure への Docker コンテナーのデプロイ\)** ダイアログ ボックスが表示されたら、次の手順を実行します。

   a. カスタムの **[Docker image name]\(Docker イメージ名\)** を入力します。
   
   b. **[Artifact to deploy]\(デプロイするアーティファクト\)** に、ビルドした `gs-spring-boot-docker-0.1.0.jar` ファイルのパスを指定します。

   ![Docker オプションの指定][PU02]

   既存の Docker ホストがすべて表示されます。 既存のホストへのデプロイを選択する場合は、スキップして手順 4 に進みます。 それ以外の場合は、次の手順に従って、新しいホストを作成する必要があります。

   a. **[追加]**をクリックします。

      ![新しい Docker ホストの追加][PU03]

   b. **[Create Docker Host]\(Docker ホストの作成\)** ダイアログ ボックスが表示されたら、新しい Docker ホストに既定値をそのまま使用するか、カスタム設定を指定できます (さまざまな設定について詳しくは、「[Azure Toolkit for IntelliJ を使用して Web アプリを Docker コンテナーとして発行する][Publish Container with Azure Toolkit]」を参照してください)。使用する設定を指定したら、**[Next]\(次へ\)** をクリックします。

      ![Docker ホスト オプションの指定][PU04]

   c. Key Vault の既存のログイン資格情報を使用することも、新しい Docker ログイン資格情報を入力することもできます。 オプションを指定したら、**[Finish]\(完了\)** をクリックします。

      ![Docker ホストの資格情報の指定][PU05]

1. Docker ホストを強調表示し、**[Next]\(次へ\)** をクリックします。

   ![使用する Docker ホストの選択][PU06]

1. **[Deploy Docker Container on Azure]\(Azure への Docker コンテナーのデプロイ\)** ダイアログ ボックスの最後のページで、次のオプションを指定します。

   a. Docker コンテナーをホストするコンテナーのカスタム名を指定するか、既定値をそのまま使用することができます。

   b. "*[外部ポート]*:*[内部ポート]*" という構文で、Docker ホストの TCP ポートを入力する必要があります。 たとえば "80:8080" では、外部ポート "80" と既定の内部 Spring Boot ポート "8080" が指定されます。
   
      内部ポートをカスタマイズした場合 (*application.yml* ファイルを編集するなどして)、Azure で正しいルーティングが実現するようポート番号を指定する必要があります。

   c. これらのオプションを構成したら、**[Finish]\(完了\)** をクリックします。

   ![Azure への Docker コンテナーのデプロイ][PU07]

1. Azure Toolkit による発行が完了したら、Azure Activity Log の状態が**発行済み**になります。

   ![正常にデプロイされた Docker ホスト][PU08]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Deploy Spring Boot on Linux in ACS]: ./container-service/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL01.png
[CL02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL02.png
[CL03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/CL09.png

[MV01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV01.png
[MV02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV02.png
[MV03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/MV03.png

[BU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/BU02.png

[PU01]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU02.png
[PU03]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU03.png
[PU04]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU06.png
[PU07]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU07.png
[PU08]: ./media/azure-toolkit-for-eclipse-publish-spring-boot-docker-app/PU08.png

