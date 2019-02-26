---
title: Azure Data Box Gateway の制限 | Microsoft Docs
description: Microsoft Azure Data Box Gateway のシステム制限と推奨サイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 34aac9a589516ace080906095ef3c14b34469bbd
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56311190"
---
# <a name="azure-data-box-gateway-limits-preview"></a>Azure Data Box Gateway の制限 (プレビュー)


Microsoft Azure Data Box Gateway ソリューションをデプロイおよび運用する際には、以下の制限事項を考慮してください。 

> [!IMPORTANT] 
> Data Box Gateway はプレビュー段階にあります。 このソリューションをデプロイする前に、[プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 


## <a name="data-box-gateway-service-limits"></a>Data Box Gateway サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="data-box-gateway-device-limits"></a>Data Box Gateway デバイスの制限

次の表で、Data Box Gateway デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 <br> 制限は、2 TB のディスク領域ごとに最大 2500 万ファイルであり、上限は 1 億です |
|いいえ。 デバイスあたりの共有数 |24 |
|共有 数 (Azure Storage コンテナーあたり) |1 |
|1 つの共有に書き込まれる最大サイズ ファイル|2 TB の仮想デバイスの場合、最大ファイル サイズは 500 GB です。 <br> 最大ファイル サイズは、最大 5 TB に達するまで、前の比率でデータ ディスク のサイズと増加します。 |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]

## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次の手順

- [Azure Data Box Gateway のデプロイを準備する](data-box-gateway-deploy-prep.md)
