---
title: Azure Blockchain Service メンバーを作成する - Azure CLI
description: Azure CLI を使用してブロックチェーン コンソーシアムの Azure Blockchain Service メンバーを作成します。
ms.date: 01/23/2020
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 703444b6077c2301e1ffec77c8096fb76ddaa731
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759928"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>クイック スタート:Azure CLI を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する

このクイックスタートでは、Azure CLI を使用して Azure Blockchain Service 内にブロックチェーンの新しいメンバーとコンソーシアムをデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell を起動する

Azure Cloud Shell は無料のインタラクティブ シェルです。この記事の手順は、Azure Cloud Shell を使って実行することができます。 一般的な Azure ツールが事前にインストールされており、アカウントで使用できるように構成されています。

Cloud Shell を開くには、コード ブロックの右上隅にある **[使ってみる]** を選択します。 [https://shell.azure.com/bash](https://shell.azure.com/bash) に移動して、別のブラウザー タブで Cloud Shell を起動することもできます。 **[コピー]** を選択してコードのブロックをコピーし、Cloud Shell に貼り付けてから、Enter キーを押して実行します。

CLI をローカルにインストールして使用する場合、このクイックスタートでは、Azure CLI バージョン 2.0.51 以降が必要です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](https://docs.microsoft.com/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>ブロックチェーン メンバーを作成する

Azure Blockchain Service メンバーは、プライベート コンソーシアム ブロックチェーン ネットワーク内のブロックチェーン ノードです。 メンバーをプロビジョニングするときは、コンソーシアム ネットワークを作成するか、またはコンソーシアム ネットワークに参加することができます。 コンソーシアム ネットワークには少なくとも 1 つのメンバーが必要です。 参加者が必要とするブロックチェーン メンバーの数は、シナリオによって異なります。 コンソーシアムの参加者は、1 つまたは複数のブロックチェーン メンバーを有するか、または他の参加者との間でメンバーを共有することができます。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。

渡す必要があるいくつかのパラメーターとプロパティがあります。 パラメーターの例は、実際の値に置き換えてください。

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| パラメーター | 説明 |
|---------|-------------|
| **resource-group** | Azure Blockchain Service リソースが作成されるリソース グループ名。 前のセクションで作成したリソース グループを使用します
| **name** | Azure Blockchain Service のブロックチェーン メンバーを識別する一意の名前。 名前はパブリック エンドポイント アドレスに使用されます。 たとえば、「 `myblockchainmember.blockchain.azure.com` 」のように入力します。
| **location** | ブロックチェーン メンバーが作成される Azure リージョン。 たとえば、「 `westus2` 」のように入力します。 ユーザーや他の Azure アプリケーションに最も近い場所を選択します。
| **password** | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。
| **consortium** | 参加または作成するコンソーシアムの名前。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。
| **consortiumAccountPassword** | コンソーシアム アカウントのパスワードは、メンバー アカウントのパスワードとも呼ばれます。 メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。
| **skuName** | レベルの種類。 Standard には S0 を使用し、Basic には B0 を使用します。

ブロックチェーン メンバーとサポート リソースの作成には約 10 分かかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したブロックチェーン メンバーは、次のクイックスタートまたはチュートリアルに使用できます。 必要なくなったら、このクイックスタートのために作成した `myResourceGroup` リソース グループを削除することによって、リソースを削除できます。

次のコマンドを実行して、リソース グループとすべての関連リソースを削除します。

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Blockchain Service のメンバーと新しいコンソーシアムをデプロイしました。 次は、Azure Blockchain Development Kit for Ethereum を使用して Azure Blockchain Service のメンバーに接続するクイックスタートに挑戦してみてください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用して Azure Blockchain Service に接続する](connect-vscode.md)
