---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 8bb607d0b3091de34c5c75b09c3b99257f6b2c2d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467760"
---
| Premium SSD のサイズ | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| ディスク サイズ (GiB) | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2,048 | 4,096 | 8,192 | 16,384 | 32,767 |
| ディスクあたりの IOPS | 120 | 120 | 120 | 120 | 240 | 500 | 1,100 | 2,300 | 5,000 | 7,500 | 7,500 | 16,000 | 18,000 | 20,000 |
| ディスクあたりのスループット | 25 MiB/秒 | 25 MiB/秒 | 25 MiB/秒 |最大 25 MiB/秒 | 50 MiB/秒 | 100 MiB/秒 | 125 MiB/秒 | 150 MiB/秒 | 200 MiB/秒 | 250 MiB/秒 | 250 MiB/秒| 500 MiB/秒 | 750 MiB/秒 | 900 MiB/秒 |
| ディスクあたりの最大バースト IOPS** | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 | 3,500 |
| ディスクあたりの最大バースト スループット** | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 | 170 MiB/秒 |
| 最大バースト時間** | 30 分  | 30 分  | 30 分  | 30 分  | 30 分  | 30 分  | 30 分  | 30 分  |

\*現在プレビュー段階のディスク サイズを示します。現在利用可能なリージョンについては、「[Premium ディスク: マネージドとアンマネージド](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#premium-disks-managed-and-unmanaged)」を参照してください。

\**現在プレビュー段階の機能を示します。詳細については、[ディスクのバースティング](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability)に関する記事をご覧ください。
