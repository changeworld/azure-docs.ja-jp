---
title: Azure App Service on Linux で Java Web アプリを作成するためのクイック スタート
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
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: 92dde19e42c1adb6d83e1708106f844f228e8989
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48239352"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>クイック スタート: App Service on Linux で Java Web アプリを作成する

[App Service on Linux](app-service-linux-intro.md) は、Linux オペレーティング システムを使用する、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供します。 このクイック スタートでは、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を [Maven Plugin for Azure Web Apps (プレビュー)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) と共に使用して Java Web アプリの Web アーカイブ (WAR) ファイルをデプロイする方法を示します。

![Azure で実行されるサンプル アプリ](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java アプリを作成する

Cloud Shell プロンプトで次の Maven コマンドを実行して、`helloworld` という名前の新しい Web アプリを作成します。

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
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> この記事では、WAR ファイル内にパッケージ化された Java アプリのみを操作します。 このプラグインは JAR Web アプリケーションもサポートしています。 これらのアプリケーションには、次の代替のプラグイン定義を使用します。 この構成は、Maven によって構築された JAR をローカル ファイル システム上の `${project.build.directory}/${project.build.finalName}.jar` にデプロイします。
>
>```xml
><plugin>
>            <groupId>com.microsoft.azure</groupId>
>            <artifactId>azure-webapp-maven-plugin</artifactId>
>            <version>1.4.0</version>
>            <configuration>
>                <deploymentType>jar</deploymentType>
>
>           <!-- Web App information -->
>            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
>            <appName>${WEBAPP_NAME}</appName>
>            <region>${REGION}</region>  
>
>                <!-- Java Runtime Stack for Web App on Linux-->
>                <linuxRuntime>jre8</linuxRuntime>
>            </configuration>
>         </plugin>
>```    


プラグイン構成で、次のプレースホルダーを更新します。

| プレースホルダー | 説明 |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | Web アプリの作成先となる新しいリソース グループの名前。 アプリのすべてのリソースを 1 つのグループ内に配置することで、それらを一緒に管理できます。 たとえば、リソース グループを削除すれば、そのアプリに関連付けられているすべてのリソースが削除されます。 この値を一意の新しいリソース グループ名 (たとえば、*TestResources*) で更新します。 このリソース グループ名を使用して、後のセクションですべての Azure リソースをクリーンアップします。 |
| `WEBAPP_NAME` | Azure にデプロイされると、このアプリ名は Web アプリのホスト名の一部になります (WEBAPP_NAME.azurewebsites.net)。 この値を、Java アプリをホストする新しい Azure Web アプリの一意の名前 (たとえば、*contoso*) で更新します。 |
| `REGION` | Web アプリがホストされている Azure リージョン (たとえば、`westus2`)。 リージョンの一覧は、`az account list-locations` コマンドを使用して Cloud Shell または CLI から取得できます。 |

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

このクイック スタートでは、Maven を使用して Java Web アプリを作成し、[Maven Plugin for Azure Web Apps (プレビュー)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) を構成した後、Web アーカイブにパッケージ化された Java Web アプリを App Service on Linux にデプロイしました。 Azure で Java を使用する方法の詳細については、以下のリンクを参照してください。

> [!div class="nextstepaction"]
> [Java 開発者向けの Azure](https://docs.microsoft.com/java/azure/)

