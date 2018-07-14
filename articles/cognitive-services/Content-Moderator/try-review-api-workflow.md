---
title: Azure Content Moderator - API コンソールからのコンテンツ モデレーション ワークフロー | Microsoft Docs
description: API コンソールからコンテンツ モデレーション ワークフローを使用する方法について説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/05/2018
ms.author: sajagtap
ms.openlocfilehash: 700b2bea5e902141659266a94d61ceb810c1b802
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373261"
---
# <a name="workflows-from-the-api-console"></a>API コンソールのワークフロー

Azure Content Moderator で[ワークフロー操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)を使用し、Review API を使用してワークフローを作成または更新し、ワークフローの詳細を取得することができます。 この API を使用すると、ワークフローに単純な式、複雑な式、入れ子にされた式を定義することができます。 ワークフローは、チームで使用できるレビュー ツールに表示されます。 ワークフローは、Review API のジョブ操作にも使用されます。

## <a name="prerequisites"></a>前提条件

1. [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動します。 まだサインアップしていない場合はサインアップします。 
2. レビュー ツールの **[Settings]\(設定\)** で、レビュー ツールに表示される[ワークフロー チュートリアル](Review-Tool-User-Guide/Workflows.md)に従って **[Workflows]\(ワークフロー\)** タブを選択します。

### <a name="browse-to-the-workflows-screen"></a>ワークフローの参照画面

Content Moderator ダッシュボードで、**[Review]\(レビュー\)** > **[Settings]\(設定\)** > **[Workflows]\(ワークフロー\)** の順に選択します。 既定のワークフローが表示されます。

  ![既定のワークフロー](images/default-workflow-listed.PNG)

### <a name="get-the-json-definition-of-the-default-workflow"></a>既定ワークフローの JSON 定義を取得する

ワークフローの **[Edit]\(編集\)** オプションを選択し、**[JSON]** タブを選択します。次の JSON 式が表示されます。

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isAdult",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": []
        }
        ],
        "Type": "Actions"
        }
    }

## <a name="get-workflow-details"></a>ワークフローの詳細を取得する

**[Workflow - Get]\(ワークフロー - 取得\)** 操作を使用して、既存の既定ワークフローの詳細を取得します。

レビュー ツールで、[[Credentials]\(資格情報\)](Review-Tool-User-Guide/credentials.md#the-review-tool) セクションに移動します。

### <a name="browse-to-the-api-reference"></a>API リファレンスを参照する

1. **[Credentials]\(資格情報\)** ビューで [[API reference]\(API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) を選択します。 
2. **[Workflow - Create Or Update]\(ワークフロー - 作成または更新\)** ページが表示されたら、[[Workflow - Get]\(ワークフロー - 取得\)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58) の参照に移動します。

### <a name="select-your-region"></a>リージョンを選択する

**API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。

  ![[Workflow - Get]\(ワークフロー - 取得\) のリージョン選択](images/test-drive-region.png)

  **[Workflow - Get]\(ワークフロー - 取得\)** API コンソールが開きます。

### <a name="enter-parameters"></a>パラメーターを入力する

**team**、**workflowname**、**Ocp-Apim-Subscription-Key** (サブスクリプション キー) の値を入力します。

- **team**: [レビュー ツール アカウント](https://contentmoderator.cognitive.microsoft.com/)の設定時に作成したチーム ID。 
- **workflowname**: ワークフローの名前。 `default`を使用します。
- **Ocp-Apim-Subscription-Key**: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページを参照してください。

  ![クエリ パラメーターとヘッダーを取得する](images/workflow-get-default.PNG)

### <a name="submit-your-request"></a>要求を送信する
  
**[送信]** を選択します。 操作が成功すると、**[Response status]\(応答の状態\)** は `200 OK` になり、**[Response Content]\(応答のコンテンツ\)** ボックスには次の JSON ワークフローが表示されます。

    {
        "Name": "default",
        "Description": "Default",
        "Type": "Image",
        "Expression": {
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "isadult",
            "Operator": "eq",
            "Value": "true",
            "AlternateInput": null,
            "Type": "Condition"
            },
        "Then": {
            "Perform": [{
                "Name": "createreview",
                "Subteam": null,
                "CallbackEndpoint": null,
                "Tags": []
            }],
            "Type": "Actions"
            },
            "Else": null,
            "Type": "Logic"
            }
    }


## <a name="create-a-workflow"></a>ワークフローを作成する

レビュー ツールで、[[Credentials]\(資格情報\)](Review-Tool-User-Guide/credentials.md#the-review-tool) セクションに移動します。

### <a name="browse-to-the-api-reference"></a>API リファレンスを参照する

**[Credentials]\(資格情報\)** ビューで [[API reference]\(API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) を選択します。 **[Workflow - Create Or Update]\(ワークフロー - 作成または更新\)** ページが開きます。

### <a name="select-your-region"></a>リージョンを選択する

**API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。

  ![[Workflow - Create Or Update]\(ワークフロー - 作成または更新\) ページのリージョンの選択](images/test-drive-region.png)

  **[Workflow - Create Or Update]\(ワークフロー - 作成または更新\)** API コンソールが開きます。

### <a name="enter-parameters"></a>パラメーターを入力する

**team**、**workflowname**、**Ocp-Apim-Subscription-Key** (サブスクリプション キー) の値を入力します。

- **team**: [レビュー ツール アカウント](https://contentmoderator.cognitive.microsoft.com/)の設定時に作成したチーム ID。 
- **workflowname**: 新しいワークフローの名前。
- **Ocp-Apim-Subscription-Key**: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページを参照してください。

  ![[Workflow - Create Or Update]\(ワークフロー - 作成または更新\) コンソールのクエリ パラメーターとヘッダー](images/workflow-console-parameters.PNG)

### <a name="enter-the-workflow-definition"></a>ワークフローの定義を入力する

1. **[Request body]\(要求の本文\)** ボックスを編集して、**[Description]\(説明\)** および **[Type]\(種類\)** (画像またはテキスト) の詳細が指定された JSON 要求を入力します。 
2. **[Expression]\(式\)** の場合は、次のように、前のセクションの既定のワークフロー式をコピーします。

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": 
                // Copy the default workflow expression from the preceding section
        }

    要求の本文は、次の JSON 要求のようになります。

        {
            "Description": "Default workflow from API console",
            "Type": "Image",
            "Expression": {
                "Type": "Logic",
                "If": {
                    "ConnectorName": "moderator",
                    "OutputName": "isAdult",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                    },
                "Then": {
                "Perform": [
                {
                    "Name": "createreview",
                    "CallbackEndpoint": null,
                    "Tags": [ ]
                }
                ],
                "Type": "Actions"
                }
            }
        }
 
### <a name="submit-your-request"></a>要求を送信する
  
**[送信]** を選択します。 操作が成功すると、**[Response status]\(応答の状態\)** は `200 OK` になり、**[Response Content]\(応答のコンテンツ\)** ボックスには `true` と表示されます。

### <a name="check-out-the-new-workflow"></a>新しいワークフローを確認する

レビュー ツールで、**[Review]\(レビュー\)** > **[Settings]\(設定\)** > **[Workflows]\(ワークフロー\)** の順に選択します。 新しいワークフローが表示され、使用できる状態になります。

  ![レビュー ツールのワークフローリスト](images/workflow-console-new-workflow.PNG)
  
### <a name="review-your-new-workflow-details"></a>新しいワークフローの詳細を確認する

1. ワークフローの **[Edit]\(編集\)** オプションを選択し、**[Designer]\(デザイナー\)** タブと **[JSON]** タブを選択します。

   ![選択したワークフローの [Designer]\(デザイナー\) タブ](images/workflow-console-new-workflow-designer.PNG)

2. ワークフローの JSON ビューを表示するには、**[JSON]** タブを選択します。

## <a name="next-steps"></a>次の手順

* より複雑なワークフローの例については、[ワークフローの概要](workflow-api.md)に関するページを参照してください。
* [コンテンツ モデレーション ジョブ](try-review-api-job.md) でワークフローを使用する方法について学習します。
