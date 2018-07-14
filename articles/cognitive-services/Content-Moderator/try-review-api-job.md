---
title: Azure Content Moderator でコンテンツ モデレーション ジョブを実行する | Microsoft Docs
description: API コンソールでコンテンツ モデレーション ジョブを実行する方法を説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 08/03/2017
ms.author: sajagtap
ms.openlocfilehash: 6f741be1001ae70d5fdbf6f374204aaad1601abe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373016"
---
# <a name="start-a-moderation-job-from-the-api-console"></a>API コンソールでモデレーション ジョブを開始する

Review API の[ジョブ操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5)を使用して、Azure Content Moderator で画像コンテンツまたはテキスト コンテンツの徹底的なコンテンツ モデレーション ジョブを開始します。 

モデレーション ジョブは、Content Moderator Image Moderation API または Text Moderation API を使用してコンテンツをスキャンします。 その後、モデレーション ジョブはワークフロー (レビュー ツールで定義) を使用して、レビュー ツールにレビューを生成します。 

ヒューマン モデレーターが自動割り当てタグと予測データを確認して、最終的なモデレーション意思決定を送信した後で、Review API がすべての情報を API エンドポイントに送信します。

## <a name="prerequisites"></a>前提条件

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動します。 サインアップしていない場合はサインアップします。 レビュー ツールで、この `Job` 操作で使用する[カスタム ワークフローを定義](Review-Tool-User-Guide/Workflows.md)します。

## <a name="use-the-api-console"></a>API コンソールを使用する
オンライン コンソールを使用して API を試験的に運用するには、次のいくつかの値をコンソールに入力する必要があります。
    
- `teamName`: レビュー ツールの資格情報画面の `Id` フィールドを使用します。 
- `ContentId`: この文字列は API に渡され、コールバックで返されます。 **ContentId** は、内部識別子またはメタデータをモデレーション ジョブの結果に関連付ける際に役立ちます。`Workflowname`: 前のセクションで[作成したワークフロー](Review-Tool-User-Guide/Workflows.md)の名前。
- `Ocp-Apim-Subscription-Key`: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページをご覧ください。

**[資格情報]** ウィンドウから API コンソールにアクセスします。

### <a name="navigate-to-the-api-reference"></a>API リファレンスにナビゲートする
**[資格情報]** ウィンドウで [[API reference]\(API リファレンス\)](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) を選択します。

  `Job.Create` ページが開きます。

### <a name="select-your-region"></a>リージョンを選択する
**[Open API testing console]\(API テスト コンソールを開く\)** で、実際の場所に最もあてはまるリージョンを選択します。
  ![「Job - Create」ページのリージョン選択肢](images/test-drive-job-1.png)

  `Job.Create` API コンソールが開きます。 

### <a name="enter-parameters"></a>パラメーターを入力する

必須のクエリ パラメーターとサブスクリプション キーの値を入力します。 **[要求本文]** ボックスで、スキャンしようとする情報の場所を指定します。 この例では、この[サンプル画像](https://moderatorsampleimages.blob.core.windows.net/samples/sample6.png)を使用します。

  ![[Job - Create]\(Job - Create\) コンソールの [クエリ パラメーター]、[ヘッダー]、および [要求本文] ボックス](images/job-api-console-inputs.PNG)

### <a name="submit-your-request"></a>要求を送信する
**[送信]** を選択します。 ジョブ ID が作成されます。 次の手順で使用するためにコピーします。

  `"JobId": "2018014caceddebfe9446fab29056fd8d31ffe"`

### <a name="open-the-get-job-details-page"></a>[取得] の [ジョブの詳細] ページを開く
**[取得]** を選択し、リージョンと一致するボタンを選択して API を開きます。

  ![[Job - Create]\(Job - Create\) コンソールの取得結果](images/test-drive-job-4.png)

### <a name="review-the-response"></a>応答を確認する

**teamName** と **JobID** の値を入力します。 サブスクリプション キーを入力して、**[送信]** を選択します。 次の応答には、サンプルのジョブ ステータスと詳細が表示されます。

```
    {
        "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
        "TeamName": "some team name",
        "Status": "InProgress",
        "WorkflowId": "OCR",
        "Type": "Image",
        "CallBackEndpoint": "",
        "ReviewId": "",
        "ResultMetaData": [],
        "JobExecutionReport": 
        [
            {
            "Ts": "2018-01-07T00:38:26.7714671",
                "Msg": "Successfully got hasText response from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:26.4181346",
                "Msg": "Getting hasText from Moderator"
            },
            {
                "Ts": "2018-01-07T00:38:25.5122828",
                "Msg": "Starting Execution - Try 1"
            }
        ]
    }
```

## <a name="navigate-to-the-review-tool"></a>レビュー ツールに移動する
Content Moderator ダッシュボードで、**[確認]** > **[イメージ]** を選択します。 スキャンした画像がヒューマン レビューのために表示されます。

  ![3 人が自転車に乗っているレビュー ツールの画像](images/ocr-sample-image.PNG)

## <a name="next-steps"></a>次の手順

コードで REST API を使用するか、[ジョブに関する .NET のクイック スタート](moderation-jobs-quickstart-dotnet.md) から開始して、アプリケーションと統合します。
