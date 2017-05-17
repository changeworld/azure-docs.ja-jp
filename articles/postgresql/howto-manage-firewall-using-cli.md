---
title: "Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理 | Microsoft Docs"
description: "Azure CLI を使用して Azure Database for PostgreSQL ファイアウォール規則を作成し、管理する方法について説明します。"
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: postgresql-database
ms.tgt_pltfrm: portal
ms.devlang: azurecli
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 5cb9a3e1fe6e5477e399fd7148fc5366f76cea38
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---
# <a name="create-and-manage-azure-database-for-postgresql-firewall-rules-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL ファイアウォール規則の作成と管理
サーバーレベルのファイアウォール規則を使用すると、管理者は特定の IP アドレスまたは IP アドレス範囲からの Azure Database for PostgreSQL サーバーへのアクセスを管理できます。 便利な Azure CLI コマンドを使用すると、サーバーを管理するためのファイアウォール規則の作成、更新、削除、一覧化、表示などができます。 Azure Database for PostgreSQL ファイアウォールの概要については、「[Azure Database for PostgreSQL Server firewall rules (Azure Database for PostgreSQL サーバーのファイアウォール規則)](concepts-firewall-rules.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)
- [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) コマンドライン ユーティリティのインストール

## <a name="configure-firewall-rules-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL のファイアウォール規則の構成
Azure CLI の **az postgres server firewall-rule** コマンドで、ファイアウォール規則の作成、削除、一覧化、表示、更新などを行います。 この 

## <a name="login-to-azure-and-list-servers"></a>Azure へのログインとサーバーの一覧表示
Azure アカウントで、Azure CLI に安全に接続します。 そのためには、**az login** コマンドを使用します。
1. コマンド ラインから次のコマンドを実行します。
```azurecli
az login
```
このコマンドは、次の手順で使用するコードを出力します。

2. Web ブラウザーを使用してページ [https://aka.ms/devicelogin](https://aka.ms/devicelogin) を開き、コードを入力します。

3. プロンプトで、Azure 資格情報を使用してログインします。

4. ログインの認証が完了すると、サブスクリプションの一覧がコンソールに表示されます。
目的のサブスクリプションの ID をコピーして、このあと使用するために、その ID を現在のサブスクリプションに設定します。
```azurecli
az account set --subscription {your subscription id}
```
5. サブスクリプションとリソース グループの名前がわからない場合は、Azure Databases for PostgreSQL サーバーを一覧表示します。

```azurecli
az postgres server list --resource-group myresourcegroup
```
この一覧に表示される名前属性を確認します。これらの名前は、使用する PostgreSQL サーバーを指定するために使用します。 必要な場合は、名前属性を使用するサーバーの詳細を確認して、名前が正しいかどうかを確認します。

```azurecli
az postgres server show --resource-group myresourcegroup --name mypgserver-20170401
```

## <a name="list-firewall-rules"></a>ファイアウォール規則の一覧表示 
サーバー名とリソース グループ名を使用して、そのサーバー上で既存のサーバー ファイアウォール規則を一覧表示します。 サーバー名属性は、**--name** スイッチではなく **--server** スイッチで指定されることに注意してください。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401
```
JSON 形式の既定では、規則がある場合は出力として規則が一覧表示されます。 出力をもっとわかりやすい表形式にする場合は、**--output table** スイッチを使用することもできます。
```azurecli
az postgres server firewall-rule list --resource-group myresourcegroup --server mypgserver-20170401 --output table
```
## <a name="create-firewall-rule"></a>ファイアウォール規則の作成
Azure Database for PostgreSQL サーバー名とリソース グループ名を使用して、サーバーに新しいファイアウォール規則を作成します。 規則の名前、開始 IP、終了 IP を入力して、アクセスを許可する IP アドレスの範囲を設定します。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.15
```
単一の IP アドレスにだけアクセスを許可する場合は、この例のように、開始 IP と終了 IP に同じアドレスを入力します。
```azurecli
az postgres server firewall-rule create --resource-group myresourcegroup  
--server mypgserver-20170401 --name "Firewall Rule with a Single Address" --start-ip-address 1.1.1.1 --end-ip-address 1.1.1.1
```
設定が完了すると、JSON 形式の既定では、作成したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 設定に失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="update-firewall-rule"></a>ファイアウォール規則の更新 
Azure Database for PostgreSQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を更新します。 更新する既存のファイアウォール規則の名前、開始 IP、終了 IP 属性を入力します。
```azurecli
az postgres server firewall-rule update --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1" --start-ip-address 13.83.152.0 --end-ip-address 13.83.152.1
```
設定が完了すると、JSON 形式の既定では、更新したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 設定に失敗した場合は、代わりにエラー メッセージ テキストが出力されます。
> [!NOTE]
> ファイアウォール規則が存在しない場合は、更新コマンドによって新しい規則が作成されます。

## <a name="show-firewall-rule-details"></a>ファイアウォール規則の詳細の表示
Azure Database for PostgreSQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則の詳細を表示します。 既存のファイアウォール規則の名前を入力します。
```azurecli
az postgres server firewall-rule show --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
設定が完了すると、JSON 形式の既定では、指定したファイアウォール規則の詳細がコマンドの出力として一覧表示されます。 設定に失敗した場合は、代わりにエラー メッセージ テキストが出力されます。

## <a name="delete-firewall-rule"></a>ファイアウォール規則の削除
Azure Database for PostgreSQL サーバー名とリソース グループ名を使用して、サーバーの既存のファイアウォール規則を削除します。 既存のファイアウォール規則の名前を入力します。
```azurecli
az postgres server firewall-rule delete --resource-group myresourcegroup --server mypgserver-20170401 --name "Firewall Rule 1"
```
完了しても出力はありません。 設定に失敗した場合は、エラー メッセージ テキストが返されます。

## <a name="next-steps"></a>次のステップ
- 同様に、Web ブラウザー上で [Azure Portal を使用して Azure Database for PostgreSQL ファイアウォール規則の作成と管理](howto-manage-firewall-using-portal.md)を行うことができます。
- 詳細については、「[Azure Database for MySQL server firewall rules (Azure Database for MySQL サーバーのファイアウォール規則)](concepts-firewall-rules.md)」をご覧ください。
- Azure Database for PostgreSQL サーバーに接続する方法のヘルプについては、「[Connection libraries for Azure Database for PostgreSQL (Azure Database for PostgreSQL の接続ライブラリ)](concepts-connection-libraries.md)」をご覧ください。

