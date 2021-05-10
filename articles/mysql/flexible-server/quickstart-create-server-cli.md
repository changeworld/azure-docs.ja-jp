---
title: クイック スタート:サーバーを作成する - Azure CLI - Azure Database for MySQL - フレキシブル サーバー
description: このクイック スタートでは、Azure CLI を使用して、Azure Database for MySQL フレキシブル サーバーを Azure リソース グループ内に作成する方法を説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 9/21/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7addfc3a0d91b85c4d63afa4ee6a55b5202c3855
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770239"
---
# <a name="quickstart-create-an-azure-database-for-mysql-flexible-server-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーを作成する

このクイック スタートでは、[Azure Cloud Shell](https://shell.azure.com) で [Azure CLI](/cli/azure/get-started-with-azure-cli) コマンドを使用して、Azure Database for MySQL フレキシブル サーバーを 5 分で作成する方法について説明します。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

> [!IMPORTANT] 
> Azure Database for MySQL フレキシブル サーバーは現在、パブリック プレビュー段階にあります。

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

[Azure Cloud Shell](../../cloud-shell/overview.md) は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を開くこともできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、 **[入力]** を選択して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[az login](/cli/azure/reference-index#az_login) コマンドを使用して、アカウントにログインする必要があります。 **id** プロパティに注意してください。これは、お使いの Azure アカウントの **サブスクリプション ID** を参照します。

```azurecli-interactive
az login
```

[az account set](/cli/azure/account#az_account_set) コマンドを使用して、アカウントの特定のサブスクリプションを選択します。 コマンドの **subscription** 引数の値として使用する、**az login** 出力の **id** 値をメモしておきます。 複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 すべてのサブスクリプションを取得するには、[az account list](/cli/azure/account#az_account_list) を使用します。

```azurecli-interactive
az account set --subscription <subscription id>
```

## <a name="create-a-flexible-server"></a>フレキシブル サーバーを作成する

`az group create` コマンドを使用して、[Azure リソース グループ](../../azure-resource-manager/management/overview.md)を作成し、このリソース グループ内に MySQL フレキシブル サーバーを作成します。 一意の名前を指定する必要があります。 次の例では、`eastus2` の場所に `myresourcegroup` という名前のリソース グループを作成します。

```azurecli-interactive
az group create --name myresourcegroup --location eastus2
```

`az mysql flexible-server create` コマンドを使用して、フレキシブル サーバーを作成します。 1 つのサーバーに複数のデータベースを含めることができます。 次のコマンドでは、サービスの既定値と Azure CLI の[ローカル コンテキスト](/cli/azure/local-context)からの値を使用してサーバーを作成します。 

```azurecli-interactive
az mysql flexible-server create
```

作成されたサーバーには、次の属性があります。 
- 自動生成されたサーバー名、管理者のユーザー名、管理者のパスワード、リソース グループ名 (ローカル コンテキストでまだ指定されていない場合)、およびリソース グループと同じ場所 
- 残りのサーバー構成のサービスの既定値: コンピューティング レベル (バースト可能)、コンピューティング サイズ/SKU (B1MS)、バックアップの保持期間 (7 日間)、および MySQL のバージョン (5.7)
- 既定の接続方法は、自動生成された仮想ネットワークとサブネットを使用するプライベート アクセス (VNet 統合) です。

> [!NOTE] 
> サーバーの作成後に接続方法を変更することはできません。 たとえば、作成時に "*プライベート アクセス (VNet 統合)* " を選択した場合、作成後に *パブリック アクセス (使用できる IP アドレス)* に変更することはできません。 VNet 統合を使用してサーバーに安全にアクセスするには、プライベート アクセスを指定してサーバーを作成することを強くお勧めします。 プライベート アクセスの詳細については、[概念に関する記事](./concepts-networking.md)を参照してください。

既定値を変更したい場合は、Azure CLI の[リファレンス ドキュメント](/cli/azure/mysql/flexible-server)で、構成可能な CLI パラメーターの完全な一覧を参照してください。 

出力例を次に示します。 

```json
Command group 'mysql flexible-server' is in preview. It may be changed/removed in a future release.
Creating Resource Group 'groupXXXXXXXXXX'...
Creating new vnet "serverXXXXXXXXXVNET" in resource group "groupXXXXXXXXXX"...
Creating new subnet "serverXXXXXXXXXSubnet" in resource group "groupXXXXXXXXXX" and delegating it to "Microsoft.DBforMySQL/flexibleServers"...
Creating MySQL Server 'serverXXXXXXXXX' in group 'groupXXXXXXXXXX'...
Your server 'serverXXXXXXXXX' is using sku 'Standard_B1ms' (Paid Tier). Please refer to https://aka.ms/mysql-pricing for pricing details
Creating MySQL database 'flexibleserverdb'...
Make a note of your password. If you forget, you would have to reset your password with 'az mysql flexible-server update -n serverXXXXXXXXX -g groupXXXXXXXXXX -p <new-password>'.
{
  "connectionString": "server=serverXXXXXXXXX.mysql.database.azure.com;database=flexibleserverdb;uid=secureusername;pwd=securepasswordstring",
  "databaseName": "flexibleserverdb",
  "host": "serverXXXXXXXXX.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.DBforMySQL/flexibleServers/serverXXXXXXXXX",
  "location": "East US 2",
  "password": "securepasswordstring",
  "resourceGroup": "groupXXXXXXXXXX",
  "skuname": "Standard_B1ms",
  "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/groupXXXXXXXXXX/providers/Microsoft.Network/virtualNetworks/serverXXXXXXXXXVNET/subnets/serverXXXXXXXXXSubnet",
  "username": "secureusername",
  "version": "5.7"
}
```

既定値を変更したい場合は、Azure CLI の[リファレンス ドキュメント](/cli/azure/mysql/flexible-server)で、構成可能な CLI パラメーターの完全な一覧を参照してください。 

## <a name="create-a-database"></a>データベースを作成する
次のコマンドを実行して、**newdatabase** というデータベースを作成します (まだ作成していない場合)。

```azurecli-interactive
az mysql flexible-server db create -d newdatabase
```

> [!NOTE]
> Azure Database for MySQL との接続では、ポート 3306 が通信に使用されます。 企業ネットワーク内から接続を試みる場合、ポート 3306 での送信トラフィックが許可されていない場合があります。 その場合、会社の IT 部門によってポート 3306 が開放されない限り、サーバーに接続することはできません。

## <a name="get-the-connection-information"></a>接続情報の取得

サーバーに接続するには、ホスト情報とアクセス資格情報を提供する必要があります。

```azurecli-interactive
az mysql flexible-server show --resource-group myresourcegroup --name mydemoserver
```

結果は JSON 形式です。 **fullyQualifiedDomainName** と **administratorLogin** の値を書き留めておきます。 JSON 出力の例を次に示します。 

```json
{
  "administratorLogin": "myadminusername",
  "administratorLoginPassword": null,
  "delegatedSubnetArguments": {
    "subnetArmResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.Network/virtualNetworks/mydemoserverVNET/subnets/mydemoserverSubnet"
  },
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/flexibleServers/mydemoserver",
  "location": "East US 2",
  "name": "mydemoserver",
  "publicNetworkAccess": "Disabled",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 0,
    "name": "Standard_B1ms",
    "tier": "Burstable"
  },
  "storageProfile": {
    "backupRetentionDays": 7,
    "fileStorageSkuName": "Premium_LRS",
    "storageAutogrow": "Disabled",
    "storageIops": 0,
    "storageMb": 10240
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/flexibleServers",
  "version": "5.7"
}
```

## <a name="connect-and-test-the-connection-using-azure-cli"></a>Azure CLI を使用して接続を作成し、テストする

Azure Database for MySQL フレキシブル サーバーを使用すると、Azure CLI の ```az mysql flexible-server connect``` コマンドで mysql サーバーに接続できます。 このコマンドを使用すると、データベース サーバーへの接続をテストし、クイック スタート データベースを作成し、サーバーに対して直接クエリを実行できます。この際、mysql.exe または MySQL ワークベンチをインストールする必要はありません。  コマンドを対話モードで実行して、複数のクエリを実行することもできます。

開発環境からデータベースへの接続をテストして検証するには、次のスクリプトを実行します。

```azurecli-interactive
az mysql flexible-server connect -n <servername> -u <username> -p <password> -d <databasename>
```
**例:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase
```
接続に成功すると、次の出力が表示されます。

```output
Command group 'mysql flexible-server' is in preview and under development. Reference and support levels: https://aka.ms/CLI_refstatus
Connecting to newdatabase database.
Successfully connected to mysqldemoserver1.
```
接続に失敗した場合は、これらの解決策を試してください。
- クライアント マシンでポート 3306 が開放されているかどうかを確認します。
- サーバー管理者のユーザー名とパスワードが正しいかどうかを確認します。
- クライアント マシンにファイアウォール規則が構成されているかどうかを確認します。
- 仮想ネットワークにプライベート アクセスを使用してサーバーを構成した場合、クライアント マシンが同じ仮想ネットワークに存在することを確認します。

単一のクエリを実行するには、次のコマンドに ```--querytext``` 引数 (```-q```) を指定して実行します。

```azurecli-interactive
az mysql flexible-server connect -n <server-name> -u <username> -p "<password>" -d <database-name> --querytext "<query text>"
```

**例:**
```azurecli-interactive
az mysql flexible-server connect -n mysqldemoserver1 -u dbuser -p "dbpassword" -d newdatabase -q "select * from table1;" --output table
```
```az mysql flexible-server connect``` コマンドの使用方法の詳細については、[接続とクエリ](connect-azure-cli.md)に関するドキュメントを参照してください。

## <a name="connect-using-mysql-command-line-client"></a>mysql コマンドライン クライアントを使用して接続する

プライベート アクセス (VNet 統合) を使用してフレキシブル サーバーを作成した場合は、サーバーと同じ仮想ネットワーク内のリソースからサーバーに接続する必要があります。 仮想マシンを作成し、フレキシブル サーバーと共に作成された仮想ネットワークに追加できます。 詳細については、[プライベート アクセス](how-to-manage-virtual-network-portal.md)の構成に関するドキュメントを参照してください。

パブリック アクセス (使用できる IP アドレス) を使用してフレキシブル サーバーを作成した場合は、サーバー上のファイアウォール規則のリストにローカル IP アドレスを追加できます。 詳細な手順については、[ファイアウォール規則の作成または管理](how-to-manage-firewall-portal.md)に関するドキュメントを参照してください。

ローカル環境からサーバーに接続するには、[mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) または [MySQL Workbench](./connect-workbench.md) のどちらかを使用できます。 Azure Database for MySQL フレキシブル サーバーでは、トランスポート層セキュリティ (TLS) (以前の Secure Sockets Layer (SSL)) を使用した MySQL サービスへのクライアント アプリケーションの接続がサポートされます。 TLS は、データベース サーバーとクライアント アプリケーションの間の暗号化されたネットワーク接続を保証する業界標準のプロトコルです。これを使用することで、ユーザーはコンプライアンス要件に準拠することができます。MySQL フレキシブル サーバーに接続するには、証明機関の検証のために[パブリック SSL 証明書](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)をダウンロードする必要があります。 暗号化された接続を使用した接続または SSL の無効化の詳細については、「[暗号化された接続を使用して Azure Database for MySQL - フレキシブル サーバーに接続する](how-to-connect-tls-ssl.md)」を参照してください。

次の例は、mysql コマンド ライン インターフェイスを使用して、フレキシブル サーバーに接続する方法を示しています。 mysql コマンド ラインがまだインストールされていない場合は、まずインストールします。 SSL 接続に必要な DigiCertGlobalRootCA 証明書をダウンロードします。 --ssl-mode=REQUIRED 接続文字列設定を使用して、TLS/SSL 証明書の検証を適用します。 ローカルの証明書ファイルのパスを --ssl-ca パラメーターに渡します。 値を実際のサーバー名とパスワードに置き換えてください。

```bash
sudo apt-get install mysql-client
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl-mode=REQUIRED --ssl-ca=DigiCertGlobalRootCA.crt.pem
```

**パブリック アクセス** を使用してフレキシブル サーバーをプロビジョニングした場合、以下に示したように、[Azure Cloud Shell](https://shell.azure.com/bash) から、プレインストールされた mysql クライアントを使用してフレキシブル サーバーに接続することもできます。

Azure Cloud Shell を使用してフレキシブル サーバーに接続するためには、Azure Cloud Shell からフレキシブル サーバーへのネットワーク アクセスを許可する必要があります。 そのためには、Azure portal で MySQL フレキシブル サーバーの **[ネットワーク]** ブレードに移動し、次のスクリーンショットに示すように、 **[ファイアウォール]** セクションの [Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\) というチェック ボックスをオンにし、[保存] をクリックして設定を保存します。

 > :::image type="content" source="./media/quickstart-create-server-portal/allow-access-to-any-azure-service.png" alt-text="パブリック アクセス ネットワーク構成で Azure Cloud Shell から MySQL フレキシブル サーバーへのアクセスを許可する方法を示すスクリーンショット。":::
 
 
> [!NOTE]
> **[Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\)** チェック ボックスは、開発やテストの用途でのみオンにしてください。 すべての Azure サービス (または資産) に割り当てられた IP アドレスからの接続を許可するようにファイアウォールが構成されます。たとえば、他のユーザーのサブスクリプションからの接続も許可されます。

**[Try it]\(試してみる\)** をクリックして Azure Cloud Shell を起動し、次のコマンドを使用してフレキシブル サーバーに接続します。 実際のサーバー名、ユーザー名、パスワードをコマンドで使用します。 

```azurecli-interactive
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
mysql -h mydemoserver.mysql.database.azure.com -u mydemouser -p --ssl=true --ssl-ca=DigiCertGlobalRootCA.crt.pem
```
> [!IMPORTANT]
> Azure Cloud Shell を使用してフレキシブル サーバーに接続している間は、--ssl-mode=REQUIRED ではなく --ssl = true パラメーターを使用する必要があります。
> その主な理由は、Azure Cloud Shell に付属する MariaDB ディストリビューションに含まれるインストール済み mysql.exe クライアントには --ssl パラメーターが必要ですが、Oracle のディストリビューションに含まれる mysql クライアントには --ssl-mode パラメーターが必要であるためです。

上のコマンドの実行後、フレキシブル サーバーに接続しているときに次のエラー メッセージが表示された場合、ファイアウォール規則の設定に不備があります。前述の [Allow public access from any Azure service within Azure to this server]\(Azure 内の Azure サービスからこのサーバーへのパブリック アクセスを許可する\) の設定が済んでいないか、オプションが保存されていません。 ファイアウォールの設定を再試行して、もう一度やり直してください。

ERROR 2002 (HY000):Can't connect to MySQL server on <servername> (115) (エラー 2002 (HY000): &lt;servername&gt; 上の MySQL サーバーに接続できません (115))

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのリソースが別のクイック スタート/チュートリアルで不要である場合、次のコマンドで削除することができます。

```azurecli-interactive
az group delete --name myresourcegroup
```

新しく作成した 1 つのサーバーを削除するだけの場合は、`az mysql server delete` コマンドを実行できます。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>次のステップ

>[!div class="nextstepaction"]
> [Azure CLI を使用した接続とクエリ](connect-azure-cli.md)
> [暗号化された接続を使用して Azure Database for MySQL - フレキシブル サーバーに接続する](how-to-connect-tls-ssl.md)
> [MySQL を使用して PHP (Laravel) Web アプリを構築する](tutorial-php-database-app.md)
