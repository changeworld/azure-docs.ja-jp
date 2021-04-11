---
title: Azure Spring Cloud でステージング環境を設定する | Microsoft Docs
description: Azure Spring Cloud でブルーグリーン デプロイを使用する方法について説明します
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 9b3a5659e91ca90d31500b10526e3e2179d4e7da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105046115"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Azure Spring Cloud でステージング環境を設定する

**この記事の適用対象:** ✔️ Java

この記事では、Azure Spring Cloud でブルーグリーン デプロイ パターンを使用して、ステージング環境のデプロイを設定する方法について説明します。 ブルーグリーン デプロイとは、新しい (グリーン) バージョンのデプロイ中に、既存 (ブルー) のバージョンを実行状態のまま保持する、Azure DevOps の継続的デリバリーのパターンです。 この記事では、運用環境デプロイに変更を加えることなく、ステージング環境のデプロイを運用環境に移す方法について説明します。

## <a name="prerequisites"></a>前提条件

* Standard 価格レベルの Azure Spring Cloud インスタンス
* Azure CLI 用の [Azure Spring Cloud 拡張機能](/cli/azure/azure-cli-extensions-overview)

この記事では、Spring Initializr からビルドされたアプリケーションを使用します。 この例に別のアプリケーションを使用したい場合は、ステージング環境のデプロイを運用環境と区別するために、そのアプリケーションの公開部分に簡単な変更を加える必要があります。

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) は無料のインタラクティブ シェルです。この記事の手順は、これを使用して実行することができます。  最新バージョンの Git、JDK、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにサインインしている場合は、Azure Cloud Shell インスタンスを開始します。 詳細については、[Cloud Shell の概要](../cloud-shell/overview.md)に関するページをご覧ください。

Azure Spring Cloud でブルーグリーン デプロイを設定するには、次のセクションの手順に従います。

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>アプリとデプロイを準備する
アプリケーションをビルドするには、こちらの手順に従います。

1. [こちらの構成](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client)で Spring Initializr を使用して、サンプル アプリのコードを生成します。

2. コードをダウンロードします。
3. `\src\main\java\com\example\hellospring\` フォルダーに、次の HelloController.java ソース ファイルを追加します。

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud!"; 
     } 

   } 
   ```
4. .jar ファイルをビルドします。

   ```azurecli
   mvn clean packge -DskipTests
   ```
5. Azure Spring Cloud インスタンスでアプリを作成します。

   ```azurecli
   az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
   ```
6. アプリを Azure Spring Cloud にデプロイします。

   ```azurecli
   az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
   ```
7. ステージング環境のデプロイのコードを変更します。

   ```java
   package com.example.hellospring; 
   import org.springframework.web.bind.annotation.RestController; 
   import org.springframework.web.bind.annotation.RequestMapping; 

   @RestController 

   public class HelloController { 

   @RequestMapping("/") 

     public String index() { 

         return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
     } 

   } 
   ```
8. .jar ファイルをリビルドします。

   ```azurecli
   mvn clean packge -DskipTests
   ```
9. グリーン デプロイを作成します。 

   ```azurecli
   az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
   ```

## <a name="view-apps-and-deployments"></a>アプリとデプロイを表示する

次の手順を使用して、デプロイされたアプリを表示します。

1. Azure portal で Azure Spring Cloud インスタンスに移動します。

1. 左側のペインから **[アプリ]** ペインを開いて、お使いのサービス インスタンスのアプリを表示します。

   ![[アプリ] ペインを開いたスクリーンショット。](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. アプリを選択して詳細を表示できます。

   ![アプリの詳細のスクリーンショット。](media/spring-cloud-blue-green-staging/app-overview.png)

1. **[デプロイ]** を開いて、アプリのすべてのデプロイを表示します。 グリッドには、運用環境とステージング環境の両方のデプロイが表示されます。

   ![アプリのデプロイの一覧を示すスクリーンショット。](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. URL を選択して、現在デプロイされているアプリケーションを開きます。
    
   ![デプロイされたアプリケーションの URL を示すスクリーンショット。](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. **[状態]** 列で **[運用]** を選択して、既定のアプリを表示します。
    
   ![既定のアプリの URL を示すスクリーンショット。](media/spring-cloud-blue-green-staging/running-default-app.png)

1. **[状態]** 列で **[ステージング]** を選択して、ステージング アプリを表示します。
    
   ![ステージング アプリの URL を示すスクリーンショット。](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * テスト エンドポイントの末尾がスラッシュ (/) であり、CSS ファイルが正しく読み込まれていることを確認します。  
> * ページを表示するためにブラウザーでログイン資格情報の入力を求められる場合は、[URL デコード](https://www.urldecoder.org/)を使用して自分のテスト エンドポイントをデコードします。 URL デコードでは、*https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green* の形式の URL が返されます。 この形式を使用してエンドポイントにアクセスします。

>[!NOTE]    
> 構成サーバーの設定はステージング環境と運用環境の両方に適用されます。 たとえば、構成サーバー内のアプリ ゲートウェイのコンテキスト パス (*server.servlet.context-path*) を *somepath* として設定すると、グリーン デプロイへのパスは *https://\<username>:\<password>@\<cluster-name>.test.azureapps.io/gateway/green/somepath/...* に変更されます。
 
このポイントで自分の公開アプリ ゲートウェイにアクセスすると、新しい変更が反映されていない古いページが表示されます。

## <a name="set-the-green-deployment-as-the-production-environment"></a>実稼働環境としてグリーン デプロイを設定する

1. ステージング環境で変更を確認したら、それを運用環境にプッシュすることができます。 **[アプリ]**  >  **[デプロイ]** ページで、現在 **[運用]** にあるアプリケーションを選択します。

1. グリーン デプロイの **[登録状態]** の後にある省略記号を選択し、 **[運用環境として設定]** を選択します。 

   ![ステージング環境のビルドを運用環境に設定するための選択を示すスクリーンショット。](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. アプリの URL に変更が反映されていることを確認します。

   ![現在、アプリの URL が運用環境であることを示すスクリーンショット。](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> グリーン デプロイを運用環境に設定したら、前のデプロイがステージング環境のデプロイになります。

## <a name="modify-the-staging-deployment"></a>ステージング環境のデプロイを変更する

変更に満足していない場合は、Azure CLI を使用することで、アプリケーション コードを変更し、新しい .jar パッケージをビルドして、それをグリーン デプロイにアップロードできます。

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>ステージング環境のデプロイを削除する

Azure portal から自分のステージング環境のデプロイを削除するには、ステージング環境のデプロイのページに移動して **[削除]** ボタンを選択します。

または、次のコマンドを実行して Azure CLI でステージング環境のデプロイを削除します。

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>次の手順

* [Azure Spring Cloud の CI/CD](./spring-cloud-howto-cicd.md?pivots=programming-language-java)