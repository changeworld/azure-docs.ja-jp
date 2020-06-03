---
title: Text Analytics API のデータ制限
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の Text Analytics API のデータ制限。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/27/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 79a12505ccc7cea709a88818513ba95710989954
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142369"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Text Analytics API のデータとレートの制限
<a name="data-limits"></a>

この記事では、サイズの制限と、Text Analytics API にデータを送信できるレートの制限について説明します。 

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 |
| 要求全体の最大サイズ | 1 MB |

1 回の要求で送信できるドキュメントの最大数は、使用している API のバージョンと機能によって異なります。

#### <a name="version-3"></a>[Version 3](#tab/version-3)

> [!NOTE]
> v3 API 要求がこれらの制限を超えている一方で v2 の制限内にある場合、API 応答で警告が返されます。 2020 年 7 月 15 日以降は、代わりに 400 エラー コードが返されます。 

API の v3 では、次の制限が変更されています。 以下の制限を超えると、API 応答で警告が生成されます。


| 機能 | 要求あたりのドキュメントの最大数 | 
|----------|-----------|
| 言語検出 | 1000 |
| 感情分析 | 10 |
| キー フレーズ抽出 | 10 |
| 名前付きエンティティの認識 | 5 |
| Entity Linking | 5 |

#### <a name="version-2"></a>[Version 2](#tab/version-2)

| 機能 | 要求あたりのドキュメントの最大数 | 
|----------|-----------|
| 言語検出 | 1000 |
| 感情分析 | 1000 |
| キー フレーズ抽出 | 1000 |
| 名前付きエンティティの認識 | 1000 |
| Entity Linking | 1000 |

---

## <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)によって異なります。 これらの制限は、API の両方のバージョンで同じです。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 該当なし                 |
| S1            | 200                 | 該当なし                 |
| S2            | 該当なし                 | 該当なし                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

要求数は、Text Analytics の機能ごとに個別に測定されます。 たとえば、各機能に対して、利用中の価格レベルで最大数の要求を同時に送信できます。  


## <a name="see-also"></a>関連項目

* [Text Analytics API とは](../overview.md)
* [価格の詳細](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
