---
title: システムのテスト結果とデプロイを確認する - Custom Translator
titleSuffix: Azure Cognitive Services
description: トレーニングが成功したら、システムのテストを確認し、トレーニング結果を分析します。 トレーニング結果に満足したら、トレーニング済みモデルのデプロイを要求します。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626993"
---
# <a name="view-system-test-results"></a>システムのテスト結果の表示

トレーニングが成功したら、システムのテストを確認し、トレーニング結果を分析します。 トレーニング結果に満足したら、トレーニング済みモデルのデプロイを要求します。 

## <a name="system-test-results-page"></a>システムのテスト結果ページ

プロジェクトを選択し、そのプロジェクトのモデル タブを選択し、使用するモデルを見つけて、最後にテスト タブを選択します。

テスト タブには次の内容が表示されます。

1.  System Test Results (システムのテスト結果): トレーニングのテスト プロセスの結果。 テスト プロセスで BLEU スコアが生成されます。

    **Sentence Count (文の数):** テスト セットで使用された並列文の数。

     **BLEU Score (BLEU スコア):** トレーニング完了後にモデルに対して生成された BLEU スコア。

    **状態:** テスト プロセスが完了したか進行中かが表示されます。

    ![システムのテスト結果](media/how-to/how-to-system-test-results.png)

2.  [System Test Results]\(システムのテスト結果\) をクリックします。テスト結果の詳細ページが表示されます。 このページには、テスト データ セットに含まれていた文の機械翻訳が表示されます。

3.  テスト結果の詳細ページの表には、2 つの列があります。ペアの各言語の列です。 ソース言語の列には、翻訳対象の文が表示されます。 ターゲット言語の列には、各行に 2 つの文が含まれています。

    **Ref:** この文は、テスト データセットに指定された原文の参考訳です。

    **MT:** この文は、トレーニングが実行された後に構築されたモデルによって行われた原文の自動翻訳です。

    ![システムのテスト結果の比較](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>テストをダウンロードする

[Download Translations]\(翻訳のダウンロード\) リンクをクリックして zip ファイルをダウンロードします。 この zip には、テスト データ セット内の原文の機械翻訳が含まれています。

![テストをダウンロードする](media/how-to/how-to-system-test-download.png)

このダウンロードされた zip アーカイブには 3 つのファイルが含まれています。

1.  custom.mt.txt: このファイルには、ユーザーのデータを使用してトレーニングしたモデルによって実行された、ソース言語の文からターゲット言語への機械翻訳が含まれています。

2.  ref.txt: このファイルには、ソース言語の文からターゲット言語へのユーザーが指定した翻訳が含まれています。

3.  source.txt: このファイルには、ソース言語の文が含まれています。

    ![システムのテスト結果をダウンロードする](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>モデルをデプロイする

デプロイを要求するには:

1.  プロジェクトを選択し、[モデル] タブに移動します。

2. 正常にトレーニングが完了したモデルで、まだデプロイされていない場合は、[デプロイ] ボタンが表示されます。

    ![モデルをデプロイする](media/how-to/how-to-deploy-model.png)

3.  [デプロイ] をクリックします。
4.  モデルをデプロイするリージョンを選択し、[保存] をクリックします。 複数のリージョンを選択することができます。

    ![モデルをデプロイする](media/how-to/how-to-deploy-model-regions.png)

5.  モデルの状態は [状態] 列で確認できます。

>[!Note]
>モデルが既にデプロイされている場合は、そのモデルに [展開解除] ボタンが表示されます。 モデルのデプロイを解除するには、[展開解除] をクリックします。

## <a name="next-steps"></a>次の手順

- [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) を介してデプロイされた翻訳モデルを使い始めます。
- [設定を管理](how-to-manage-settings.md)してワークスペースを共有し、サブスクリプション キーを管理する方法について説明します。
- [Microsoft Translator Hub](https://hub.microsofttranslator.com) から[ワークスペースとプロジェクトを移行する方法](how-to-migrate.md)について説明します。