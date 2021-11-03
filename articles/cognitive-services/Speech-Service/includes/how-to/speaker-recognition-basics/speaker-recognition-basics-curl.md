---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 09/28/2020
ms.author: v-jawe
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: 11e93561a0f78ad1fb62d1cab1f3e8d8f3f3b293
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131069152"
---
このクイックスタートでは、Speech SDK を使用した Speaker Recognition の基本的な設計パターンについて学習します。これには次のものが含まれます。

* テキスト依存およびテキスト非依存の認証
* 音声のグループから音声サンプルを識別する話者識別
* 音声プロファイルの削除

Speaker Recognition の概念の概要については、[概要](../../../speaker-recognition-overview.md)に関する記事を参照してください。 サポートされているプラットフォームの一覧については、左側のナビゲーションの参照ノードを参照してください。

## <a name="prerequisites"></a>前提条件

この記事は、Azure アカウントと Speech Service サブスクリプションをお持ちであることを前提としています。 アカウントとサブスクリプションをお持ちでない場合は、[Speech Service を無料でお試しください](../../../overview.md#try-the-speech-service-for-free)。

> [!IMPORTANT]
> 現在、Speaker Recognition は、`westus` リージョンで作成された Azure Speech リソース "*のみ*" でサポートされています。

## <a name="text-dependent-verification"></a>テキスト依存の認証

Speaker Verification では、話者が既知の、または **登録されている** 音声と一致することを確認します。 最初の手順では、音声プロファイルを **登録** し、サービスで今後の音声サンプルと比較できるようにします。 この例では、**テキスト依存** の戦略を使用してプロファイルを登録します。その場合、登録と認証の両方に使用する特定のパスフレーズが必要になります。 サポートされるパスフレーズの一覧については、[リファレンス ドキュメント](/rest/api/speakerrecognition/)を参照してください。

まず、[音声プロファイルを作成](/rest/api/speakerrecognition/verification/textdependent/createprofile)します。 この記事の各 curl コマンドに、Speech Service のサブスクリプション キーとリージョンを挿入する必要があります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile":::

音声プロファイルには次の 3 種類があることに注意してください。

- テキスト依存の認証
- テキストに依存しない認証
- テキストに依存しない識別

この場合は、テキスト依存の認証音声プロファイルを作成します。 次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_create_profile_response":::

次に、[音声プロファイルを登録](/rest/api/speakerrecognition/verification/textdependent/createenrollment)します。 `--data-binary` パラメーター値には、サポートされているパスフレーズのいずれか (たとえば、"my voice is my passport, verify me") が含まれている、お使いのコンピューター上のオーディオ ファイルを指定します。 このようなオーディオ ファイルは、[Windows ボイス レコーダー](https://www.microsoft.com/p/windows-voice-recorder/9wzdncrfhwkn?activetab=pivot:overviewtab)などのアプリを使用して録音できます。また、[テキスト読み上げ](../../../index-text-to-speech.yml)を使用して生成することもできます。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_1":::

この応答は、さらに 2 つのオーディオ サンプルを登録する必要があることを示します。

合計 3 つのオーディオ サンプルを登録すると、次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_enroll_response_2":::

これで、[音声プロファイルに対してオーディオ サンプルを認証する](/rest/api/speakerrecognition/verification/textdependent/verifyprofile)準備が整いました。 このオーディオ サンプルには、音声プロファイルの登録に使用したサンプルと同じパスフレーズが含まれている必要があります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_verify_response":::

`Accept` は、パスフレーズが一致し、認証が成功したことを意味します。 また、応答には、0.0 から 1.0 の範囲の類似性スコアも含まれています。

終了するには、[音声プロファイルを削除](/rest/api/speakerrecognition/verification/textdependent/deleteprofile)します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tdv_delete_profile":::

応答はありません。

## <a name="text-independent-verification"></a>テキストに依存しない認証

**テキストに依存する** 認証とは対照的に、**テキストに依存しない** 認証は次のようになります。

* 特定のパスフレーズを読み上げる必要はなく、何を話してもかまいません
* 3 つのオーディオ サンプルは必要ありませんが、合計 20 秒のオーディオが "*必要*" になります

まず、[テキストに依存しない認証プロファイルを作成](/rest/api/speakerrecognition/verification/textindependent/createprofile)します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_create_profile_response":::

次に、[音声プロファイルを登録](/rest/api/speakerrecognition/verification/textindependent/createenrollment)します。 ここでも、3 つのオーディオ サンプルを送信するのではなく、合計 20 秒のオーディオを含むオーディオ サンプルを送信する必要があります。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll":::

十分なオーディオ サンプルを送信すると、次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_enroll_response":::

これで、[音声プロファイルに対してオーディオ サンプルを認証する](/rest/api/speakerrecognition/verification/textindependent/verifyprofile)準備が整いました。 繰り返しますが、このオーディオ サンプルにパスフレーズを含める必要はありません。 合計で少なくとも 4 秒のオーディオが含まれていれば、任意の音声を含めることができます。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_verify_response":::

`Accept` は、認証が成功したことを意味します。 また、応答には、0.0 から 1.0 の範囲の類似性スコアも含まれています。

終了するには、[音声プロファイルを削除](/rest/api/speakerrecognition/verification/textindependent/deleteprofile)します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tiv_delete_profile":::

応答はありません。

## <a name="speaker-identification"></a>話者識別

話者識別を使用して、登録されている音声の特定のグループの **誰** が話しているのかを判別します。 このプロセスは **テキストに依存しない認証** とよく似ています。主な違いは、1 つのプロファイルに対して認証するのではなく、一度に複数の音声プロファイルに対して認証できることです。

まず、[テキストに依存しない識別プロファイルを作成](/rest/api/speakerrecognition/identification/textindependent/createprofile)します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_create_profile_response":::

次に、[音声プロファイルを登録](/rest/api/speakerrecognition/identification/textindependent/createenrollment)します。 ここでも、合計 20 秒のオーディオを含むオーディオ サンプルを送信する必要があります。 これらのサンプルにパスフレーズを含める必要はありません。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll":::

十分なオーディオ サンプルを送信すると、次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_enroll_response_2":::

これで、[音声プロファイルを使用してオーディオ サンプルを識別する](/rest/api/speakerrecognition/identification/textindependent/identifysinglespeaker)準備が整いました。 識別コマンドは、使用可能な音声プロファイル ID のコンマ区切りリストを受け取ります。 この場合は、先ほど作成した音声プロファイルの ID を渡すだけです。 ただし、必要に応じて、複数の音声プロファイルの ID を渡すことができます。この場合、各音声プロファイルは、異なる音声のオーディオ サンプルに登録されています。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify":::

次の応答が返されます。

:::code language="json" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_identify_response":::

この応答には、送信したオーディオ サンプルに最も合致する音声プロファイルの ID が含まれています。 また、類似性の順でランク付けされた、候補となる音声プロファイルのリストも含まれています。

終了するには、[音声プロファイルを削除](/rest/api/speakerrecognition/identification/textindependent/deleteprofile)します。

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speaker-recognition.sh" id="tii_delete_profile":::

応答はありません。
