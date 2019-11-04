---
title: クイック スタート:Maven を使用してアプリケーションを起動する - Azure Spring Cloud
description: Maven を使用してサンプル アプリケーションを起動する
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554560"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>クイック スタート:Maven プラグインを使用して Azure Spring Cloud アプリを起動する

Azure Spring Cloud の Maven プラグインを使用すると、自分の Azure Spring Cloud サービス アプリケーションを簡単に作成および更新できます。 構成を事前定義することにより、既存の Azure Spring Cloud サービスにアプリケーションをデプロイできます。 この記事では、PiggyMetrics というサンプル アプリケーションを使用して、この機能を紹介します。

>[!Note]
> このクイックスタートを開始する前に、自分の Azure サブスクリプションで Azure Spring Cloud にアクセスできることを確認してください。 プレビュー サービスは招待制となっており、ご連絡をいただいたお客様のサブスクリプションをこちらで許可リストに追加させていただきます。 Azure Spring Cloud の機能を試してみたい方は、[Azure Spring Cloud (プライベート プレビュー) - 試用申請フォーム](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u)に必要事項を記入して送信してください。

>[!TIP]
> Azure Cloud Shell は無料のインタラクティブ シェルです。この記事のコマンドは、Azure Cloud Shell を使って実行することができます。 最新バージョンの Git、Java Development Kit (JDK)、Maven、Azure CLI など、一般的な Azure ツールがプレインストールされています。 Azure サブスクリプションにサインインしている場合は、[Azure Cloud Shell](https://shell.azure.com) を起動します。 詳細については、[Azure Cloud Shell の概要](../cloud-shell/overview.md)に関するページを参照してください。

このクイック スタートを完了するには、以下が必要です。

1. [Git をインストールします](https://git-scm.com/)。
2. [JDK 8 をインストールします](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)。
3. [Maven 3.0 以上をインストールします](https://maven.apache.org/download.cgi)。
4. [Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)を実行します。
5. [無料の Azure サブスクリプションにサインアップします](https://azure.microsoft.com/free/)。

## <a name="install-the-azure-cli-extension"></a>Azure CLI 拡張機能をインストールする

次のコマンドを使用して、Azure CLI 用の Azure Spring Cloud 拡張機能をインストールします。

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Azure portal でサービス インスタンスをプロビジョニングする

1. Web ブラウザーで [Azure portal](https://portal.azure.com) を開き、自分のアカウントにサインインします。

1. **Azure Spring Cloud** を検索して選択します。 
1. 概要ページで **[作成]** を選択し、次の手順を実行します。  

    a. **[サービス名]** ボックスに、サービス インスタンスの名前を指定します。 名前の長さは 4 文字から 32 文字で、小文字、数字、およびハイフンのみを使用できます。 サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。  

    b. **[サブスクリプション]** ボックスの一覧から、このリソースの請求先のサブスクリプションを選択します。 そのサブスクリプションが Microsoft 側で Azure Spring Cloud の許可リストに追加されていることを確認してください。  

    c. **[リソース グループ]** ボックスで、新しいリソース グループを作成します。 新しいリソース用にリソース グループを作成することをお勧めします。  

    d. **[場所]** ボックスの一覧から、サービス インスタンスの場所を選択します。 現在サポートされている場所は、米国東部、米国西部 2、西ヨーロッパ、東南アジアなどです。
    
サービスのデプロイには約 5 分かかります。 サービスがデプロイされると、サービス インスタンスの **[概要]** ページが表示されます。

## <a name="set-up-your-configuration-server"></a>構成サーバーを設定する

1. サービスの **[概要]** ページで、 **[Config Server]\(構成サーバー\)** を選択します。
1. **[既定のリポジトリ]** セクションで **[URI]** を **https://github.com/Azure-Samples/piggymetrics** に設定し、 **[ラベル]** を **config** に設定して、 **[適用]** を選択し、変更を保存します。

## <a name="clone-and-build-the-sample-application-repository"></a>サンプル アプリケーション リポジトリを複製してビルドする

1. 次のコマンドを実行して、Git リポジトリを複製します。

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. 次のコマンドを実行して、ディレクトリを変更し、プロジェクトをビルドします。

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Azure Spring Cloud 構成を生成してデプロイする

1. Azure Spring Cloud で Maven を使用できるようにするために、*pom.xml* ファイルまたは *settings.xml* ファイルに次のコードを追加します。

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. 次のコマンドを実行して、構成を生成します。

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. `gateway`、`auth-service`、および `account-service` モジュールを選択します。

    b. 自分のサブスクリプションと Azure Spring Cloud サービス クラスターを選択します。

    c. 提示されるプロジェクトの一覧で、`gateway` に対応する番号を入力し、パブリック アクセス権を付与します。
    
    d. 構成を確認します。

1. 次のコマンドを使用して、アプリをデプロイします。

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. 前のコマンドからの出力によって提供された URL を使用して、PiggyMetrics にアクセスできます。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Maven リポジトリから Spring Cloud アプリケーションをデプロイしました。 Azure Spring Cloud の詳細を学習するには、デプロイ用のアプリの準備に関するチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Spring Cloud アプリケーションをデプロイ用に準備する](spring-cloud-tutorial-prepare-app-deployment.md)
