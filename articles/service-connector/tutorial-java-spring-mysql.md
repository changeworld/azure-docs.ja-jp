---
title: 'チュートリアル: Service Connector を使用して Azure Database for MySQL に接続されている Spring Cloud アプリケーションをデプロイする'
description: Service Connector を使用して Azure Database for MySQL に接続されている Spring Boot アプリケーションを作成します。
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f25ab8e1eb144675567316194c9b4ca3a547f111
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131475238"
---
# <a name="tutorial-deploy-spring-cloud-application-connected-to-azure-database-for-mysql-with-service-connector"></a>チュートリアル: Service Connector を使用して Azure Database for MySQL に接続されている Spring Cloud アプリケーションをデプロイする

このチュートリアルでは、Azure portal または Azure CLI を使用して、次のタスクを実行します。 以下の手順では、両方の方法を説明します。

> [!div class="checklist"]
> * Azure Spring Cloud のインスタンスをプロビジョニングする
> * アプリをビルドして Azure Spring Cloud にデプロイする
> * Service Connector を使用した Azure Spring Cloud と Azure Database for MySQL の統合

## <a name="1-prerequisites"></a>1.前提条件

* [JDK 8 または JDK 11 をインストールする](/azure/developer/java/fundamentals/java-jdk-install)
* [Azure サブスクリプションにサインアップする](https://azure.microsoft.com/free/)
* [Azure CLI バージョン 2.0.67 以降をインストール](/cli/azure/install-azure-cli)し、`az extension add --name spring-cloud` コマンドを使用して Azure Spring Cloud 拡張機能をインストールする

## <a name="2-provision-an-instance-of-azure-spring-cloud"></a>2.Azure Spring Cloud のインスタンスをプロビジョニングする

次の手順では、Azure CLI 拡張機能を使用して、Azure Spring Cloud のインスタンスをプロビジョニングします。

1. Azure Spring Cloud 拡張機能で Azure CLI を更新します。

    ```azurecli
    az extension update --name spring-cloud
    ```

1. Azure CLI にサインインし、アクティブなサブスクリプションを選択します。

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Azure Spring Cloud サービスの名前を準備します。  名前の長さは 4 文字から 32 文字で、小文字、数字、およびハイフンのみを使用できます。  サービス名の最初の文字は英字でなければならず、最後の文字は英字または数字でなければなりません。

1. Azure Spring Cloud サービスが含まれるリソース グループを作成します。  Azure Spring Cloud サービスのインスタンスを作成します。

    ```azurecli
    az group create --name ServiceConnector-tutorial-rg
    az spring-cloud create -n <service instance name> -g ServiceConnector-tutorial-rg
    ```

## <a name="3-create-an-azure-database-for-mysql"></a>3. Azure Database for MySQL を作成する

次の手順では、Azure CLI 拡張機能を使用して、Azure Database for MySQL のインスタンスをプロビジョニングします。

1. [db-up](/cli/azure/ext/db-up/mysql) 拡張機能をインストールします。

```azurecli
az extension add --name db-up
```

次のコマンドを使用して、Azure Database for MySQL サーバーを作成します。

```azurecli
az mysql up --resource-group ServiceConnector-tutorial-rg --admin-user <admin-username> --admin-password <admin-password>
```

- *\<admin-username>* と *\<admin-password>* には、この MySQL サーバーの管理者ユーザーを作成するための資格情報を指定します。 管理者のユーザー名に *azure_superuser*、*azure_pg_admin*、*admin*、*administrator*、*root*、*guest*、または *public* は使用できません。 *pg_* で始めることはできません。 パスワードには、次のうちの 3 つのカテゴリの、**8 から 128 文字** が含まれている必要があります: 英大文字、英小文字、数字 (0 から 9)、英数字以外の文字 (!、#、% など)。 パスワードにユーザー名を含めることはできません。

サーバーは以下の既定値で作成されます (これらを手動でオーバーライドしない限り)。

**設定** | **既定値** | **説明**
---|---|---
server-name | システム生成 | Azure Database for MySQL サーバーを識別する一意の名前。
sku-name | GP_Gen5_2 | SKU の名前。 省略表現の {価格レベル}\_{コンピューティング世代}\_{仮想コア} という規則に従います。 既定値は、2 個の仮想コアを備えた General Purpose Gen5 サーバーです。 レベルの詳細については、[価格に関するページ](https://azure.microsoft.com/pricing/details/mysql/)を参照してください。
backup-retention | 7 | バックアップを保持する必要のある時間。 単位は日数です。
geo-redundant-backup | 無効 | このサーバーに対して geo 冗長バックアップを有効にする必要があるかどうかどうか。
location | westus2 | サーバーの Azure の場所。
ssl-enforcement | Enabled | このサーバーに対して SSL を有効にする必要があるかどうかどうか。
storage-size | 5120 | サーバーのストレージ容量 (単位はメガバイト)。
version | 5.7 | MySQL のメジャー バージョン。


> [!NOTE]
> `az mysql up` コマンドとその追加パラメーターの詳細については、[Azure CLI のドキュメント](/cli/azure/mysql#az_mysql_up)を参照してください。

サーバーが作成されると、設定は次のようになります。

- "devbox" というファイアウォール規則が作成されます。 Azure CLI では、`az mysql up` コマンドの実行元となるマシンの IP アドレスの検出が試行され、その IP アドレスが許可されます。
- [Azure サービスへのアクセスを許可] が [オン] に設定されます。 この設定により、サブスクリプションにはないリソースを含む、すべての Azure リソースからの接続を受け入れるようにサーバーのファイアウォールが構成されます。
- `wait_timeout` パラメーターは 8 時間に設定されています。
- `sampledb` という名前の空のデータベースが作成されます
- `sampledb` に対する特権を持つ "root" という名前の新しいユーザーが作成されます

## <a name="4-build-and-deploy-the-app"></a>4. アプリを構築してデプロイする

1. パブリック エンドポイントが割り当てられるアプリを作成します。 Spring Cloud プロジェクトの生成時に Java バージョン 11 を選択した場合、`--runtime-version=Java_11` スイッチを含めます。

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg --assign-endpoint true
    ```

1. Spring Cloud と MySQL データベースの間のサービス接続を作成します。

    ```azurecli
    az spring-cloud connection create mysql
    ```

    > [!NOTE]
    > "The subscription is not registered to use Microsoft.ServiceLinker (サブスクリプションが Microsoft.ServiceLinker を使用するように登録されていません)" というエラー メッセージが表示される場合は、`az provider register -n Microsoft.ServiceLinker` を実行して Service Connector リソース プロバイダーを登録し、接続コマンドを再度実行してください。 

1. サンプル コードを複製する

    ```bash
    git clone https://github.com/Azure-Samples/serviceconnector-springcloud-mysql-springboot.git
    ```

1. maven を使用してプロジェクトをビルドします。

    ```bash
    cd serviceconnector-springcloud-mysql-springboot
    mvn clean package -DskipTests 
    ```

1. アプリの Jar ファイルをデプロイします (`target/demo-0.0.1-SNAPSHOT.jar`)。

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g ServiceConnector-tutorial-rg  --artifact-path target/demo-0.0.1-SNAPSHOT.jar
    ```

1. 次のコマンドを使用して、デプロイ後のアプリの状態を照会します。

    ```azurecli
    az spring-cloud app list -o table
    ```

    次のような出力結果が表示されます。

    ```
    Name               Location    ResourceGroup    Production Deployment    Public Url                                           Provisioning Status    CPU    Memory    Running Instance    Registered Instance    Persistent Storage
    -----------------  ----------  ---------------  -----------------------  ---------------------------------------------------  ---------------------  -----  --------  ------------------  ---------------------  --------------------
    hellospring         eastus    <resource group>   default                                                                       Succeeded              1      2         1/1                 0/1                    -
    ```

## <a name="next-steps"></a>次のステップ

Service Connector の詳細については、以下のチュートリアルに従ってください。

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
