---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 8a877e1773431053c5ad7344209076cb868a0ee3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424904"
---
このクイックスタートでは、Speech Service と cURL を使用してテキストを音声に変換する方法について学習します。

テキスト読み上げの概念の概要については、[概要](../../../text-to-speech.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

## <a name="convert-text-to-speech"></a>テキストを音声に変換する

コマンド プロンプトで、次のコマンドを実行します。 次の値をコマンドに挿入する必要があります。
- 音声サービスのサブスクリプション キー。
- Speech Service のリージョン。

次の値を変更することもできます。
- オーディオ出力形式を制御する `X-Microsoft-OutputFormat` ヘッダー値。 サポートされているオーディオ出力形式の一覧については、[REST API リファレンス](../../../rest-text-to-speech.md#audio-outputs)を参照してください。
- 出力音声。 対象の Speech エンドポイントで使用可能な音声の一覧を取得するには、次のセクションを参照してください。
- 出力ファイル。 この例では、サーバーからの応答を `output.wav` という名前のファイルに送信します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>対象の Speech エンドポイントで使用可能な音声を一覧表示する

対象の Speech エンドポイントで使用可能な音声を一覧表示するには、次のコマンドを実行します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

次のような応答を受け取ります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::