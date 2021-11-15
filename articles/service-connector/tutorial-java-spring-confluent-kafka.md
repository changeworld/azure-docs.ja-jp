---
title: 'チュートリアル: Azure Spring Cloud の Service Connector を使用して、Confluent Cloud で Apache Kafka に接続された Spring Boot アプリをデプロイする'
description: Azure Spring Cloud の Service Connector を使用して、Confluent Cloud で Apache Kafka に接続された Spring Boot アプリを作成します。
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a407d164ec0214ddce6e1d8bf6254876d3642230
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449224"
---
# <a name="tutorial-deploy-a-spring-boot-app-connected-to-apache-kafka-on-confluent-cloud-with-service-connector-in-azure-spring-cloud"></a>チュートリアル: Azure Spring Cloud の Service Connector を使用して、Confluent Cloud で Apache Kafka に接続された Spring Boot アプリをデプロイする

Azure Spring Cloud で実行される spring boot アプリケーション用に、Confluent Cloud で Apache Kafka にアクセスする方法について説明します。 このチュートリアルでは、次のタスクを実行します。

> [!div class="checklist"]
> * Confluent Cloud で Apache Kafka を作成する
> * Spring Cloud アプリケーションを作成する
> * Spring Boot アプリをビルドしてデプロイする
> * Service Connector を使用して Confluent Cloud の Apache Kafka を Azure Spring Cloud に接続する

## <a name="1-set-up-your-initial-environment"></a>1.初期環境を設定する

1. アクティブなサブスクリプションが含まれる Azure アカウントを用意します。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
2. Java 8 または 11 をインストールします。
3. <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 以降をインストールします。これを任意のシェルで使用してコマンドを実行し、Azure リソースのプロビジョニングと構成を行います。

## <a name="2-clone-or-download-the-sample-app"></a>2.サンプル アプリをクローンまたはダウンロードする

サンプル リポジトリをクローンします。

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

次に、そのフォルダーに移動します。

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## <a name="3-prepare-cloud-services"></a>3. クラウド サービスを準備する

### <a name="31-create-an-instance-of-apache-kafka-for-confluent-cloud"></a>3.1 Apache Kafka for Confluent Cloud のインスタンスを作成する

Apache Kafka for Confluent Cloud のインスタンスを作成するには、[このガイダンス](../partner-solutions/apache-kafka-confluent-cloud/create.md)に従います。

### <a name="32-create-kafka-cluster-and-schema-registry-on-confluent-cloud"></a>3.2 Confluent Cloud で Kafka クラスターとスキーマ レジストリを作成する

1. Azure によって提供される SSO を使用して Confluent Cloud にログインします

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="Azure portal を使用した Confluent Cloud SSO ログインのリンク" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. 既定の環境を使用するか、新しい環境を作成します

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Confluent Cloud 上の Apache Kafka のクラウド環境" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. 次の情報を使用して Kafka クラスターを作成します

    * クラスターの種類: 標準
    * リージョン/ゾーン: eastus (バージニア)、単一ゾーン
    * クラスター名: `cluster_1` またはその他の任意の名前。

1. **[Cluster overview]\(クラスターの概要\)**  ->  **[Cluster settings]\(クラスター設定\)** で、Kafka の **ブートストラップ サーバーの URL** を確認してメモしておきます。

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Confluent Cloud 上の Apache Kafka のクラスター設定" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. **[Data integration]\(データ統合\)**  ->  **[API Keys]\(API キー\)**  ->  **[+ Add key]\(+ キーの追加\)** で、 **[Global access]\(グローバル アクセス\)** を有効にしてクラスターの API キーを作成します。 キーとシークレットをメモしておきます。
1. パーティションが 6 個の `test` という名前のトピックを、 **[Topics]\(トピック\)**  ->  **[+ Add topic]\(+ トピックの追加\)** で作成します。
1. 既定の環境では、 **[Schema Registry]\(スキーマ レジストリ\)** タブをクリックします。スキーマ レジストリを有効にし、 **[API endpoint]\(API エンドポイント\)** をメモします。
1. スキーマ レジストリの API キーを作成します。 キーとシークレットをメモしておきます。

### <a name="33-create-a-spring-cloud-instance"></a>3.3 Spring Cloud インスタンスを作成する

Java の[このガイダンス](../spring-cloud/quickstart.md)に従って、Azure Spring Cloud のインスタンスを作成します。 Spring Cloud インスタンスが [Service Connector がサポートするリージョン](concept-region-support.md)に作成されていることを確認します。

## <a name="4-build-and-deploy-the-app"></a>4. アプリを構築してデプロイする

### <a name="41-build-the-sample-app-and-create-a-new-spring-app"></a>4.1 サンプル アプリをビルドし、新しい Spring アプリを作成する

1. Azure にサインインしてサブスクリプションを選択します。

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Gradle を使用してプロジェクトをビルドします。

```Bash
./gradlew build
```

1. パブリック エンドポイントが割り当てられるアプリを作成します。 Spring Cloud プロジェクトの生成時に Java バージョン 11 を選択した場合、--runtime-version=Java_11 スイッチを含めます。

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## <a name="42-create-service-connection-using-service-connector"></a>4.2 Service Connector を使用してサービス接続を作成する

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

次のコマンドを実行して、Confluent クラウド上の Apache Kafka を Spring Cloud アプリに接続します。

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* *\<your-resource-group-name>* を、Spring Cloud インスタンスを作成したリソース グループ名に **置き換え** ます。
* *\<kafka-bootstrap-server-url>* を、Kafka のブートストラップ サーバーの URL に **置き換え** ます (値は `pkc-xxxx.eastus.azure.confluent.cloud:9092` のようになります)
* *\<cluster-api-key>* および *\<cluster-api-secret>* を、クラスター API キーとシークレットに **置き換え** ます。
* *\<kafka-schema-registry-endpoint>* を、Kafka スキーマ レジストリ エンドポイントに **置き換え** ます (値は `https://psrc-xxxx.westus2.azure.confluent.cloud` のようになります)
* *\<registry-api-key>* および *\<registry-api-secret>* を、Kafka のスキーマ レジストリ API キーとシークレットに **置き換え** ます。

> [!NOTE]
> "The subscription is not registered to use Microsoft.ServiceLinker (サブスクリプションが Microsoft.ServiceLinker を使用するように登録されていません)" というエラー メッセージが表示される場合は、`az provider register -n Microsoft.ServiceLinker` を実行して Service Connector リソース プロバイダーを登録し、接続コマンドを再度実行してください。 

#### <a name="portal"></a>[ポータル](#tab/Azure-portal)

**[Service Connector (Preview)]\(Service Connector \(プレビュー\)\)** をクリックします。次の設定を選択するか入力します。

| 設定      | 推奨値  | 説明                               |
| ------------ |  ------- | -------------------------------------------------- |
| **[サービスの種類]** | Confluent Cloud での Apache Kafka | 対象サービスの種類。 Confluent Cloud に Apache Kafka がない場合は、このチュートリアルの前の手順を完了してください。 |
| **名前** | 生成された一意の名前 | Spring Cloud とターゲット サービスの間の接続を識別する接続名  |
| **Kafka ブートストラップ サーバーの URL** | お使いの Kafka ブートストラップ サーバーの URL | この値はステップ 3.2 から取得します |
| **クラスター API キー** | お使いのクラスター API キー |  |
| **クラスター API シークレット** |  お使いのクラスター API シークレット |  |
| **スキーマ レジストリの接続の作成**  | checked | また、スキーマ レジストリへの接続も作成します |
| **スキーマ レジストリ エンドポイント** | お使いの Kafka スキーマ レジストリ エンドポイント  |  |
| **スキーマ レジストリ API キー** | お使いの Kafka スキーマ レジストリ API キー | |
| **スキーマ レジストリ API シークレット** | お使いの Kafka スキーマ レジストリ API シークレット | |

**[確認と作成]** を選択して、接続の設定を確認します。 次に **[作成]** を選択して、サービス接続の作成を開始します。

---

## <a name="43-deploy-the-jar-file-for-the-app"></a>4.3 アプリの Jar ファイルをデプロイする

次のコマンドを実行して、Spring Cloud アプリに jar ファイル (`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`) をアップロードします

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## <a name="5-validate-the-kafka-data-ingestion"></a>5. Kafka データ インジェストを検証する

Azure portal から Spring Cloud アプリのエンドポイントに移動し、アプリケーションの URL をクリックします。 "10 messages were produced to topic test (トピック test に対して 10 個のメッセージが生成されました)" と表示されます。

次に、Confluent ポータルにアクセスすると、トピックのページに運用スループットが表示されます。

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="メトリックの例" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
