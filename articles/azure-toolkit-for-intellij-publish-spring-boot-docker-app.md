---
title: "Azure Toolkit for IntelliJ を使用して Spring Boot アプリを Docker コンテナーとして発行する | Microsoft Docs"
description: "Azure Toolkit for IntelliJ を使用して、Web アプリを Docker コンテナーとして Microsoft Azure に発行する方法について説明します。"
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
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 4f9c118fbfe445dd38670e3b7549a0d89681e5db
ms.contentlocale: ja-jp
ms.lasthandoff: 07/22/2017

---

# <a name="publish-a-spring-boot-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して Spring Boot アプリを Docker コンテナーとして発行する

[Spring Framework] は Java 開発者のエンタープライズ レベルのアプリケーション作成を支援するオープンソース ソリューションです。 このプラットフォームで構築される特に知られたプロジェクトの 1 つが [Spring Boot] です。これによって、スタンドアロンの Java アプリケーションの作成方法が簡略化されます。

[Docker] は、開発者が、コンテナーで実行されるアプリケーションのデプロイ、スケーリング、および管理を自動化することを支援するオープン ソース ソリューションです。

このチュートリアルでは、Azure Toolkit for IntelliJ を使用して Spring Boot アプリケーションを Docker コンテナーとして Microsoft Azure にデプロイする手順について説明します。

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="clone-the-default-spring-boot-docker-repo"></a>既定の Spring Boot Docker リポジトリの複製

次の手順では、IntelliJ を使用して、Spring Boot Docker リポジトリを複製する方法について説明します。 コマンド ラインの使用が適している場合は、「[Azure Container Service で Spring Boot アプリケーションを Linux にデプロイする][Deploy Spring Boot on Linux in ACS]」を参照してください。

1. IntelliJ を開きます。

1. [ようこそ] 画面で、**[Check out from Version Control]\(バージョン管理からチェックアウト\)** 一覧の **[GitHub]** オプションを選択します。

   ![バージョン コントロールの GitHub オプション][CL01]

1. ログインを求められたら、資格情報を入力します。

   * ユーザー名/パスワードを使用して GitHub にログインする場合:

      ![GitHub ユーザー名とパスワードを入力するためのダイアログ ボックス][CL02a]

   * トークンを使用して GitHub にログインする場合:

      ![GitHub トークンを入力するためのダイアログ ボックス][CL02b]

1. リポジトリ URL に「**https://github.com/spring-guides/gs-spring-boot-docker.git**」と入力し、ローカル パスとフォルダー情報を指定して、**[複製]**をクリックします。

   ![リポジトリの複製ダイアログ ボックス][CL03]

1. IntelliJ プロジェクトの作成を求められた場合は、**[いいえ]** を選択します。

   ![IntelliJ プロジェクトの作成を拒否][CL04]

1. ホーム ページで **[プロジェクトのインポート]** をクリックします。

   ![[プロジェクトのインポート] の選択内容][CL05]

1. Spring Boot リポジトリを複製したパスを見つけて、ルートの下の **complete** フォルダーを選択して、**[OK]** をクリックします。

   ![インポート用フォルダーの選択][CL06]

1. メッセージが表示されたら、**[Create project from existing sources]\(既存のソースからプロジェクトを作成\)** を選択します。

   ![[Create project from existing sources]\(既存のソースからプロジェクトを作成\)][CL07]

1. プロジェクト名を指定するか、既定値を受け入れて、**complete** フォルダーの正確なパスを確認し、**[次へ]** をクリックします。

   ![プロジェクト名の指定][CL08]

1. インポート用にディレクトリをカスタマイズし、**[次へ]** をクリックします。

   ![ディレクトリの選択][CL09]

1. インポートするライブラリを確認し、**[次へ]** をクリックします。

   ![プロジェクト ライブラリの確認][CL10]

1. モジュールの構造を確認し、**[次へ]** をクリックします。

   ![モジュールの構造を確認][CL11]

1. JDK を指定し、**[次へ]** をクリックします。

   ![JDK の指定][CL12]

1. **[完了]**をクリックします。

   ![[完了] ボタン][CL13]

IntelliJ により Spring Boot アプリがプロジェクトとしてインポートされ、インポートが完了すると構造が表示されます。

![IntelliJ での Spring Boot アプリ][CL14]

## <a name="build-your-spring-boot-app"></a>Spring Boot アプリのビルド

### <a name="build-the-app-by-using-the-maven-pom"></a>Maven POM を使用したアプリのビルド

1. Maven ツール ウィンドウがまだ開かれていない場合は、Maven ツール ウィンドウを開きます。 **[表示]** > **[ツール ウィンドウ]** > **[Maven Projects]\(Maven プロジェクト\)** の順にクリックします。

   ![ツール ウィンドウおよび Maven プロジェクト コマンド][BU01]

1. Maven ツール ウィンドウで **[パッケージ]** を右クリックして、**[Run Maven Build]\(Maven ビルドの実行\)** を選択します。 (Maven プロジェクトが自動的に表示されない場合は、Maven ツール バーで **[Reimport]\(再インポート\)** アイコンをクリックします。)

   ![Maven ビルド コマンドの実行][BU02]

1. Spring Boot アプリが正常に作成されると、IntelliJ により、**BUILD SUCCESS** メッセージが表示されます。

   ![ビルド成功のメッセージ][BU03]

### <a name="create-a-deployment-ready-artifact"></a>デプロイの準備ができたアーティファクトの作成

Spring Boot アプリを発行するには、デプロイの準備ができたアーティファクトを作成する必要があります。 次の手順に従います。

1. IntelliJ で、Web アプリ プロジェクトを開きます。

1. **[ファイル]** をクリックし、**[プロジェクトの構造]** をクリックします。

   ![[プロジェクトの構造] コマンド][ART01]

1. 緑色のプラス (**+**) 記号をクリックしてアーティファクトを追加し、**[JAR]** をクリックして、**[Empty]\(空にする\)** をクリックします。

   ![アーティファクトの追加][ART02]

1. ".jar" 拡張子を追加しないようにしてアーティファクトに名前を付け、Maven の出力用のターゲット フォルダーを指定します。

   ![アーティファクトのプロパティを指定][ART03]

1. アーティファクトのマニフェストを作成します (省略可能)。

   a. **[Create Manifest]\(マニフェストの作成\)** をクリックします。

      ![[マニフェストの作成] ボタンをクリック][ART04a]

   b. アーティファクトの既定のパスを選択し、**[OK]** をクリックします。

      ![アーティファクトのパスを指定][ART04b]

   c. 省略記号 (**...**) をクリックして、メイン クラスを検索します。

      ![メイン クラスの検索][ART04c]

   d. メイン クラスを選択して、**[OK]** をクリックします。

      ![メイン クラスの指定][ART04d]

1. **[OK]**をクリックします。

   ![[プロジェクト構造] ダイアログ ボックスを閉じる][ART05]

> [!NOTE]
> IntelliJ のアーティファクトの作成について詳しくは、JetBrains の Web サイトの「[Configuring Artifacts (アーティファクトの構成)]」をご覧ください。
>

### <a name="build-the-artifact-for-deployment"></a>デプロイ用のアーティファクトのビルド

1. **[Build]\(ビルド\)** をクリックし、**[Artifacts]\(アーティファクト\)** をクリックします。

   ![[Build Artifacts]\(ビルド アーティファクト\) コマンド][BU04]

1. **[Build Artifacts]\(ビルド アーティファクト\)** コンテキスト メニューが表示されたら、**[Build]\(ビルド\)** をクリックします。

   ![[Build Artifacts]\(ビルド アーティファクト\) コンテキスト メニュー][BU05]

IntelliJ でプロジェクト ツール ウィンドウに、Spring Boot アプリの完成したアーティファクトが表示されます。

   ![作成されたアーティファクト][BU06]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Docker コンテナーを使用して Web アプリを Azure に発行する

1. Azure アカウントにサインインしていない場合は、「[Azure Toolkit for IntelliJ のサインイン手順][Azure Sign In for IntelliJ]」の手順に従います。

1. プロジェクト エクスプローラー ツール ウィンドウで、プロジェクトを右クリックし、**[Azure]** > **[Publish as Docker Container]\(Docker コンテナーとして発行\)** をクリックします。

   ![[Publish as Docker Container]\(Docker コンテナーとして発行\) コマンド][PU01]

1. **[Deploy Docker Container on Azure]\(Azure への Docker コンテナーのデプロイ\)** ダイアログ ボックスが表示され、既存の Docker ホストが表示されます。 既存のホストへのデプロイを選択する場合は、スキップして手順 4 に進みます。 そうでない場合は、次の手順に従ってホストを作成します。

   a. 緑色のプラス ("**+**") 記号をクリックします。

      ![新しい Docker ホストの追加][PU02]

   b. **[Create Docker Host]\(Docker ホストの作成\)** ダイアログ ボックスが表示されたら、新しい Docker ホストに既定値をそのまま使用するか、カスタム設定を指定できます  (さまざまな設定について詳しくは、「[Azure Toolkit for IntelliJ を使用して Web アプリを Docker コンテナーとして発行する][Publish Container with Azure Toolkit]」を参照してください)。使用する設定を指定したら、**[次へ]** をクリックします。

      ![Docker ホスト オプションの指定][PU03a]

   c. Azure Key Vault の既存のログイン資格情報を使用することも、新しい Docker ログイン資格情報を入力することもできます。 オプションを指定したら、**[完了]** をクリックします。

      ![Docker ホストの資格情報を指定][PU03b]

1. Docker ホストを選択し、**[次へ]** をクリックします。

   ![使用する Docker ホストの選択][PU04]

1. **[Deploy Docker Container on Azure]\(Azure への Docker コンテナーのデプロイ\)** ダイアログ ボックスの最後のページで、次のオプションを指定します。

   a. Docker コンテナーをホストするコンテナーのカスタム名を指定するか、既定値をそのまま使用することができます。

   b. "*[外部ポート]*:*[内部ポート]*" という構文で、Docker ホストの TCP ポートを入力します。 たとえば "**80:8080**" では、外部ポート "80" と既定の内部 Spring Boot ポート "8080" が指定されます。
   
      内部ポートをカスタマイズした場合 (application.yml ファイルを編集するなどして)、Azure で正しいルーティングが実現するようポート番号を指定する必要があります。

   c. これらのオプションを構成したら、**[完了]** をクリックします。

   ![Azure への Docker コンテナーのデプロイ][PU05]

1. Azure Toolkit による発行が完了したら、Azure Activity Log の状態が**発行済み**になります。

   ![正常にデプロイされた Docker ホスト][PU06]

## <a name="next-steps"></a>次のステップ

[!INCLUDE [azure-toolkit-additional-resources](../includes/azure-toolkit-additional-resources.md)]

IntelliJ を使用して Spring Boot アプリを作成するための他の方法については、JetBrains Web サイトの [Spring Boot プロジェクトの作成](https://www.jetbrains.com/help/idea/creating-spring-boot-projects.html)に関する記事を参照してください。

<!-- URL List -->

[Azure Management Portal]: http://go.microsoft.com/fwlink/?LinkID=512959
[Azure Sign In for IntelliJ]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Configuring Artifacts (アーティファクトの構成)]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html
[Deploy Spring Boot on Linux in ACS]:container-service/kubernetes/container-service-deploy-spring-boot-app-on-linux.md
[Docker]: https://www.docker.com/
[Publish Container with Azure Toolkit]: ./azure-toolkit-for-intellij-publish-as-docker-container.md
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[CL01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL01.png
[CL02a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02a.png
[CL02b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL02b.png
[CL03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL03.png
[CL04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL04.png
[CL05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL05.png
[CL06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL06.png
[CL07]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL07.png
[CL08]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL08.png
[CL09]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL09.png
[CL10]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL10.png
[CL11]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL11.png
[CL12]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL12.png
[CL13]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL13.png
[CL14]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/CL14.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART03.png
[ART04a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04a.png
[ART04b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04b.png
[ART04c]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04c.png
[ART04d]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART04d.png
[ART05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/ART05.png

[BU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU01.png
[BU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU02.png
[BU03]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU03.png
[BU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU04.png
[BU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU05.png
[BU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/BU06.png

[PU01]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU01.png
[PU02]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU02.png
[PU03a]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03a.png
[PU03b]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU03b.png
[PU04]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU04.png
[PU05]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU05.png
[PU06]: ./media/azure-toolkit-for-intellij-publish-spring-boot-docker-app/PU06.png

