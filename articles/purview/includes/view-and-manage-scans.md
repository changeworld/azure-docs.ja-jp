---
author: whhender
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 6/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ea867a13772b4a0d10d21d3ca732320b404d9c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010891"
---
### <a name="view-your-scans-and-scan-runs"></a>スキャンとスキャンの実行を表示する

既存のスキャンを確認するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) に移動します。 左側のペインで **[Data Map]** タブを選択します。

1. 目的のデータ ソースを選択します。 そのデータ ソースの既存のスキャンの一覧が **[Recent scans]\(最近のスキャン\)** の下に表示されます。または、 **[スキャン]** タブですべてのスキャンを確認できます。

1. 表示する結果を含むスキャンを選択します。

1. このページには、前回のすべてのスキャン実行と、各スキャン実行の状態とメトリックが表示されます。 また、そのスキャンがスケジュールされたスキャンと手動スキャンのどちらかであるか、分類が適用された資産の数、検出された資産の合計数、スキャンの開始時刻と終了時刻、スキャンの実行時間の合計も表示されます。

### <a name="manage-your-scans---edit-delete-or-cancel"></a>スキャンを管理する - 編集、削除、またはキャンセル

スキャンを管理または削除するには、次の操作を行います。

1. [Purview Studio](https://web.purview.azure.com/resource/) に移動します。 左側のペインで **[Data Map]** タブを選択します。

1. 目的のデータ ソースを選択します。 そのデータ ソースの既存のスキャンの一覧が **[Recent scans]\(最近のスキャン\)** の下に表示されます。または、 **[スキャン]** タブですべてのスキャンを確認できます。

1. 管理するスキャンを選択します。 スキャンを編集するには、 **[Edit scan]\(スキャンの編集\)** を選択します。

1. 進行中のスキャンをキャンセルするには、 **[スキャンの実行のキャンセル]** を選択します。

1. スキャンを削除するには、 **[スキャンの削除]** を選択します。

> [!NOTE]
> * スキャンを削除しても、以前のスキャンから作成されたカタログ アセットは削除されません。
> * Purview の [スキーマ] タブの説明を編集した後に、ソース テーブルが変更され、ソース テーブルを再スキャンした場合、アセットはスキーマの変更によって更新されなくなります。
