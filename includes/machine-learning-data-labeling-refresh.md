---
author: sgilley
ms.service: machine-learning
ms.topic: include
ms.date: 09/23/2021
ms.author: sdgilley
ms.openlocfilehash: 2a1c7c65721483e851a7686c38736f0664b965ad
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129367448"
---
データセットに新しいファイルを追加する予定がある場合は、増分更新を使用して、これらの新しいファイルをプロジェクトに追加します。   **増分更新** が有効になっている場合、ラベル付けの完了率に基づいて、プロジェクトに追加する新しい画像がないかどうかデータセットが定期的にチェックされます。   プロジェクトに最大 50 万個のファイルが含まれている場合、新しいデータのチェックは停止されます。

プロジェクトにさらに多くのファイルを追加するには、[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使用して、Blob Storage 内の適切なフォルダーにアップロードします。 

プロジェクトでデータストア内の新しいデータを継続的に監視する場合は、 **[増分更新を有効にする]** を選択します。 有効にした場合、データが 1 日 1 回プロジェクトにプルされます。 データストアに新しいデータを追加した後は、プロジェクトに表示されるまで待機する必要があります。  データが最後に更新されたときのタイムスタンプは、プロジェクトの **[詳細]** タブの **[増分更新]** セクションで確認できます。
データストアの新しいファイルをプロジェクトに追加しない場合は、選択解除します。