---
title: Speech to Text について
description: Speech to Text API の機能の概要。
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 7cb0257a7302221f80bb90c0a6c3446cde07290a
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434128"
---
# <a name="about-the-speech-to-text-api"></a>Speech to Text API について

**Speech to Text** API は、オーディオ ストリームを、アプリケーションでユーザーに表示できるテキストまたはコマンド入力として操作できるテキストに ”*書き起こします*”。 API は、SDK クライアント ライブラリ (サポートされているプラットフォームと言語の場合) または REST API のいずれかで使用できます。

**Speech to Text** API には、次の機能が用意されています。

- Microsoft の高度な音声認識テクノロジ: Cortana、Office、およびその他の Microsoft 製品で使用されているものと同じです。

- リアルタイムの継続的な認識。 **Speech to Text** により、ユーザーはリアルタイムでオーディオをテキストに書き起こすことができます。 また、これまでに認識されている単語の中間結果の受信もサポートされています。 サービスは音声の末尾を自動的に認識します。 ユーザーはまた、大文字/小文字の設定と句読点、不適切な表現のマスキング、逆テキスト正規化など、追加の書式設定オプションを選択することもできます。

- 対話、会話、およびディクテーションのシナリオでの**Speech to Text** 結果の最適化。 認識された結果は、語彙形式と表示形式の両方で返されます (語彙の結果については、例または API の DetailedSpeechRecognitionResult を参照)。

- 多くの音声言語および方言のサポート。 各認識モードでサポートされている言語の完全な一覧については、[サポートされている言語](language-support.md#speech-to-text)に関するページを参照してください。

- カスタマイズされた言語および音響モデル。これにより、ユーザーの特殊なドメイン ボキャブラリ、音声環境、および話し方に合わせてアプリケーションを調整できます。

- 自然言語の理解。 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) との統合を通して、音声から意図とエンティティを派生させることができます。 ユーザーはアプリのボキャブラリについて知る必要はありませんが、必要なものを独自の言葉で記述することができます。

## <a name="api-capabilities"></a>API の機能

**Speech to Text** API の機能の多く、とりわけカスタマイズ関係は、REST を介して使用することができます。 次の表に API にアクセスする各メソッドの機能をまとめます。 機能の完全な一覧と API の詳細については、[Swagger](https://swagger/service/11ed9226-335e-4d08-a623-4547014ba2cc#/) を参照してください

| ユース ケース | REST () | SDK |
|-----|-----|-----|----|
| コマンド (15 秒未満の長さ) などの短い発話を書き起こします。中間結果はありません | [はい] | [はい] |
| 長い発話 (15 秒を超える) を書き起こします | いいえ  | [はい] |
| ストリーミング オーディオを書き起こします。中間結果は省略可能です | いいえ  | [はい] |
| LUIS を介して話者の意図を理解します | いいえ\* | [はい] |
| 正確性テストを作成します | [はい] | いいえ  |
| モデル適応のためのデータセットをアップロードします | [はい] | いいえ  |
| 音声モデルを作成および管理します | [はい] | いいえ  |
| モデル デプロイを作成および管理します | [はい] | いいえ  |
| サブスクリプションの管理 | [はい] | いいえ  |
| モデル デプロイを作成および管理します | [はい] | いいえ  |
| モデル デプロイを作成および管理します | [はい] | いいえ  |

> [!NOTE]
> REST API で、API 要求を 5 秒ごとに 25 個までに制限するスロットリングを実装します。 この制限はメッセージ ヘッダーで通知されます

\* *LUIS の意図とエンティティは、個別の LUIS サブスクリプションを使用して派生させることができます。このサブスクリプションの場合は、SDK で自動的に LUIS を呼び出して、エンティティの結果、意図の結果、音声文字起こしを提供することができます。REST API の場合は、LUIS を自分で呼び出すことで、LUIS サブスクリプションを使用して意図とエンティティを派生させることができます。*

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [クイック スタート: C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
* [C# で音声から意図を認識する方法を確認する](how-to-recognize-intents-from-speech-csharp.md)
