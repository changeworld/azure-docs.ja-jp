---
title: クイック スタート:サーバーを作成する - Azure CLI - Azure Database for PostgreSQL - Single Server
description: Azure CLI (コマンド ライン インターフェイス) を使用して Azure Database for PostgreSQL - Single Server を作成するためのクイックスタート ガイド。
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: d103ed0ebd565df77032237638c775991324ea44
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87030183"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Database for PostgreSQL - 単一サーバーを作成する

> [!TIP]
> よりシンプルな [az postgres up](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up) Azure CLI コマンド (現在はプレビュー段階) の使用を検討してください。 こちらの[クイック スタート](./quickstart-create-server-up-azure-cli.md) をお試しください。

このクイックスタートでは、[Azure Cloud Shell](https://shell.azure.com) の [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) コマンドを使用して、Azure Database for PostgreSQL サーバーを 5 分で作成する方法について説明します。  Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

>[!Note]
>CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。 

## <a name="prerequisites"></a>前提条件
この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの**サブスクリプション ID** のことです。 

```azurecli-interactive
az login
```

[az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 **az login** 出力の **id** の値をメモしておいて、このコマンドの **subscription** 引数の値として使用します。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list) を使用します。

```azurecli
az account set --subscription <subscription id>
```
## <a name="create-an-azure-database-for-postgresql-server"></a>Azure Database for PostgreSQL サーバーの作成

[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) コマンドを使用して、[Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成し、このリソース グループ内に PostgreSQL サーバーを作成します。 一意の名前を指定する必要があります。 次の例では、`westus` の場所に `myresourcegroup` という名前のリソース グループを作成します。
```azurecli-interactive
az group create --name myresourcegroup --location westus
```

[az postgres server create](/cli/azure/postgres/server) コマンドを使用して、[Azure Database for PostgreSQL サーバー](overview.md)を作成します。 1 つのサーバーに複数のデータベースを含めることができます。

```azurecli-interactive
az postgres server create --resource-group myresourcegroup --name mydemoserver  --location westus --admin-user myadmin --admin-password <server_admin_password> --sku-name GP_Gen5_2 
```
上記の引数の詳細を次に示します。 

**設定** | **値の例** | **説明**
---|---|---
name | mydemoserver | Azure Database for PostgreSQL サーバーを識別する一意の名前を選択します。 サーバー名に含めることができるのは、英小文字、数字、およびハイフン (-) のみであり、 3 ～ 63 文字にする必要があります。
resource-group | myresourcegroup | Azure リソース グループの名前を指定します。
location | westus | サーバーの Azure の場所。
admin-user | myadmin | 管理者ログインのユーザー名。 これを **azure_superuser**、**admin**、**administrator**、**root**、**guest**、**public** にすることはできません。
admin-password | *セキュリティで保護されたパスワード* | 管理者ユーザーのパスワード。 8 ～ 128 文字にする必要があります。 パスワードには、英大文字、英小文字、数字、英数字以外の文字のうち、3 つのカテゴリの文字が含まれている必要があります。
sku-name|GP_Gen5_2|価格レベルとコンピューティング構成の名前を入力します。 省略表現の {価格レベル} _{コンピューティング世代}_ {仮想コア} という規則に従います。 詳しくは、[Azure Database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/server/) に関する記事をご覧ください。

>[!IMPORTANT] 
>- サーバーの既定の PostgreSQL バージョンは 9.6 です。 サポートされているすべてのバージョンについては、[こちら](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions)を参照してください。
>- **az postgres server create** コマンドのすべての引数を確認するには、この[リファレンス ドキュメント](https://docs.microsoft.com/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create)を参照してください
>- サーバーでは、既定で SSL が有効になっています。 SSL の詳細については、[SSL 接続の構成](./concepts-ssl-connection-security.md)に関するページを参照してください

## <a name="configure-a-server-level-firewall-rule"></a>サーバーレベルのファイアウォール規則の構成 
既定では、作成されたサーバーにはパブリックにアクセスすることはできません。ファイアウォール規則で保護されています。 サーバーに接続するためのアクセス権をローカル環境に付与するには、[az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule) コマンドを使用して、サーバーにファイアウォール規則を構成できます。 

次の例では、特定の IP アドレス 192.168.0.1 からの接続を許可する、`AllowMyIP` と呼ばれるファイアウォール規則を作成しています。 実際の接続元の場所に対応する IP アドレスまたは IP アドレスの範囲で置き換えます。  IP アドレスの検索方法がわからない場合は、[https://whatismyipaddress.com/](https://whatismyipaddress.com/) にアクセスして IP アドレスを取得してください。


```azurecli-interactive
az postgres server firewall-rule create --resource-group myresourcegroup --server mydemoserver --name AllowMyIP --start-ip-address 192.168.0.1 --end-ip-address 192.168.0.1
```

> [!NOTE]
>  接続の問題が発生しないように、Azure Database for PostgreSQL サーバーによって使用されるポート 5432 が、ネットワークのファイアウォールによって許可されていることを確認してください。 

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。
```azurecli-interactive
az postgres server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **administratorLogin** と **fullyQualifiedDomainName** の値を書き留めておきます。
```json
{
  "administratorLogin": "myadmin",
  "earliestRestoreDate": null,
  "fullyQualifiedDomainName": "mydemoserver.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/mydemoserver",
  "location": "westus",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 2,
    "family": "Gen5",
    "name": "GP_Gen5_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforPostgreSQL/servers",
  "userVisibleState": "Ready",
  "version": "9.6"
}
```

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>psql を使用して Azure Database for PostgreSQL サーバーに接続する
[**psql**](https://www.postgresql.org/docs/current/static/app-psql.html) は、PostgreSQL サーバーへの接続に使用される一般的なクライアントです。 **psql** と [Azure Cloud Shell](../cloud-shell/overview.md) を使用して、サーバーに接続できます。 または、可能な場合は、ローカル環境で psql を使用することもできます。 新しい PostgreSQL サーバーでは、次に示すように、psql に接続するために使用できる空のデータベース "postgres" が既に作成されています 

   ```bash
 psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   > [!TIP]
   > Postgres への接続に URL パスを使用する場合は、`%40` を使用してユーザー名の @ 記号を URL エンコードします。 たとえば、psql の接続文字列は次のようになります。
   > ```
   > psql postgresql://myadmin%40mydemoserver@mydemoserver.postgres.database.azure.com:5432/postgres
   > ```


## <a name="clean-up-resources"></a>リソースをクリーンアップする
これらのリソースが別のクイックスタートやチュートリアルで不要である場合、次のコマンドを実行して削除することができます。 

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成した 1 つのサーバーを削除するだけの場合は、[az postgres server delete](/cli/azure/postgres/server) コマンドを実行してください。
```azurecli-interactive
az postgres server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [エクスポートとインポートを使用したデータベースの移行](./howto-migrate-using-export-and-import.md)
> 
> [PostgreSQL を使用する Django Web アプリのデプロイ](../app-service/containers/tutorial-python-postgresql-app.md)
>
> [Node.JS アプリを使用した接続](./connect-nodejs.md)

