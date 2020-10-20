---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 5eb204da12b3c3405d52382285b5d75363f266cf
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91875504"
---
このクイックスタートでは、Speech SDK を使用した Speaker Recognition の基本的な設計パターンについて学習します。これには次のものが含まれます。

* テキスト依存およびテキスト非依存の認証
* 音声のグループから音声サンプルを識別する話者識別
* 音声プロファイルの削除

Speech Recognition の概念の概要については、[概要](../../../speaker-recognition-overview.md)に関する記事を参照してください。

## <a name="skip-to-samples-on-github"></a>記事をスキップして GitHub 上のサンプルにアクセスする

この記事をスキップしてサンプル コードをご覧になりたい方は、GitHub 上の [JavaScript クイックスタート サンプル](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition)を参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 現在、Speaker Recognition は、`westus` リージョンで作成された Azure Speech リソース "*のみ*" でサポートされています。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

何らかの操作を行うには、事前に <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK for JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> をインストールしておく必要があります。 ご利用のプラットフォームに応じて、次の手順を行います。

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web ブラウザー <span class="docon docon-navigate-external x-hidden-focus"></span></a>

また、ターゲット環境によっては、次のいずれかを使用します。

# <a name="script"></a>[script](#tab/script)

<a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">Speech SDK for JavaScript <span class="docon docon-navigate-external x-hidden-focus"></span></a> *microsoft.cognitiveservices.speech.sdk.bundle.js* ファイルをダウンロードして抽出し、HTML ファイルにアクセス可能なフォルダーに配置します。

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Web ブラウザーを対象としていて、`<script>` タグを使用する場合は、`sdk` プレフィックスは必要ありません。 `sdk` プレフィックスは、`require` モジュールに名前を付けるために使用されるエイリアスです。

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

`import` の詳細については、「<a href="https://javascript.info/import-export" target="_blank">export および import <span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

`require` の詳細については、「<a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">require とは<span class="docon docon-navigate-external x-hidden-focus"></span></a>」を参照してください。

---

## <a name="import-dependencies"></a>依存関係のインポート

この記事の例を実行するには、.js ファイルの先頭に次のステートメントを追加します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

これらのステートメントでは、必要なライブラリをインポートし、環境変数から Speech Service のサブスクリプション キーとリージョンを取得します。 また、次のタスクで使用するオーディオ ファイルへのパスも指定します。

## <a name="create-helper-function"></a>ヘルパー関数を作成する

Speech Service で使用するためにオーディオ ファイルをストリームに読み取るには、次のヘルパー関数を追加します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

この関数では、[AudioInputStream.createPushStream](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream?view=azure-node-latest#createpushstream-audiostreamformat-) および [AudioConfig.fromStreamInput](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) メソッドを使用して、[AudioConfig](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig?view=azure-node-latest) オブジェクトを作成します。 この `AudioConfig` オブジェクトは、オーディオ ストリームを表します。 次のタスクでは、これらの `AudioConfig` オブジェクトのいくつかを使用します。

## <a name="text-dependent-verification"></a>テキスト依存の認証

Speaker Verification では、話者が既知の、または**登録されている**音声と一致することを確認します。 最初の手順では、音声プロファイルを**登録**し、サービスで今後の音声サンプルと比較できるようにします。 この例では、**テキスト依存**の戦略を使用してプロファイルを登録します。その場合、登録と認証の両方に使用する特定のパスフレーズが必要になります。 サポートされるパスフレーズの一覧については、[リファレンス ドキュメント](https://docs.microsoft.com/rest/api/speakerrecognition/)を参照してください。

### <a name="textdependentverification-function"></a>TextDependentVerification 関数

まず、`TextDependentVerification` 関数を作成します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

この関数では、[VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) メソッドを使用して [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) オブジェクトを作成します。 `VoiceProfile` には次の 3 つの[種類](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype?view=azure-node-latest)があることに注意してください。

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

この場合は、`VoiceProfileType.TextDependentVerification` を `VoiceProfileClient.createProfileAsync` に渡します。

その後、次に定義する 2 つのヘルパー関数 `AddEnrollmentsToTextDependentProfile` と `SpeakerVerify` を呼び出します。 最後に、[VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) を呼び出してプロファイルを削除します。

### <a name="addenrollmentstotextdependentprofile-function"></a>AddEnrollmentsToTextDependentProfile 関数

音声プロファイルを登録するには、次の関数を定義します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

この関数では、前に定義した `GetAudioConfigFromFile` 関数を呼び出して、オーディオ サンプルから `AudioConfig` オブジェクトを作成します。 これらのオーディオ サンプルには、"my voice is my passport, verify me" などのパスフレーズが含まれています。 その後、[VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) メソッドを使用して、これらのオーディオ サンプルを登録します。

### <a name="speakerverify-function"></a>SpeakerVerify 関数

`SpeakerVerify` を次のように定義します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

この関数では、[SpeakerVerificationModel.FromProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest#fromprofile-voiceprofile-) メソッドを使用して [SpeakerVerificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel?view=azure-node-latest) オブジェクトを作成し、先ほど作成した [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) オブジェクトを渡します。

次に、[SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) メソッドを呼び出して、前に登録したオーディオ サンプルと同じパスフレーズを含むオーディオ サンプルを認証します。 `SpeechRecognizer.recognizeOnceAsync` は [SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest) オブジェクトを返します。このオブジェクトの `score` プロパティには、0.0 から 1.0 の範囲の類似性スコアが含まれています。 また、`SpeakerRecognitionResult` オブジェクトには、[ResultReason](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason?view=azure-node-latest) 型の `reason` プロパティも含まれています。 認証が成功した場合、`reason` プロパティの値は `RecognizedSpeaker` になります。

## <a name="text-independent-verification"></a>テキストに依存しない認証

**テキストに依存する**認証とは対照的に、**テキストに依存しない**認証は次のようになります。

* 特定のパスフレーズを読み上げる必要はなく、何を話してもかまいません
* 3 つのオーディオ サンプルは必要ありませんが、合計 20 秒のオーディオが "*必要*" になります

### <a name="textindependentverification-function"></a>TextIndependentVerification 関数

まず、`TextIndependentVerification` 関数を作成します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

`TextDependentVerification` 関数と同様に、この関数では、[VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) メソッドを使用して [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) オブジェクトを作成します。

この場合は、`VoiceProfileType.TextIndependentVerification` を `createProfileAsync` に渡します。

その後、2 つのヘルパー関数を呼び出します。1 つは次に定義する `AddEnrollmentsToTextIndependentProfile`、もう 1 つは既に定義した `SpeakerVerify` です。 最後に、[VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) を呼び出してプロファイルを削除します。

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

音声プロファイルを登録するには、次の関数を定義します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

この関数では、前に定義した `GetAudioConfigFromFile` 関数を呼び出して、オーディオ サンプルから `AudioConfig` オブジェクトを作成します。 その後、[VoiceProfileClient.enrollProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-) メソッドを使用して、これらのオーディオ サンプルを登録します。

## <a name="speaker-identification"></a>話者識別

話者識別を使用して、登録されている音声の特定のグループの**誰**が話しているのかを判別します。 このプロセスは**テキストに依存しない認証**とよく似ています。主な違いは、1 つのプロファイルに対して認証するのではなく、一度に複数の音声プロファイルに対して認証できることです。

### <a name="textindependentidentification-function"></a>TextIndependentIdentification 関数

まず、`TextIndependentIdentification` 関数を作成します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

`TextDependentVerification` および `TextIndependentVerification` 関数と同様に、この関数では、[VoiceProfileClient.createProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-) メソッドを使用して [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) オブジェクトを作成します。

この場合は、`VoiceProfileType.TextIndependentIdentification` を `VoiceProfileClient.createProfileAsync` に渡します。

その後、2 つのヘルパー関数を呼び出します。1 つは既に定義した `AddEnrollmentsToTextIndependentProfile`、もう 1 つは次に定義する `SpeakerIdentify` です。 最後に、[VoiceProfileClient.deleteProfileAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) を呼び出してプロファイルを削除します。

### <a name="speakeridentify-function"></a>SpeakerIdentify 関数

`SpeakerIdentify` 関数を次のように定義します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

この関数では、[SpeakerIdentificationModel.fromProfiles](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest#fromprofiles-voiceprofile---) メソッドを使用して [SpeakerIdentificationModel](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel?view=azure-node-latest) オブジェクトを作成し、先ほど作成した [VoiceProfile](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile?view=azure-node-latest) オブジェクトを渡します。

次に、[SpeechRecognizer.recognizeOnceAsync](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer?view=azure-node-latest#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) メソッドを呼び出して、オーディオ サンプルを渡します。
`SpeechRecognizer.recognizeOnceAsync` は、`SpeakerIdentificationModel` の作成に使用した `VoiceProfile` オブジェクトに基づいて、このオーディオ サンプルの音声の識別を試みます。 これにより、[SpeakerRecognitionResult](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult?view=azure-node-latest) オブジェクトが返されます。その `profileId` プロパティでは一致する `VoiceProfile` が識別され (存在する場合)、`score` プロパティには 0.0 から 1.0 までの類似性スコアが含まれています。

## <a name="main-function"></a>main 関数

最後に、`main` 関数を次のように定義します。

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

この関数では、音声プロファイルの作成、登録、および削除に使用される [VoiceProfileClient](https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient?view=azure-node-latest) オブジェクトを作成します。 その後、前に定義した関数を呼び出します。
