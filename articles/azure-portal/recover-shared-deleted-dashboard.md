---
title: Azure portal で削除されたダッシュボードを復旧する
description: Azure portal で発行されたダッシュボードを削除した場合、そのダッシュボードを復旧できます。
ms.date: 01/21/2020
ms.topic: troubleshooting
ms.openlocfilehash: 095964691a3cb22f8a805af2e8fe37af4c47cb28
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745623"
---
# <a name="recover-a-deleted-dashboard-in-the-azure-portal"></a>Azure portal で削除されたダッシュボードを復旧する

パブリック Azure クラウドを使用していて、Azure portal で "_発行された_" ダッシュボードを削除した場合は、削除から 14 日以内であればそのダッシュボードを回復できます。 Azure Government クラウドを使用している場合、またはダッシュボードが発行されていない場合は、回復できないため、再構築する必要があります。 ダッシュボードの発行の詳細については、「[ダッシュボードの発行](azure-portal-dashboard-share-access.md#publish-dashboard)」を参照してください。 発行されたダッシュボードを復旧するには、次の手順に従います。

1. [Azure portal] メニューで、 **[リソース グループ]** を選択し、ダッシュボードを発行したリソース グループを選択します (既定では、**ダッシュボード** という名前です)。

1. **[アクティビティ ログ]** で、 **[ダッシュボードの削除]** 操作を展開します。 **[変更履歴]** タブを選択し、 **[\<deleted resource\>]** を選択します。

    ![変更履歴タブのスクリーンショット](media/recover-shared-deleted-dashboard/change-history-tab.png)

1. 左側のペインの内容を選択してコピーし、 _.json_ ファイル拡張子の付いたテキストファイルに保存します。 ポータルは、JSON ファイルを使用してダッシュボードを再作成します。

    ![変更履歴の差分のスクリーンショット](media/recover-shared-deleted-dashboard/change-history-diff.png)

1. [Azure portal] メニューの **[ダッシュボード]** を選択し、 **[アップロード]** を選択します。

    ![ダッシュボードのアップロードのスクリーンショット](media/recover-shared-deleted-dashboard/dashboard-upload.png)

1. 保存した JSON ファイルを選択します。 ポータルは、削除されたダッシュボードと同じ名前と要素を使用してダッシュボードを再作成します。

1. **[共有]** を選択してダッシュボードを発行し、適切なアクセスの制御を再確立します。

    ![ダッシュボードの共有のスクリーンショット](media/recover-shared-deleted-dashboard/dashboard-share.png)
