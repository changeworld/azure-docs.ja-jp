---
title: Azure Stack Edge Pro GPU での仮想マシン イメージのアップロードのトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro GPU で仮想マシン イメージをアップロード、ダウンロード、または削除するときに発生する問題のトラブルシューティング方法について説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 05/13/2021
ms.author: alkohli
ms.openlocfilehash: 3ec5cfb952e666832fe887f65672e228004423bc
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483167"
---
# <a name="troubleshoot-virtual-machine-image-uploads-in-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU での仮想マシン イメージのアップロードのトラブルシューティング

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge Pro GPU デバイスで仮想マシン (VM) イメージをダウンロードおよび管理するときに発生する問題のトラブルシューティング方法について説明します。


## <a name="unable-to-add-vm-image-to-blob-container"></a>BLOB コンテナーに VM イメージを追加できない

**エラーの説明:** Azure portal で VM イメージを BLOB コンテナーにアップロードしようとしたときに、 **[追加]** ボタンが使用できないために、イメージをアップロードできません。 リソース グループに対する必要な共同作成者の役割またはデバイスのサブスクリプションを持っていないときには、 **[追加]** ボタンは使用できません。

**推奨されている解決方法:** リソース グループまたはストレージ アカウントにファイルを追加するために必要な共同作成者アクセス許可を持っていることを確認します。 詳細については、「[Azure Stack Edge リソースの前提条件](azure-stack-edge-deploy-prep.md#prerequisites)」を参照してください。


## <a name="invalid-blob-type-for-the-source-blob-uri"></a>ソース BLOB URI の BLOB の種類が無効です

**エラーの説明:** ブロック BLOB として保存されている VHD はダウンロードできません。 ダウンロードするには、VHD をページ BLOB として保存する必要があります。

**推奨されている解決方法:** VHD をページ BLOB として Azure Storage アカウントにアップロードします。 その後で BLOB をダウンロードします。 アップロード手順については、「[Storage Explorer を使用してアップロードする](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)」を参照してください。


## <a name="only-blobs-formatted-as-vhds-can-be-imported"></a>VHD として書式設定された BLOB しかインポートできない

**エラーの説明:** 書式設定要件を満たしていないために VHD をインポートできません。 インポートするには、仮想ハード ディスクが固定サイズの第 1 世代 VHD である必要があります。

**推奨されている解決方法:** 

- 「[Azure Stack Edge Pro GPU で VM をデプロイするために Windows VHD から汎用化されたイメージを準備する](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)」の手順に従って、VHD または VHDX から第 1 世代の仮想マシンの固定サイズの VHD を作成します。

- PowerShell を使用する場合:

   - Hyper-V 用の Windows PowerShell モジュールで [Convert-VHD](/powershell/module/hyper-v/convert-vhd?view=windowsserver2019-ps&preserve-view=true) を使用できます。 Convert-VHD を使用して、VM イメージを第 2 世代 VM から第 1 世代に変換することはできません。代わりに、「[Windows VHD から一般化されたイメージを準備して、Azure Stack Edge Pro GPU で VM をデプロイする](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)」のポータル手順を使用します。
  
   - 現在の VHD の種類を確認する必要がある場合は、[Get-VHD](/powershell/module/hyper-v/get-vhd?view=windowsserver2019-ps&preserve-view=true) を使用します。


## <a name="the-condition-specified-using-http-conditional-headers-is-not-met"></a>HTTP 条件ヘッダーを使用して指定した条件が満たされません。

**エラーの説明:** Azure から VHD をダウンロードしようとしたときに VHD に変更が加えられた場合、Azure の VHD がダウンロードされている VHD と一致しないため、ダウンロードは失敗します。 このエラーは、Azure への VHD のアップロードが完了する前にダウンロードが試行された場合にも発生します。

**推奨されている解決方法:** VHD に変更が加えられない状態で VHD のアップロードが完了するまで待機します。 その後、もう一度 VHD をダウンロードしてみてください。


## <a name="next-steps"></a>次のステップ

* [Azure portal を使用して VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)方法について説明します。
* [Azure PowerShell を使用して VM をデプロイする](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)方法について説明します。
