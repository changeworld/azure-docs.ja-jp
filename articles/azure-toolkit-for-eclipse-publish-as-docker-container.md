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
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: f738647fa1dad757b53611c7d4abe2dd99fe8838
ms.lasthandoff: 04/22/2017


---

# <a name="how-to-publish-a-web-app-as-a-docker-container-using-the-azure-toolkit-for-eclipse"></a>Azure Toolkit for Eclipse を使用して、Web アプリを Docker コンテナーとして発行する方法

Docker コンテナーは Web アプリケーションのデプロイに広く使用されている方法で、開発者はすべてのプロジェクト ファイルと依存関係をサーバーにデプロイするために 1 つのパッケージに統合できます。 Azure Toolkit for Eclipse は、Microsoft Azure にデプロイするための "*Publish as Docker Container (Docker コンテナーとして発行)*" 機能を追加して、Java 開発者のためにこのプロセスを簡略化します。この記事では、アプリケーションを Docker コンテナーとして Azure に発行するために必要な手順を説明します。

> [!NOTE]
>
> Docker について詳しくは、[Docker の Web サイト]をご覧ください。
>

[!INCLUDE [azure-toolkit-for-eclipse-prerequisites](../includes/azure-toolkit-for-eclipse-prerequisites.md)]

## <a name="publishing-your-web-app-to-azure-using-a-docker-container"></a>Docker コンテナーを使用して Web アプリを Azure に発行する

1. Eclipse で、Web アプリ プロジェクトを開きます。

1. 次のいずれかの方法を使用して Publish as Docker Container (Docker コンテナーとして発行) ウィザードを起動します。

   * **[ナビゲーター]** ビューでプロジェクトを右クリックし、**[Azure]** をクリックしてから **[Publish as Docker Container (Docker コンテナーとして発行)]** をクリックします。 ![Publish as Docker Container (Docker コンテナーとして発行)][PUB01]

   * Eclipse ツール バーの **[Publish (発行)]** メニューをクリックし、**[Publish as Docker Container (Docker コンテナーとして発行)]** をクリックします。 ![Publish as Docker Container (Docker コンテナーとして発行)][PUB02]

1. **Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ)** ウィザードが表示されると、次の図のようなダイアログ ボックスが表示されます。![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ)][PUB03]

   a. **[Docker image name (Docker イメージ名)]** テキスト ボックスの Docker ホストのテキスト ボックスに一意の名前を入力します。 (名前はウィザードによって自動的に作成されますが、変更することも選択できます。)

   b. **[ホスト]** ウィンドウに、作成したすべての Docker ホストが表示されます。
      * Docker ホストを作成していない場合、ダイアログ ボックスのこのセクションは空になります。
      * Docker ホストを既に作成してある場合は、Web アプリを既存のホストにデプロイすることを選択できます。それ以外の場合、新しい Docker ホストを作成するには以下の手順に従います。

1. 新しい Docker ホストを作成するには、**[追加]** をクリックします。これにより、**[Create Docker Host (Docker ホストの作成)]** ダイアログ ボックスが表示されます。
      ![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ) ウィザード][PUB04]

   a. Docker ホストの以下のオプションを指定します。 (ほとんどのオプションはウィザードによって自動的に生成されますが、オプションを変更してカスタマイズできます。)

      * **[名前]**: Docker ホストの一意の名前です。 (これは前に指定した Docker イメージの名前と同じではありません。)

      * **[サブスクリプション]**: ホストに使用する Azure サブスクリプションを指定します。
      
      * **[地域]**: ホストが配置される地理的なリージョンを指定します。
      
      * **[OS and Size (OS とサイズ)]** タブ:
         * **[Host OS (ホスト OS)]**: ホストを格納する仮想マシンのオペレーティング システムを指定します。
         * **[サイズ]**: ホストの仮想マシン サイズを指定します。

      * **[リソース グループ]** タブ:
         * **[新しいリソース グループ]**: ホストの新しいリソース グループを作成できます。
         * **[既存のリソース グループ]**: Azure アカウントの既存のリソース グループを指定できます。

      * **[ネットワーク]** タブ:
         * **[新しい仮想ネットワーク]**: ホストの新しい仮想ネットワークを作成できます。
         * **[Existing virtual network (既存の仮想ネットワーク)]**: Azure アカウントの既存の仮想ネットワークを指定できます。

      * **[ストレージ]** タブ:
         * **[新規ストレージ アカウント]**: ホストの新しいストレージ アカウントを作成できます。
         * **[Existing storage account (既存のストレージ アカウント)]**: Azure アカウントの既存のストレージ アカウントを指定できます。

   b. 上記のオプションを指定したら、**[次へ]** をクリックします。

   c. Docker ホストの仮想マシンのログインについて次のいずれかのオプションを選びます。   ![Create Docker Host (Docker ホストの作成)][PUB05]

      * **[Import credentials from Azure Key Vault (Azure Key Vault から資格情報をインポート)]**: 以前に保存した、Azure サブスクリプションに格納されている資格情報のセットを指定できます。

      > [!NOTE]
      > 特定のアカウントまたはサービス プリンシパルで作成された Azure Key Vault は、同じサブスクリプションを共有する別のアカウントまたはサービス プリンシパルによって自動的にはアクセスできません。 別のアカウントまたはサービス プリンシパルが Key Vault を使用するためには、Azure Portal を使用して、アカウントまたはサービス プリンシパルを追加する必要があります。

      * **[New log in credentials (新しいログイン資格情報)]**: ログイン資格情報の新しいセットを作成できます。その場合は、**[VM Credentials (VM 資格情報)]** タブで以下のオプションを指定する必要があります。
         * **[ユーザー名]**: 仮想マシン ログインのユーザー名を指定します。
         * **[パスワード]** と **[確認]**: 仮想マシン ログインのパスワードを指定します。
         * **[SSH]**: Docker ホストの Secure Shell (SSH) 設定を指定します。以下のオプションから選択できます。
            * **[なし]**: 仮想マシンが SSH 接続を許可しないことを指定します。
            * **[自動生成]**: このオプションは、SSH 経由で接続するために必要な設定を自動的に作成します。
            * **[Import from directory (ディレクトリからインポート)]**: 以前に保存した SSH 設定のセットを含むディレクトリを指定できます。 具体的には、ディレクトリには次の 2 つのファイルが含まれている必要があります。
               * *id_rsa*: このファイルには、ユーザーの RSA ID が含まれています。
               * *id_rsa.pub*: このファイルには、認証に使用される RSA 公開キーが含まれています。
        
      * **[Docker Daemon Access (Docker デーモン アクセス)]** タブで、以下のオプションを指定します。![Create Docker Host (Docker ホストの作成)][PUB06]

         * **[Docker Daemon port (Docker デーモン ポート)]**: Docker ホストの固有の TCP ポートを指定します。
         * **[TLS Security (TLS セキュリティ)]**: Docker ホストのトランスポート層セキュリティ設定を指定します。以下のオプションから選択できます。
            * **[なし]**: 仮想マシンが TLS 接続を許可しないことを指定します。
            * **[自動生成]**: このオプションは、TLS 経由で接続するために必要な設定を自動的に作成します。
            * **[Import from directory (ディレクトリからインポート)]**: 以前に保存した TLS 設定のセットを含むディレクトリを指定できます。 具体的には、ディレクトリには次の 6 つのファイルが含まれている必要があります。
               * *ca.pem* と *ca key.pem*: これらのファイルには TLS 証明機関の証明書と公開キーが含まれています。
               * *cert.pem* と *key.pem*: これらのファイルには、TLS 認証に使用されるクライアント証明書と公開キーが含まれています。
               * *server.pem* と *server key.pem*: これらのファイルには、ホストのサーバー証明書と公開キーが含まれています。

   d. 上記のオプションをすべて入力したら、**[完了]** をクリックします。

1. **Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ)** ウィザードが再表示されたら、**[次へ]** をクリックします。
   ![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ) ウィザード][PUB07]

1. ウィザードの最後のページで、以下のオプションを指定します。

   * **[Docker container name (Docker コンテナー名)]**: Docker コンテナーの一意名です。

   * 次のいずれかの Docker イメージを選びます。

      * **[Predefined Docker image (定義済みの Docker イメージ)]**: Azure の既存の Docker イメージを指定できます。

      > [!NOTE]
      > このドロップダウン メニュー内の Docker イメージの一覧は、アーティファクトが自動的にデプロイされるように Azure Toolkit が修正プログラムに対して構成されている複数のイメージから構成されます。

      * **[Custom Dockerfile (カスタム Dockerfile)]**: ローカル コンピューターの、以前に保存した Dockerfile を指定できます。

      > [!NOTE]
      > これは、独自の Dockerfile をデプロイする開発者向けのより高度な機能です。 ただし、Dockerfile が正しく構築されていることを確認するのは、このオプションを使用する開発者の責任です。 Azure Toolkit ではカスタム Dockerfile 内のコンテンツを検証しないため、Dockerfile に問題がある場合はデプロイが失敗します。 さらに、Azure Toolkit はカスタム Dockerfile に Web アプリのアーティファクトが含まれることを期待し、HTTP 接続を開こうとします。開発者が別の種類のアーティファクトを発行する場合、デプロイ後に無害なエラーが表示されることがあります。

   * **[ポートの設定]**: Docker コンテナーの一意の TCP ポートのバインドを指定します。
   ![Deploy Docker Container on Azure (Azure に Docker コンテナーをデプロイ) ウィザード][PUB08]

1. 上記の手順をすべて完了したら、**[完了]** をクリックします。

Azure Toolkit により、Docker コンテナーでの Azure への Web アプリのデプロイが開始します。 

## <a name="see-also"></a>関連項目
Azure Toolkits for Java IDE の詳細については、次のリンクをご覧ください。

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

Docker の他のリソースについては、公式の [Docker の Web サイト]をご覧ください。

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

