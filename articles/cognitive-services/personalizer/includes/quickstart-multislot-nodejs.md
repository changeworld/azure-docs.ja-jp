---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: cog-serv-seo-aug-2020
ms.date: 03/23/2021
ms.openlocfilehash: feb9a43b68d668e19e1fcb4b2a978f113d9ef436
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255314"
---
[リファレンス ドキュメント](/javascript/api/@azure/cognitiveservices-personalizer/) | [マルチスロットの概念](..\concept-multi-slot-personalization.md) | [サンプル](https://aka.ms/personalizer/ms-nodejs)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Node.js](https://nodejs.org) と NPM をインストールします (Node.js v 14.16.0 と NPM 6.14.11 で検証されています)。
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer リソースを作成"  target="_blank">Personalizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Personalizer API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-nodejs-application"></a>新しい Node.js アプリケーションを作成する

コンソール ウィンドウ (cmd、PowerShell、Bash など) で、ご利用のアプリ用に新しいディレクトリを作成し、そこに移動します。

```console
mkdir myapp && cd myapp
```

`npm init -y` コマンドを実行して、`package.json` ファイルを作成します。

```console
npm init -y
```

任意のエディターまたは IDE で、`sample.js` という名前の新しい Node.js アプリケーションを作成し、リソースのエンドポイントとサブスクリプション キー用の変数を作成します。 

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```javascript
const axios = require('axios');
const { v4: uuidv4 } = require('uuid');
const readline = require('readline-sync');
// The endpoint specific to your personalization service instance; 
// e.g. https://<your-resource-name>.cognitiveservices.azure.com
const PersonalizationBaseUrl = '<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>';
// The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
const ResourceKey = '<REPLACE-WITH-YOUR-PERSONALIZER-KEY>';
```

### <a name="install-the-npm-packages-for-quickstart"></a>クイックスタート用の NPM パッケージをインストールする

```console
npm install readline-sync uuid axios --save
```

## <a name="object-model"></a>オブジェクト モデル

各スロットに対してコンテンツの 1 つの最適な項目を要求するには、 **[rankRequest]** を作成してから、POST 要求を [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank) に送信します。 その後、応答は **[rankResponse]** に解析されます。

報酬スコアを Personalizer に送信するには、 **[rewards]** を作成してから、POST 要求を [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) に送信します。

このクイックスタートでは、報酬スコアの決定は重要ではありません。 実稼働システムでは、何がどの程度まで[報酬スコア](../concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、この設計上の意思決定を主要な意思決定に含めるようにしてください。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、NodeJS に HTTP 要求を送信することによって以下のタスクを実行する方法が示されています。

* [ベース URL を作成する](#create-base-urls)
* [Rank API](#request-the-best-action)
* [Reward API](#send-a-reward)


## <a name="create-base-urls"></a>ベース URL を作成する

このセクションでは、ベース URL を使用してランクと報酬の URL を作成し、リソース キーを使用して要求ヘッダーを作成します。

```javascript
const MultiSlotRankUrl = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/rank');
const MultiSlotRewardUrlBase = PersonalizationBaseUrl.concat('personalizer/v1.1-preview.1/multislot/events/');
const Headers = {
    'ocp-apim-subscription-key': ResourceKey,
    'Content-Type': 'application/json'
};
```

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションはコンテンツの選択肢を表します。Personalizer を使用して、この中から最適なコンテンツ項目を選択します。 一連のアクションとそのフィーチャーを表す次のメソッドをスクリプトに追加します。 

```javascript
function getActions() {
    return [
        {
            'id': 'Red-Polo-Shirt-432',
            'features': [
                {
                    'onSale': 'true',
                    'price': 20,
                    'category': 'Clothing'
                }
            ]
        },
        {
            'id': 'Tennis-Racket-133',
            'features': [
                {
                    'onSale': 'false',
                    'price': 70,
                    'category': 'Sports'
                }
            ]
        },
        {
            'id': '31-Inch-Monitor-771',
            'features': [
                {
                    'onSale': 'true',
                    'price': 200,
                    'category': 'Electronics'
                }
            ]
        },
        {
            'id': 'XBox-Series X-117',
            'features': [
                {
                    'onSale': 'false',
                    'price': 499,
                    'category': 'Electronics'
                }
            ]
        }
    ];
}
```

## <a name="get-user-preferences-for-context"></a>コンテキストに対するユーザーの好みを取得する

スクリプトに次のメソッドを追加し、時間帯とユーザーが使用しているデバイスの種類に関するユーザーの入力をコマンド ラインから取得します。 これらはコンテキストのフィーチャーとして使用されます。

```javascript
function getContextFeatures() {
    const timeOfDayFeatures = ['morning', 'afternoon', 'evening', 'night'];
    const deviceFeatures = ['mobile', 'tablet', 'desktop'];

    let answer = readline.question('\nWhat time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n');
    let selection = parseInt(answer);
    const timeOfDay = selection >= 1 && selection <= 4 ? timeOfDayFeatures[selection - 1] : timeOfDayFeatures[0];

    answer = readline.question('\nWhat type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n');
    selection = parseInt(answer);
    const device = selection >= 1 && selection <= 3 ? deviceFeatures[selection - 1] : deviceFeatures[0];

    console.log('Selected features:\n');
    console.log('Time of day: ' + timeOfDay + '\n');
    console.log('Device: ' + device + '\n');

    return [
        {
            'time': timeOfDay
        },
        {
            'device': device
        }
    ];
}
```

## <a name="get-slots"></a>スロットを取得する

スロットにより、ユーザーが対話するページが構成されます。 Personalizer により、定義されている各スロットに表示するアクションが決定されます。 アクションは、`ExcludeActions` として示されている特定のスロットから除外できます。 `BaselineAction` はスロットの既定のアクションであり、Personalizer を使用しない場合に表示されていたものです。


このクイックスタートには、単純なスロット機能があります。 運用システムでは、[フィーチャー](../concepts-features.md)を決定して[評価](../concept-feature-evaluation.md)することが、簡単ではない場合があります。

```javascript
function getSlots() {
    return [
        {
            'id': 'BigHeroPosition',
            'features': [
                {
                    'size': 'large',
                    'position': 'left',
                }
            ],
            'excludedActions': ['31-Inch-Monitor-771'],
            'baselineAction': 'Red-Polo-Shirt-432'
        },
        {
            'id': 'SmallSidebar',
            'features': [
                {
                    'size': 'small',
                    'position': 'right',
                }
            ],
            'excludedActions': ['Tennis-Racket-133'],
            'baselineAction': 'XBox-Series X-117'
        }
    ];
}
```

## <a name="make-http-requests"></a>HTTP 要求を行う

マルチスロットの Rank 呼び出しと Reward 呼び出しの場合は、これらの関数を追加して POST 要求を Personalizer のエンドポイントに送信します。

```javascript
async function sendMultiSlotRank(rankRequest) {
    try {
        let response = await axios.post(MultiSlotRankUrl, rankRequest, { headers: Headers })
        return response.data;
    }
    catch (err) {
        if(err.response)
        {
            throw err.response.data
        }
        console.log(err)
        throw err;
    }
}
```

```javascript
async function sendMultiSlotReward(rewardRequest, eventId) {
    try {
        let rewardUrl = MultiSlotRewardUrlBase.concat(eventId, '/reward');
        let response = await axios.post(rewardUrl, rewardRequest, { headers: Headers })
    }
    catch (err) {
        console.log(err);
        throw err;
    }
}
```

## <a name="get-feedback-for-personalizer-decisions"></a>Personalizer の決定に関するフィードバックを取得する


次のメソッドをスクリプトに追加します。 Personalizer によりコマンド ライン プロンプトを使用して各スロットの適切な決定が行われたかどうかを通知します。

```javascript
function getRewardForSlot() {
    let answer = readline.question('\nIs this correct? (y/n)\n').toUpperCase();
    if (answer === 'Y') {
        console.log('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n');
        return 1;
    }
    else if (answer === 'N') {
        console.log('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n');
        return 0;
    }
    console.log('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n');
    return 0;
}
```

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[Rank](#request-the-best-action) 呼び出しと [Reward](#send-a-reward) 呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各 Rank 呼び出しの後に Reward 呼び出しを行って、サービスがどの程度適切に実行されたかを Personalizer に伝えます。

次のコードでは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信して各スロットに最適なアクションを選択し、その選択をリストから選択できるようユーザーに提示した後、その選択にサービスがどの程度寄与したかを示す報酬スコアを Personalizer に送る形でサイクルをループで処理しています。

```javascript
let runLoop = true;

(async () => {
    do {

        let multiSlotRankRequest = {};

        // Generate an ID to associate with the request.
        multiSlotRankRequest.eventId = uuidv4();

        // Get context information from the user.
        multiSlotRankRequest.contextFeatures = getContextFeatures();

        // Get the actions list to choose from personalization with their features.
        multiSlotRankRequest.actions = getActions();

        // Get the list of slots for which Personalizer will pick the best action.
        multiSlotRankRequest.slots = getSlots();

        multiSlotRankRequest.deferActivation = false;

        try {
            //Rank the actions for each slot
            let multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
            let multiSlotrewards = {};
            multiSlotrewards.reward = [];
    
            for (let i = 0; i < multiSlotRankResponse.slots.length; i++) {
                console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));
    
                let slotReward = {};
                slotReward.slotId = multiSlotRankResponse.slots[i].id;
                // User agrees or disagrees with Personalizer decision for slot
                slotReward.value = getRewardForSlot();
                multiSlotrewards.reward.push(slotReward);
            }
    
            // Send the rewards for the event
            await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
    
            let answer = readline.question('\nPress q to break, any other key to continue:\n').toUpperCase();
            if (answer === 'Q') {
                runLoop = false;
            }
        }
        catch (err) {
            console.log(err);
            throw err;
        }



    } while (runLoop);
})()
```

rank 呼び出しと reward 呼び出しについて、以降の各セクションでさらに詳しく見ていきましょう。

次のメソッドを追加します。これにより、コード ファイルを実行する前に、[コンテンツの選択肢が取得](#get-content-choices-represented-as-actions)され、[コンテキストのユーザー設定が取得](#get-user-preferences-for-context)され、[スロットが取得](#get-slots)され、[HTTP 要求が行われ](#make-http-requests)て、[各スロットの報酬が取得](#get-feedback-for-personalizer-decisions)されます。

* getActions
* getContextFeatures
* getSlots
* sendRank
* sendReward
* getRewardForSlot

## <a name="request-the-best-action"></a>最適なアクションを要求する

Rank 要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢を作成します。 要求本文には、コンテキスト、アクション、スロットと、それぞれの機能が含まれています。 `sendMultiSlotRank` メソッドは、rankRequest を受け取り、マルチスロットのランク要求を実行します。

このクイックスタートのコンテキストのフィーチャーは、時間帯とユーザーのデバイスという単純なものです。 実稼働システムでは、[アクションとフィーチャー](../concepts-features.md)を決定し、[評価](../concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。

```javascript
let multiSlotRankRequest = {};

// Generate an ID to associate with the request.
multiSlotRankRequest.eventId = uuidv4();

// Get context information from the user.
multiSlotRankRequest.contextFeatures = getContextFeatures();

// Get the actions list to choose from personalization with their features.
multiSlotRankRequest.actions = getActions();

// Get the list of slots for which Personalizer will pick the best action.
multiSlotRankRequest.slots = getSlots();

multiSlotRankRequest.deferActivation = false;

//Rank the actions for each slot
try {
    let multiSlotRankResponse = await sendMultiSlotRank(multiSlotRankRequest);
}
catch (err) {
    console.log(err);
    throw err;
}
```

## <a name="send-a-reward"></a>報酬を送信する

Reward 要求用の報酬スコアを取得するために、プログラムでは各スロットのユーザーの選択をコマンド ラインから取得し、選択に数値 (報酬スコア) を割り当てた後、一意のイベント ID、スロット ID、各スロットの報酬スコアを `sendMultiSlotReward` メソッドに送信します。 スロットごとに報酬を定義する必要はありません。

このクイックスタートでは、0 または 1 という単純な数値を報酬スコアとして割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](../concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。

```javascript
let multiSlotrewards = {};
multiSlotrewards.reward = [];

for (i = 0; i < multiSlotRankResponse.slots.length; i++) {
    console.log('\nPersonalizer service decided you should display: '.concat(multiSlotRankResponse.slots[i].rewardActionId, ' in slot ', multiSlotRankResponse.slots[i].id, '\n'));

    let slotReward = {};
    slotReward.slotId = multiSlotRankResponse.slots[i].id;
    // User agrees or disagrees with Personalizer decision for slot
    slotReward.value = getRewardForSlot();
    multiSlotrewards.reward.push(slotReward);
}

// Send the rewards for the event
await sendMultiSlotReward(multiSlotrewards, multiSlotRankResponse.eventId);
```

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから Node.js を使用して、アプリケーションを実行します。

```console
node sample.js
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


[こちらでこのクイックスタート用のソース コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Personalizer/multislot-quickstart)を入手できます。
