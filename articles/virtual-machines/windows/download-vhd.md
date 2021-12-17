---
title: Azure から Windows VHD をダウンロードする
description: Azure Portal を使用して Windows VHD をダウンロードします。
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 5d43ce73efa602cded23c533fefd29009998b809
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131003354"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure から Windows VHD をダウンロードする

**適用対象:** :heavy_check_mark: Windows VM 

この記事では、Azure portal を使用して Azure から Windows 仮想ハード ディスク (VHD) ファイルをダウンロードする方法を説明します。

## <a name="optional-generalize-the-vm"></a>省略可能:VM の汎用化

他の VM を作成するための[イメージ](tutorial-custom-images.md)として VHD を使用したい場合には、[Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) でオペレーティング システムを一般化する必要があります。 それ以外の場合は、作成する VM ごとにディスクのコピーを作成する必要があります。

他の VM を作成するためのイメージとして VHD を使用するには、VM を一般化してください。

1. まだサインインしていない場合は、 [Azure ポータル](https://portal.azure.com/)にサインインします。
2. [VM に接続します](connect-logon.md)。 
3. VM で、管理者としてコマンド プロンプト ウィンドウを開きます。
4. ディレクトリを *%windir%\system32\sysprep* に変更し、sysprep.exe を実行します。
5. [システム準備ツール] ダイアログ ボックスで **[システムの OOBE (Out-of-Box Experience) に入る]** を選択し、 **[一般化する]** チェック ボックスがオンになっていることを確認します。
6. [シャットダウン オプション] の **[シャットダウン]** を選択し、 **[OK]** をクリックします。 

現在の VM を一般化したくない場合でも、最初に [OS ディスクのスナップショットを作成し](#alternative-snapshot-the-vm-disk)、スナップショットから新しい VM を作成してから、コピーを一般化することで、一般化されたイメージを作成できます。

## <a name="stop-the-vm"></a>VM を停止する

VHD は、実行中の VM に接続されている場合、Azure からダウンロードできません。 VM を実行し続けたい場合は、[スナップショットを作成してから、スナップショットをダウンロードします](#alternative-snapshot-the-vm-disk)。

1. Azure Portal のハブ メニューで、 **[仮想マシン]** をクリックします。
1. 一覧から VM を選択します。
1. VM のブレードで、 **[停止]** をクリックします。

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
> 最初に VM を停止しないと、クリーンなスナップショットになりません。 スナップショットは、スナップショットが作成された時点で VM の電源を入れ直したか、クラッシュした場合と同じ状態になります。  通常は安全ですが、その時点で実行中のアプリケーションがクラッシュ耐性を持っていない場合に問題が発生する可能性があります。
>  
> この方法は、OS ディスクが 1 つの VM にのみ推奨されます。 データ ディスクが 1 つ以上の VM の場合、ダウンロードする前、または OS ディスクと各データ ディスクのスナップショットを作成する前に、停止する必要があります。

## <a name="generate-download-url"></a>ダウンロード URL の生成

VHD ファイルをダウンロードするには、[Shared Access Signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL を生成する必要があります。 URL が生成されると、その URL に有効期限が割り当てられます。

1. VM のページで、左側のメニューの **[ディスク]** をクリックします。
1. VM のオペレーティング システム ディスクを選択します。
1. ディスクのページで、左側のメニューから **[ディスクのエクスポート]** を選択します。
1. URL の有効期限の既定値は *3,600* 秒 (1 時間) です。 Windows OS ディスクまたは大容量データ ディスクの場合は、必要に応じてこれを増やします。 通常は **36,000** 秒 (10 時間) で十分です。
1. **[URL の生成]** をクリックします。

> [!NOTE]
> Windows Server オペレーティング システム用の大きな VHD ファイルをダウンロードするのに十分な時間を確保するために、有効期限を既定値から増やしています。 大規模な VHD は、接続と VM のサイズによっては、ダウンロードに数時間かかる場合があります。 
> 
> 

## <a name="download-vhd"></a>VHD のダウンロード

1. 生成された URL の下にある [VHD ファイルのダウンロード] をクリックします。
1. ダウンロードを開始するために、ブラウザーで **[保存]** をクリックする必要がある場合もあります。 VHD ファイルの既定の名前は *abcd* です。

## <a name="next-steps"></a>次のステップ

- [VHD ファイルを Azure にアップロードする](upload-generalized-managed.md)方法を学習します。 
- [ストレージ アカウント内の非管理対象ディスクからマネージド ディスクを作成する](attach-disk-ps.md)。
- [PowerShell を使用した Azure ディスクの管理](tutorial-manage-data-disk.md)。
