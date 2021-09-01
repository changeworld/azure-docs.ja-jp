---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/14/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: b2ed6b2b47ead64eac664f789aa8eba7887e68e7
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228802"
---
Azure ファイル共有の SMB マルチチャネルには、現在、次の制限があります。
- SMB 3.1.1 を使用している [Windows](../articles/storage/files/storage-how-to-use-files-windows.md) および [Linux](../articles/storage/files/storage-how-to-use-files-linux.md) クライアントでのみサポートされます
- チャネルの最大数は 4 です。詳細については、[こちら](../articles/storage/files/storage-troubleshooting-files-performance.md#cause-4-number-of-smb-channels-exceeds-four)を参照してください。
- SMB ダイレクトはサポートされていません。
- ストレージ アカウントのプライベート エンドポイントはサポートされていません。