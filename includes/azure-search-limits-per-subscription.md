---
title: インクルード ファイル
description: インクルード ファイル
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 04/04/2018
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 8a4f794c8ef24a90498954629c131904621c5b43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755892"
---
サービスは 1 つのサブスクリプションの枠内で複数作成することができます。作成したサービスはそれぞれ、一定のレベルでプロビジョニングされます。ただし、各レベルにはサービス数に上限が設けられています。 たとえば、Basic レベルのサービスであれば 12 個まで作成できますが、同じサブスクリプションの枠内で S1 レベルのサービスをさらにもう 12 個まで作成できます。 レベルの詳細については、「[Azure Search 用の SKU または価格レベルの選択](../articles/search/search-sku-tier.md)」を参照してください。

サービス数の上限は、依頼により引き上げが可能です。 同一のサブスクリプションで通常よりも多数のサービスが必要な場合には、Azure Support にお問い合わせください。

| Resource            | Free&nbsp;<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD |
| ------------------- | ---- | ----- | --- | -- | -- | ----- |
| 最大サービス数    |1     | 12    | 12  | 6  | 6  | 6     |
| SU の最大スケール&nbsp;<sup>2</sup> |該当なし |3 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> Free は、専用リソースではなく、共有リソースに基づいています。 スケールアップは、共有リソースではサポートされていません。

<sup>2</sup> Search ユニット (SU) は、*レプリカ*または*パーティション*のいずれかとして割り当てられている請求単位です。 ストレージ、インデックス作成、およびクエリ操作については、両方のリソースが必要になります。 SU の計算の詳細については、[クエリとインデックス作成のワークロードに応じたリソース レベルのスケーリング](../articles/search/search-capacity-planning.md)に関するページを参照してください。 