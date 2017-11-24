---
title: "Azure PowerShell のサンプル スクリプト - BLOB コンテナーのサイズを計算する | Microsoft Docs"
description: "コンテナー内の BLOB のサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。"
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob ストレージ コンテナーのサイズを計算する

このスクリプトでは、コンテナー内のブロブのサイズを合計して、Azure Blob ストレージ内のコンテナーのサイズを計算します。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> この PowerShell スクリプトは、コンテナーの推定サイズを算出しますが、課金の計算には使用できません。 課金目的のコンテナー サイズを計算するスクリプトについては、「[Calculate the size of a Blob storage container for billing purposes](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)」 (課金を目的に Blob ストレージ コンテナーのサイズを計算する) を参照してください。 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

次のコマンドを実行して、リソース グループ、コンテナー、すべての関連リソースを削除します。

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは次のコマンドを使用して、Blob ストレージ コンテナーのサイズを計算します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | リソース グループまたはサブスクリプション内の指定された Storage アカウントまたはすべての Storage アカウントを取得します。 |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | コンテナー内の BLOB を一覧表示します。 ||

## <a name="next-steps"></a>次のステップ

課金目的のコンテナー サイズを計算するスクリプトについては、「[Calculate the size of a Blob storage container for billing purposes](../scripts/storage-blobs-container-calculate-billing-size-powershell.md)」 (課金を目的に Blob ストレージ コンテナーのサイズを計算する) を参照してください。

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他のストレージ PowerShell サンプル スクリプトは、[Azure Storage 用 PowerShell サンプル](../blobs/storage-samples-blobs-powershell.md)のページにあります。
