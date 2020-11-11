---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 0964872d03d6e321d25d51a18edbb4a6f0be8a4f
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425255"
---
このクイックスタートでは、Speech Service と cURL を使用して音声をテキストに変換する方法について学習します。

音声テキスト変換の概念の概要については、[概要](../../../speech-to-text.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-speech-to-text"></a>音声をテキストに変換する

コマンド プロンプトで、次のコマンドを実行します。 次の値をコマンドに挿入する必要があります。
- 音声サービスのサブスクリプション キー。
- Speech Service のリージョン。
- 入力オーディオ ファイルのパス。 [テキスト読み上げ](../../../get-started-text-to-speech.md)を使用してオーディオ ファイルを生成できます。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

次のような応答を受け取ります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

詳細については、[音声テキスト変換 REST API リファレンス](../../../rest-speech-to-text.md)を参照してください。