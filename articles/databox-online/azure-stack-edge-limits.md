---
title: Azure Stack Edge Pro GPU/Pro FPGA の制限 | Microsoft Docs
description: Azure Stack Edge Pro GPU/Pro FPGA を配置して操作するときの制限 (サービスの制限、デバイスの制限、記憶域の制限など) と推奨されるサイズについて説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/12/2020
ms.author: alkohli
ms.openlocfilehash: 64be85db0e2d11d2aee5a61742f427087a77cef2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736719"
---
# <a name="azure-stack-edge-limits"></a>Azure Stack Edge の制限

Microsoft Azure Stack Edge Pro GPU または Azure Stack Edge Pro FPGA ソリューションを配置および操作するときは、以下の制限事項を考慮してください。 

## <a name="azure-stack-edge-service-limits"></a>Azure Stack Edge サービスの制限

[!INCLUDE [data-box-edge-gateway-service-limits](../../includes/data-box-edge-gateway-service-limits.md)]

## <a name="azure-stack-edge-device-limits"></a>Azure Stack Edge デバイスの制限

次の表では、Azure Stack Edge デバイスの制限について説明します。

| 説明 | 値 |
|---|---|
|いいえ。 デバイスあたりのファイル数 |1 億 |
|いいえ。 コンテナーあたりの共有数 |1 |
|最大の デバイスあたり共有エンドポイントと REST エンドポイントの数 (GPU デバイスのみ)| 24 |
|最大の デバイスあたり階層化ストレージ アカウント数 (GPU デバイスのみ)| 24|
|1 つの共有に書き込まれる最大サイズ ファイル| 5 TB |
|最大のデバイスあたりリソース グループ数| 800 |

## <a name="azure-storage-limits"></a>Azure Storage の制限

[!INCLUDE [data-box-edge-gateway-storage-limits](../../includes/data-box-edge-gateway-storage-limits.md)]

## <a name="data-upload-caveats"></a>データのアップロードに関する注意事項

[!INCLUDE [data-box-edge-gateway-storage-data-upload-caveats](../../includes/data-box-edge-gateway-storage-data-upload-caveats.md)]

## <a name="azure-storage-account-size-and-object-size-limits"></a>Azure ストレージ アカウント サイズとオブジェクト サイズの制限

[!INCLUDE [data-box-edge-gateway-storage-acct-limits](../../includes/data-box-edge-gateway-storage-acct-limits.md)]


## <a name="azure-object-size-limits"></a>Azure オブジェクトのサイズ制限

[!INCLUDE [data-box-edge-gateway-storage-object-limits](../../includes/data-box-edge-gateway-storage-object-limits.md)]

## <a name="next-steps"></a>次のステップ

- [Azure Stack Edge Pro GPU のデプロイを準備する](azure-stack-edge-gpu-deploy-prep.md)
- [Azure Stack Edge Pro FPGA のデプロイを準備する](azure-stack-edge-deploy-prep.md)
