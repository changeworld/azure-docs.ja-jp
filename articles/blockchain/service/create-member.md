---
title: Azure Blockchain Service メンバーを作成する - Azure portal
description: Azure portal を使用してブロックチェーン コンソーシアムの Azure Blockchain Service メンバーを作成します。
ms.date: 11/18/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 90f859e4d2e2621afad9cab11d66e81018bd3147
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455787"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>クイック スタート:Azure portal を使用して Azure Blockchain Service ブロックチェーン メンバーを作成する

このクイックスタートでは、Azure portal を使用して Azure Blockchain Service 内にブロックチェーンの新しいメンバーとコンソーシアムをデプロイします。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>ブロックチェーン メンバーを作成する

新規または既存のコンソーシアムで Quorum 台帳プロトコルを実行するブロックチェーン メンバーを作成します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. Azure Portal の左上隅にある **[リソースの作成]** を選択します。
1. **[ブロックチェーン]**  >  **[Azure Blockchain Service (プレビュー)]** の順に選択します。

    ![サービスの作成](./media/create-member/create-member.png)

    Setting | 説明
    --------|------------
    Subscription | サービスに使用する Azure サブスクリプションを選択します。 複数のサブスクリプションをお持ちの場合は、リソースの課金対象となるサブスクリプションを選択してください。
    Resource group | 新しいリソース グループ名を作成するか、サブスクリプションの既存のリソース グループ名を選択します。
    リージョン | メンバーを作成するリージョンを選択します。 コンソーシアムのメンバーの場所は全員同じにする必要があります。
    Protocol | プレビュー版の Azure Blockchain Service は現在、Quorum プロトコルをサポートしています。
    コンソーシアム | 新しいコンソーシアムの一意の名前を入力します。 招待状を介してコンソーシアムに参加する場合には、参加しようとしているコンソーシアムを選択します。
    名前 | Azure Blockchain Service のメンバーの一意の名前を選択します。 ブロックチェーン メンバー名に使用できるのは、小文字と数字のみです。 先頭の文字は英字にする必要があります。 値の長さは 2 から 20 文字にする必要があります。
    メンバー アカウントのパスワード | メンバー アカウントのパスワードは、メンバー用に作成される Ethereum アカウントの秘密キーの暗号化に使用されます。 メンバー アカウントとメンバー アカウントのパスワードをコンソーシアムの管理に使用します。
    説明 | コンソーシアムの説明。
    価格 | 新しいサービスのノード構成とコスト。 **[変更]** リンクを選択して **[Standard]** と **[Basic]** のいずれかのレベルを選択します。
    ノードのパスワード | メンバーの既定のトランザクション ノードのパスワード。 このパスワードは、ブロックチェーン メンバーの既定のトランザクション ノード パブリック エンドポイントに接続する際の基本認証に使用します。

1. **[確認および作成]** を選択して設定を検証します。 **[作成]** を選択して、サービスをプロビジョニングします。 プロビジョニングには約 10 分かかります。
1. デプロイ プロセスを監視するには、ツール バーの **[通知]** を選択します。
1. デプロイ後、ブロックチェーン メンバーに移動します。

**[概要]** を選択すると、RootContract アドレスやメンバー アカウントなど、サービスに関する基本情報を確認できます。

![ブロックチェーン メンバーの概要](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成したメンバーは、次のクイックスタートまたはチュートリアルに使用できます。 必要なくなったら、このクイックスタートのために作成した `myResourceGroup` リソース グループを削除することによって、リソースを削除できます。

リソース グループを削除するには:

1. Azure Portal で、左側のナビゲーション ウィンドウの **[リソース グループ]** に移動し、削除するリソース グループを選択します。
2. **[リソース グループの削除]** を選択します。 リソース グループ名を入力して削除を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このクイックスタートでは、Azure Blockchain Service のメンバーと新しいコンソーシアムをデプロイしました。 次は、Azure Blockchain Development Kit for Ethereum を使用して Azure Blockchain Service 上のコンソーシアムに接続するクイックスタートに挑戦してみてください。

> [!div class="nextstepaction"]
> [Visual Studio Code を使用して Azure Blockchain Service に接続する](connect-vscode.md)
