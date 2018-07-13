---
title: Azure CLI を使用した Azure Database for PostgreSQL VNet サービス エンドポイントおよびルールの作成と管理 | Microsoft Docs
description: この記事では、Azure CLI コマンド ラインを使用して Azure Database for PostgreSQL VNet サービス エンドポイントおよびルールを作成し、管理する方法について説明します。
services: postgresql
author: mbolz
ms.author: mbolz
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.devlang: azure-cli
ms.topic: article
ms.date: 06/01/2018
ms.openlocfilehash: 7312000d1f22af3eb0091b46caac2c9607231513
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38531703"
---
# <a name="create-and-manage-azure-database-for-postgresql-vnet-service-endpoints-using-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL VNet サービス エンドポイントの作成と管理
Virtual Network (VNet) サービス エンドポイントおよびルールは、仮想ネットワークのプライベート アドレス空間を Azure Database for PostgreSQL サーバーに拡張します。 便利な Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サーバーを管理するための VNet サービス エンドポイントおよびルールの作成、更新、削除、一覧化、表示を行うことができます。 制限事項も含め、Azure Database for PostgreSQL VNet サービス エンドポイントの概要については、[Azure Database for PostgreSQL サーバー VNet サービス エンドポイント](concepts-data-access-and-security-vnet.md)に関する記事をご覧ください。 VNet サービス エンドポイントは、Azure Database for PostgreSQL でサポートされるすべてのリージョンで、パブリック プレビューとして利用できます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure CLI 2.0](/cli/azure/install-azure-cli) コマンド ライン ユーティリティをインストールするか、ブラウザーで Azure Cloud Shell を使用します。
- [Azure Database for PostgreSQL サーバーとデータベース](quickstart-create-server-database-azure-cli.md)。

> [!NOTE]
> VNet サービス エンドポイントは、汎用サーバーとメモリ最適化サーバーでのみサポートされています。

## <a name="configure-vnet-service-endpoints-for-azure-database-for-postgresql"></a>Azure Database for PostgreSQL の VNet サービス エンドポイントを構成する
Virtual Network を構成するには、[az network vnet](https://docs.microsoft.com/cli/azure/network/vnet?view=azure-cli-latest) コマンドを使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、この記事では、Azure CLI バージョン 2.0 以降を実行していることが要件です。 インストールされているバージョンを確認するには、`az --version` コマンドを実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

CLI をローカルで実行している場合は、[az login](/cli/azure/authenticate-azure-cli?view=interactive-log-in) コマンドを使用してアカウントにログインする必要があります。 対応するサブスクリプション名のコマンド出力で **id** プロパティを確認します。
```azurecli-interactive
az login
```
`az extension add --name rdbms-vnet` コマンドを使用して、Azure Database for PostgreSQL VNet サービス エンドポイント向けの CLI 拡張機能をインストールします。 
```azurecli-interactive
az extension add --name rdbms-vnet
```

`az extension list` コマンドを実行して、CLI 拡張機能のインストールを確認します。
```azurecli-interactive
az extension list
```
このコマンドの出力には、インストールされているすべての拡張機能が示されます。 Azure Database for PostgreSQL の CLI 拡張機能は次のとおりです。

 { "extensionType": "whl", "name": "rdbms-vnet", "version": "10.0.0" }

> [!NOTE]
> CLI 拡張機能をアンインストールするには、`az extension remove -n rdbms-vnet` コマンドを実行します。 

複数のサブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。 [az account set](/cli/azure/account#az_account_set) コマンドを使用して、アカウントの特定のサブスクリプション ID を選択します。 そのサブスクリプションの **az login** 出力の **id** プロパティで、subscription id プレースホルダーを置き換えます。

- アカウントには、仮想ネットワークとサービス エンドポイントを作成するためのアクセス許可が必要です。

サービス エンドポイントは、仮想ネットワークへの書き込みアクセス権を持つユーザーが仮想ネットワーク上で個別に構成できます。

Azure サービス リソースへのアクセスを VNet に限定するには、ユーザーが、追加されるサブネットの "Microsoft.Network/JoinServicetoaSubnet" へのアクセス許可を持っている必要があります。 このアクセス許可は、既定では組み込みのサービス管理者のロールに含まれ、カスタム ロールを作成することで変更できます。

[組み込みロール](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles)と、特定のアクセス許可を[カスタム ロール](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles)に割り当てる方法の詳細をご覧ください。

Vnet と Azure サービス リソースのサブスクリプションは、同じでも異なっていてもかまいません。 このプレビューの時点では、VNet と Azure サービス リソースのサブスクリプションが異なる場合、リソースは同じ Active Directory (AD) テナントに存在する必要があります。

> [!IMPORTANT]
> 下記のサンプル スクリプトを実行したり、サービス エンドポイントを構成したりする前に、サービス エンドポイントの構成と考慮事項について、この記事を読むことを強くお勧めします。 **Virtual Network サービス エンドポイント** : [Virtual Network サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md) は、プロパティ値に 1 つ以上の正式な Azure サービスの種類名が含まれるサブネットです。 VNet サービス エンドポイントでは、SQL Database という名前の Azure サービスを参照する **Microsoft.Sql** というサービス種類名を使用します。 このサービス タグは、Azure SQL Database、Azure Database for PostgreSQL および MySQL サービスにも適用されます。 VNet サービス エンドポイントに **Microsoft.Sql** サービス タグを適用すると、サブネット上の Azure SQL Database、Azure Database for PostgreSQL、Azure Database for MySQL の各サーバーを含むすべての Azure Database サービスに対してサービス エンドポイント トラフィックが構成されることに注意してください。 
> 

### <a name="sample-script-to-create-an-azure-database-for-postgresql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Azure Database for PostgreSQL データベース、VNet、VNet サービス エンドポイントを作成し、VNet ルールを使用してサブネットのサーバーをセキュリティで保護するサンプル スクリプト
このサンプル スクリプトでは、強調表示された行を変更して、管理者のユーザー名とパスワードをカスタマイズします。 `az account set --subscription` コマンドで使用するサブスクリプション ID を、自分のサブスクリプション ID に置き換えてください。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/create-postgresql-server.sh?highlight=5,20 "Create an Azure Database for PostgreSQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
スクリプト サンプルの実行後は、次のコマンドを使用してリソース グループとすべての関連リソースを削除することができます。
[!code-azurecli-interactive[main](../../cli_scripts/postgresql/create-postgresql-server-vnet/delete-postgresql.sh "Delete the resource group.")]
