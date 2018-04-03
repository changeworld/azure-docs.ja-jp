---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 03/23/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 575483192954f4e05db50e701e223829e041cffc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2018
---
| リソース | Basic | 標準 | Premium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| 1 分あたりの ReadOps<sup>1、2</sup> | 1,000 | 3000 | 10000 |
| 1 分あたりの WriteOps<sup>1、3</sup> | 100 | 500 | 2000 |
| ダウンロード帯域幅 MBps<sup>1</sup> | 30 | 60 | 100 |
| アップロード帯域幅 MBps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| geo レプリケーション | 該当なし | 該当なし | [サポート *(プレビュー)*](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*、*WriteOps*、および*帯域幅*は最小推定値です。 ACR は、使用状況で必要とするパフォーマンスの向上に努めます。

<sup>2</sup> [Docker プル](https://docs.docker.com/registry/spec/api/#pulling-an-image)は、イメージ内のレイヤー数とマニフェストの取得に基づいて、複数の読み取り操作に変換します。

<sup>3</sup> [Docker プッシュ](https://docs.docker.com/registry/spec/api/#pushing-an-image)は、プッシュする必要があるレイヤーの数に基づいて、複数の書き込み操作に変換します。 `docker push` には、*ReadOps* が含まれ、既存のイメージのマニフェストを取得します。