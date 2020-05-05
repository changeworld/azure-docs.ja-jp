---
title: Azure Resource Manager テンプレートを使用して Azure Blockchain Service メンバーを作成する
description: Azure Resource Manager テンプレートを使用して Azure Blockchain Service メンバーを作成する方法を学習します。
services: azure-resource-manager
author: PatAltimore
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: patricka
ms.date: 04/22/2020
ms.openlocfilehash: db5cabd82ebfed3b1de7659e4b9450ccee0ff001
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82115297"
---
# <a name="quickstart-create-an-azure-blockchain-service-member-using-an-azure-resource-manager-template"></a>クイック スタート:Azure Resource Manager テンプレートを使用して Azure Blockchain Service メンバーを作成する

このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Azure Blockchain Service 内に新しいブロックチェーン メンバーとコンソーシアムをデプロイします。

Azure Blockchain Service メンバーは、プライベート コンソーシアム ブロックチェーン ネットワーク内のブロックチェーン ノードです。 メンバーをプロビジョニングするときは、コンソーシアム ネットワークを作成するか、またはコンソーシアム ネットワークに参加することができます。 コンソーシアム ネットワークには少なくとも 1 つのメンバーが必要です。 参加者が必要とするブロックチェーン メンバーの数は、シナリオによって異なります。 コンソーシアムの参加者は、1 つまたは複数のブロックチェーン メンバーを有するか、または他の参加者との間でメンバーを共有することができます。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="prerequisites"></a>前提条件

[なし] :

## <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/201-blockchain-asaservice/)からのものです。

[!code-json[<Azure Resource Manager template create blockchain member>](~/quickstart-templates/201-blockchain-asaservice/azuredeploy.json)]

テンプレート内に定義されている Azure リソース:

* [**Microsoft.Blockchain/blockchainMembers**](https://docs.microsoft.com/azure/templates/microsoft.blockchain/blockchainmembers)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインして、テンプレートを開くには、次のリンクを選択します。

    [![Azure へのデプロイ](./media/create-member-template/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-blockchain-asaservice%2Fazuredeploy.json)

1. Azure Blockchain Service のメンバーの設定を選択します。

    設定 | 説明
    --------|------------
    サブスクリプション | サービスに使用する Azure サブスクリプションを選択します。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
    Resource group | 新しいリソース グループ名を作成するか、サブスクリプションの既存のリソース グループ名を選択します。
    場所 | メンバーを作成する場所を選択します。 コンソーシアムのメンバーの場所は全員同じにする必要があります。 デプロイに使用できる場所は *westeurope、eastus、southeastasia、westeurope、northeurope、westus2*、および *japaneast* です。
    メンバー名 | Azure Blockchain Service のメンバーの一意の名前を選択します。 ブロックチェーン メンバー名に使用できるのは、小文字と数字のみです。 先頭の文字は英字にする必要があります。 値の長さは 2 から 20 文字にする必要があります。
    コンソーシアム名 | 一意の名前を入力します。 コンソーシアムの詳細については、「[Azure Blockchain Service のコンソーシアム](consortium.md)」を参照してください。
    メンバー パスワード | メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。
    SKU レベル | 新しいサービスの価格レベル。 **Standard** と **Basic** のいずれかのレベルを選択します。 開発、テスト、概念実証には、*Basic* レベルを使用します。 運用グレードのデプロイには、*Standard* レベルを使用します。 また、Blockchain Data Manager を使用している場合や大量のプライベート トランザクションを送信する場合にも、*Standard* レベルを使用する必要があります。 メンバーの作成後に価格レベルを Basic と Standard の間で変更することはできません。
    SKU 名 | 新しいサービスのノード構成とコスト。

1. **[購入]** を選択してテンプレートをデプロイします。

  テンプレートをデプロイするために、ここでは Azure portal を使用します。 Azure PowerShell、Azure CLI、および REST API を使用することもできます。 他のデプロイ方法については、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したブロックチェーン メンバーは、次のクイックスタートまたはチュートリアルに使用できます。 必要がなくなったら、このクイックスタートのために作成したリソース グループを削除することによって、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
2. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Blockchain Service のメンバーと新しいコンソーシアムをデプロイしました。 次は、Azure Blockchain Development Kit for Ethereum を使用して Azure Blockchain Service のメンバーに接続するクイックスタートに挑戦してみてください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用して Azure Blockchain Service に接続する](connect-vscode.md)