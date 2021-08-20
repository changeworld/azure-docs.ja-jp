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
ms.openlocfilehash: da6a271275c0b3b4f8d412bf622e6171609b3128
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/16/2021
ms.locfileid: "112255729"
---
[マルチスロットの概念](..\concept-multi-slot-personalization.md) | [サンプル](https://aka.ms/personalizer/ms-python)

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* Azure サブスクリプションを用意できたら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer"  title="Personalizer リソースを作成"  target="_blank">Personalizer リソースを作成</a>し、自分のキーとエンドポイントを取得します。 デプロイされたら、 **[リソースに移動]** をクリックします。
    * アプリケーションを Personalizer API に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。

## <a name="setting-up"></a>設定

[!INCLUDE [Upgrade Personalizer instance to Multi-Slot](upgrade-personalizer-multi-slot.md)]

[!INCLUDE [Change model frequency](change-model-frequency.md)]

[!INCLUDE [Change reward wait time](change-reward-wait-time.md)]

### <a name="create-a-new-python-application"></a>新しい Python アプリケーションを作成する

新しい Python ファイルを作成し、リソースのエンドポイントとサブスクリプション キー用の変数を作成します。

[!INCLUDE [Personalizer find resource info](find-azure-resource-info.md)]

```python
import json, uuid, requests

# The endpoint specific to your personalization service instance; 
# e.g. https://<your-resource-name>.cognitiveservices.azure.com
PERSONALIZATION_BASE_URL = "<REPLACE-WITH-YOUR-PERSONALIZER-ENDPOINT>"
# The key specific to your personalization service instance; e.g. "0123456789abcdef0123456789ABCDEF"
RESOURCE_KEY = "<REPLACE-WITH-YOUR-PERSONALIZER-KEY>"
```

## <a name="object-model"></a>オブジェクト モデル

各スロットに対してコンテンツの 1 つの最適な項目を要求するには、**rankRequest** を作成してから、POST 要求を [multislot/rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Rank) に送信します。 その後、応答が **rank_response** に解析されます。

報酬スコアを Personalizer に送信するには、 **[rewards]** を作成してから、POST 要求を [multislot/events/{eventId}/reward](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api-v1-1-preview-1/operations/MultiSlot_Events_Reward) に送信します。

このクイックスタートでは、報酬スコアの決定は重要ではありません。 実稼働システムでは、何がどの程度まで[報酬スコア](../concept-rewards.md)に影響を及ぼすかを特定するのは複雑なプロセスとなる場合があり、そのプロセスはやがて変更することになる場合もあります。 実際の Personalizer アーキテクチャでは、この設計上の意思決定を主要な意思決定に含めるようにしてください。

## <a name="code-examples"></a>コード例

これらのコード スニペットでは、Python に HTTP 要求を送信することによって以下のタスクを実行する方法が示されています。

* [ベース URL を作成する](#create-base-urls)
* [Rank API](#request-the-best-action)
* [Reward API](#send-a-reward)

## <a name="create-base-urls"></a>ベース URL を作成する

このセクションでは、ベース URL を使用してランクと報酬の URL を作成し、リソース キーを使用して要求ヘッダーを作成します。

```python
MULTI_SLOT_RANK_URL = '{0}personalizer/v1.1-preview.1/multislot/rank'.format(PERSONALIZATION_BASE_URL)
MULTI_SLOT_REWARD_URL_BASE = '{0}personalizer/v1.1-preview.1/multislot/events/'.format(PERSONALIZATION_BASE_URL)
HEADERS = {
    'ocp-apim-subscription-key': RESOURCE_KEY,
    'Content-Type': 'application/json'
}
```

## <a name="get-content-choices-represented-as-actions"></a>アクションとして表されるコンテンツの選択肢を取得する

アクションはコンテンツの選択肢を表します。Personalizer を使用して、この中から最適なコンテンツ項目を選択します。 一連のアクションとそのフィーチャーを表す次のメソッドをスクリプトに追加します。 

```python
def get_actions():
    return [
        {
            "id": "Red-Polo-Shirt-432",
            "features": [
                {
                    "onSale": "true",
                    "price": 20,
                    "category": "Clothing"
                }
            ]
        },
        {
            "id": "Tennis-Racket-133",
            "features": [
                {
                    "onSale": "false",
                    "price": 70,
                    "category": "Sports"
                }
            ]
        },
        {
            "id": "31-Inch-Monitor-771",
            "features": [
                {
                    "onSale": "true",
                    "price": 200,
                    "category": "Electronics"
                }
            ]
        },
        {
            "id": "XBox-Series X-117",
            "features": [
                {
                    "onSale": "false",
                    "price": 499,
                    "category": "Electronics"
                }
            ]
        }
    ]

```

## <a name="get-user-preferences-for-context"></a>コンテキストに対するユーザーの好みを取得する

スクリプトに次のメソッドを追加し、時間帯とユーザーが使用しているデバイスの種類に関するユーザーの入力をコマンド ラインから取得します。 これらはコンテキストのフィーチャーとして使用されます。

```python
def get_context_features():
    time_features = ["morning", "afternoon", "evening", "night"]
    time_pref = input("What time of day is it (enter number)? 1. morning 2. afternoon 3. evening 4. night\n")
    try:
        parsed_time = int(time_pref)
        if(parsed_time <=0 or parsed_time > len(time_features)):
            raise IndexError
        time_of_day = time_features[parsed_time-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", time_features[0] + ".")
        time_of_day = time_features[0]

    device_features = ['mobile', 'tablet', 'desktop']
    device_pref = input("What type of device is the user on (enter number)? 1. mobile 2. tablet 3. desktop\n")
    try:
        parsed_device = int(device_pref)
        if(parsed_device <=0 or parsed_device > len(device_features)):
            raise IndexError
        device = device_features[parsed_device-1]
    except (ValueError, IndexError):
        print("Entered value is invalid. Setting feature value to", device_features[0]+ ".")
        device = device_features[0]

    return [
        {'time': time_of_day},
        {'device': device}
        ]
```

## <a name="get-slots"></a>スロットを取得する

スロットにより、ユーザーが対話するページが構成されます。 Personalizer により、定義されている各スロットに表示するアクションが決定されます。 アクションは、`ExcludeActions` として示されている特定のスロットから除外できます。 `BaselineAction` はスロットの既定のアクションであり、Personalizer を使用しない場合に表示されていたものです。

このクイックスタートには、単純なスロット機能があります。 運用システムでは、[フィーチャー](../concepts-features.md)を決定して[評価](../concept-feature-evaluation.md)することが、簡単ではない場合があります。

```python
def get_slots():
    return [
        {
            "id": "BigHeroPosition",
            "features": [
                {
                    "size": "large",
                    "position": "left",
                }
            ],
            "excludedActions": ["31-Inch-Monitor-771"],
            "baselineAction": "Red-Polo-Shirt-432"
        },
        {
            "id": "SmallSidebar",
            "features": [
                {
                    "size": "small",
                    "position": "right",
                }
            ],
            "excludedActions": ["Tennis-Racket-133"],
            "baselineAction": "XBox-Series X-117"
        }
    ]
```

## <a name="make-http-requests"></a>HTTP 要求を行う

マルチスロットの Rank 呼び出しと Reward 呼び出しの場合は、これらの関数を追加して POST 要求を Personalizer のエンドポイントに送信します。

```python
def send_multi_slot_rank(rank_request):
multi_slot_response = requests.post(MULTI_SLOT_RANK_URL, data=json.dumps(rank_request), headers=HEADERS)
if multi_slot_response.status_code != 201:
    raise Exception(multi_slot_response.text)
return json.loads(multi_slot_response.text)
```

```python
def send_multi_slot_reward(reward_request, event_id):
    reward_url = '{0}{1}/reward'.format(MULTI_SLOT_REWARD_URL_BASE, event_id)
    requests.post(reward_url, data=json.dumps(reward_request), headers=HEADERS)
```

## <a name="get-feedback-for-personalizer-decisions"></a>Personalizer の決定に関するフィードバックを取得する

次のメソッドをスクリプトに追加します。 Personalizer によりコマンド ライン プロンプトを使用して各スロットの適切な決定が行われたかどうかを通知します。

```python
def get_reward_for_slot():
    answer = input('\nIs this correct? (y/n)\n').upper()
    if (answer == 'Y'):
        print('\nGreat! The application will send Personalizer a reward of 1 so it learns from this choice of action for this slot.\n')
        return 1
    elif (answer == 'N'):
        print('\nYou didn\'t like the recommended item.The application will send Personalizer a reward of 0 for this choice of action for this slot.\n')
        return 0
    print('\nEntered choice is invalid. Service assumes that you didn\'t like the recommended item.\n')
    return 0
```

## <a name="create-the-learning-loop"></a>学習ループを作成する

Personalizer の学習ループとは、[Rank](#request-the-best-action) 呼び出しと [Reward](#send-a-reward) 呼び出しのサイクルです。 このクイックスタートでは、コンテンツをパーソナライズするための各 Rank 呼び出しの後に Reward 呼び出しを行って、サービスがどの程度適切に実行されたかを Personalizer に伝えます。

次のコードでは、コマンド ラインでユーザーに好みをたずね、その情報を Personalizer に送信して各スロットに最適なアクションを選択し、その選択をリストから選択できるようユーザーに提示した後、その選択にサービスがどの程度寄与したかを示す報酬スコアを Personalizer に送る形でサイクルをループで処理しています。

```python
run_loop = True

while run_loop:

    eventId = str(uuid.uuid4())
    context = get_context_features()
    actions = get_actions()
    slots = get_slots()

    rank_request = {
        "eventId": eventId,
        "contextFeatures": context,
        "actions": actions,
        "slots": slots,
        "deferActivation": False
      }

    #Rank the actions for each slot
    multi_slot_rank_response = send_multi_slot_rank(rank_request)
    multi_slot_rewards = {"reward": []}

    for i in range(len(multi_slot_rank_response['slots'])):
        print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

        slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
        # User agrees or disagrees with Personalizer decision for slot
        slot_reward['value'] = get_reward_for_slot()
        multi_slot_rewards['reward'].append(slot_reward)

    # Send the rewards for the event
    send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])

    answer = input('\nPress q to break, any other key to continue:\n').upper()
    if (answer == 'Q'):
        run_loop = False
```

rank 呼び出しと reward 呼び出しについて、以降の各セクションでさらに詳しく見ていきましょう。

次のメソッドを追加します。これにより、コード ファイルを実行する前に、[コンテンツの選択肢が取得](#get-content-choices-represented-as-actions)され、[コンテキストのユーザー設定が取得](#get-user-preferences-for-context)され、[スロットが取得](#get-slots)され、[HTTP 要求が行われ](#make-http-requests)て、[各スロットの報酬が取得](#get-feedback-for-personalizer-decisions)されます。

* get_actions
* get_context_features
* get_slots
* send_rank
* send_reward
* get_reward_for_dsot

## <a name="request-the-best-action"></a>最適なアクションを要求する

Rank 要求を実行するために、このプログラムは、ユーザーの好みをたずねてコンテンツの選択肢を作成します。 要求本文には、コンテキスト、アクション、スロットと、それぞれの機能が含まれています。 `send_multi_slot_rank` メソッドは、rankRequest を受け取り、マルチスロットのランク要求を実行します。

このクイックスタートのコンテキストのフィーチャーは、時間帯とユーザーのデバイスという単純なものです。 実稼働システムでは、[アクションとフィーチャー](../concepts-features.md)を決定し、[評価](../concept-feature-evaluation.md)することが、決して簡単ではない場合もあります。

```python
eventId = str(uuid.uuid4())
context = get_context_features()
actions = get_actions()
slots = get_slots()

rank_request = {
    "eventId": eventId,
    "contextFeatures": context,
    "actions": actions,
    "slots": slots,
    "deferActivation": False
    }

#Rank the actions for each slot
multi_slot_rank_response = send_multi_slot_rank(rank_request)
```

## <a name="send-a-reward"></a>報酬を送信する

Reward 要求用の報酬スコアを取得するために、プログラムでは各スロットのユーザーの選択をコマンド ラインから取得し、選択に数値 (報酬スコア) を割り当てた後、一意のイベント ID、スロット ID、各スロットの報酬スコアを `send_multi_slot_reward` メソッドに送信します。 スロットごとに報酬を定義する必要はありません。

このクイックスタートでは、0 または 1 という単純な数値を報酬スコアとして割り当てます。 実際のニーズにもよりますが、実稼働システムでは、いつ何を [Reward](../concept-rewards.md) 呼び出しに送信するかが決して簡単な決定事項ではない場合もあります。

```python
multi_slot_rewards = {"reward": []}

for i in range(len(multi_slot_rank_response['slots'])):
    print('\nPersonalizer service decided you should display: {0} in slot {1}\n'.format(multi_slot_rank_response['slots'][i]['rewardActionId'], multi_slot_rank_response['slots'][i]['id']))

    slot_reward = {'slotId': multi_slot_rank_response['slots'][i]['id']}
    # User agrees or disagrees with Personalizer decision for slot
    slot_reward['value'] = get_reward_for_slot()
    multi_slot_rewards['reward'].append(slot_reward)

# Send the rewards for the event
send_multi_slot_reward(multi_slot_rewards, multi_slot_rank_response['eventId'])
```

## <a name="run-the-program"></a>プログラムの実行

アプリケーション ディレクトリから Python を使用して、アプリケーションを実行します。

```console
python sample.py
```

![クイック スタート プログラムは、フィーチャーと呼ばれるユーザー設定を収集するためにいくつかの質問をしてから、最上位のアクションを提供します。](../media/csharp-quickstart-commandline-feedback-loop/multislot-quickstart-program-feedback-loop-example-1.png)


[こちらでこのクイックスタート用のソース コード](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Personalizer/multislot-quickstart)を入手できます。
