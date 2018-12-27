---
title: Azure Content Moderator でワークフローを定義して使用する | Microsoft Docs
description: コンテンツ ポリシーに基づいてカスタム ワークフローを作成する方法を説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/07/2018
ms.author: sajagtap
ms.openlocfilehash: dfe3ba8a2ef1bcbc69ef585b504a9367d9420bf0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373024"
---
# <a name="define-test-and-use-workflows"></a>ワークフローの定義、テスト、使用

Azure Content Moderator ワークフロー デザイナーおよび API を使用し、コンテンツ ポリシーに基づいてカスタム ワークフローとしきい値を定義できます。

ワークフローは、コネクタを使用して Content Moderator API と「接続」します。 他の API のコネクタがある場合には、その API を使用できます。 この例では、既定で含まれている Content Moderator コネクタを使用します。

## <a name="browse-to-the-workflows-section"></a>[ワークフロー] セクションを参照する

**[設定]** タブで **[ワークフロー]** を選択します。

  ![ワークフローの設定](images/2-workflows-0.png)

## <a name="start-a-new-workflow"></a>新しいワークフローを開始する

**[ワークフローの追加]** を選択します。

  ![ワークフローの追加](images/2-workflows-1.png)

## <a name="assign-a-name-and-description"></a>名前と説明を割り当てる

ワークフローに名前を付け、説明を入力し、ワークフローで処理するのがテキストか画像かを選択します。

  ![ワークフローの名前と説明](images/ocr-workflow-step-1.PNG)

## <a name="define-the-evaluation-criteria-condition"></a>評価基準 ("条件") を定義する

次のスクリーンショットには、ワークフローについて定義する必要があるフィールドと If-Then-Else 選択肢が表示されます。 コネクタを選択してください。 この例では、**Content Moderator** を使用します。 選択するコネクタに応じて、出力のために使用できるオプションが変わります。

  ![ワークフロー条件の定義](images/ocr-workflow-step-2-condition.PNG)

コネクタと必要な出力を選択したら、演算子と条件の値を選択します。

## <a name="define-the-action-to-take"></a>実行するアクションを定義する

実行するアクションと満たすべき条件を選択します。 次の例では、画像のレビューが作成され、タグ `a` が割り当てられ、表示された条件に対してタグが強調表示されます。 複数の条件を組み合わせて、必要な結果を得ることもできます。 必要に応じて、代替パス (Else) を追加します。

  ![ワークフロー アクションの定義](images/ocr-workflow-step-3-action.PNG)

## <a name="save-your-workflow"></a>ワークフローを保存する

最後に、ワークフローを保存して、ワークフロー名をメモします。 Review API を使用してモデレーション ジョブを開始するときに名前が必要になります。

## <a name="test-the-workflow"></a>ワークフローをテストする

カスタム ワークフローを定義したところで、サンプル コンテンツを使用してワークフローをテストします。

対応する **[ワークフローの実行]** ボタンを選択します。

  ![ワークフローのテスト](images/ocr-workflow-step-6-list.PNG)

### <a name="upload-a-file"></a>ファイルをアップロードする

[サンプル画像](https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png)をローカル ドライブに保存します。 ワークフローをテストするには、**[ファイルの選択]** を選択して、画像をアップロードします。

  ![画像ファイルのアップロード](images/ocr-workflow-step-7-upload.PNG)

### <a name="track-the-workflow"></a>ワークフローを追跡する

ワークフローの実行を追跡します。

  ![ワークフロー実行の追跡](images/ocr-workflow-step-4-test.PNG)

### <a name="review-any-images-flagged-for-human-moderation"></a>ヒューマン モデレーションのフラグが設定されたイメージをレビューする

画像のレビューを表示するには、**[確認]** の **[イメージ]** タブに移動します。

  ![画像のレビュー](images/ocr-sample-image-workflow1.PNG)

## <a name="next-steps"></a>次の手順 

コードからワークフローを起動するには、[`Job` API コンソールのクイック スタート](../try-review-api-job.md)および[.NET SDK クイック スタート](../moderation-jobs-quickstart-dotnet.md)でカスタム ワークフローを使用します。
