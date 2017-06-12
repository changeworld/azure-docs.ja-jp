---
title: "Azure Toolkit for Eclipse を使用した Docker コンテナーの発行 | Microsoft Docs"
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
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 746bb0a073396b84fa8592adda6748a2a5692ee8
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---

# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse を使用して Web アプリを Docker コンテナーとして発行する

Docker コンテナーは、Web アプリケーションをデプロイするための広範に使用されている方法です。 Docker コンテナーを使用すると、開発者はすべてのプロジェクト ファイルおよび依存関係を、サーバーにデプロイするための 1 つのパッケージに統合できます。 Azure Toolkit for Eclipse は、Microsoft Azure にデプロイするための *[Docker コンテナーとして発行]* 機能を追加することによって、Java 開発者のこのプロセスを簡略化します。 この記事では、アプリケーションを Docker コンテナーとして Azure に発行するために必要な手順について説明します。

> [!NOTE]
> Docker の詳細については、[Docker の Web サイト]を参照してください。
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Docker コンテナーを使用して Web アプリを Azure に発行する

1. Eclipse で、Web アプリ プロジェクトを開きます。

2. **[Docker コンテナーとして発行]** ウィザードを起動するには、次のいずれかを実行します。

   * **[ナビゲーター]** ビューで、プロジェクトを右クリックし、**[Azure]** をクリックしてから **[Docker コンテナーとして発行]** をクリックします。

      ![ナビゲーター ビューの [Docker コンテナーとして発行] コマンド][PUB01]

   * Eclipse ツールバーの **[発行]** ボタンをクリックしてから、**[Docker コンテナーとして発行]** をクリックします。

      ![Eclipse ツールバーの [Docker コンテナーとして発行] コマンド][PUB02]
      
    **[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ)** ウィザードが開きます。

    ![[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ) ウィザード][PUB03]

3. **[Type image name, select artifact's path and check Docker host to be used] (イメージ名を入力し、アーティファクトのパスを選択して、使用される Docker ホストを確認する)** ウィンドウで、以下の手順を実行します。

    a. **[Docker image name] (Docker イメージ名)** ボックスに、Docker ホストの一意の名前を入力します。 (このウィザードでは名前が自動的に作成されますが、それは変更できます)。

    b. **[ホスト]** 領域には、既に作成しているすべての Docker ホストが表示されます。 次のいずれかを実行します。

    * 既存の Docker ホストがある場合は、それに Web アプリをデプロイできます。
    * 新しい Docker ホストを作成するには、**[追加]** をクリックします。  
      
    **[Create Docker Host] (Docker ホストの作成)** ダイアログ ボックスが開きます。

    ![[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ) ウィザード][PUB04a]

4. **[Configure the new virtual machine] (新しい仮想マシンの構成)** ウィンドウで、Docker ホストの次のオプションを指定します。 (このウィザードではほとんどのオプションが自動的に生成されますが、いずれのオプションも変更できます)。

   a. **[名前]**: Docker ホストの一意の名前を入力します。 (これは、前に指定した Docker イメージ名と同じではありません)。

   b. **[サブスクリプション]**: ホストに使用する Azure サブスクリプションを入力します。

   c. **[リージョン]**: ホストが配置される地理的リージョンを入力します。

   d. **[Host OS and Size] (ホスト OS とサイズ)**  タブ:
     * **[ホスト OS]**: ホストが含まれている仮想マシンのオペレーティング システムを入力します。
     * **[サイズ]**: ホストの仮想マシンのサイズを入力します。

   e. **[リソース グループ]** タブ:
     * **[新しいリソース グループ]**: ホストの新しいリソース グループを作成します。
     * **[Existing resource group] (既存のリソース グループ)**: Azure アカウントの既存のリソース グループを入力します。

   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[ネットワーク]** タブ:
     * **[新しい仮想ネットワーク]**: ホストの新しい仮想ネットワークを作成します。
     * **[既存の仮想ネットワーク]**: Azure アカウントの既存の仮想ネットワークを入力します。

   g. **[ストレージ]** タブ:
     * **[新しいストレージ アカウント]**: ホストの新しいストレージ アカウントを作成します。
     * **[既存のストレージ アカウント]**: Azure アカウントの既存のストレージ アカウントを入力します。

5. **[次へ]** をクリックします。

6. **[Configure log in credentials and port settings] (ログイン資格情報とポート設定の構成)** ウィンドウで、次のいずれかのオプションを選択します。

    * **[Import credentials from Azure Key Vault] (Azure Key Vault からの資格情報のインポート)**: Azure サブスクリプションに格納されている、以前に保存された一連の資格情報を指定します。

      >[!NOTE]
      >特定のアカウントまたはサービス プリンシパルで作成された Azure Key Vault は、サブスクリプションを共有する別のアカウントまたはサービス プリンシパルから自動的にアクセスできるようにはなりません。 別のアカウントまたはサービス プリンシパルが Key Vault を使用できるようにするには、Azure Portal を使用して、アカウントまたはサービス プリンシパルを追加する必要があります。

    * **[New log in credential] (新しいログイン資格情報)**: 新しい一連のログイン資格情報を作成します。 このオプションを選択する場合は、次の操作を行います。
    
      * **[VM Credentials] (VM 資格情報)** タブで、Docker ホストの仮想マシン ログイン資格情報の次のいずれかのオプションを選択します。

          * **[ユーザー名]**: 仮想マシン ログイン資格情報のユーザー名を入力します。
          * **[パスワード]** および **[確認]**: 仮想マシン ログイン資格情報のパスワードを入力します。
          * **[SSH]**: Docker ホストの Secure Shell (SSH) 設定を入力します。 次のオプションから選択できます。
            * **[なし]**: 仮想マシンが SSH 接続を許可しないことを指定します。
            * **[自動生成]**: SSH 経由で接続するために必要な設定を自動的に作成します。
            * **[Import from directory] (ディレクトリからのインポート)**: 以前に保存された一連の SSH 設定を含むディレクトリを指定します。 このディレクトリには、次の 2 つのファイルが含まれている必要があります。
                * *id_rsa*: ユーザーの RSA ID が含まれています。
                * *id_rsa.pub*: 認証に使用される RSA 公開キーが含まれています。
        
        ![[Create Docker Host] (Docker ホストの作成)][PUB05]

      * **[Docker Daemon Credentials] (Docker デーモン資格情報)** タブで、次のオプションを指定します。

          * **[Docker Daemon port] (Docker デーモン ポート)**: Docker ホストの固有の TCP ポートを入力します。
          * **[TLS Security] (TLS セキュリティ)**: Docker ホストのトランスポート層セキュリティ設定を入力します。 次のオプションから選択できます。
            * **[なし]**: 仮想マシンが TLS 接続を許可しないことを指定します。
            * **[自動生成]**: TLS 経由で接続するために必要な設定を自動的に作成します。
            * **[Import from directory] (ディレクトリからのインポート)**: 以前に保存された一連の TLS 設定を含むディレクトリを指定します。 具体的には、ディレクトリには次の 6 つのファイルが含まれている必要があります。
                * *ca.pem* と *ca-key.pem*: TLS 証明機関の証明書と公開キーが含まれています。
                * *cert.pem* と *key.pem*: TLS 認証に使用されるクライアント証明書と公開キーが含まれています。
                * *server.pem* と *server-key.pem*: ホストのサーバー証明書と公開キーが含まれています。

        ![[Create Docker Host] (Docker ホストの作成)][PUB06]

7. 上記の情報をすべて入力したら、**[完了]** をクリックします。

8. **[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ)** ウィザードで、**[次へ]** をクリックします。

   ![[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ) ウィザード][PUB07]

9. **[Configure the Docker container to be created] (作成される Docker コンテナーの構成)** ウィンドウで、以下の手順を実行します。

   a. **[Docker container name] (Docker コンテナー名)** ボックスに、Docker コンテナーの一意の名前を入力します。

   b. 次のいずれかの Docker イメージを選びます。
     * **[Predefined Docker image] (定義済みの Docker イメージ)**: Azure の既存の Docker イメージを指定します。

       >[!NOTE]
       >このボックス内の Docker イメージの一覧は、アーティファクトが自動的にデプロイされるように、Azure Toolkit が修正するように構成されているいくつかのイメージで構成されています。

     * **[Custom Dockerfile] (カスタム Dockerfile)**: ローカル コンピューターから以前に保存された Dockerfile を指定します。

       >[!NOTE]
       >これは、独自の Dockerfile をデプロイする開発者向けのより高度な機能です。 ただし、Dockerfile が正しく構築されていることを確認するのは、このオプションを使用する開発者の責任です。 Azure Toolkit はカスタム Dockerfile 内のコンテンツを検証しないため、Dockerfile に問題がある場合はデプロイが失敗することがあります。 さらに、Azure Toolkit はカスタム Dockerfile に Web アプリ アーティファクトが含まれていることを期待し、また HTTP 接続を開こうとします。 開発者が別の種類のアーティファクトを発行した場合は、デプロイ後に無害のエラーが表示されることがあります。

   c. **[ポートの設定]**: Docker コンテナーの固有の TCP ポート バインドを入力します。

     ![[Configure the Docker container to be created] (作成される Docker コンテナーの構成) ウィンドウ][PUB08]

10. 上記の手順をすべて完了したら、**[完了]** をクリックします。

Azure Toolkit が Docker コンテナーでの Azure への Web アプリのデプロイを開始します。 

## <a name="next-steps"></a>次のステップ
Azure Toolkit for Java IDE の詳細については、次のリソースを参照してください。

* [Azure Toolkit for Eclipse]
  * [Azure Toolkit for Eclipse の新機能]
  * [Azure Toolkit for Eclipse のインストール]
  * [Azure Toolkit for Eclipse のサインイン手順]
  * [Eclipse で Azure 用の Hello World Web アプリを作成する]
* [Azure Toolkit for IntelliJ]
  * [Azure Toolkit for IntelliJ の新機能]
  * [Azure Toolkit for IntelliJ のインストール]
  * [Azure Toolkit for IntelliJ のサインイン手順]
  * [IntelliJ で Azure 用の Hello World Web アプリを作成する]

Java での Azure の使用の詳細については、[Azure Java デベロッパー センター]と [Java Tools for Visual Studio Team Services] を参照してください。

Docker のその他のリソースについては、公式の [Docker の Web サイト]を参照してください。

<!-- URL List -->

[Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Eclipse で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[IntelliJ で Azure 用の Hello World Web アプリを作成する]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Azure Toolkit for Eclipse のインストール]: ./azure-toolkit-for-eclipse-installation.md
[Azure Toolkit for IntelliJ のインストール]: ./azure-toolkit-for-intellij-installation.md
[Azure Toolkit for Eclipse のサインイン手順]: ./azure-toolkit-for-eclipse-sign-in-instructions.md
[Azure Toolkit for IntelliJ のサインイン手順]: ./azure-toolkit-for-intellij-sign-in-instructions.md
[Azure Toolkit for Eclipse の新機能]: ./azure-toolkit-for-eclipse-whats-new.md
[Azure Toolkit for IntelliJ の新機能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/

[Docker の Web サイト]: https://www.docker.com/

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-eclipse-publish-as-docker-container/PUB08.png
