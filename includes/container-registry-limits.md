---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 05/29/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 92a5d162e7a0b2c752a2f8e9c5941edf43e539e3
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991087"
---
| Resource | Basic | 標準 | Premium |
|---|---|---|---|---|
| Storage | 10 GiB | 100 GiB| 500 GiB |
| イメージ レイヤーの最大サイズ | 20 GiB | 20 GiB | 50 GiB |
| 1 分あたりの ReadOps<sup>1、2</sup> | 1,000 | 3,000 | 10,000 |
| 1 分あたりの WriteOps<sup>1、3</sup> | 100 | 500 | 2,000 |
| ダウンロード帯域幅 MBps<sup>1</sup> | 30 | 60 | 100 |
| アップロード帯域幅 MBps<sup>1</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| geo レプリケーション | 該当なし | 該当なし | [サポートされています](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication) |

<sup>1</sup> *ReadOps*、*WriteOps*、および*帯域幅*は最小推定値です。 ACR は、使用状況で必要とするパフォーマンスの向上に努めます。

<sup>2</sup> [Docker プル](https://docs.docker.com/registry/spec/api/#pulling-an-image)は、イメージ内のレイヤー数とマニフェストの取得に基づいて、複数の読み取り操作に変換します。

<sup>3</sup> [Docker プッシュ](https://docs.docker.com/registry/spec/api/#pushing-an-image)は、プッシュする必要があるレイヤーの数に基づいて、複数の書き込み操作に変換します。 `docker push` には、*ReadOps* が含まれ、既存のイメージのマニフェストを取得します。
