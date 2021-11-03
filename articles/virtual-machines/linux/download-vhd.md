---
title: Azure から Linux VHD をダウンロードする
description: Azure CLI と Azure Portal を使用して Linux VHD をダウンロードします。
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 393706ca463d8a3cc6edaede2689feae3e867bfa
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131074500"
---
# <a name="download-a-linux-vhd-from-azure"></a>Azure から Linux VHD をダウンロードする

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

この記事では、Azure portal を使用して Azure から Linux 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。 

## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VM を実行し続けたい場合は、[スナップショットを作成してから、スナップショットをダウンロードします](#alternative-snapshot-the-vm-disk)。

VM を停止するには:

1.  [Azure portal](https://portal.azure.com/) にサインインします。
2.  左側のメニューで **[Virtual Machines]** を選択します。
3.  一覧から VM を選択します。
4.  VM のページで、 **[停止]** を選択します。

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="VM を停止するためのメニュー ボタンを示す。":::

### <a name="alternative-snapshot-the-vm-disk"></a>代替手段: VM ディスクのスナップショットを作成する

ダウンロードするディスクのスナップショットを作成します。

1. [ポータル](https://portal.azure.com)で VM を選択します。
2. 左側のメニューで **[ディスク]** を選択し、スナップショットを作成するディスクを選択します。 ディスクの詳細が表示されます。  
3. ページ上部のメニューから **[スナップショットの作成]** を選択します。 **[スナップショットの作成]** ページが開きます。
4. **[名前]** にスナップショットの名前を入力します。 
5. **[スナップショットの種類]** に、 **[完全]** または **[増分]** を選択します。
6. 完了したら、 **[確認および作成]** を選択します。

スナップショットはすぐに作成され、別のVMのダウンロードや作成に使用することができます。

> [!NOTE]
> 最初に VM を停止しないと、クリーンなスナップショットになりません。 スナップショットは、スナップショットが作成された時点で VM の電源を入れ直したか、クラッシュした場合と同じ状態になります。 通常は安全ですが、その時点で実行中のアプリケーションがクラッシュ耐性を持っていない場合に問題が発生する可能性があります。
>  
> この方法は、OS ディスクが 1 つの VM にのみ推奨されます。 データ ディスクが 1 つ以上の VM の場合、ダウンロードする前、または OS ディスクと各データ ディスクのスナップショットを作成する前に、停止する必要があります。

## <a name="generate-sas-url"></a>SAS URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1. VM に対するページのメニューで、 **[ディスク]** を選択します。
2. VM 用のオペレーティング システム ディスクを選択して、 **[ディスクのエクスポート]** を選択します。
1. 必要に応じて、 **[URL の期限が切れるまでの秒数]** の値を更新して、ダウンロードを完了するのに十分な時間を設けます。 既定値は 3600 秒 (1 時間) です。
3. **[URL の生成]** を選択します。
 
      
## <a name="download-vhd"></a>VHD のダウンロード

1.  生成された URL の下にある **[VHD ファイルのダウンロード]** を選択します。

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="VHD をダウンロードするボタンを示す。":::

2.  ブラウザーで **[保存]** を選択しないと、ダウンロードが開始されない場合があります。 VHD ファイルの既定の名前は *abcd* です。

## <a name="next-steps"></a>次のステップ

- [Azure CLI を使用してカスタム ディスクをアップロードし、Linux VM を作成する](upload-vhd.md)方法を学習します。 
- [Azure CLI を使用した Azure ディスクの管理](tutorial-manage-disks.md)。
