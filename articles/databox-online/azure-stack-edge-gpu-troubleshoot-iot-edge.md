---
title: GPU 搭載 Azure Stack Edge Pro デバイス上の IoT Edge に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Stack Edge Pro GPU デバイス上の IoT Edge に関するエラーのトラブルシューティング方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: alkohli
ms.custom: contperf-fy21q4
ms.openlocfilehash: 405a9c62a551a011eb6d7b00025c6ae0a563e858
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111987848"
---
# <a name="troubleshoot-iot-edge-issues-on-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU デバイス上の IoT Edge に関する問題のトラブルシューティング 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、IoT Edge エージェントのランタイム応答と、お使いのデバイスにインストールされている IoT Edge サービスのエラーを確認することで、Azure Stack Edge Pro GPU デバイス上のコンピューティング関連のエラーをトラブルシューティングする方法について説明します。

## <a name="review-iot-edge-runtime-responses"></a>IoT Edge ランタイム応答を確認する

[!INCLUDE [Troubleshoot IoT Edge runtime](../../includes/azure-stack-edge-iot-troubleshoot-compute.md)]

## <a name="troubleshoot-iot-edge-service-errors"></a>IoT Edge サービスのエラーのトラブルシューティング

次のエラーは、ご使用の Azure Stack Edge Pro GPU デバイス上の IoT Edge サービスに関連しています。

[!INCLUDE [Troubleshoot IoT Edge errors](../../includes/azure-stack-edge-iot-troubleshoot-compute-error-detail.md)]


## <a name="next-steps"></a>次のステップ

- [IoT Edge に関連する Kubernetes の問題をデバッグする](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge)。
- [デバイスの問題のトラブルシューティング](azure-stack-edge-gpu-troubleshoot.md)。