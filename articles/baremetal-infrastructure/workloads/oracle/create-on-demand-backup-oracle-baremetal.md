---
title: SnapCenter で Oracle Database のオンデマンド バックアップを作成する
description: Oracle BareMetal インフラストラクチャ上の SnapCenter で Oracle Database のオンデマンド バックアップを作成する方法について説明します。
ms.topic: how-to
ms.subservice: baremetal-oracle
ms.date: 05/07/2021
ms.openlocfilehash: 699c070a3eeca2904f9620ca5f12c95c509210ab
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579158"
---
# <a name="create-on-demand-backup-of-your-oracle-database-in-snapcenter"></a>SnapCenter で Oracle Database のオンデマンド バックアップを作成する

この記事では、SnapCenter で Oracle Database のオンデマンド バックアップを作成する手順について説明します。 

[SnapCenter](configure-snapcenter-oracle-baremetal.md) を構成すると、データ ファイル、制御ファイル、アーカイブ ログのバックアップは、リソース グループの作成時に入力したスケジュールに基づいて続行されます。 ただし、通常のデータベース保護の一環として、オンデマンド バックアップも必要になる場合があります。

## <a name="steps-to-create-an-on-demand-backup"></a>オンデマンド バックアップを作成する手順

1. 左側のメニューで **[Resources]\(リソース\)** を選択します。 次に、 **[View]\(表示\)** の横にあるドロップダウン メニューで、 **[Resource Group]\(リソース グループ\)** を選択します。 作成するオンデマンド バックアップに対応するリソース グループ名を選択します。

2. 右上にある **[Back up now]\(今すぐバックアップ\)** を選択します。

3. リソース グループと保護ポリシーがオンデマンド バックアップに対して正しいことを確認します。 このバックアップを確認する場合は、 **[Verify after backup]\(バックアップ後に確認する\)** チェック ボックスをオンにします。 **[Backup]\(バックアップ\)** を選択します。

バックアップが完了すると、バックアップは **[Resources]\(リソース\)** の下のバックアップの一覧からアクセスできるようになります。 バックアップしたリソース グループに関連付けられている 1 つまたは複数のデータベースを選択します。 このバックアップは、保護ポリシーの作成時に設定したオンデマンド保持ポリシーに従って保持されます。

## <a name="next-steps"></a>次のステップ

SnapCenter で Oracle Database を復元する方法について学習します。

> [!div class="nextstepaction"]
> [Oracle Database を復元する](restore-oracle-database-baremetal.md)
