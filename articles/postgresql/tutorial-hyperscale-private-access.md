---
title: プライベート アクセス (プレビュー) を使用するサーバー グループを作成する - Hyperscale (Citus) - Azure Database for PostgreSQL
description: サーバー グループのプライベート エンドポイントに VM を接続する
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: tutorial
ms.date: 10/15/2021
ms.openlocfilehash: 1ae844b4778f8a117bd8ae8807fa28849d9d310d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131056350"
---
# <a name="create-server-group-with-private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL でプライベート アクセス (プレビュー) を使用するサーバー グループを作成する - Hyperscale (Citus)

このチュートリアルでは、仮想マシンと Hyperscale (Citus) サーバー グループを作成し、それらの間に[プライベート アクセス](concepts-hyperscale-private-access.md)を確立します。

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

最初に、リソース グループと仮想ネットワークを設定します。 それにより、サーバー グループと仮想マシンが保持されます。

```sh
az group create \
    --name link-demo \
    --location eastus

az network vnet create \
    --resource-group link-demo \
    --name link-demo-net \
    --address-prefix 10.0.0.0/16

az network nsg create \
    --resource-group link-demo \
    --name link-demo-nsg

az network vnet subnet create \
    --resource-group link-demo \
    --vnet-name link-demo-net \
    --name link-demo-subnet \
    --address-prefixes 10.0.1.0/24 \
    --network-security-group link-demo-nsg
```

## <a name="create-a-virtual-machine"></a>仮想マシンの作成

デモンストレーション用に、Debian Linux を実行する仮想マシンと、`psql` PostgreSQL クライアントを使用します。

```sh
# provision the VM
az vm create \
    --resource-group link-demo \
    --name link-demo-vm \
    --vnet-name link-demo-net \
    --subnet link-demo-subnet \
    --nsg link-demo-nsg \
    --public-ip-address link-demo-net-ip \
    --image debian \
    --admin-username azureuser \
    --generate-ssh-keys

# install psql database client
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts \
        "sudo touch /home/azureuser/.hushlogin" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get update" \
        "sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-client"
```

## <a name="create-a-server-group-with-a-private-link"></a>プライベート リンクを使用するサーバー グループを作成する

1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。

2. **[新規]** ページで **[データベース]** を選択し、 **[データベース]** ページで **[Azure Database for PostgreSQL]** を選択します。

3. デプロイ オプションについては、 **[Hyperscale (Citus) サーバー グループ]** の下にある **[作成]** ボタンを選択します。

4. 新しいサーバーの詳細フォームには次の情報を入力してください。

    - **リソース グループ**: `link-demo`
    - **サーバー グループ名**: `link-demo-sg`
    - **場所**: `East US`
    - **パスワード**: (任意)

    > [!NOTE]
    >
    > サーバー グループ名によって DNS エントリが作成されるため、Azure 全体で一意のものにしてください。 `link-demo-sg` を使用できない場合は、別の名前を選択し、それに応じて以下の手順を調整してください。

5. **[サーバー グループの構成]** を選択してから **Basic** プランを選択し、 **[保存]** を選択します。

6. **ページの下部にある [Next: Networking]\(次へ: ネットワーク\)** を選択します。

7. **[プライベート アクセス (プレビュー)]** を選択します。

    > [!NOTE]
    >
    > プライベート アクセスは、[特定のリージョン](concepts-hyperscale-limits.md#regions)でのみプレビューで使用できます。
    >
    > サーバー グループが許可されたリージョン内にあるにもかかわらず、サーバー グループに対してプライベート アクセス オプションを選択できない場合は、Azure [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開き、Azure サブスクリプション ID を含めて、アクセス権を取得してください。

8. **[プライベート エンドポイントの作成]** という画面が表示されます。 こちらの値を入力して **[OK]** を選択します。

    - **リソース グループ**: `link-demo`
    - **場所**: `(US) East US`
    - **名前**: `link-demo-sg-c-pe1`
    - **対象サブリソース**: `coordinator`
    - **仮想ネットワーク**: `link-demo-net`
    - **サブネット**: `link-demo-subnet`
    - **プライベート DNS ゾーンと統合する**: はい

9. プライベート エンドポイントを作成したら、 **[確認と作成]** を選択して、Hyperscale (Citus) サーバー グループを作成します。

## <a name="access-the-server-group-privately-from-the-virtual-machine"></a>仮想マシンからサーバー グループにプライベートにアクセスする

プライベート リンクを使用すると、仮想マシンからサーバー グループに接続でき、外部ホストがそうするのを防止します。 この手順では、仮想マシン上の `psql` データベース クライアントがサーバー グループのコーディネーター ノードと通信できることを確認します。

```sh
# save db URI
#
# obtained from Settings -> Connection Strings in the Azure portal
#
# replace {your_password} in the string with your actual password
PG_URI='host=c.link-demo-sg.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require'

# attempt to connect to server group with psql in the virtual machine
az vm run-command invoke \
    --resource-group link-demo \
    --name link-demo-vm \
    --command-id RunShellScript \
    --scripts "psql '$PG_URI' -c 'SHOW citus.version;'" \
    --query 'value[0].message' \
    | xargs printf
```

Citus のバージョン番号が出力に表示されるはずです。 その場合は、psql がコマンドを実行でき、プライベート リンクが機能しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想マシンと Hyperscale (Citus) サーバー グループの間にプライベート リンクを作成する方法について説明しました。 これで、リソースをプロビジョニング解除できます。

リソース グループを削除すると、内部のリソースがプロビジョニング解除されます。

```sh
az group delete --resource-group link-demo

# press y to confirm
```

## <a name="next-steps"></a>次のステップ

* [プライベート アクセス](concepts-hyperscale-private-access.md) (プレビュー) の詳細を学習する
* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)について学習する
* [仮想ネットワーク](../virtual-network/concepts-and-best-practices.md)について学習する
* [プライベート DNS ゾーン](../dns/private-dns-overview.md)について学習する