---
title: Azure App Configuration の使用方法に関するクイック スタート | Microsoft Docs
description: Java Spring アプリで Azure App Configuration を使用する場合のクイック スタートです。
services: azure-app-configuration
documentationcenter: ''
author: yidon
manager: jeffya
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: java
ms.topic: quickstart
ms.tgt_pltfrm: Spring
ms.workload: tbd
ms.date: 01/08/2019
ms.author: yidon
ms.openlocfilehash: d607d6cd813b23051e1676153cbb134261bcf5bc
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960621"
---
# <a name="quickstart-create-a-java-spring-app-with-app-configuration"></a>クイック スタート:App Configuration を使用して Java Spring アプリを作成する

Azure App Configuration は、Azure 内にあるマネージド構成サービスです。 すべてのアプリケーション設定を、お使いのコードとは別の 1 つの場所に簡単に保存して管理することが可能です。 このクイック スタートでは、Java Spring アプリにサービスを組み込む方法を示します。

このクイック スタートの手順は、任意のコード エディターを使用して実行できます。 ただし、推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

## <a name="prerequisites"></a>前提条件

このクイック スタートを完了するには、サポートされている [Java Development Kit (JDK)](https://aka.ms/azure-jdks) のバージョン 8 と [Apache Maven](http://maven.apache.org/) のバージョン 3.0 以降をインストールします。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

1. 新しいアプリ構成ストアを作成するには、まず [Azure portal](https://aka.ms/azconfig/portal) にサインインします。 ページの左上で、**[+ リソースの作成]** をクリックします。 **[Marketplace を検索]** テキスト ボックスに「**App Configuration**」と入力し、**Enter** キーを押します。

    ![App Configuration を検索する](./media/quickstarts/azure-app-configuration-new.png)

2. 検索結果の **[アプリ構成]** をクリックし、**[作成]** をクリックします。

3. **[アプリの構成]** > **[作成]** ページで、次の設定を入力します。

    | Setting | 推奨値 | 説明 |
    |---|---|---|
    | **リソース名** | グローバルに一意の名前 | アプリ構成ストア リソースに使用する一意のリソース名を入力します。 名前は 1 から 63 文字の文字列で、数字、英字、`-` 文字のみを使用する必要があります。 名前の先頭と末尾には `-` 文字を使用できません。また、連続する `-` 文字は無効です。  |
    | **サブスクリプション** | 該当するサブスクリプション | App Configuration のテストに使用する Azure サブスクリプションを選択します。 アカウントにサブスクリプションが 1 つしかない場合は自動的に選択されるため、**[サブスクリプション]** ドロップダウンは表示されません。 |
    | **リソース グループ** | *AppConfigTestResources* | アプリ構成ストア リソースのリソース グループを選択または作成します。 このグループは複数のリソースを整理し、そのリソース グループを削除することで複数のリソースを同時削除できるため便利です。 詳細については、[リソース グループを使用した Azure リソースの管理](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)に関するページを参照してください。 |
    | **場所** | *米国中部* | **[場所]** を使用して、SignalR リソースがホストされている地理的位置を指定します。 最高のパフォーマンスを得るには、アプリケーションの他のコンポーネントと同じリージョンにリソースを作成することをお勧めします。 |

    ![アプリ構成ストアを作成する](./media/quickstarts/azure-app-configuration-create.png)

4. **Create** をクリックしてください。 デプロイが完了するまでに数分かかる場合があります。

5. デプロイが完了したら、**[設定]** > **[アクセス キー]** をクリックします。 プライマリ読み取り専用キー接続文字列またはプライマリ読み取り/書き込みキー接続文字列を書き留めます。 これは、作成したアプリ構成ストアと通信するための構成を後でアプリケーションに対して行う際に使用します。 接続文字列は次のような形式です。

        Endpoint=<your_endpoint>;Id=<your_id>;Secret=<your_secret>

    アプリケーションでは文字列全体を使用する必要があります。

6. **[キー/値のエクスプローラー]** と **[+ 作成]** をクリックして、次のキーと値のペアを追加します。

    | キー | 値 |
    |---|---|
    | /application/config.message | こんにちは |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておいてください。

## <a name="create-a-spring-boot-app"></a>Spring Boot アプリを作成する

[Spring Initializr](https://start.spring.io/) を使用して、新しい Spring Boot プロジェクトを作成します。

1. <https://start.spring.io/> を参照します。

2. 次のオプションを指定します。

   * **Java** で **Maven** プロジェクトを生成します。
   * **Spring Boot** のバージョンとして、2.0 以上を指定します。
   * アプリケーションの**グループ (Group)** と**成果物 (Artifact)** の名前を指定します。
   * **Web** 依存関係を追加します。

3. 上記のオプションを指定したら、**[Generate Project]\(プロジェクトの生成\)** をクリックします。 メッセージが表示されたら、ローカル コンピューター上のパスにプロジェクトをダウンロードします。

## <a name="connect-to-app-configuration-store"></a>アプリ構成ストアに接続する

1. ファイルをローカル システム上に展開したら、シンプルな Spring Boot アプリケーションの編集を開始できます。 アプリのルート ディレクトリで *pom.xml* ファイルを探します。

2. テキスト エディターで *pom.xml* ファイルを開き、Spring Cloud Azure Config スターターを `<dependencies>` のリストに追加します。

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>spring-cloud-starter-azure-appconfiguration-config</artifactId>
        <version>1.1.0.RC2</version>
    </dependency>
    ```

3. アプリのパッケージ ディレクトリに、*MessageProperties.java* という名前の新しい Java ファイルを作成します。 次の行を追加します。

    ```java
    @ConfigurationProperties(prefix = "config")
    public class MessageProperties {
        private String message;

        public String getMessage() {
            return message;
        }

        public void setMessage(String message) {
            this.message = message;
        }
    }
    ```

4. アプリのパッケージ ディレクトリに、*HelloController.java* という名前の新しい Java ファイルを作成します。 次の行を追加します。

    ```java
    @RestController
    public class HelloController {
        private final MessageProperties properties;

        public HelloController(MessageProperties properties) {
            this.properties = properties;
        }

        @GetMapping
        public String getMessage() {
            return "Message: " + properties.getMessage();
        }
    }
    ```

5. メイン アプリケーションの Java ファイルを開き、`@EnableConfigurationProperties` を追加してこの機能を有効にします。

    ```java
    @SpringBootApplication
    @EnableConfigurationProperties(MessageProperties.class)
    public class AzureConfigApplication {
        public static void main(String[] args) {
            SpringApplication.run(AzureConfigApplication.class, args);
        }
    }
    ```

6. アプリのリソース ディレクトリの下に `bootstrap.yaml` という名前の新しいファイルを作成し、ファイルに次の行を追加して、サンプルの値をアプリ構成ストアの適切なプロパティに置き換えます。

    ```yaml
    spring:
        cloud:
            azure:
                config:
                    stores:
                        - connection-string: [your-connection-string]
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドし、ローカルで実行する

1. Spring Boot アプリケーションを Maven でビルドし、実行します。次に例を示します。

    ```shell
    mvn clean package
    mvn spring-boot:run
    ```
2. アプリケーションが実行されたら、*curl* を使用してアプリケーションをテストできます。次に例を示します。

      ```shell
      curl -X GET http://localhost:8080/
      ```
    アプリ構成ストアに入力したメッセージが表示されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このクイック スタートでは、新しいアプリ構成ストアを作成して、Java Spring アプリで使用しました。 詳しくは、[Azure の Spring のホーム ページ](https://docs.microsoft.com/java/azure/spring-framework/)をご覧ください。

App Configuration の使用についてさらに学習するには、認証について示した次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure リソースのマネージド ID の統合](./integrate-azure-managed-service-identity.md)