---
title: コンテナーの要件と推奨事項
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 3ab5462cc2b368bd1518606b03c8ec1027bc31c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397192"
---
> [!NOTE]
> 要件と推奨事項は、29 行におよぶ合計 803 文字のビジネス レターのスキャン画像 (8 MB) を使用した、1 秒あたり 1 つの要求というベンチマークに基づいています。

次の表に、各読み取りコンテナーに割り当てるリソースの最小値と推奨値を示します。

| コンテナー | 最小値 | 推奨 |
|-----------|---------|-------------|
| Read 2.0-preview | 1 コア、8 GB のメモリ |  8 コア、16 GB のメモリ |
| Read 3.0-preview | 8 コア、16 GB のメモリ | 8 コア、24 GB のメモリ |
| Read 3.1-preview | 8 コア、16 GB のメモリ | 8 コア、24 GB のメモリ |

* 各コアは少なくとも 2.6 ギガヘルツ (GHz) 以上にする必要があります。

コアとメモリは、`docker run` コマンドの一部として使用される `--cpus` と `--memory` の設定に対応します。
