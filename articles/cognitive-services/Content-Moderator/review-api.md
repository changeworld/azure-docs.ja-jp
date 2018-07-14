---
title: Azure Content Moderator - モデレーション ジョブと human-in-the-loop (人間参加) レビュー | Microsoft Docs
description: 最適な結果を得るには、マシン支援型モデレーションに人による監視を適用します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 1/21/2018
ms.author: sajagtap
ms.openlocfilehash: 35b3cdaa410712c3fd08d3df4ebe4c83e3955d50
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373253"
---
# <a name="moderation-jobs-and-reviews"></a>モデレーション ジョブとレビュー

ビジネスに最適な結果を得るには、Azure Content Moderator [Review API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5) を使用してマシン支援型モデレーションと human-in-the-loop (人間参加) 機能を組み合わせます。

Review API には、コンテンツ モデレーション プロセスに人による監視を組み込むための次の方法が用意されています。

* `Job` 操作は、マシン支援型モデレーションとヒューマン レビューの作成を 1 ステップとして開始するために使用されます。
* `Review` 操作は、モデレーション手順以外の、ヒューマン レビューの作成に使用されます。
* `Workflow` 操作は、レビュー作成のしきい値を使用してスキャンを自動化するワークフローの管理に使用されます。

`Job` 操作と `Review` 操作は、状態と結果を受け取るためのコールバック エンドポイントを受け入れます。

この記事では、`Job` 操作と `Review` 操作について説明します。 ワークフロー定義の作成、編集、取得の方法については、[ワークフローの概要](workflow-api.md)に関するページを参照してください。

## <a name="job-operations"></a>ジョブ操作

### <a name="start-a-job"></a>ジョブを開始する
`Job.Create` 操作を使用して、モデレーションとヒューマン レビューの作成ジョブを開始します。 Content Moderator でコンテンツがスキャンされ、指定したワークフローが評価されます。 ワークフローの結果に基づいて、レビューが作成されるか、その手順がスキップされます。 また、モデレーション後およびレビュー後のタグがコールバック エンドポイントに送信されます。

入力には次の情報が含まれています。

- レビュー チーム ID。
- モデレーション対象のコンテンツ。
- ワークフロー名 (既定は "default" ワークフローです)。
- 通知用の API コールバック ポイント。
 
次の応答は、開始されたジョブの識別子を示しています。 ジョブ ID は、ジョブの状態の取得と、詳細情報の受け取りに使用します。

    {
        "JobId": "2018014caceddebfe9446fab29056fd8d31ffe"
    }

### <a name="get-job-status"></a>ジョブの状態の取得

`Job.Get` 操作とジョブ ID を使用して、実行中または完了したジョブの詳細情報を取得します。 この操作は、モデレーション ジョブが非同期に実行されている間に、すぐに結果を返します。 結果はコールバック エンドポイントを介して返されます。

入力には次の情報が含まれています。

- レビュー チーム ID: 前回の操作で返されたジョブ識別子

応答には次の情報が含まれています。

- 作成したレビューの識別子 (この ID を使用して最終的なレビュー結果を取得します)。
- ジョブの状態 (完了または進行中): 割り当てられたモデレーション タグ (キーと値のペア)。
- ジョブ実行レポート。
 
 
        {
            "Id": "2018014caceddebfe9446fab29056fd8d31ffe",
            "TeamName": "some team name",
            "Status": "Complete",
            "WorkflowId": "OCR",
            "Type": "Image",
            "CallBackEndpoint": "",
            "ReviewId": "201801i28fc0f7cbf424447846e509af853ea54",
            "ResultMetaData":[
            {
            "Key": "hasText",
            "Value": "True"
            },
            {
            "Key": "ocrText",
            "Value": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n"
            }
            ],
            "JobExecutionReport": [
            {
                "Ts": "2018-01-07T00:38:29.3238715",
                "Msg": "Posted results to the Callbackendpoint: https://requestb.in/vxke1mvx"
                },
                {
                "Ts": "2018-01-07T00:38:29.2928416",
                "Msg": "Job marked completed and job content has been removed"
                },
                {
                "Ts": "2018-01-07T00:38:29.0856472",
                "Msg": "Execution Complete"
                },
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
 
![ヒューマン モデレーター用の画像レビュー](images/ocr-sample-image.PNG)

## <a name="review-operations"></a>レビュー操作

### <a name="create-reviews"></a>レビューを作成する

ヒューマン レビューを作成するには、`Review.Create` 操作を使用します。 他の場所でモデレートするか、カスタム ロジックを使用してモデレーション タグを割り当てます。

この操作には、次のような内容が入力されます。

- レビュー対象のコンテンツ。
- ヒューマン モデレーターによるレビューのために割り当てられたタグ (キーと値のペア)。

次の応答はレビュー識別子を示します。

    [
        "201712i46950138c61a4740b118a43cac33f434",
    ]


### <a name="get-review-status"></a>レビューの状態を取得する
モデレートされた画像のヒューマン レビューが完了した後の結果を取得するには、`Review.Get` 操作を使用します。 指定したコールバック エンドポイントを介して通知を受け取ります。 

この操作は、2 つのタグ セットを返します。 

* モデレーション サービスによって割り当てられたタグ
* ヒューマン レビューが完了した後のタグ

入力には、少なくとも以下が含まれています。

- レビュー チーム名
- 前の操作で返されたレビュー識別子

応答には次の情報が含まれています。

- レビューの状態
- レビュー担当者によって確認されたタグ (キーと値のペア)
- モデレーション サービスによって割り当てられたタグ (キーと値のペア)

次のサンプル応答には、レビュー担当者が割り当てたタグ (**reviewerResultTags**) と初期タグ (**メタデータ**) の両方があります。

    {
        "reviewId": "201712i46950138c61a4740b118a43cac33f434",
        "subTeam": "public",
        "status": "Complete",
        "reviewerResultTags": [
        {
            "key": "a",
            "value": "False"
        },
        {
            "key": "r",
            "value": "True"
        },
        {
            "key": "sc",
            "value": "True"
        }
        ],
        "createdBy": "{teamname}",
        "metadata": [
        {
            "key": "sc",
            "value": "true"
        }
        ],
        "type": "Image",
        "content": "https://reviewcontentprod.blob.core.windows.net/{teamname}/IMG_201712i46950138c61a4740b118a43cac33f434",
        "contentId": "0",
        "callbackEndpoint": "{callbackUrl}"
    }

## <a name="next-steps"></a>次の手順

* [Job API コンソール](try-review-api-job.md)を試験運用して、REST API コード サンプルを使用してください。 Visual Studio と C# に精通している場合は、[ジョブの .NET クイック スタート](moderation-jobs-quickstart-dotnet.md)も確認してください。 
* レビューについては、[Review API コンソール](try-review-api-review.md)に関するページから始めて、REST API コード サンプルを使用してください。 次に、[レビュー の.NET クイック スタート](moderation-reviews-quickstart-dotnet.md)を参照してください。
* 動画レビューについては、[動画レビューのクイック スタート](video-reviews-quickstart-dotnet.md)を参照し、[動画レビューにトランスクリプトを追加する方法](video-transcript-reviews-quickstart-dotnet.md)に関するページを参照してください。
