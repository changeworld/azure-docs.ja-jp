---
title: "Azure Toolkit for IntelliJ を使用して Docker コンテナーを発行する | Microsoft Docs"
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
ms.date: 04/14/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 96680319a6c4c0f0a4673cd6303a5b172f428797
ms.contentlocale: ja-jp
ms.lasthandoff: 06/10/2017


---

# <a name="publish-a-web-app-as-a-docker-container-by-using-the-azure-toolkit-for-intellij"></a>Azure Toolkit for IntelliJ を使用して Web アプリを Docker コンテナーとして発行する

Docker コンテナーは、Web アプリケーションをデプロイするために広く使用されている方法です。 Docker コンテナーを使用すると、開発者は、すべてのプロジェクト ファイルと依存関係を、サーバーにデプロイするために 1 つのパッケージに統合できます。 Java 開発者のこのプロセスを簡略化するために、Azure Toolkit for IntelliJ には、Microsoft Azure にデプロイするための "*Docker コンテナーとして発行*" 機能が追加されました。 この記事では、アプリケーションを Docker コンテナーとして Azure に発行するために必要な手順を説明します。

> [!NOTE]
>
> Docker の詳細については、[Docker の Web サイト]を参照してください。
>

[!INCLUDE [azure-toolkit-for-intellij-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## <a name="publish-your-web-app-to-azure-by-using-a-docker-container"></a>Docker コンテナーを使用して Web アプリを Azure に発行する

> [!NOTE]
> * Web アプリを発行するには、デプロイの準備ができたアーティファクトを作成する必要があります。 詳細については、「[アーティファクトの作成に関する追加情報](#artifacts)」セクションを参照してください。
>
> * デプロイ ウィザードを少なくとも 1 回完了した後、ウィザードをもう一度実行するときには、設定の大部分が既定値として使用されます。
>

1. IntelliJ で、Web アプリ プロジェクトを開きます。

2. **Publish as Docker Container (Docker コンテナーとして発行)** ウィザードを起動するには、次のいずれかの操作を行います。

   * **[プロジェクト]** ツール ウィンドウでプロジェクトを右クリックし、**[Azure]** をクリックしてから **[Publish as Docker Container]\(Docker コンテナーとして発行\)** をクリックします。

      ![[Publish as Docker Container]\(Docker コンテナーとして発行\) コマンド][PUB01]

   * IntelliJ ツール バーの **[Publish Group]\(発行グループ\)** ボタンをクリックし、**[Publish as Docker Container]\(Docker コンテナーとして発行\)** をクリックします。

      ![[Publish as Docker Container]\(Docker コンテナーとして発行\) コマンド][PUB02]  
    **Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ)** ウィザードが開かれます。

   ![[Deploy Docker Container on Azure] (Azure への Docker コンテナーのデプロイ) ウィザード][PUB03]

3. **[Type image name, select artifact's path and check Docker host to be used] \(イメージ名を入力し、アーティファクトのパスを選択して、使用される Docker ホストを確認する)** ウィンドウで、以下の手順を実行します。 

   a. **[Docker image name] \(Docker イメージ名)** ボックスに、Docker ホストの一意の名前を入力します。 (このウィザードでは名前が自動的に作成されますが、それは変更できます)。 

   b. **[ホスト]** 領域には、既に作成しているすべての Docker ホストが表示されます。 次のいずれかを実行します。 
      * 既存の Docker ホストがある場合は、それに Web アプリをデプロイできます。
      * Docker ホストを作成するには、緑色のプラス記号 (**+**) をクリックします。  
       **[Create Docker Host]\(Docker ホストの作成\)** ダイアログ ボックスが表示されます。 

      ![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ) ウィザード][PUB04a]

4. **[Configure the new virtual machine]\(新しい仮想マシンの構成\)** ウィンドウで、Docker ホストについての以下の情報を指定します  (ほとんどの情報はウィザードによって自動的に生成されますが、どの情報も変更することができます)。 

   a. **[名前]** ボックスに、Docker ホストの一意の名前を入力します  (これは前に指定した Docker イメージの名前と同じではありません)。 
    
   b. **[サブスクリプション]** ボックスで、ホストのために使用する Azure サブスクリプションを入力します。 
      
   c. **[リージョン]** ボックスで、ホストが配置される地理的なリージョンを入力します。
      
   d. **[OS and Size]\(OS とサイズ\)** タブで、以下の操作を行います。      
      * **[Host OS]\(ホスト OS\)**: ホストがある仮想マシンのオペレーティング システムを入力します。 
      * **[サイズ]**: ホストの仮想マシンのサイズを入力します。   
       
   e. **[リソース グループ]** タブで、以下のいずれかを選択します。      
      * **[新しいリソース グループ]**: ホストのリソース グループを作成します。
      * **[既存のリソース グループ]**: Azure アカウントの既存のリソース グループを指定します。 
       
   f.SAML 属性の属性名またはスキーマ リファレンスを入力します。 **[ネットワーク]** タブで、以下のいずれかを選択します。      
      * **[新しい仮想ネットワーク]**: ホストの仮想ネットワークを作成します。
      * **[Existing virtual network]\(既存の仮想ネットワーク\)**: Azure アカウントの既存の仮想ネットワークを指定します。 
       
   g. **[ストレージ]** タブで、以下のいずれかを選択します。      
      * **[新規ストレージ アカウント]**: ホストのストレージ アカウントを作成します。
      * **[Existing storage account]\(既存のストレージ アカウント\)**: Azure アカウントの既存のストレージ アカウントを指定します。
       
5. **[次へ]** をクリックします。  
     **[Configure log in credentials and port settings]\(ログイン資格情報とポート設定の構成\)** ウィンドウが開きます。

      ![[Configure log in credentials and port settings]\(ログイン資格情報とポート設定の構成\) ウィンドウ][PUB05]

6. 次のいずれかのオプションを選択します。

      * **[Import credentials from Azure Key Vault]\(Azure Key Vault から資格情報をインポート\)**: 以前に保存した、Azure サブスクリプションに格納されている資格情報のセットを指定します。

          > [!NOTE]
          > 特定のアカウントまたはサービス プリンシパルで作成された Azure Key Vault は、サブスクリプションを共有する別のアカウントまたはサービス プリンシパルから自動的にアクセスできるようにはなりません。 別のアカウントまたはサービス プリンシパルが Key Vault を使用できるようにするには、Azure Portal を使用して、アカウントまたはサービス プリンシパルを追加する必要があります。

      * **[New log in credentials]\(新しいログイン資格情報\)**: ログイン資格情報の新しいセットを作成します。 このオプションを選択する場合は、次の操作を行います。

        a. **[VM Credentials]\(VM 資格情報)** タブで、Docker ホストの仮想マシン ログイン資格情報について、以下の情報を指定します。 * **[ユーザー名]**: 仮想マシン ログイン資格情報のユーザー名を入力します。
             * **[パスワード]** および **[確認]** : 仮想マシン ログイン資格情報のパスワードを入力します。
             * **[SSH]**: Docker ホストの Secure Shell (SSH) 設定を入力します。 以下のいずれかのオプションを選択できます。 * **[なし]**: 仮想マシンが SSH 接続を許可しないことを指定します。
                * **[自動生成]**: SSH 経由で接続するために必要な設定を自動的に作成します。
                * **[Import from directory]\(ディレクトリからインポート\)**: 以前に保存した SSH 設定のセットがあるディレクトリを指定できます。 このディレクトリには、次の 2 つのファイルが含まれている必要があります。
                
                  * *id_rsa*: Contains the RSA identification for a user.
                  * *id_rsa.pub*: Contains the RSA public key that is used for authentication.
            
        b. **[Docker Daemon Access]\(Docker デーモン アクセス\)** タブで、以下の情報を指定します。

          ![Docker ホストを作成する][PUB06]
    
             * **Docker Daemon port**: Enter the unique TCP port for your Docker host.
             * **TLS Security**: Enter the Transport Layer Security settings for your Docker host. You can choose from the following options:
                * **None**: Specifies that your virtual machine does not allow TLS connections.
                * **Auto-generate**: Automatically creates the requisite settings for connecting via TLS.
                * **Import from directory**: Specifies a directory that contains a set of previously saved TLS settings. The directory must contain the following six files: 
                   * *ca.pem* and *ca-key.pem*: Contain the certificate and public key for the TLS Certificate Authority.
                   * *cert.pem* and *key.pem*: Contain client certificate and public key which will be used for TLS authentication.
                   * *server.pem* and *server-key.pem*: Contain the client certificate and public key that is used for TLS authentication.

7. 必要な情報を入力したら、**[完了]** をクリックします。  
    **Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ)** ウィザードが再び表示されます。

   ![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ) ウィザード][PUB07]

8. **[次へ]** をクリックします。  
    **[Configure the Docker container to be created]\(作成される Docker コンテナーの構成\)** ウィンドウが開きます。

   ![[Configure the Docker container to be created]\(作成される Docker コンテナーの構成\) ウィンドウ][PUB08]

9. **[Configure the Docker container to be created]\(作成される Docker コンテナーの構成\)** ウィンドウで、以下の情報を指定します。 

   a. **[Docker container name] \(Docker コンテナー名)** ボックスに、Docker コンテナーの一意の名前を入力します。

   b. 次のいずれかの Docker イメージを選びます。 

      * **[Predefined Docker image]\(定義済みの Docker イメージ\)**: Azure から既存の Docker イメージを指定します。 

        > [!NOTE]
        > このボックス内の Docker イメージの一覧は、アーティファクトが自動的にデプロイされるように Azure Toolkit が修正する複数のイメージで構成されています。 

      * **[Custom Dockerfile]\(カスタム Dockerfile\)**: ローカル コンピューターから、以前に保存した Dockerfile を指定します。

        > [!NOTE]
        > これは、独自の Dockerfile をデプロイする開発者向けのより高度な機能です。 ただし、Dockerfile が正しく構築されていることを確認するのは、このオプションを使用する開発者の責任です。 Azure Toolkit はカスタム Dockerfile の内容を検証しないため、Dockerfile に問題がある場合はデプロイが失敗します。 また、Azure Toolkit はカスタム Dockerfile に Web アプリ アーティファクトが含まれていると想定しているため、HTTP 接続を開こうとします。 開発者が別の種類のアーティファクトを発行すると、デプロイ後に無害なエラーが表示されることがあります。

   c. **[ポートの設定]** ボックスで、Docker コンテナーの一意の TCP ポート バインドを入力します。 

10. 上記の手順を完了したら、**[完了]** をクリックします。 

Azure Toolkit により、Docker コンテナーでの Azure への Web アプリのデプロイが開始されます。 バックグラウンドでデプロイされるように IntelliJ を構成していない場合は、**[Deploying to Azure]\(Azure にデプロイ中\)** 進行状況バーが表示されます。 

![デプロイの進行状況バー][PUB09]

<a name="artifacts"></a>
## <a name="additional-information-about-creating-artifacts"></a>アーティファクトの作成に関する追加情報

デプロイの準備ができたアーティファクトを作成するには、次の操作を行います。

1. IntelliJ で、Web アプリ プロジェクトを開きます。

2. **[ファイル]** をクリックし、**[プロジェクトの構造]** をクリックします。

   ![[プロジェクトの構造] コマンド][ART01]

3. アーティファクトを追加するには、緑のプラス記号 (**+**) をクリックし、**[Web Application: Archive]\(Web アプリケーション: アーカイブ\)** をクリックします。

   ![[Web Application: Archive]\(Web アプリケーション: アーカイブ\) コマンド][ART02]

4. **[名前]** ボックスで、アーティファクトの名前を入力し (*.war* 拡張子は含めないでください)、**[OK]** をクリックします。

   ![アーティファクトの [名前] ボックス][ART03]

IntelliJ でのアーティファクトの作成の詳細については、JetBrains Web サイトの「[Configuring Artifacts (アーティファクトの構成)]」を参照してください。

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

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター] と[Java Tools for Visual Studio Team Services] を参照してください。

Docker の他のリソースについては、公式の [Docker の Web サイト]を参照してください。

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
[Configuring Artifacts (アーティファクトの構成)]: https://www.jetbrains.com/help/idea/2016.1/configuring-artifacts.html

<!-- IMG List -->

[PUB01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB01.png
[PUB02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB02.png
[PUB03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB03.png
[PUB04a]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04a.png
[PUB04b]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04b.png
[PUB04c]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04c.png
[PUB04d]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB04d.png
[PUB05]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB05.png
[PUB06]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB06.png
[PUB07]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB07.png
[PUB08]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB08.png
[PUB09]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/PUB09.png

[ART01]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART01.png
[ART02]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART02.png
[ART03]: ./media/azure-toolkit-for-intellij-publish-as-docker-container/ART03.png

