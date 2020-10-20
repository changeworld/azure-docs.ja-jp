---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940747"
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
- オーディオ出力形式を制御する `X-Microsoft-OutputFormat` ヘッダー値。 サポートされているオーディオ出力形式の一覧については、[REST API リファレンス](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs)を参照してください。
- 出力音声。 対象の Speech エンドポイントで使用可能な音声の一覧を取得するには、次のセクションを参照してください。
- 出力ファイル。 この例では、サーバーからの応答を `output.wav` という名前のファイルに送信します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>対象の Speech エンドポイントで使用可能な音声を一覧表示する

対象の Speech エンドポイントで使用可能な音声を一覧表示するには、次のコマンドを実行します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

次のような応答を受け取ります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
