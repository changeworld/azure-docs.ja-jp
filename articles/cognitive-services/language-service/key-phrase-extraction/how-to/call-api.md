---
title: Key Phrase Extraction API を呼び出す方法
titleSuffix: Azure Cognitive Services
description: Key Phrase Extraction API を使ってキー フレーズを抽出する方法。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: article
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: 72a7d077b5ee7752f7b91a2f5db5e1b5614c4ddd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091639"
---
# <a name="how-to-use-key-phrase-extraction"></a>キー フレーズ抽出を使用する方法 

キー フレーズ抽出機能を使うと、非構造化テキストを評価し、各ドキュメントについてキー フレーズのリストを返すことができます。

この機能は、ドキュメントのコレクション内の要点をすばやく特定する必要がある場合に便利です。 たとえば、"*食べ物はおいしく、スタッフはすばらしかった*" というテキストを入力すると、サービスから "*食べ物*" と "*すばらしいスタッフ*" というメイン トピックが返されます。

> [!TIP]
> [クイックスタートの記事](../quickstart.md)に従って、この機能を使い始めることができます。 また、[Language Studio](../../language-studio.md) を使うと、コードを記述しなくても要求の例を作成できます。


## <a name="determine-how-to-process-the-data-optional"></a>データの処理方法を決定する (省略可能)

### <a name="specify-the-key-phrase-extraction-model"></a>キー フレーズ抽出モデルを指定する

既定では、テキストで利用できる最新の AI モデルがキー フレーズ抽出によって使われます。 モデルの特定のバージョンを使うように API 要求を構成することもできます。 指定したモデルを使って、キー フレーズ抽出操作が実行されます。

| サポートされているバージョン | 最新バージョン |
|--|--|
| `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |

### <a name="input-languages"></a>入力言語

キー フレーズ抽出で処理されるドキュメントを送信するときに、[サポートされている言語](../language-support.md)のどれでそれが書かれているかを指定できます。 言語を指定しないと、英語がキー フレーズ抽出によって既定で使われます。 さまざまな[多言語と絵文字のエンコード](../../concepts/multilingual-emoji-support.md)をサポートするため、API からの応答でオフセットを返すことができます。 

## <a name="submitting-data"></a>データの送信

キー フレーズ抽出は、扱うテキストの量が多いほど効果を発揮します。 感情分析では逆で、テキストの量が少ないほどパフォーマンスが向上します。 両方の操作から最善の結果を得るには、両方の操作に応じて入力を再構成することを検討してください。

API 要求を送信するには、言語リソースのエンドポイントとキーが必要です。

> [!NOTE]
> Azure portal で言語リソースのキーとエンドポイントを確認できます。 それらは、リソースの **[Key and endpoint]\(キーとエンドポイント\)** ページの **[リソース管理]** にあります。 

要求が受信されると分析が実行されます。 1 分間および 1 秒間に送信できる要求のサイズと数については、以下のデータ制限をご覧ください。

キー フレーズ抽出機能の同期的な使用はステートレスです。 データはアカウントに保存されず、結果がすぐに応答で返されます。

この機能を非同期的に使うと、API の結果は、応答で示される要求取り込み時刻から 24 時間利用できます。 この時間が経過すると、結果は消去され、取得できなくなります。


## <a name="getting-key-phrase-extraction-results"></a>キー フレーズ抽出の結果の取得

API から結果を受け取るとき、返されるキー フレーズの順序は、モデルによって内部的に決定されます。 結果をアプリケーションにストリーミングすることも、ローカル システムのファイルに出力を保存することもできます。

## <a name="data-limits"></a>データ制限

> [!NOTE]
> * 制限を超えるドキュメントを分析する必要がある場合は、テキストを小さなチャンクに分割してから API に送信することができます。 
> * ドキュメントとは、テキスト文字の 1 つの文字列です。  

| 制限 | 値 | 
|------------------------|---------------|
| 1 つのドキュメントの最大サイズ (同期) | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測された 5,120 文字。 |
| 要求ごとの最大文字数 (非同期) | 送信ドキュメント全体で 125,000 文字。[StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) によって計測されます。 |
| 要求全体の最大サイズ | 1 MB。  |
| 要求あたりのドキュメントの最大数 |  10 |

ドキュメントが文字数制限を超えている場合、使用しているエンドポイントに応じて、API の動作は異なります。

* 非同期: 要求内のいずれかのドキュメントが最大サイズを超えている場合、API によってその全体が拒否されて、`400 bad request` エラーが返されます。
* 同期: 最大サイズを超えるドキュメントは API によって処理されず、無効ドキュメント エラーが返されます。 API 要求に複数のドキュメントが含まれている場合、API は、それらが文字数制限内であれば処理を続行します。

### <a name="rate-limits"></a>転送率の制限

レート制限は[価格レベル](https://aka.ms/unifiedLanguagePricing)によって異なります。

| レベル          | 1 秒あたりの要求数 | 1 分あたりの要求数 |
|---------------|---------------------|---------------------|
| S/マルチサービス | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |

## <a name="next-steps"></a>次のステップ

[キー フレーズ抽出の概要](../overview.md)
