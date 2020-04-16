---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 3ca50a9bad36e0174dc4ee0059c9d01fcc18a5f1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400984"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [開発環境を設定し、空のプロジェクトを作成する](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [ソース ファイルを Azure BLOB にアップロードする](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

## <a name="download-and-install-the-api-client-library"></a>API クライアント ライブラリをダウンロードしてインストールする

サンプルを実行するには、[Swagger](https://swagger.io) によって生成された REST API の Python ライブラリを生成する必要があります。

次のインストール手順に従います。

1. https://editor.swagger.io にアクセスします。
1. **[ファイル]** 、 **[URL のインポート]** の順にクリックします。
1. ご利用の Speech サービス サブスクリプションのリージョンを含む Swagger URL (`https://<your-region>.cris.ai/docs/v2.0/swagger`) を入力します。
1. **[クライアントの生成]** をクリックし、 **[Python]** を選択します。
1. クライアント ライブラリを保存します。
1. ダウンロードした python-client-generated.zip を、ご自身のファイル システム内の任意の場所に抽出します。
1. pip: `pip install path/to/package/python-client` を使用して、抽出された python-client モジュールを、ご自身の Python 環境にインストールします。
1. インストールされているパッケージの名前は `swagger_client` です。 コマンド `python -c "import swagger_client"` を使用して、インストールが正常に実行されたことを確認できます。

> [!NOTE]
> [Swagger 自動生成の既知のバグ](https://github.com/swagger-api/swagger-codegen/issues/7541)のため、`swagger_client` パッケージをインポートするときにエラーが発生することがあります。
> これらを修正するには、次のコンテンツを含む行を削除します。
> ```py
> from swagger_client.models.model import Model  # noqa: F401,E501
> ```
> 上記のコンテンツを `swagger_client/models/model.py` ファイルから削除します。また、次のコンテンツを含む行を削除します。
> ```py
> from swagger_client.models.inner_error import InnerError  # noqa: F401,E501
> ```
> 上記のコンテンツを、インストールされているパッケージ内の `swagger_client/models/inner_error.py` ファイルから削除します。 エラー メッセージには、上記のファイルがインストールされている場所が示されています。

## <a name="install-other-dependencies"></a>その他の依存関係をインストールする

サンプルでは `requests` ライブラリが使用されます。 これは、次のコマンドでインストールできます

```bash
pip install requests
```

## <a name="start-with-some-boilerplate-code"></a>定型コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加しましょう。

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-2,7-34,115-119)]

[!INCLUDE [placeholder-replacements](../placeholder-replacement.md)]

## <a name="create-and-configure-an-http-client"></a>Http クライアントを作成して構成する
最初に必要なのは、適切なベース URL と認証セットを含む Http クライアントです。
このコードを `transcribe` に挿入します[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=37-45)]

## <a name="generate-a-transcription-request"></a>文字起こし要求を生成する
次に、文字起こし要求を生成します。 このコードを `transcribe` に追加します[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=52-54)]

## <a name="send-the-request-and-check-its-status"></a>要求を送信し、その状態を確認する
ここで、Speech サービスに要求を投稿し、初期の応答コードを確認します。 この応答コードは、サービスが要求を受信したかどうかを示すだけに過ぎません。 サービスからは応答ヘッダーで URL が返され、文字起こしの状態はこの URL に格納されます。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=65-73)]

## <a name="wait-for-the-transcription-to-complete"></a>文字起こしが完了するのを待つ
サービスでは文字起こしが非同期的に処理されるため、その状態を頻繁にポーリングする必要があります。 チェックは 5 秒ごとに実行します。

この Speech サービス リソースが処理しているすべての文字起こしを列挙し、作成した文字起こしを検索します。

ここでは、正常な完了を除くすべての状態が示されているポーリング コードを示します。正常な完了については次に取り上げます。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=75-94,99-112)]

## <a name="display-the-transcription-results"></a>文字起こしの結果を表示する
サービスによる文字起こしが正常に完了すると、その結果は、状態の応答から取得できる別の URL に格納されます。

ここでは、JSON の結果を取得し、表示します。
[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=95-98)]

## <a name="check-your-code"></a>コードを確認する
この時点で、コードは次のようになります。(このバージョンにはいくつかのコメントを追加してあります) [!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/from-blob/python-client/main.py?range=1-118)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [footer](./footer.md)]
