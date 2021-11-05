---
title: 'チュートリアル: シングル インスタンスの Java Spring アプリでプッシュ更新による動的な構成を使用する'
titleSuffix: Azure App Configuration
description: このチュートリアルでは、プッシュ更新を使用して、Java Spring アプリの構成データを動的に更新する方法について説明します
services: azure-app-configuration
documentationcenter: ''
author: mrm9084
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: java
ms.topic: tutorial
ms.date: 04/05/2021
ms.author: mametcal
ms.openlocfilehash: 340638bd5fba981525ac3ff148ed4076f093e178
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131020922"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-java-spring-app"></a>チュートリアル: Java Spring アプリでプッシュ更新による動的な構成を使用する

App Configuration Java Spring クライアント ライブラリでは、アプリケーションを再起動させることなく必要に応じて構成を更新することがサポートされます。 次の 2 つの方法のどちらかまたは両方を使用して、App Configuration 内の変更を検出するようにアプリケーションを構成することができます。

- ポーリング モデル: ポーリングを使用して構成の変更を検出する既定の動作です。 キャッシュされた設定値の有効期限が切れた後、次に `AppConfigurationRefresh` の `refreshConfigurations` を呼び出すと、構成が変更されているかどうかをチェックするための要求がサーバーに送信され、必要に応じて、更新された構成がプルされます。

- プッシュ モデル: [App Configuration のイベント](./concept-app-configuration-event.md)を使用して構成の変更を検出します。 キー値の変更イベントを [Webhook](../event-grid/handler-event-hubs.md) を使用して Event Grid で送信するように App Configuration を設定すると、アプリケーションでは、それらのイベントを使用して、構成を最新の状態に保つために必要な要求の総数を最適化できます。

このチュートリアルでは、自分が作成するコードに、プッシュ更新を使用して構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されているアプリに基づいています。 先に進む前に、まず「[App Configuration を使用して Java Spring アプリを作成する](./quickstart-java-spring-app.md)」を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * App Configuration から Webhook に構成の変更イベントを送信するようにサブスクリプションを設定する
> * Spring Boot アプリケーションを App Service にデプロイする
> * App Configuration への変更に合わせて構成を更新するように Java Spring アプリを設定する。
> * 最新の構成をアプリケーションに取り込む。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- バージョン 8 を含む、サポートされている [Java Development Kit (JDK)](/java/azure/jdk)。
- [Apache Maven](https://maven.apache.org/download.cgi) バージョン 3.0 以降。
- 既存の Azure App Configuration ストア。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="setup-push-refresh"></a>プッシュ更新をセットアップする

1. *pom.xml* ファイルを開いて、次の依存関係でファイルを更新します。

   ```xml
           <dependency>
               <groupId>com.azure.spring</groupId>
               <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
               <version>2.0.0</version>
           </dependency>
   
           <!-- Adds the Ability to Push Refresh -->
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-actuator</artifactId>
           </dependency>
   ```

1. [Maven App Service のデプロイ](../app-service/quickstart-java.md?tabs=javase)をセットアップして、アプリケーションを Maven 経由で Azure App Service にデプロイできるようにします。

   ```console
   mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
   ```

1. bootstrap.properties を開いて、Azure App Configuration のプッシュ更新と Azure Service Bus を構成します

   ```properties
   # Azure App Configuration Properties
   spring.cloud.azure.appconfiguration.stores[0].connection-string= ${AppConfigurationConnectionString}
   spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled= true
   spring.cloud.azure.appconfiguration.stores[0].monitoring.cacheExpiration= 30d
   spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key= sentinel
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.name= myToken
   spring.cloud.azure.appconfiguration.stores[0].monitoring.push-notification.primary-token.secret= myTokenSecret
   
   management.endpoints.web.exposure.include= "appconfiguration-refresh"
   ```

スロットリングが生じる可能性を減らすために、キャッシュされている値がダーティとしてマークされる前に、ランダムな待ち時間が追加されます。 キャッシュされている値がダーティとしてマークされるまでの最大待ち時間の既定値は 30 秒です。

> [!NOTE]
> プライマリ トークン名は、キーとして App Configuration に格納する必要があります。その後、セキュリティを強化するために、プライマリ トークン シークレットを App Configuration キー コンテナー参照として格納する必要があります。

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

Event Grid Webhook は、作成時に検証する必要があります。 検証するには、この[ガイド](../event-grid/webhook-event-delivery.md)に従います。または、既に構成されている Azure App Configuration Spring Web ライブラリを使用してアプリケーションを起動すると、アプリケーションが登録されます。 イベント サブスクリプションを使用するには、次の 2 つのセクションの手順に従います。

1. 環境変数に App Configuration インスタンスの接続文字列を設定します。

    #### <a name="windows-command-prompt"></a>[Windows コマンド プロンプト](#tab/cmd)

    ```cmd
    setx AppConfigurationConnectionString <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```PowerShell
    $Env:AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

    #### <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export AppConfigurationConnectionString = <connection-string-of-your-app-configuration-store>
    ```

1. 次のコマンドを実行して、コンソール アプリをビルドします。

   ```shell
    mvn package
   ```

1. ビルドが正常に完了したら、次のコマンドを実行して、アプリをローカルで実行します。

    ```shell
    mvn spring-boot:deploy
    ```

## <a name="set-up-an-event-subscription"></a>イベント サブスクリプションをセットアップする

1. Azure portal で App Configuration リソースを開き、[`Events`] ペインの [`+ Event Subscription`] をクリックします。

    :::image type="content" source="./media/events-pane.png" alt-text="イベント ペインには、新しいサブスクリプションを作成するためのオプションがあります。" :::

1. `Event Subscription` と `System Topic` の名前を入力します。 既定では、[イベントの種類のキーと値の変更] と [キーと値の削除] が設定されます。これは、[フィルター] タブを使用してプッシュ イベントが送信される正確な理由を選択することにより変更できます。

    :::image type="content" source="./media/create-event-subscription.png" alt-text="イベントには、名前、トピック、フィルターが必要です。" :::

1. `Endpoint Type` に `Web Hook` を選択し、`Select an endpoint` を選択します。

    :::image type="content" source="./media/event-subscription-webhook-endpoint.png" alt-text="[エンドポイント] を選択すると、エンドポイント URI を入力する新しいブレードが作成されます。" :::

1. エンドポイントは、アプリケーションの URI + "/actuator/appconfiguration-refresh?{your-token-name}={your-token-secret}" です。 例: `https://my-azure-webapp.azurewebsites.net/actuator/appconfiguration-refresh?myToken=myTokenSecret`。

1. [`Create`] をクリックしてイベント サブスクリプションを作成します。 `Create` を選択すると、Webhook の登録要求がアプリケーションに送信されます。 これは、Azure App Configuration クライアント ライブラリによって受信され、検証されて、有効な応答が返されます。

1. [`Events`] ペインの [`Event Subscriptions`] をクリックして、サブスクリプションが正しく作成されたことを確認します。

    :::image type="content" source="./media/event-subscription-view-webhook.png" alt-text="Webhook が、ページの下部にあるテーブルに表示されます。" :::

> [!NOTE]
> 構成の変更をサブスクライブするとき、1 つまたは複数のフィルターを使用することで、アプリケーションに送信されるイベントの数を減らすことができます。 これらは、[Event Grid サブスクリプション フィルター](../event-grid/event-filtering.md)または [Service Bus サブスクリプション フィルター](../service-bus-messaging/topic-filters.md)として構成できます。 たとえばサブスクリプション フィルターを使用すると、特定の文字列で始まるキーの変更イベントだけをサブスクライブすることができます。

## <a name="verify-and-test-application"></a>アプリケーションの検証とテスト

1. アプリケーションが実行されたら、*curl* を使用してアプリケーションをテストできます。次に例を示します。

   ```cmd
   curl -X GET http://localhost:8080
   ```

1. **Azure portal** を開き、アプリケーションに関連付けられている App Configuration リソースに移動します。 **[操作]** の下の **[構成エクスプローラー]** を選択し、次のキーの値を更新します。

    | キー | 値 |
    |---|---|
    | /application/config.message | Hello - Updated |

1. ブラウザー ページを最新の情報に更新し、新しいメッセージが表示されるのを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように Java アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)