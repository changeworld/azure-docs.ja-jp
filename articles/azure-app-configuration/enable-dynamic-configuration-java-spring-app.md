---
title: Spring Boot アプリで動的な構成を使用する
titleSuffix: Azure App Configuration
description: Spring Boot アプリの構成データを動的に更新する方法について説明します
services: azure-app-configuration
author: mrm9084
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 12/09/2020
ms.custom: devx-track-java
ms.author: mametcal
ms.openlocfilehash: 34225576d435f9ff275f74372afa2cc9f7d50b9f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132287250"
---
# <a name="tutorial-use-dynamic-configuration-in-a-java-spring-app"></a>チュートリアル:Java Spring アプリで動的構成を使用する

App Configuration には、Spring  用のライブラリが 2 つあります。 `azure-spring-cloud-appconfiguration-config` は Spring Boot を必要とし、`spring-cloud-context` に依存します。 `azure-spring-cloud-appconfiguration-config-web` は Spring Boot と共に Spring Web を必要とします。 どちらのライブラリも、更新された構成値をチェックするための手動トリガーをサポートしています。 `azure-spring-cloud-appconfiguration-config-web` によって、構成の更新の自動チェックに対するサポートも追加されます。

更新により、アプリケーションを再起動することなく構成値を更新できますが、`@RefreshScope` のすべての Bean が再作成されることになります。 クライアント ライブラリは、現在読み込まれている構成のハッシュ ID をキャッシュすることで、構成ストアへの過剰な呼び出しが行われないようにします。 構成ストア内の値が変更されたとしても、キャッシュされた値の有効期限が切れるまでは、更新操作で値は更新されません。 各要求の既定の有効期限は 30 秒です。 その設定は、必要に応じてオーバーライドできます。

`azure-spring-cloud-appconfiguration-config-web` の自動更新は、アクティビティ (特に Spring Web の `ServletRequestHandledEvent`) に基づいてトリガーされます。 `ServletRequestHandledEvent` がトリガーされない場合は、キャッシュの有効期限が切れても、`azure-spring-cloud-appconfiguration-config-web` の自動更新によって更新がトリガーされることはありません。

## <a name="use-manual-refresh"></a>手動更新を使用する

App Configuration は `AppConfigurationRefresh` を公開します。これは、キャッシュの有効期限が切れているかどうかを確認し、有効期限が切れていれば更新をトリガーするために使用できます。

```java
import com.azure.spring.cloud.config.AppConfigurationRefresh;

...

@Autowired
private AppConfigurationRefresh appConfigurationRefresh;

...

public void myConfigurationRefreshCheck() {
    Future<Boolean> triggeredRefresh = appConfigurationRefresh.refreshConfigurations();
}
```

`AppConfigurationRefresh` の `refreshConfigurations()` は、更新がトリガーされている場合は true、そうでない場合は false である `Future` を返します。 False は、キャッシュの有効期限が切れていないか、変更がなかったか、または別のスレッドが現在更新をチェック中であることを意味します。

## <a name="use-automated-refresh"></a>自動更新を使用する

自動更新を使用するためには、App Configuration を使用する Spring Boot アプリから始めてみましょう。たとえば、[App Configuration 用の Spring Boot クイックスタート](quickstart-java-spring-app.md)で作成したアプリが利用できます。

テキスト エディターで *pom.xml* ファイルを開き、次のコードを使用して `azure-spring-cloud-appconfiguration-config-web` の `<dependency>` を追加します。

**Spring Boot**

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0</version>
</dependency>
```

> [!NOTE]
> 古い依存関係のサポートが必要な場合は、[以前のライブラリ](https://github.com/Azure/azure-sdk-for-java/blob/spring-cloud-starter-azure-appconfiguration-config_1.2.9/sdk/appconfiguration/spring-cloud-starter-azure-appconfiguration-config/README.md)を参照してください。

1. 最新の情報への更新を有効にするために、`bootstrap.properties` を更新します。

    ```properties
    spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled=true
    spring.cloud.azure.appconfiguration.stores[0].monitoring.triggers[0].key=sentinel
    ```

1. **Azure portal** を開き、アプリケーションに関連付けられている App Configuration リソースに移動します。 **[操作]** で **[構成エクスプローラー]** を選択し、 **[+ 作成]**  >  **[キー値]** の順に選択して、新しいキーと値のペアを作成します。

    | キー | 値 |
    |---|---|
    | Sentinel | 1 |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

1. **[適用]** を選択します。

1. 自分の Spring Boot アプリケーションを Maven でビルドし、実行します。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```

1. ブラウザー ウィンドウを開き、`http://localhost:8080` に移動します。  キーに関連付けられているメッセージが表示されます。

    *curl* を使用してアプリケーションをテストすることもできます。次に例を示します。

    ```cmd
    curl -X GET http://localhost:8080/
    ```

1. 動的構成をテストするには、アプリケーションに関連付けられている Azure App Configuration ポータルを開きます。 **[構成エクスプローラー]** を選択し、表示されたキーの値を更新します。次に例を示します。

    | Key | Value |
    |---|---|
    | /application/config.message | Hello - Updated |

1. 前に作成した sentinel キーを新しい値に更新します。 この変更により、更新間隔が経過すると、アプリケーションですべての構成キーが更新されます。

    | キー | 値 |
    |---|---|
    | Sentinel | 2 |

1. ブラウザー ページを最新の情報に更新し、新しいメッセージが表示されるのを確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、App Configuration から動的に構成設定を更新できるように Spring Boot アプリを設定しました。 App Configuration へのアクセスを効率化する Azure マネージド ID を使用する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
