---
title: Azure Content Moderator - モデレーション ワークフロー | Microsoft Docs
description: コンテンツ モデレーションと共にワークフローを使用します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/04/2018
ms.author: sajagtap
ms.openlocfilehash: 079fcd119f1536f9e76ca57fccc76538b3c3ed78
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373381"
---
# <a name="moderation-workflows"></a>モデレーション ワークフロー

Content Moderator には、ワークフローを管理するツールと API が含まれています。 [Review API のジョブ操作](review-api.md)と共にワークフローを使用して、コンテンツ ポリシーとしきい値に基づいて human-in-the-loop (人間参加) レビューの作成を自動化します。

Review API には、コンテンツ モデレーション プロセスに人による監視を組み込むための次の方法が用意されています。

1. **ジョブ**操作。マシン支援型モデレーションとヒューマン レビューの作成を 1 手順で開始します。
1. **レビュー**操作。モデレーション手順以外のヒューマン レビューを作成します。
1. **ワークフロー操作**。レビュー作成のしきい値を使用してスキャンを自動化するワークフローを管理します。

この記事では、**ワークフロー**操作について説明します。 コンテンツ モデレーション ジョブとレビューの詳細については、[ジョブとレビュー](review-api.md)の概要に関するページを参照してください。

Content Moderator のワークフローの概要を理解するには、**既定**のワークフローを確認することをお勧めします。

## <a name="your-first-workflow"></a>最初のワークフロー

最初のワークフローは、[レビュー ツール チーム](https://contentmoderator.cognitive.microsoft.com/)にバンドルされています。 まだサインアップしていない場合はサインアップします。

[レビュー ツールの [Workflows]\(ワークフロー\)](Review-Tool-User-Guide/Workflows.md) 画面で [Settings]\(設定\) タブに移動します。次の図のように、**既定**のワークフローが表示されます。

![Content Moderator のワークフロー](Review-Tool-User-Guide/images/2-workflows-1.png)

### <a name="open-the-default-workflow"></a>既定のワークフローを開く

次の図のように、**編集**オプションを使用してワークフローの編集ページを開きます。![Content Moderator の既定のワークフロー](images/default-workflow-listed.PNG)

### <a name="the-designer-view"></a>デザイナー ビュー

ワークフローの **[Designer]\(デザイナー\)** タブが表示されます。 デザイナー ビューには、次の手順が表示されます。

1. 評価するワークフローの**条件**。 この例では、ワークフローは Content Moderator の Image API を呼び出し、`isAdult` の出力が `true` と等しいかどうかを確認します。
1. 条件が満たされた場合に実行される**アクション**。 この例では、`isAdult` の出力が `true` の場合に、ワークフローはレビュー ツールでレビューを作成します。

![Content Moderator の既定のワークフロー - デザイナー](images/default-workflow-designer.png)

### <a name="the-json-view"></a>JSON ビュー

ワークフローの JSON 定義を表示するには、**[JSON]** タブを選択します。

    {
        "Type": "Logic",
        If": {
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

### <a name="key-learning"></a>主な学習内容

Content Moderator のワークフローは構成が簡単で柔軟性があります。 組み込みのデザイナーが要件を満たしていない場合は、**JSON** 形式でワークフロー定義を記述します。 次に、[Workflow API](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) で JSON 定義を使用して、アプリケーションからワークフローを作成し、管理します。

## <a name="define-a-custom-workflow"></a>カスタム ワークフローを定義する

Content Moderator のワークフロー機能を使用すると、カスタム ワークフローを定義して使用することができます。 [レビュー ツール ワークフローの使用方法](Review-Tool-User-Guide/Workflows.md)に関する記事を参照して、カスタム ワークフローを定義してください。 このワークフローでは、Content Moderator の OCR 機能を使用して、サンプル画像からテキストを抽出します。 次に、レビュー ツールでレビューを作成します。

### <a name="the-sample-image"></a>サンプル画像

[サンプル画像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)をローカル ドライブに保存します。 演習にはこの画像が必要です。

### <a name="the-designer-view"></a>デザイナー ビュー

カスタム ワークフローを定義するには、**[Designer]\(デザイナー\)** タブ、[[workflow creation tutorial]\(ワークフロー作成チュートリアル\)](Review-Tool-User-Guide/Workflows.md) の順に選択します。 次の図は、デザイナーの **[Condition]\(条件\)** ビューを示しています。 残りの手順については、チュートリアルを参照してください。

![Content Moderator - ワークフローの条件](Review-Tool-User-Guide/images/ocr-workflow-step-2-condition.PNG)

### <a name="the-json-view"></a>JSON ビュー

カスタム ワークフローの次の JSON 定義を表示するには、**[JSON]** タブを選択します。 JSON 定義の **If-Then** 文が、デザイナー ビューを使用して定義した手順とどのように対応しているかに注目してください。

    {
        "Type": "Logic",
        "If": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

### <a name="workflow-result"></a>ワークフローの結果

ワークフロー画面からワークフローをテストすると、次のレビューが作成されます。 **[Review]\(レビュー\)** の **[Image]\(画像\)** タブに移動してレビューを確認します。
このワークフローによってレビューが作成されたのは、プライマリ条件のテスト結果がテキストの存在を満たしていたためです。 レビューでは、画像レビューの **`a`** タグも強調表示されました。

![Content Moderator - 簡単なワークフロー出力](images/ocr-sample-image-workflow1.PNG)


## <a name="advanced-workflow-with-combination"></a>組み合わせを使用した高度なワークフロー

### <a name="the-sample-image"></a>サンプル画像

前のセクションで使用したものと同じ[サンプル画像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)を使用します。

ただし、今回は、プライマリ条件を 2 つのチェックの組み合わせに変更します。 テキストのチェックに加え、テキストに不適切な言葉が含まれているかどうかをチェックします。 テキストが見つかり、**かつ**そのテキスト内に不適切な言葉が検出された場合、ワークフローはレビューを作成します。

### <a name="the-designer-view"></a>デザイナー ビュー

**[Condition]\(条件\)** を **[Combination]\(組み合わせ\)** に変更するには、ワークフローを変更します。 次の図は、デザイナーに表示される新しいビューです。

![Content Moderator - 変更されたワークフローの条件](images/ocr-workflow-2-designer.PNG)

### <a name="the-json-view"></a>JSON ビュー

変更されたカスタム ワークフローの次の JSON 定義を表示するには、**[JSON]** タブを選択します。 JSON 定義の **If-Then** 文が、ワークフローに追加した新しい手順にどのように対応しているかに注目してください。

    {
        "Type": "Logic",
        "If": {
        "Left": {
            "ConnectorName": "moderator",
            "OutputName": "hasText",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition"
            },
        "Right": {
            "ConnectorName": "moderator",
            "OutputName": "text.HasProfanity",
            "Operator": "eq",
            "Value": "true",
            "Type": "Condition",
            "AlternateInput": "moderator.ocrText"
            },
        "Combine": "AND",
        "Type": "Combine"
        },
        "Then": {
        "Perform": [
        {
            "Name": "createreview",
            "CallbackEndpoint": null,
            "Tags": [
            {
                "Tag": "a",
                "IfCondition": {
                    "ConnectorName": "moderator",
                    "OutputName": "hasText",
                    "Operator": "eq",
                    "Value": "true",
                    "Type": "Condition"
                }
            }
            ]
        }
        ],
        "Type": "Actions"
        }
    }

    
### <a name="workflow-result"></a>ワークフローの結果

ワークフローを再度テストすると、今度はレビューが作成されません。 レビューがないことを確認するには、**[Review]\(レビュー\)** の **[Image]\(画像\)** タブに移動します。
ワークフローは、抽出されたテキスト内に不適切な言葉を検出できなかったため、レビューを作成しませんでした。

![Content Moderator - 変更されたワークフロー出力](images/ocr-workflow-2-result.PNG)


## <a name="the-workflow-api"></a>Workflow API

[ワークフロー操作](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)には、ワークフロー機能へのプログラミング インターフェイスが用意されています。 Workflow API を使用して、ワークフローを作成し、ワークフローの詳細を取得し、ワークフロー定義を更新します。

### <a name="get-all-workflow-details"></a>[すべての] ワークフローの詳細を取得する

**Workflow-Get** 操作は、次の入力を受け取ります。

- **team**: [レビュー ツール アカウント](https://contentmoderator.cognitive.microsoft.com/)の設定時に作成したチーム ID。 
- **workflowname**: ワークフローの名前。 まず `default` を使用します。
- **Ocp-Apim-Subscription-Key**: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページを参照してください。

操作が成功すると、**[Response status]\(応答の状態\)** は `200 OK` になり、**[Response Content]\(応答のコンテンツ\)** ボックスには JSON 形式のワークフロー定義が表示されます。
詳細については、[Workflow API コンソールのクイック スタート](try-review-api-job.md)を参照してください。

### <a name="create-or-update-workflow"></a>ワークフローの作成または更新

作成および更新操作を使用して、API からワークフローを作成できます。

**Workflow-Create または Update** 操作は、次の入力を受け取ります。

- **team**: [レビュー ツール アカウント](https://contentmoderator.cognitive.microsoft.com/)の設定時に作成したチーム ID。 
- **workflowname**: ワークフローの名前。 まず `default` を使用します。
- **Ocp-Apim-Subscription-Key**: **[設定]** タブにあります。詳細については、[概要](overview.md)に関するページを参照してください。

操作が成功すると、**[Response status]\(応答の状態\)** は `200 OK` になり、**[Response Content]\(応答のコンテンツ\)** ボックスには `true` と表示されます。 詳細については、[`Create` 操作の試験運用](try-review-api-job.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

カスタム ワークフローを作成する方法については、[レビュー ツールのワークフロー チュートリアル](Review-Tool-User-Guide/Workflows.md)を参照してください。 

[Workflow API コンソール](try-review-api-job.md)を試験運用して、REST API コード サンプルを使用します。 

最後に、[Job API コンソール](try-review-api-job.md)と [Jobs .NET クイック スタート](moderation-jobs-quickstart-dotnet.md)に従って、**ジョブ**操作でカスタム ワークフローを使用します。
