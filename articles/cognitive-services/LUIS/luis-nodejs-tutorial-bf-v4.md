---
title: チュートリアル:Language Understanding ボット Node.js v4
description: このチュートリアルでは、Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このチャット ボットは、人事アプリを使用して、ボット ソリューションをすばやく実装します。 このボットは、Bot Framework バージョン 4 と Azure Web アプリ ボットで作成します。
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987844"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>チュートリアル:Node.js で Language Understanding に対応した Web アプリ ボットを使用する

Node.js を使用して、Language Understanding (LUIS) と統合されたチャット ボットを作成します。 このボットは、Azure [Web アプリ ボット](https://docs.microsoft.com/azure/bot-service/) リソースと [Bot Framework バージョン](https://github.com/Microsoft/botbuilder-dotnet) V4 で作成します。

**このチュートリアルで学習する内容は次のとおりです。**

> [!div class="checklist"]
> * Web アプリ ボットを作成する。 このプロセスにより、新しい LUIS アプリが作成されます。
> * Web ボット サービスによって作成されたボット プロジェクトをダウンロードする
> * ボットとエミュレーターをコンピューター上でローカルに起動する
> * ボットの発話結果を表示する

## <a name="prerequisites"></a>前提条件

* [Bot emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Web アプリ ボット リソースを作成する

1. [Azure portal](https://portal.azure.com) で、 **[新しいリソースの作成]** を選択します。

1. 検索ボックスで、 **[Web アプリ ボット]** を検索して選択します。 **作成** を選択します。

1. **[ボット サービス]** で、必要な情報を指定します。

    |設定|目的|推奨される設定|
    |--|--|--|
    |ボット名|リソース名|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |サブスクリプション|サブスクリプション ボットを作成するサブスクリプション。|プライマリ サブスクリプション。
    |Resource group|Azure リソースの論理グループ|このボットで使用するすべてのリソースを格納する新しいグループを作成し、グループに `luis-nodejs-bot-resource-group` という名前を付けます。|
    |Location|Azure リージョン - これは LUIS の作成または公開リージョンと同じである必要はありません。|`westus`|
    |Pricing tier|サービス要求の制限と課金に使用されます。|`F0` は無料レベルです。
    |アプリの名前|この名前は、ボットがクラウドにデプロイされるときに、サブドメインとして使用されます (humanresourcesbot.azurewebsites.net など)。|`luis-nodejs-bot-` + `<your-name>` (例: `luis-nodejs-bot-johnsmith`)|
    |ボット テンプレート|Bot Framework の設定 - 次の表を参照|
    |LUIS アプリの場所|LUIS リソース リージョンと同じである必要があります|`westus`|
    |App Service プラン/場所|指定されている既定値は変更しないでください。|
    |Application Insights|指定されている既定値は変更しないでください。|
    |Microsoft App ID and password (Microsoft アプリ ID とパスワード)|指定されている既定値は変更しないでください。|

1. **[ボット テンプレート]** で、次の項目を選択して、それらの設定の下にある **[選択]** ボタンを選択します。

    |設定|目的|[選択]|
    |--|--|--|
    |SDK バージョン|Bot Framework のバージョン|**SDK v4**|
    |SDK 言語|ボットのプログラミング言語|**Node.js**|
    |ボット|ボットの種類|**基本ボット**|

1. **作成** を選択します。 これによって、ボット サービスが作成され、Azure にデプロイされます。 このプロセスの一環として、`luis-nodejs-bot-XXXX` という LUIS アプリが作成されます。 この名前は、Azure Bot Service のアプリ名に基づきます。

    > [!div class="mx-imgBorder"]
    > [![Web アプリ ボットの作成](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    ボット サービスが作成されるまで待ち、次に進みます。

## <a name="the-bot-has-a-language-understanding-model"></a>ボットの Language Understanding モデル

ボット サービスの作成プロセスにより、意図と発話の例を含む新しい LUIS アプリも作成されます。 このボットは、以下の意図のために新しい LUIS アプリへの意図のマッピングを行います。

|基本的なボット LUIS の意図|発話の例|
|--|--|
|航空便の予約|`Travel to Paris`|
|Cancel|`bye`|
|GetWeather|`what's the weather like?`|
|なし|アプリのドメインの外側にあるものすべて。|

## <a name="test-the-bot-in-web-chat"></a>Web チャットでのボットのテスト

1. 引き続き Azure portal で、新しいボットに対して **[Test in Web Chat]\(Web チャットでのテスト\)** を選択します。
1. **[Type your message]\(メッセージを入力\)** ボックスに、テキスト `Book a flight from Seattle to Berlin tomorrow` を入力します。 このボットは、フライトを予約したい旨の確認で応答します。

    ![Azure portal のスクリーンショット。テキスト「hello」を入力しています。](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    ボットをすばやくテストするために、テスト機能を使用できます。 デバッグを含む詳細なテストを行う場合、ボット コードをダウンロードし、Visual Studio を使用します。

## <a name="download-the-web-app-bot-source-code"></a>Web アプリ ボットのソース コードをダウンロードする
Web アプリ ボットのコードを開発するためには、コードをダウンロードし、ローカル コンピューターで使用します。

1. Azure portal の **[ボットの管理]** セクションで **[ビルド]** を選択します。

1. **[ボットのソース コードをダウンロードする]** を選択します。

    [![基本ボットの Web アプリ ボットのソース コードのダウンロード](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. ポップアップ ダイアログで、 **[Include app settings in the downloaded zip file?]\(ダウンロードする zip ファイルにアプリの設定を含めますか?\)** と尋ねられたら、 **[はい]** を選択します。 これで LUIS の設定が得られます。

1. ソース コードが圧縮されている場合は、コードをダウンロードするリンクを示すメッセージが表示されます。 リンクを選択します。

1. .zip ファイルをローカル コンピューターに保存し、ファイルを抽出します。 Visual Studio でプロジェクトを開きます。

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>LUIS に発話を送信し、応答を取得するコードを確認する

1. ユーザーの発話を LUIS の予測エンドポイントに送信するには、**dialogs の flightBookingRecognizer.js** ファイルを開きます。 これは、ボット内に入力されたユーザーの発話が LUIS に送信される場所です。 LUIS からの応答は、**executeLuisQuery** メソッドから返されます。

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. **dialogs の mainDialog** は発話をキャプチャし、actStep メソッドで executeLuisQuery に送信します。


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>ボット エミュレーターを使用してボットをテストする

航空便を予約する意図があることをボットに伝えます。

1. ボット エミュレーターを開始し、 **[Open Bot]\(ボットを開く\)** を選択します。
1. **[Open a bot]\(ボットを開く\)** ポップアップ ダイアログで、ボットの URL (`http://localhost:3978/api/messages` など) を入力します。 `/api/messages` ルートは、ボットの Web アドレスです。
1. **Microsoft アプリ ID** と **Microsoft アプリ パスワード**を入力します。これらは、ダウンロードしたボット コードのルートにある **.env** ファイル内にあります。

1. ボット エミュレーターで、「`Book a flight from Seattle to Berlin tomorrow`」と入力します。これにより、 **[Test in Web Chat]\(Web チャットでのテスト\)** で受け取った応答と同じ基本ボットの応答を受け取ります。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. **[はい]** を選択します。 ボットは、その操作の要約で応答します。
1. ボット エミュレーターのログから、`Luis Trace` を含む行を選択します。 これにより、発話の意図とエンティティについて LUIS からの JSON 応答が表示されます。

    [![エミュレーターでの基本ボットの返答](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>次のステップ

会話型ボットのある他の[サンプル](https://github.com/microsoft/botframework-solutions)を確認してください。

> [!div class="nextstepaction"]
> [カスタムの主題の領域を使用して Language Understanding アプリを作成する](luis-quickstart-intents-only.md)
