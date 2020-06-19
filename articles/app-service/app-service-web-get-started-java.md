---
title: クイック スタート:Windows で Java アプリを作成する
description: Azure App Service on Windows に、初めての Java の Hello World を数分でデプロイします。 Java アプリのデプロイには、Azure Web App Plugin for Maven が便利です。
keywords: Azure, App Service, Web アプリ, Windows, Java, Maven, クイック スタート
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: ed46859184dd17141892373943ac43e407b21eb6
ms.sourcegitcommit: 1383842d1ea4044e1e90bd3ca8a7dc9f1b439a54
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2020
ms.locfileid: "84817667"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>クイック スタート:Azure App Service on Windows で Java アプリを作成する

> [!NOTE]
> この記事では、Windows 上の App Service にアプリをデプロイします。 _Linux_ 上の App Service にデプロイするには、[Linux での Java Web アプリの作成](./containers/quickstart-java.md)に関するページを参照してください。
>

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。  このクイックスタートでは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) と共に使用して Java Web アーカイブ (WAR) ファイルをデプロイする方法を示します。

> [!NOTE]
> IntelliJ や Eclipse のような一般的な IDE を使っても同じことができます。 [Azure Toolkit for IntelliJ のクイック スタート](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app)または [Azure Toolkit for Eclipse のクイック スタート](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)で類似するドキュメントを確認してください。
>
![Azure App Service で実行されているサンプル アプリ](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java アプリを作成する

Cloud Shell プロンプトで次の Maven コマンドを実行して、`helloworld` という名前の新しいアプリを作成します。

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp -Dversion=1.0-SNAPSHOT
```

次に、作業ディレクトリをプロジェクト フォルダーに変更します。

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Maven プラグインを構成する

Azure App Service へのデプロイ プロセスでは、Azure CLI から Azure 資格情報を自動的に取得できます。 Azure CLI がインストールされていない場合、Maven プラグインは Oauth またはデバイス ログインでサインインします。 必要に応じて、[Maven プラグインによる認証](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)の詳細を確認してください。

コマンド プロンプトで次の Maven コマンドを実行してデプロイを構成します。最初の手順では **windows** OS を表す **2** を選択します。次に **Confirm (Y/N)** プロンプトが表示されるまで **Enter** キーを押して既定の構成値を使用し、**y** キーを押して構成を完了します。 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

サンプル プロセスは次のようになります。

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```

> [!NOTE]
> この記事では、WAR ファイル内にパッケージ化された Java アプリのみを操作します。 このプラグインは、JAR Web アプリケーションもサポートしています。[Java SE JAR ファイルを App Service on Linux にデプロイする方法](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関するページにアクセスしてお試しください。

`pom.xml` を開いて、更新された構成を確認します。

```bash
code pom.xml
```

必要に応じて、pom ファイルで App Service の構成を直接変更できます。その一般的なものを次に示します。

 プロパティ | 必須 | 説明 | Version
---|---|---|---
`<schemaVersion>` | false | 構成スキーマのバージョンを指定します。 サポートされる値は `v1`、`v2` です。 | 1.5.2
`<resourceGroup>` | true | Web アプリの Azure リソース グループ。 | 0.1.0 以降
`<appName>` | true | Web アプリの名前。 | 0.1.0 以降
`<region>` | true | Web アプリがホストされるリージョンを指定します。既定値は **westeurope** です。 すべての有効なリージョンについては、「[サポートされているリージョン](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)」を参照してください。 | 0.1.0 以降
`<pricingTier>` | false | Web アプリの価格レベル。 既定値は **P1V2** です。| 0.1.0 以降
`<runtime>` | true | ランタイム環境の構成の詳細については、[こちら](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)を参照してください。 | 0.1.0 以降
`<deployment>` | true | デプロイ構成の詳細については、[こちら](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)を参照してください。 | 0.1.0 以降

> [!div class="nextstepaction"]
> [問題が発生しました](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

Azure App Service へのデプロイ プロセスでは、Azure CLI からアカウントの資格情報を使います。 続行する前に、[Azure CLI にサインイン](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)します。

```azurecli
az login
```
次のコマンドを使用して、Java アプリを Azure にデプロイできます。

```bash
mvn package azure-webapp:deploy
```

デプロイが完了したら、Web ブラウザーで次の URL を使用して、デプロイされたアプリケーションを参照します (たとえば、`http://<webapp>.azurewebsites.net/`)。

![Azure App Service で実行されているサンプル アプリ](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**お疲れさまでした。** App Service on Windows に初めての Java アプリをデプロイしました。

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Java を使用して Azure SQL データベースに接続する](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Java を使用して Azure DB for MySQL に接続する](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Java を使用して Azure DB for PostgreSQL に接続する](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Java 開発者向けの Azure リソース](/java/azure/)

> [!div class="nextstepaction"]
> [カスタム ドメインをマップする](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Azure 用の Maven プラグインの詳細はこちら](https://github.com/microsoft/azure-maven-plugins)
