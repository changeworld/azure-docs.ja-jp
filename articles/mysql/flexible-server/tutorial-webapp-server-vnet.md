---
title: チュートリアル:同じ仮想ネットワークに Azure Database for MySQL フレキシブル サーバー (プレビュー) と Azure App Service Web アプリを作成する
description: 仮想ネットワークに Azure Database for MySQL フレキシブル サーバー (プレビュー) と Web アプリを作成するためのクイックスタート ガイド
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 13baf8f033338e242610d7b8c4eec14806cd5ec5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770023"
---
# <a name="tutorial-create-an-azure-database-for-mysql---flexible-server-preview-with-app-services-web-app-in-virtual-network"></a>チュートリアル:仮想ネットワークに Azure Database for MySQL - フレキシブル サーバー (プレビュー) と App Service Web アプリを作成する

> [!IMPORTANT]
> Azure Database for MySQL - フレキシブル サーバーは、現在パブリック プレビュー段階にあります。

このチュートリアルでは、MySQL フレキシブル サーバー (プレビュー) を使用して、[仮想ネットワーク](../../virtual-network/virtual-networks-overview.md)に Azure App Service Web アプリを作成する方法について説明します。

このチュートリアルで学習する内容は次のとおりです。
>[!div class="checklist"]
> * 仮想ネットワークに MySQL フレキシブル サーバーを作成する
> * App Service に委任するサブネットを作成する
> * Web アプリを作成する
> * Web アプリを仮想ネットワークに追加する
> * Web アプリから Postgres に接続する 

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

この記事では、Azure CLI バージョン 2.0 以降をローカルで実行している必要があります。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

[az login](/cli/azure/reference-index#az_login) コマンドを使用して、アカウントにログインする必要があります。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。

```azurecli
az login
```

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 サブスクリプション ID プレースホルダーへのサブスクリプションを、**az login** 出力の **サブスクリプション ID** プロパティに置き換えます。

```azurecli
az account set --subscription <subscription ID>
```

## <a name="create-an-azure-database-for-mysql-flexible-server"></a>Azure Database for MySQL フレキシブル サーバーを作成する

次のコマンドを使用して、仮想ネットワーク (VNET) にプライベート フレキシブル サーバーを作成します。
```azurecli
az mysql flexible-server create --resource-group myresourcegroup --location westus2
```
新しく作成した仮想ネットワークの接続文字列と名前をコピーします。 このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- リソース グループがまだ存在していない場合は作成します。
- サーバー名が指定されていない場合は、それが生成されます。
- 新しい MySQL サーバー用の新しい仮想ネットワークが作成されます。 Web アプリを同じ仮想ネットワークに追加する必要があるため、サーバー用に作成された仮想ネットワーク名とサブネット名をメモしておいてください。
- サーバーの管理者のユーザー名とパスワードが指定されていない場合は、それらが作成されます。
- **flexibleserverdb** という名前の空のデータベースが作成されます。

> [!NOTE]
> 指定されていない場合に生成されるパスワードをメモしておいてください。 パスワードを忘れた場合は、``` az mysql flexible-server update``` コマンドを使用してパスワードをリセットする必要があります。

## <a name="create-subnet-for-app-service-endpoint"></a>App Service エンドポイントのサブネットを作成する
次に、App Service Web アプリのエンドポイントに委任されるサブネットが必要です。 次のコマンドを実行して、データベース サーバーが作成されたのと同じ仮想ネットワーク内に新しいサブネットを作成します。 

```azurecli
az network vnet subnet create -g myresourcegroup --vnet-name VNETName --name webappsubnetName  --address-prefixes 10.0.1.0/24  --delegations Microsoft.Web/serverFarms --service-endpoints Microsoft.Web
```
このコマンドの後、仮想ネットワーク名とサブネット名をメモしておいてください。作成後、Web アプリの VNET 統合ルールを追加するために必要になります。 

## <a name="create-a-web-app"></a>Web アプリを作成する

このセクションでは App Service アプリにアプリ ホストを作成し、このアプリを MySQL データベースに接続します。 ターミナルで、アプリケーション コードのリポジトリのルートにいることを確認します。

az webapp up コマンドを使用して、App Service アプリ (ホスト プロセス) を作成します。

```azurecli
az webapp up --resource-group myresourcegroup --location westus2 --plan testappserviceplan --sku P2V2 --name mywebapp
```

> [!NOTE]
> - --location 引数には、前のセクションでデータベースに使用したのと同じ場所を使用します。
> - _&lt;app-name>_ を、Azure 全体で一意の名前に置き換えます (サーバー エンドポイントは https://\<app-name>.azurewebsites.net)。 <app-name> に使用できる有効な文字は A から Z、0 から 9、および - です。 会社名とアプリ識別子を組み合わせて使用すると、適切なパターンになります。
> - App Service の Basic レベルでは、VNET 統合はサポートされていません。 Standard または Premium を使用してください。 

このコマンドによって次の操作が実行されます。これには数分かかる場合があります。

- リソース グループがまだ存在していない場合は作成します。 (このコマンドでは、先ほどデータベースを作成したのと同じリソース グループを使用します)。
- Basic 価格レベル (B1) で App Service プラン ```testappserviceplan``` を作成します (存在しない場合)。 --plan と --sku は省略可能です。
- App Service アプリが存在しない場合は作成します。
- アプリの既定のログがまだ有効になっていない場合は、有効にします。
- ビルド オートメーションを有効にし、ZIP デプロイを使用してリポジトリをアップロードします。

## <a name="add-the-web-app-to-the-virtual-network"></a>Web アプリを仮想ネットワークに追加する

**az webapp vnet-integration** コマンドを使用して、リージョンの仮想ネットワーク統合を webapp に追加します。 _&lt;vnet-name>_ と _&lt;subnet-name_ を、フレキシブル サーバーで使用している仮想ネットワークとサブネット名に置き換えます。

```azurecli
az webapp vnet-integration add -g myresourcegroup -n  mywebapp --vnet VNETName --subnet webappsubnetName
```

## <a name="configure-environment-variables-to-connect-the-database"></a>データベースに接続するための環境変数を構成する

コードを App Service にデプロイしたので、次の手順として、アプリを Azure のフレキシブル サーバーに接続します。 アプリ コードでは、さまざまな環境変数でデータベース情報を検索することを想定しています。 App Service に環境変数を設定するには、```az webapp config appsettings``` 設定コマンドを使用して "アプリ設定" を作成します。

```azurecli
az webapp config appsettings set --settings DBHOST="<mysql-server-name>.mysql.database.azure.com" DBNAME="flexibleserverdb" DBUSER="<username>" DBPASS="<password>"
```

- 新しく作成されるフレキシブル サーバー コマンド用に、 _&lt;mysql-server-name>_ 、 _&lt;username>_ 、および _&lt;password>_ を置き換えます。
- _&lt;username>_ と _&lt;password>_ を、コマンドによって生成される資格情報に置き換えます。
- リソース グループとアプリ名は、 .azure/config ファイル内のキャッシュされた値から取得されます。
- コマンドによって、DBHOST、DBNAME、DBUSER、および DBPASS という名前の設定が作成されます。 アプリケーション コードでデータベース情報に別の名前を使用している場合は、コードで使用されている名前をアプリ設定で使用します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

以下のコマンドを使用して、チュートリアルで作成したすべてのリソースをクリーンアップします。 このコマンドによって、このリソース グループ内にあるすべてのリソースが削除されます。

```azurecli
az group delete -n myresourcegroup
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](../../app-service/app-service-web-tutorial-custom-domain.md)
