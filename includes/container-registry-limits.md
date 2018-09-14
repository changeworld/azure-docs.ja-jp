---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: mmacy
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: bddb17a5163333a5aeb86b296a21867f0611d12f
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303861"
---
| リソース | Basic | 標準 | Premium |
|---|---|---|---|---|
| ストレージ<sup>1</sup> | 10 GiB | 100 GiB| 500 GiB |
| イメージ レイヤーの最大サイズ | 20 GiB | 20 GiB | 50 GiB |
| 1 分あたりの ReadOps<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 1 分あたりの WriteOps<sup>2、4</sup> | 100 | 500 | 2,000 |
| ダウンロード帯域幅 MBps<sup>2</sup> | 30 | 60 | 100 |
| アップロード帯域幅 MBps<sup>2</sup> | 10 | 20 | 50 |
| Webhook | 2 | 10 | 100 |
| geo レプリケーション | 該当なし | 該当なし | [サポートされています][geo-replication] |
| コンテンツの信頼 (プレビュー) | 該当なし | 該当なし | [サポートされています][content-trust] |

<sup>1</sup> 指定された容量の上限は、各レベルに "*含まれる*" ストレージの容量です。 これらの制限を超えてイメージを保存する場合は、GiB あたりの追加料金が日単位で課金されます。 詳細については、「[Container Registry の価格][pricing]」を参照してください。

<sup>2</sup> *ReadOps*、*WriteOps*、および "*帯域幅*" は最小推定値です。 ACR は、使用状況で必要とするパフォーマンスの向上に努めます。

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) は、イメージ内のレイヤー数とマニフェストの取得に基づいて、複数の読み取り操作に変換します。

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) は、プッシュする必要があるレイヤーの数に基づいて、複数の書き込み操作に変換します。 `docker push` には、*ReadOps* が含まれ、既存のイメージのマニフェストを取得します。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md