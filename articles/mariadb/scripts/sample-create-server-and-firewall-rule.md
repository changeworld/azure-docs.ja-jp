---
title: Azure CLI スクリプト - Azure Database for MariaDB を作成する
description: このサンプル CLI スクリプトは、Azure Database for MariaDB サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。
services: mariadb
author: ajlam
ms.author: andrela
editor: jasonwhowell
ms.service: mariadb
ms.devlang: azure-cli
ms.topic: sample
ms.custom: mvc
ms.date: 11/28/2018
ms.openlocfilehash: 80609035933e45ea9b7cc911a908f6ed24b9f6a7
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585243"
---
# <a name="create-a-mariadb-server-and-configure-a-firewall-rule-using-the-azure-cli"></a>Azure CLI を使用して MariaDB サーバーを作成し、ファイアウォール規則を構成する
このサンプル CLI スクリプトは、Azure Database for MariaDB サーバーを作成して、サーバー レベルのファイアウォール規則を構成するものです。 スクリプトが正常に実行されると、すべての Azure サービスと構成済みの IP アドレスから MariaDB サーバーにアクセスできるようになります。

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

CLI をローカルで実行する場合、この記事では、Azure CLI バージョン 2.0 以降が必要です。 `az --version` を実行してバージョンを確認します。 「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照して Azure CLI をインストールするか、お使いのバージョンをアップグレードしてください。

## <a name="sample-script"></a>サンプル スクリプト
このサンプル スクリプトでは、強調表示された行を編集して、管理者のユーザー名とパスワードを自分のユーザー名とパスワードに更新します。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/create-mariadb-server-and-firewall-rule.sh?highlight=15-16 "Create an Azure Database for mariadb, and server-level firewall rule.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプトの実行後は、次のコマンドを使用してリソース グループとそこに関連付けられているすべてのリソースを削除してください。
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/create-mariadb-server-and-firewall-rule/delete-mariadb.sh "Delete the resource group.")]

## <a name="script-explanation"></a>スクリプトの説明
このスクリプトで使用されているコマンドを次の表にまとめました。

| **コマンド** | **メモ** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | データベースのホストとなる MariaDB サーバーを作成します。 |
| [az mariadb server firewall create](/cli/azure/mariadb/server/firewall-rule#az-mariadb-server-firewall-rule-create) | 対象のサーバーとそこにあるデータベースに対し、入力した IP アドレス範囲からアクセスできるようにするファイアウォール規則を作成します。 |
| [az group delete](/cli/azure/group#az-group-delete) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次の手順
- Azure CLI に関する詳細を読む: [Azure CLI ドキュメント](/cli/azure)
- 「[Azure Database for MariaDB 用の Azure CLI サンプル](../sample-scripts-azure-cli.md)」にあるその他のスクリプトを試す
