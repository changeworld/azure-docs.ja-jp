---
title: チュートリアル:同じ仮想ネットワークに Azure Database for PostgreSQL - フレキシブル サーバーと Azure App Service Web アプリを作成する
description: 仮想ネットワークに Azure Database for PostgreSQL - フレキシブル サーバーと Web アプリを作成するためのクイックスタート ガイド
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 06/30/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 146a3a252a0f8105245c2a6826876e1400efdc13
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766988"
---
# <a name="tutorial-create-an-azure-database-for-postgresql---flexible-server-with-app-services-web-app-in-virtual-network"></a>チュートリアル:仮想ネットワークに Azure Database for PostgreSQL - フレキシブル サーバーと App Service Web アプリを作成する

> [!IMPORTANT]
> Azure Database for PostgreSQL - フレキシブル サーバーはプレビュー段階です

このチュートリアルでは、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)に Azure Database for PostgreSQL - フレキシブル サーバー (プレビュー) と Azure App Service Web アプリを作成する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * 仮想ネットワークに PostgreSQL フレキシブル サーバーを作成する
> * Web アプリを作成する
> * Web アプリを仮想ネットワークに追加する
> * Web アプリから Postgres に接続する 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- [Azure CLI](/cli/azure/install-azure-cli) (バージョン 2.0 以降) をローカルにインストールします。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 
- [az login](/cli/azure/authenticate-azure-cli) コマンドを使用して、お使いのアカウントにログインします。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。

  ```azurecli
  az login
  ```
- 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。

  ```azurecli
  az account set --subscription <subscription ID>
  ```

## <a name="create-a-postgresql-flexible-server-in-a-new-virtual-network"></a>新しい仮想ネットワークに PostgreSQL フレキシブル サーバーを作成する

次のコマンドを使用して、仮想ネットワーク (VNET) にプライベート フレキシブル サーバーを作成します。

```azurecli
az postgres flexible-server create --resource-group demoresourcegroup --name demoserverpostgres --vnet demoappvnet --location westus2
```
このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- リソース グループがまだ存在していない場合は作成します。
- サーバー名が指定されていない場合は、それが生成されます。
- 新しい postgreSQL サーバー用の新しい仮想ネットワークと、この仮想ネットワーク内にデータベース サーバー用のサブネットを作成します。
- サーバーの管理者のユーザー名とパスワードが指定されていない場合は、それらが作成されます。
- **postgres** という名前の空のデータベースが作成されます。

出力例を次に示します。

```json
Local context is turned on. Its information is saved in working directory /home/jane. You can run `az local-context off` to turn it off.
Command argument values from local context: --resource-group demoresourcegroup, --location: eastus
Command group 'postgres flexible-server' is in preview. It may be changed/removed in a future release.
Checking the existence of the resource group ''...
Creating Resource group 'demoresourcegroup ' ...
Creating new vnet "demoappvnet" in resource group "demoresourcegroup" ...
Creating new subnet "Subnet095447391" in resource group "demoresourcegroup " and delegating it to "Microsoft.DBforPostgreSQL/flexibleServers"...
Creating PostgreSQL Server 'demoserverpostgres' in group 'demoresourcegroup'...
Your server 'demoserverpostgres' is using sku 'Standard_D2s_v3' (Paid Tier). Please refer to https://aka.ms/postgres-pricing for pricing details
Make a note of your password. If you forget, you would have to resetyour password with 'az postgres flexible-server update -n demoserverpostgres --resource-group demoresourcegroup -p <new-password>'.
{
  "connectionString": "postgresql://generated-username:generated-password@demoserverpostgres.postgres.database.azure.com/postgres?sslmode=require",
  "host": "demoserverpostgres.postgres.database.azure.com",
  "id": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.DBforPostgreSQL/flexibleServers/demoserverpostgres",
  "location": "East US",
  "password": "generated-password",
  "resourceGroup": "demoresourcegroup",
  "skuname": "Standard_D2s_v3",
  "subnetId": "/subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/demoresourcegroup/providers/Microsoft.Network/virtualNetworks/VNET095447391/subnets/Subnet095447391",
  "username": "generated-username",
  "version": "12"
}
```

## <a name="create-a-web-app"></a>Web アプリの作成
このセクションでは App Service アプリでアプリ ホストを作成し、このアプリを Postgres データベースに接続して、そのホストにコードをデプロイします。 ターミナルで、アプリケーション コードのリポジトリのルートにいることを確認します。 Basic プランでは VNET 統合はサポートされていないことに注意してください。 Standard または Premium を使用してください。 

az webapp up コマンドを使用して、App Service アプリ (ホスト プロセス) を作成します。

```azurecli
az webapp up --resource-group demoresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - _--location_ 引数には、前のセクションでデータベースに使用したのと同じ場所を使用します。
> - \<app-name\> は、すべての Azure で一意の名前に置き換えます。 \<app-name\> に使用できる文字は A から Z、0 から 9、および - です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- リソース グループがまだ存在していない場合は作成します。 (このコマンドでは、先ほどデータベースを作成したのと同じリソース グループを使用します)。
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。

### <a name="create-subnet-for-web-app"></a>Web アプリ用のサブネットを作成する
VNET 統合を有効にする前に、App Service Web アプリに委任されたサブネットを用意する必要があります。 サブネットを作成する前に、データベースのサブネット アドレスを確認し、Web アプリのサブネットに同じアドレス プレフィックスを使用しないようにします。 

```azurecli
az network vnet show --resource-group demoresourcegroup -n demoappvnet
```

次のコマンドを実行して、データベース サーバーが作成されたのと同じ仮想ネットワーク内に新しいサブネットを作成します。 **データベースのサブネットと競合しないようにアドレス プレフィックスを更新します。**

```azurecli
az network vnet subnet create --resource-group demoresourcegroup --vnet-name demoappvnet --name webappsubnet  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms
```

## <a name="add-the-web-app-to-the-virtual-network"></a>Web アプリを仮想ネットワークに追加する
[az webapp vnet-integration](/cli/azure/webapp/vnet-integration) コマンドを使用して、リージョンの仮想ネットワーク統合を webapp に追加します。 

```azurecli
az webapp vnet-integration add --resource-group demoresourcegroup -n  mywebapp --vnet demoappvnet --subnet webappsubnet
```

## <a name="configure-environment-variables-to-connect-the-database"></a>データベースに接続するための環境変数を構成する
コードを App Service にデプロイしたので、次の手順として、アプリを Azure のフレキシブル サーバーに接続します。 アプリ コードでは、さまざまな環境変数でデータベース情報を検索することを想定しています。 App Service で環境変数を設定するには、[az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) コマンドを使用します。

  
```azurecli
  
az webapp config appsettings set  --name mywebapp --settings DBHOST="<postgres-server-name>.postgres.database.azure.com" DBNAME="postgres" DBUSER="<username>" DBPASS="<password>" 
```
- 新しく作成されるフレキシブル サーバー コマンドに合わせて、**postgres-server-name**、**username**、**password** を置き換えます。
- **\<username\>** と **\<password\>** も、そのコマンドで生成された資格情報に置き換えます。
- リソース グループとアプリ名は、 .azure/config ファイル内のキャッシュされた値から取得されます。
- コマンドによって、**DBHOST**、**DBNAME**、**DBUSER** _、_*DBPASS** という名前の設定が作成されます。 アプリケーション コードでデータベース情報に別の名前を使用している場合は、コードで使用されている名前をアプリ設定で使用します。

仮想ネットワーク内からの送信接続をすべて許可するように Web アプリを構成します。
```azurecli
az webapp config set --name mywebapp --resource-group demoresourcegroup --generic-configurations '{"vnetRouteAllEnabled": true}'
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

以下のコマンドを使用して、チュートリアルで作成したすべてのリソースをクリーンアップします。 このコマンドによって、このリソース グループ内にあるすべてのリソースが削除されます。

```azurecli
az group delete -n demoresourcegroup
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](../../app-service/app-service-web-tutorial-custom-domain.md)
