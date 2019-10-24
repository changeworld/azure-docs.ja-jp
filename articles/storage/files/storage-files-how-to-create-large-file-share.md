---
title: 大きなファイル共有の有効化と作成 - Azure Files
description: この記事では、大きなファイル共有を有効化して作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 00db5905c008644bc21704179363849756fa7dcc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518163"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>大きなファイル共有を有効化して作成する方法

当初、Standard ファイル共有は 5 TiB までしかスケールアップできませんでした。現在は、大きなファイル共有を使用して 100 TiB までスケールアップできます。 Premium ファイル共有は、既定では 100 TiB までスケールアップされます。 

Standard ファイル共有を使用して 100 TiB までスケールアップするためには、大きなファイル共有を使用できるようにストレージ アカウントを有効にする必要があります。 既存のアカウントを有効にするか、新しいアカウントを作成して大きなファイル共有を使用できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="restrictions"></a>制限

大きなファイル共有が有効になっているアカウントでは、LRS または ZRS がサポートされます。 現時点では、大きなファイル共有が有効になっているアカウントは、GZRS、GRS、RA-GRS をサポートしていません。 アカウントで大きなファイル共有を有効にするプロセスは元に戻せません。有効にすると、アカウントを GZRS、GRS、または RA-GRS に変換することはできません。

## <a name="create-a-new-storage-account"></a>新しいストレージ アカウントの作成

[Azure Portal](https://portal.azure.com) にサインインします。

1. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で「**ストレージ アカウント**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[ストレージ アカウント]** を選択します。
1. 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
1. ストレージ アカウントを作成するサブスクリプションを選択します。
1. **[リソース グループ]** フィールドの下の **[新規作成]** を選択します。 次の図のように、新しいリソース グループの名前を入力します。

    ![ポータルでリソース グループを作成する方法を示すスクリーンショット](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
1. ストレージ アカウントの場所を選択し、それが [LFS でサポートされているリージョンの 1 つ](storage-files-planning.md#regional-availability)であることを確認します。
1. レプリケーションを **[ローカル冗長ストレージ]** または **[ゾーン冗長ストレージ]** に設定します。
1. 以下のフィールドは既定値に設定されたままにします。

   |フィールド  |値  |
   |---------|---------|
   |デプロイメント モデル     |リソース マネージャー         |
   |パフォーマンス     |Standard         |
   |アカウントの種類     |StorageV2 (汎用 v2)         |
   |アクセス層     |ホット         |

1. **[詳細設定]** を選択し、 **[Large file shares]\(大きなファイル共有\)** で **[有効]** を選択します。
1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。

    ![large-file-shares-advanced-enable.png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. **作成** を選択します。

## <a name="enable-on-existing-account"></a>既存のアカウントで有効にする

既存のアカウントで大きなファイル共有を有効にすることもできます。 これを行うと、アカウントは GZRS、GRS、または RA-GRS に変換できなくなります。 この選択は、このアカウントで元に戻すことはできません。

1. [Azure portal](https://portal.azure.com) を開き、大きなファイル共有を有効にするストレージ アカウントに移動します。
1. ストレージ アカウントを開き、 **[構成]** を選択します。
1. **[Large file shares]\(大きなファイル共有\)** で **[有効]** を選択し、[保存] を選択します。
1. **[概要]** を選択し、 **[最新の情報に更新]** を選択します。

![enable-large-file-shares-on-existing.png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

これで、大きなファイル共有をストレージ アカウントで有効にしました。

次のエラー メッセージが表示される場合:"このアカウントで大きなファイル共有はまだ利用できません。" 当該リージョンがロール アウトの完了中である可能性があるため、しばらく待つか、緊急の必要性がある場合は、サポートにお問い合わせください。

## <a name="create-a-large-file-share"></a>大きなファイル共有の作成

大きなファイル共有の作成は、Standard ファイル共有の作成とほぼ同じです。 主な違いは、クォータを最大 100 TiB に設定できることです。

1. ストレージ アカウントで **[ファイル共有]** を選択します。
1. **+ [ファイル共有]** を選択します。
1. ファイル共有の名前と、必要に応じて、希望するクォータ サイズ (最大 100 TiB) を入力し、 **[作成]** を選択します。 

![large-file-shares-create-share.png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

## <a name="expand-existing-file-shares"></a>既存のファイル共有の拡張

ストレージ アカウントで大きなファイル共有を有効にすると、既存の共有をより高いクォータに拡張できます。

1. ストレージ アカウントで **[ファイル共有]** を選択します。
1. ファイル共有を右クリックし、 **[クォータ]** を選択します。
1. 希望する新しいサイズを入力し、 **[OK]** を選択します。

![update-large-file-share-quota.png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

## <a name="next-steps"></a>次の手順

* [ファイル共有の接続とマウント - Windows](storage-how-to-use-files-windows.md)
* [ファイル共有の接続とマウント - Linux](../storage-how-to-use-files-linux.md)
* [ファイル共有の接続とマウント - macOS](storage-how-to-use-files-mac.md)