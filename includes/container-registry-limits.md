---
title: インクルード ファイル
description: インクルード ファイル
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: b4701260a7d8da030f9f3019060aaa83e7a3a483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803305"
---
| リソース | Basic | Standard | Premium |
|---|---|---|---|
| 含まれている記憶域<sup>1</sup> (GiB) | 10 | 100 | 500 |
| ストレージの制限 (TiB) | 20| 20 | 20 |
| イメージ レイヤーの最大サイズ (GiB) | 200 | 200 | 200 |
| 1 分あたりの ReadOps<sup>2、3</sup> | 1,000 | 3,000 | 10,000 |
| 1 分あたりの WriteOps<sup>2、4</sup> | 100 | 500 | 2,000 |
| ダウンロード帯域幅<sup>2</sup> (Mbps) | 30 | 60 | 100 |
| アップロード帯域幅 <sup>2</sup> (Mbps) | 10 | 20 | 50 |
| Webhooks | 2 | 10 | 500 |
| geo レプリケーション | 該当なし | 該当なし | [サポートされています][geo-replication] |
| 可用性ゾーン | 該当なし | 該当なし | [プレビュー][zones] |
| コンテンツの信頼 | 該当なし | 該当なし | [サポートされています][content-trust] |
| プライベート エンドポイントがあるプライベート リンク | 該当なし | 該当なし | [サポートされています][plink] |
| &bull; プライベート エンドポイント | 該当なし | 該当なし | 10 |
| パブリック IP ネットワーク ルール | 該当なし | 該当なし | 100 |
| サービス エンドポイントの VNet アクセス | 該当なし | 該当なし | [プレビュー][vnet] |
| カスタマー マネージド キー | 該当なし | 該当なし | [サポートされています][cmk] |
| リポジトリがスコープ指定されたアクセス許可 | 該当なし | 該当なし | [プレビュー][token]|
| &bull; トークン | 該当なし | 該当なし | 20,000 |
| &bull; シェイプ マップ | 該当なし | 該当なし | 20,000 |
| &bull; スコープ マップごとのリポジトリ | 該当なし | 該当なし | 500 |


<sup>1</sup> 各サービス レベルの 1 日あたりの料金に含まれるストレージ。 ストレージの上限に達するまで追加のストレージを使用できますが、GiB あたりの追加料金が日単位で課金されます。 詳細については、[Azure Container Registry の価格][pricing]に関するページを参照してください。 レジストリのストレージの上限を超えるストレージが必要な場合は、Azure サポートまでお問い合わせください。

<sup>2</sup>*ReadOps*、*WriteOps*、および "*帯域幅*" は最小推定値です。 Azure Container Registry は、使用状況で必要とするパフォーマンスの向上に努めます。

<sup>3</sup>[docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) は、イメージ内のレイヤー数とマニフェストの取得に基づいて、複数の読み取り操作に変換します。

<sup>4</sup>[docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) は、プッシュする必要があるレイヤーの数に基づいて、複数の書き込み操作に変換します。 `docker push` には、*ReadOps* が含まれ、既存のイメージのマニフェストを取得します。

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md
[zones]: ../articles/container-registry/zone-redundancy.md
