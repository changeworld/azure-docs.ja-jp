---
title: Linux での Java Web アプリの作成 - Azure App Service
description: このクイック スタートでは、Azure App Service on Linux で、初めての Java の Hello World を数分でデプロイします。
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 12/10/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 0c72318f6c80563d138d9c885ea5984a22c5c7fa
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/20/2018
ms.locfileid: "53653902"
---
# <a name="quickstart-create-a-java-app-in-app-service-on-linux"></a>クイック スタート:App Service on Linux で Java アプリを作成する

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を [Maven Plugin for Azure Web Apps (プレビュー)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) と共に使用して Java Web アーカイブ (WAR) ファイルをデプロイする方法を示します。

![Azure で実行されるサンプル アプリ](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java アプリを作成する

Cloud Shell プロンプトで次の Maven コマンドを実行して、`helloworld` という名前の新しいアプリを作成します。

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Maven プラグインを構成する

Maven からデプロイするには、Cloud Shell でコード エディターを使用して `helloworld` ディレクトリ内のプロジェクト `pom.xml` ファイルを開きます。 

```bash
code pom.xml
```

次に、`pom.xml` ファイルの `<build>` 要素内に次のプラグイン定義を追加します。

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
      
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.4.0</version>
        <configuration>
   
            <!-- App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> この記事では、WAR ファイル内にパッケージ化された Java アプリのみを操作します。 このプラグインは、JAR Web アプリケーションもサポートしています。[Java SE JAR ファイルを App Service on Linux にデプロイする方法](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)に関するページにアクセスしてお試しください。


プラグイン構成で、次のプレースホルダーを更新します。

| プレースホルダー | 説明 |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | その中にアプリを作成する新しいリソース グループの名前。 アプリのすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すれば、そのアプリに関連付けられているすべてのリソースが削除されます。 この値を一意の新しいリソース グループ名 (たとえば、*TestResources*) で更新します。 このリソース グループ名を使用して、後のセクションですべての Azure リソースをクリーンアップします。 |
| `WEBAPP_NAME` | Azure にデプロイされると、このアプリ名はアプリのホスト名の一部になります (WEBAPP_NAME.azurewebsites.net)。 この値を、Java アプリをホストする新しい App Service アプリの一意の名前 (たとえば、*contoso*) で更新します。 |
| `REGION` | アプリがホストされている Azure リージョン (たとえば、`westus2`)。 リージョンの一覧は、`az account list-locations` コマンドを使用して Cloud Shell または CLI から取得できます。 |

## <a name="deploy-the-app"></a>アプリケーションのデプロイ

次のコマンドを使用して、Java アプリを Azure にデプロイします。

```bash
mvn package azure-webapp:deploy
```

デプロイが完了したら、Web ブラウザーで次の URL を使用して、デプロイされたアプリケーションを参照します (たとえば、`http://<webapp>.azurewebsites.net/helloworld`)。 

![Azure で実行されるサンプル アプリ](media/quickstart-java/java-hello-world-in-browser-curl.png)

**お疲れさまでした。** App Service on Linux に初めての Java アプリをデプロイしました。


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>次の手順

このクイック スタートでは、Maven を使用して Java アプリを作成し、[Maven Plugin for Azure Web Apps](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) を構成した後、Web アーカイブにパッケージ化された Java アプリを App Service on Linux にデプロイしました。 App Serivce on Linux での Java アプリケーションのホストについて詳しくは、次のチュートリアルとハウツー記事を参照してください。

- [チュートリアル: PostgreSQL を使って Java Enterprise アプリをデプロイする](tutorial-java-enterprise-postgresql-app.md)
- [Tomcat データ ソースを構成する](app-service-linux-java.md#connecting-to-data-sources)
- [Jenkins での CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)
- [アプリケーション パフォーマンス監視ツールのセットアップ](how-to-java-apm-monitoring.md)

