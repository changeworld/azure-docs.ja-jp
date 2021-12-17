---
title: 'クイックスタート: Azure portal を使用して Azure Data Explorer プールを作成する (プレビュー)'
description: このガイドの手順に従って、Azure portal を使用して Azure Data Explorer プールを作成します。
ms.topic: quickstart
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: shsagir
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9a24d9bda03cdb456d92461546b606572f127bcb
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131467262"
---
# <a name="quickstart-create-a-data-explorer-pool-using-the-azure-portal-preview"></a>クイックスタート: Azure portal を使用して Azure Data Explorer プールを作成する (プレビュー)

Azure Synapse Data Explorer は、アプリケーション、Web サイト、IoT デバイスなどからの大量のデータ ストリーミングをリアルタイムに分析するための高速でフル マネージド data analytics サービスです。 Azure Data Explorer を使用して、まず Azure Data Explorer プールを作成します。

この記事では、Azure portal を使用して、Synapse ワークスペース内に Azure Data Explorer プールを作成する手順について説明します。

> [!IMPORTANT]
> Azure Data Explorer インスタンスの料金は、それを使用しているかどうかに関係なく、分単位で課金されます。 必ず、Azure Data Explorer インスタンスの使用を終了した後にシャットダウンするか、短いタイムアウトを設定してください。 詳細については、「**リソースをクリーンアップする**」を参照してください。

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Synapse ワークスペース](../quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

[Azure ポータル](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Synapse ワークスペースに移動する

1. Synapse ワークスペースに移動します。ここでは、検索バーにサービス名 (またはリソース名を直接) 入力して、専用 Azure Data Explorer プールを作成します。

    ![「Synapse ワークスペース」と入力されている Azure portal の検索バー。](../media/quickstart-create-sql-pool/create-sql-pool-00a.png)

1. ワークスペースの一覧で、開くワークスペースの名前 (または名前の一部) を入力します。 この例では、**contosoanalytics** という名前のワークスペースを使用します。

    ![名前に Contoso を含むワークスペースを表示するためにフィルター処理された Synapse ワークスペースの一覧。](../media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="create-a-new-data-explorer-pool"></a>新しい Azure Data Explorer プールを作成する

1. Azure Data Explorer プールを作成する Synapse ワークスペースで、 **[New Data Explorer pool]\(新しい Azure Data Explorer プール\)** を選択します。

    ![[概要] セクションが強調表示された Azure portal ホーム ページを開きます。](media/create-data-explorer-pool-portal/goto-data-explorer-pool-portal.png)

1. **[基本]** タブで、以下の詳細を入力します。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | Azure Data Explorer プール名 | contosodataexplorer | これは、Azure Data Explorer プールに付けられる名前です。 |
    | ワークロード | コンピューティング最適化 | このワークロードにより、CPU から SSD へのストレージの比率が高くなります。 |
    | ノード サイズ | 小 (4 コア) | このクイックスタートのコストを削減するために、最小サイズに設定します |

    ![Azure portal の新しい Azure Data Explorer プールのための基本](media/create-data-explorer-pool-portal/create-data-explorer-pool-basics-portal.png)

    > [!IMPORTANT]
    > Azure Data Explorer プールで使用できる名前には、特定の制限があることに注意してください。 この名前には、4 文字から 15 文字の間で小文字と数字のみを含める必要があります。また、先頭は文字で始める必要があります。

1. **[次へ: 追加の設定]** を選択します。 次の設定を使用し、残りの設定は既定値のままにします。

    | 設定 | 推奨値 | 説明 |
    |--|--|--|
    | Scaling | 手動でのスケーリング | このクイックスタートでは、自動スケーリングは必要ありません |
    | Number count | 2 | このクイックスタートのコストを削減するために、最小サイズに設定します |

    ![Azure portal の新しい Azure Data Explorer プールの詳細設定](media/create-data-explorer-pool-portal/create-data-explorer-pool-advanced-settings-portal.png)

1. **[次へ: タグ]** を選択します。 タグは追加しないでください。
1. **[Review + create]\(レビュー + 作成\)** を選択します。
1. 詳細情報が正しいことを確認し、 **[作成]** を選択します。

    Azure Data Explorer プールのプロビジョニング プロセスが開始されます。

    ![Azure portal の新しい Azure Data Explorer プールを作成する](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. プロビジョニングが完了したら、ワークスペースの **[概要]** ページに戻り、新しい Azure Data Explorer プールのウェルカム ページが表示されることを確認します。

    ![Azure portal の新しい Azure Data Explorer プールの一覧](media/create-data-explorer-pool-portal/verify-data-explorer-pool-portal.png)

## <a name="clean-up-data-explorer-pool-resources-using-the-azure-portal"></a>Azure portal を使用して、Azure Data Explorer プール リソースをクリーン アップする

次の手順に従い、Azure portal を使用して、Azure Data Explorer プールをワークスペースから削除します。

> [!WARNING]
> Azure Data Explorer プールを削除すると、ワークスペースから分析エンジンが削除されます。 プールに接続することはできなくなります。また、この削除されたプールを使用するすべてのクエリ、パイプライン、ノートブックは動作しなくなります。

### <a name="delete-the-data-explorer-pool"></a>Azure Data Explorer プールを削除する

1. ワークスペース内の Azure Data Explorer プールに移動します。
1. Azure Data Explorer プールを削除するには (この場合は **contosodataexplorer**)、 **[詳細 [...]]**  >  **[削除]** の順に選択します。

    ![最近作成されたプールが選択されている Azure Data Explorer プールの一覧。](media/create-data-explorer-pool-portal/create-data-explorer-pool-portal.png)

1. 削除を確認するために、削除するプールの名前を入力し、 **[削除]** を選択します。

    ![最近作成したプールの削除を確認します。](media/create-data-explorer-pool-portal/confirm-deletion-data-explorer-pool-portal.png)

1. プロセスが正常に完了したら、プールが一覧に表示されなくなったことを確認します。

## <a name="next-steps"></a>次のステップ

- [インジェストの概要](ingest-data/data-explorer-ingest-data-overview.md)
- [Data Explorer を使用して分析する](../get-started-analyze-data-explorer.md)
- [Data Explorer プールを監視する](data-explorer-monitor-pools.md)
