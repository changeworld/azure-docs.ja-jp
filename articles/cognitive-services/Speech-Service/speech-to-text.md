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
ms.openlocfilehash: ba6710c8b5b8de1c63fa6778ea3853ab52365254
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325338"
---
# <a name="about-the-speech-to-text-api"></a>Speech to Text API について

**Speech to Text** API は、オーディオ ストリームを、アプリケーションでユーザーに表示できるテキストまたはコマンド入力として操作できるテキストに ”*書き起こします*”。 API は、SDK クライアント ライブラリ (サポートされているプラットフォームと言語の場合) または REST API のいずれかで使用できます。

**Speech to Text** API には、次の機能が用意されています。

- Microsoft の高度な音声認識テクノロジ: Cortana、Office、およびその他の Microsoft 製品で使用されているものと同じです。

- リアルタイムの継続的な認識。 **Speech to Text** により、ユーザーはリアルタイムでオーディオをテキストに書き起こすことができます。 また、これまでに認識されている単語の中間結果の受信もサポートされています。 サービスは音声の末尾を自動的に認識します。 ユーザーはまた、大文字/小文字の設定と句読点、不適切な表現のマスキング、逆テキスト正規化など、追加の書式設定オプションを選択することもできます。

- 対話、会話、およびディクテーションのシナリオでの**Speech to Text** 結果の最適化。 

- 多くの音声言語および方言のサポート。 各認識モードでサポートされている言語の完全な一覧については、[サポートされている言語](supported-languages.md#speech-to-text)に関するページを参照してください。

- カスタマイズされた言語および音響モデル。これにより、ユーザーの特殊なドメイン ボキャブラリ、音声環境、および話し方に合わせてアプリケーションを調整できます。

- 自然言語の理解。 [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) との統合を通して、音声から意図とエンティティを派生させることができます。 ユーザーはアプリのボキャブラリについて知る必要はありませんが、必要なものを独自の言葉で記述することができます。

## <a name="api-capabilities"></a>API の機能

**Speech to Text** API の一部の機能は、REST を介して利用することができません。 次の表に API にアクセスする各メソッドの機能をまとめます。

| ユース ケース | REST () | SDK |
|-----|-----|-----|----|
| コマンド (15 秒未満の長さ) などの短い発話を書き起こします。中間結果はありません | [はい] | [はい] |
| 長い発話 (15 秒を超える) を書き起こします | いいえ  | [はい] |
| ストリーミング オーディオを書き起こします。中間結果は省略可能です | いいえ  | [はい] |
| LUIS を介して話者の意図を理解します | いいえ\* | [はい] |

\* *LUIS の意図とエンティティは、個別の LUIS サブスクリプションを使用して派生させることができます。このサブスクリプションの場合は、SDK で自動的に LUIS を呼び出して、エンティティの結果、意図の結果、音声文字起こしを提供することができます。REST API の場合は、LUIS を自分で呼び出すことで、LUIS サブスクリプションを使用して意図とエンティティを派生させることができます。*

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [クイック スタート: C# で音声を認識する](quickstart-csharp-dotnet-windows.md)
* [C# で音声から意図を認識する方法を確認する](how-to-recognize-intents-from-speech-csharp.md)
