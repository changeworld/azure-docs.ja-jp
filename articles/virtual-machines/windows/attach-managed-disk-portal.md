---
title: マネージド データ ディスクを Windows VM に接続する - Azure
description: Azure portal を使用して Windows VM にマネージド データ ディスクを接続する方法について説明します。
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7b6b29307ce936ee6b95f68d15f383fc9b2f8c40
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692628"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Azure portal を使用して Windows VM にマネージド データ ディスクを接続する

**適用対象:** :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット 


この記事では、Azure portal で新規のマネージド データ ディスクを Windows 仮想マシン (VM) に接続する方法について説明します。 VM のサイズによって、接続できるデータ ディスクの数が決まります。 詳細については、 [仮想マシンのサイズ](../sizes.md)に関するページをご覧ください。


## <a name="add-a-data-disk"></a>データ ディスクの追加

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[仮想マシン]** を検索して選択します。
1. 一覧から仮想マシンを選択します。
1. **[仮想マシン]** ペインで、 **[ディスク]** を選択します。
1. **[ディスク]** ペインで、 **[新しいディスクを作成し接続する]** を選択します。
1. 新しいディスクのドロップダウンで、必要な選択を行い、ディスクの名前を指定します。
1. **[保存]** を選択して、新しいデータ ディスクを作成し、VM に接続します。

## <a name="initialize-a-new-data-disk"></a>新しいデータ ディスクの初期化

1. VM に接続します
1. 実行中の VM 内で Windows の **スタート** メニューを選択し、検索ボックスに「**diskmgmt.msc**」と入力します。 **[ディスクの管理]** コンソールが開きます。
1. 新しい未初期化ディスクがディスクの管理によって認識され、**[ディスクの初期化]** ウィンドウが表示されます。
1. 新しいディスクが選択されていることを確認し、**[OK]** をクリックしてディスクを初期化します。
1. 新しいディスクが、**未割り当て** として表示されます。 ディスクの任意の場所を右クリックし、**[新しいシンプル ボリューム]** を選択します。 **新しいシンプル ボリューム ウィザード** が開きます。
1. すべての設定を既定値のままウィザードを進め、完了したら **[完了]** を選択します。
1. **[ディスクの管理]** を閉じます。
1. ポップアップが表示され、新しいディスクは使用前にフォーマットする必要があることが通知されます。 **[ディスクのフォーマット]** を選択します。
1. **[新しいディスクのフォーマット]** ウィンドウで設定を確認し、**[開始]** を選択します。
1. ディスクをフォーマットするとデータがすべて削除されることを警告するメッセージが表示されます。 **[OK]** を選択します。
1. フォーマットが完了したら、**[OK]** を選択します。

## <a name="next-steps"></a>次のステップ

- [PowerShell を使用してデータ ディスクを接続する](attach-disk-ps.md)こともできます。
- アプリケーションで *D:* ドライブを使用してデータを保存する必要がある場合は、[Windows 一時ディスクのドライブ文字を変更](change-drive-letter.md)できます。