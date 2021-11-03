---
title: Entity Linking API を呼び出す方法
titleSuffix: Azure Cognitive Services
description: Entity Linking API を使って、テキストで見つかったエンティティを識別し、リンクする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: a7edc27898c1af9fcb8f7bc72698d2d6c3a63e68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017551"
---
# <a name="how-to-use-entity-linking"></a>エンティティ リンク設定を使用する方法

エンティティ リンク設定機能を使うと、テキスト内で見つかったエンティティの ID を識別し、あいまいさを解消することができます (例: "*Mars*" という単語が出現した場合に、それが惑星を指すのか、古代ローマの戦争の神を指すのかを判定する)。 テキスト内のエンティティと、ナレッジ ベースとしての [Wikipedia](https://www.wikipedia.org/) へのリンクが返されます。

> [!TIP]
> [クイックスタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても要求の例を作成できます。

## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-entity-linking-model"></a>エンティティ リンク設定モデルを指定する

既定では、テキストで利用可能な最新の AI モデルがエンティティ リンク設定で使われます。 モデルの特定のバージョンを使うように API 要求を構成することもできます。 指定したモデルを使って、エンティティ リンク設定操作が実行されます。

| サポートされているバージョン | 最新バージョン |
|--|--|
| `2019-10-01`, `2020-02-01` | `2020-02-01` |

### <a name="input-languages"></a>入力言語

エンティティ リンク設定で処理されるドキュメントを送信するときに、[サポートされている言語](../language-support.md)のどれでそれが書かれているかを指定できます。 言語を指定しないと、英語がエンティティ リンク設定によって既定で使われます。 [多言語と絵文字のサポート](../../concepts/multilingual-emoji-support.md)により、応答にテキスト オフセットが含まれる場合があります。 

## <a name="submitting-data"></a>データの送信

処理するテキストの量が少ないほど、エンティティ リンク設定によって生成される結果の品質が高くなります。 これは、テキストのブロックが大きいほどパフォーマンスが向上する、キー フレーズ抽出などの一部の機能とは反対です。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

API 要求を送信するには、言語リソースのエンドポイントとキーが必要です。

> [!NOTE]
> Azure portal で言語リソースのキーとエンドポイントを確認できます。 それらは、リソースの **[Key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

要求が受信されると分析が実行されます。 1 分間および 1 秒間に送信できる要求のサイズと数については、以下のデータ制限をご覧ください。

エンティティ リンク設定を同期的に使うとステートレスになります。 データはアカウントに保存されず、結果がすぐに応答で返されます。

この機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。

### <a name="getting-entity-linking-results"></a>エンティティ リンク設定の結果の取得  

結果をアプリケーションにストリーミングすることも、ローカル システムのファイルに出力を保存することもできます。

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 |
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 |
| 要求ごとの最大文字数 (非同期)  | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。 |
| 要求全体の最大サイズ | 1 MB |
| 要求あたりのドキュメントの最大数 | 5 |

ドキュメントが文字数制限を超えている場合、同期または非同期のどちらでそれを使っているかに応じて、API の動作は異なります。

* 非同期: 要求内のいずれかのドキュメントが最大サイズを超えている場合、API によってその全体が拒否されて、`400 bad request` エラーが返されます。
* 同期: 最大サイズを超えるドキュメントは API によって処理されず、無効ドキュメント エラーが返されます。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="see-also"></a>関連項目

* [エンティティ リンク設定の概要](../overview.md)
