---
title: コンテナーの要件と推奨事項
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/09/2021
ms.author: aahi
ms.openlocfilehash: 5cbf07ab97206ae3509701a6d6b0f71e961b0bf8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308781"
---
> [!NOTE]
> 要件と推奨事項は、29 行におよぶ合計 803 文字のビジネス レターのスキャン画像 (8 MB) を使用した、1 秒あたり 1 つの要求というベンチマークに基づいています。

次の表に、各 Read OCR コンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| Read 2.0-preview | 1 コア、8 GB のメモリ |    8 コア、16 GB のメモリ |
| Read 3.2 | 8 コア、16 GB のメモリ | 8 コア、24 GB のメモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。
