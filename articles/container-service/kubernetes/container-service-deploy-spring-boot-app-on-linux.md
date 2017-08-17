---
title: "Azure Container Service で Spring Boot Web アプリを Linux にデプロイする | Microsoft Docs"
description: "このチュートリアルでは、Microsoft Azure の Linux Web アプリとして Spring Boot アプリケーションをデプロイする方法について説明します。"
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
ms.author: asirveda;robmcm
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: 6035b0e2fe27b46c441012144caf545eb17e1825
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-spring-boot-application-on-linux-in-azure-container-service"></a>Azure Container Service で Spring Boot アプリケーションを Linux にデプロイする

[Spring Framework] は Java 開発者のエンタープライズ レベルのアプリケーション作成を支援するオープンソース ソリューションです。 Java でビルドされる特に知られたプロジェクトの 1 つが [Spring Boot] です。これによって、スタンドアロンの Java アプリケーションの作成方法が簡略化されます。

[Docker] は、開発者が、コンテナーで実行されるアプリケーションのデプロイ、スケーリング、管理を自動化することを支援するオープン ソース ソリューションです。

このチュートリアルでは、Docker を使用して Spring Boot アプリケーションを開発し、[Azure Container Service] の Linux ホストにデプロイする方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要になります。

* Azure サブスクリプション。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典]を有効にするか、[Azure の無料アカウント]にサインアップしてください。
* [Azure コマンド ライン インターフェイス (CLI)]。
* 最新の [Java Developer Kit (JDK)]。
* Apache の [Maven] 構築ツール (バージョン 3)。
* [Git] クライアント。
* [Docker] クライアント。

> [!NOTE]
>
> このチュートリアルには仮想化要件があるため、この記事の手順を仮想マシンで実行することはできません。 物理コンピューターを仮想化機能を有効にして使用する必要があります。
>

## <a name="create-the-spring-boot-on-the-docker-getting-started-web-app"></a>Docker で Spring Boot の使用を開始する Web アプリを作成する

次の手順に従って、単純な Spring Boot Web アプリケーションを作成し、ローカルにテストできます。

1. コマンド プロンプトを開きます。 アプリケーションを保持するためのローカル ディレクトリを作成し、そのディレクトリに移動します。次に例を示します。
   ```
   md C:\SpringBoot
   cd C:\SpringBoot
   ```
   -- または --
   ```
   md /users/robert/SpringBoot
   cd /users/robert/SpringBoot
   ```

2. [Docker での Spring Boot の使用開始]のサンプル プロジェクトを今作成したディレクトリに複製します。次に例を示します。
   ```
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

3. 完成したプロジェクトにディレクトリを変更します。次に例を示します。
   ```
   cd gs-spring-boot-docker/complete
   ```

4. **省略可能な手順**: 埋め込みの Tomcat サーバーを既定のポート 8080 ではなくポート 80 で実行する場合 (Spring Boot プロジェクトをローカルでテストする場合など)、次の手順を使用してポートを構成できます。

   a. リソース ディレクトリにディレクトリを変更します。次に例を示します。
   ```
   cd src/main/resources
   ```

   b. テキスト エディターで **application.yml** ファイルを開きます。

   c. サーバーがポート 80 で実行されるように、**server:** の設定を変更します。次に例を示します。
   ```
   server:
      port: 80
   ```

   d. **application.yml** ファイルを保存して閉じます。

   e. 完成したプロジェクトのルート フォルダーにディレクトリを変更します。次に例を示します。
   ```
   cd ../../..
   ```

5. Maven を使用して JAR ファイルをビルドします。次に例を示します。
   ```
   mvn package
   ```

6. Web アプリが作成されたら、JAR ファイルがある `target` ディレクトリに移動し、Web アプリを起動します。次に例を示します。
   ```
   cd target
   java -jar gs-spring-boot-docker-0.1.0.jar
   ```

7. Web アプリのテストは、Web ブラウザーでアプリをローカルに参照することで実行します。 たとえば、curl が使用可能であり、Tomcat サーバーをポート 80 で実行するように構成している場合は、次のコマンドを使用できます。
   ```
   curl http://localhost
   ```

8. 次のメッセージが表示されるはずです。**Hello Docker World!**

   ![サンプル アプリをローカルに参照する][SB01]

## <a name="create-an-azure-container-registry-to-use-as-a-private-docker-registry"></a>Azure コンテナー レジストリをプライベート Docker レジストリとして使用するために作成する

Azure ポータルを使用して Azure コンテナー レジストリを作成する手順は次のとおりです。

> [!NOTE]
> Azure ポータルではなく Azure CLI を使用する場合は、「[Azure CLI 2.0 を使用したプライベート Docker コンテナー レジストリの作成][1]」の手順に従ってください。

1. [Azure ポータル]にサインインします。

    Azure ポータルで自分のアカウントにサインインした後、「[Azure ポータルを使用したプライベート Docker コンテナー レジストリの作成]」の手順に従います。 以下は、その記事に記載されている手順の要約です。

2. **[+ 新規]** メニュー アイコンを選択します。

3. **[コンテナー]** を選択し、**[Azure Container Registry]** を選択します。

   ![新しい Azure コンテナー レジストリを作成する][AR01]

4. Azure コンテナー レジストリ テンプレートの情報ページが表示されたら、**[作成]** をクリックします。

   ![新しい Azure コンテナー レジストリを作成する][AR02]

5. **[コンテナー レジストリの作成]** ブレードが表示されます。

   a. **[レジストリ名]** と **[リソース グループ]** を入力します。  
   
   b. **[管理者ユーザー]** で **[有効]** を選択します。
   
   c. **[作成]**を選択します。

   ![Azure コンテナー レジストリの設定を構成する][AR03]

6. コンテナー レジストリが作成されたら、Azure ポータルに移動します。 **[アクセス キー]** を選択します。 次の手順で使用するため、ユーザー名とパスワードをメモします。

   ![Azure コンテナー レジストリのアクセス キー][AR04]

## <a name="configure-maven-to-use-your-azure-container-registry-access-keys"></a>Azure Container Registry のアクセス キーを使用するために Maven を構成する

1. Maven インストールの構成ディレクトリに移動します。 **settings.xml** ファイルをテキスト エディターで開きます。

2. このチュートリアルの前のセクションから、Azure Container Registry のアクセス設定を **settings.xml** ファイルの `<servers>` コレクションに追加します。次に例を示します。

   ```xml
   <servers>
      <server>
         <id>wingtiptoysregistry</id>
         <username>wingtiptoysregistry</username>
         <password>AbCdEfGhIjKlMnOpQrStUvWxYz</password>
      </server>
   </servers>
   ```

3. Spring Boot アプリケーションの完了プロジェクト ディレクトリ ("**C:\SpringBoot\gs-spring-boot-docker\complete**" や "**/users/robert/SpringBoot/gs-spring-boot-docker/complete**" など) に移動します。 次に、**pom.xml** ファイルをテキスト エディターで開きます。

1. **pom.xml** ファイルの `<properties>` コレクションを更新します。 このチュートリアルの前のセクションにあった Azure コンテナー レジストリのログイン サーバーの値を使用します。次に例を示します。

   ```xml
   <properties>
      <docker.image.prefix>wingtiptoysregistry.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
   </properties>
   ```

1. `<plugin>` にこのチュートリアルの前のセクションにあった Azure コンテナー レジストリのログイン サーバー アドレスとレジストリ名が含まれるように、**pom.xml** ファイル内の `<plugins>` コレクションを更新します。 次に例を示します。

   ```xml
   <plugin>
      <groupId>com.spotify</groupId>
      <artifactId>docker-maven-plugin</artifactId>
      <version>0.4.11</version>
      <configuration>
         <imageName>${docker.image.prefix}/${project.artifactId}</imageName>
         <dockerDirectory>src/main/docker</dockerDirectory>
         <resources>
            <resource>
               <targetPath>/</targetPath>
               <directory>${project.build.directory}</directory>
               <include>${project.build.finalName}.jar</include>
            </resource>
         </resources>
         <serverId>wingtiptoysregistry</serverId>
         <registryUrl>https://wingtiptoysregistry.azurecr.io</registryUrl>
      </configuration>
   </plugin>
   ```

1. Spring Boot アプリケーションの完了プロジェクト ディレクトリに移動します。 次のコマンドを実行してアプリケーションをリビルドし、コンテナーを Azure コンテナー レジストリにプッシュします。

   ```
   mvn package docker:build -DpushImage
   ```

> [!NOTE]
>
> Docker コンテナーを Azure にプッシュすると、Docker コンテナーが正常に作成されている場合でも、次のいずれかのようなエラー メッセージが表示されることがあります。
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: no basic auth credentials`
>
> * `[ERROR] Failed to execute goal com.spotify:docker-maven-plugin:0.4.11:build (default-cli) on project gs-spring-boot-docker: Exception caught: Incomplete Docker registry authorization credentials. Please provide all of username, password, and email or none.`
>
> こレが発生した場合は、Docker コマンド ラインから Azure にサインインする必要があります。次に例を示します。
>
> `docker login -u wingtiptoysregistry -p "AbCdEfGhIjKlMnOpQrStUvWxYz" wingtiptoysregistry.azurecr.io`
>
> これで、次のようにしてコマンド ラインからコンテナーをプッシュできるようになります。
>
> `docker push wingtiptoysregistry.azurecr.io/gs-spring-boot-docker`
>

## <a name="create-a-web-app-on-linux-on-azure-app-service-by-using-your-container-image"></a>コンテナー イメージを使用して Azure App Service の Linux 上に Web アプリを作成する

1. [Azure ポータル]にサインインします。

1. **[+ 新規]** メニュー アイコンをクリックし、**[Web + モバイル]** をクリックします。

2.  **[Web App on Linux]** をクリックします。

   ![Azure ポータルで Web アプリを新しく作成する][LX01]

3. **[Web App on Linux]** ブレードが表示されたら、次の手順に従います。

   a. **[アプリ名]** に一意の名前 (例: "*wingtiptoyslinux*") を入力します。

   b. **[サブスクリプション]** ボックスの一覧で、サブスクリプションを選択します。

   c. 新しいリソース グループを作成するには、既存の**[リソース グループ]** を選択するか、名前を指定します。

   d. **[コンテナーの構成]** をクリックし、次の情報を入力します。

      * **[プライベート レジストリ]** を選択します。

      * **[イメージとオプションのタグ]**: 先に設定したコンテナー名 ("*wingtiptoysregistry.azurecr.io/gs-spring-boot-docker:latest*" など) を指定します。

      * **[サーバーの URL]**: 先に設定したレジストリの URL ("*https://wingtiptoysregistry.azurecr.io*" など) を指定します。

      * **[ログイン ユーザー名]** と **[パスワード]**: 前の手順で使用した**アクセス キー**から、サインイン資格情報を指定します。

   e. すべての情報を入力したら、**[OK]** を選択します。

   ![Web アプリの設定を構成する][LX02]

1. **[作成]**をクリックします。

> [!NOTE]
> Azure は、80 または 8080 の標準のポートで実行されている埋め込みの Tomcat サーバーにインターネットの要求を自動的にマップします。 ただし、埋め込みの Tomcat サーバーをカスタム ポートで実行するように構成している場合は、埋め込みの Tomcat サーバーのポートを定義する環境変数を Web アプリに追加する必要があります。 そのためには、次の手順を実行してください。
>
>1. [Azure ポータル]にサインインします。

>2. **[App Services]** のアイコンをクリックします  (下の図の項目番号 1 を参照してください)。

>3. 一覧から Web アプリを選択します  (下の図の項目番号 2 を参照してください)。

>4. **[アプリケーションの設定]**をクリックします (下の図の項目番号 3 を参照してください)。

>5. **[アプリの設定]** セクションに、**PORT** という名前の新しい環境変数を追加し、 値としてカスタム ポート番号を入力します  (下の図の項目番号 4 を参照してください)。

>6. **[保存]** を選択します  (下の図の項目番号 5 を参照してください)。

> ![Azure ポータルでのカスタム ポート番号の保存][LX03]
>

## <a name="next-steps"></a>次のステップ

Azure での Spring Boot アプリケーションの使用の詳細については、次の記事を参照してください。

- [Spring Boot アプリケーションを Azure App Service にデプロイする][2]


- [Spring Boot アプリケーションを Azure Container Service の Kubernetes クラスターで実行する](container-service-deploy-spring-boot-app-on-kubernetes.md)

## <a name="additional-resources"></a>その他のリソース

Java での Azure の使用の詳細については、 [Azure Java デベロッパー センター]と [Java Tools for Visual Studio Team Services] を参照してください。

Docker での Spring Boot の使用開始のサンプル プロジェクトの詳細については、[Docker での Spring Boot の使用開始]に関するページを参照してください。

独自の Spring Boot アプリケーションの使用開始に関するヘルプについては、「[Spring Initializr](https://start.spring.io/)」を参照してください。

単純な Spring Boot アプリケーションの作成の詳細については、「[Spring Initializr](https://start.spring.io/)」を参照してください。

Azure でカスタム Docker イメージを使用する方法に関するその他の例については、「[Azure Web App on Linux 向けのカスタム Docker イメージを使用する]」を参照してください。

<!-- URL List -->

[Azure コマンド ライン インターフェイス (CLI)]: /cli/azure/overview
[Azure Container Service]: https://azure.microsoft.com/services/container-service/
[Azure Java デベロッパー センター]: https://azure.microsoft.com/develop/java/
[Azure ポータル]: https://portal.azure.com/
[Azure ポータルを使用したプライベート Docker コンテナー レジストリの作成]: /azure/container-registry/container-registry-get-started-portal
[Azure Web App on Linux 向けのカスタム Docker イメージを使用する]: /azure/app-service-web/app-service-linux-using-custom-docker-image
[Docker]: https://www.docker.com/
[Azure の無料アカウント]: https://azure.microsoft.com/pricing/free-trial/
[Git]: https://github.com/
[Java Developer Kit (JDK)]: http://www.oracle.com/technetwork/java/javase/downloads/
[Java Tools for Visual Studio Team Services]: https://java.visualstudio.com/
[Maven]: http://maven.apache.org/
[MSDN サブスクライバーの特典]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Docker での Spring Boot の使用開始]: https://github.com/spring-guides/gs-spring-boot-docker
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[SB01]: ./media/container-service-deploy-spring-boot-app-on-linux/SB01.png
[SB02]: ./media/container-service-deploy-spring-boot-app-on-linux/SB02.png

[AR01]: ./media/container-service-deploy-spring-boot-app-on-linux/AR01.png
[AR02]: ./media/container-service-deploy-spring-boot-app-on-linux/AR02.png
[AR03]: ./media/container-service-deploy-spring-boot-app-on-linux/AR03.png
[AR04]: ./media/container-service-deploy-spring-boot-app-on-linux/AR04.png

[LX01]: ./media/container-service-deploy-spring-boot-app-on-linux/LX01.png
[LX02]: ./media/container-service-deploy-spring-boot-app-on-linux/LX02.png
[LX03]: ./media/container-service-deploy-spring-boot-app-on-linux/LX03.png

<!--Reference links in article-->
[1]: https://docs.microsoft.com/azure/container-registry/container-registry-get-started-azure-cli/
[2]: https://docs.microsoft.com/azure/app-service/app-service-deploy-spring-boot-web-app-on-azure/


---

