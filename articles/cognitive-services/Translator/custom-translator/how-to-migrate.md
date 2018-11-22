---
title: Microsoft Translator Hub ワークスペースとプロジェクトの移行 - Custom Translator
titleSuffix: Azure Cognitive Services
description: Microsoft Translator Hub ワークスペースとプロジェクトを移行します。
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627143"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Microsoft Translator Hub ワークスペースとプロジェクトを移行する

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) ワークスペースとプロジェクトを Custom Translator に移行できます。 移行は Hub から始まります。


次の項目は、このプロセス中に移行されます。

1.  プロジェクトの定義。

2.  トレーニングの定義は、Custom Translator で新しいモデル定義を作成するために使用されます。

3.  トレーニング内で使用されている並列ファイルとモノリンガル ファイルは、すべて Custom Translator の新しいドキュメントとして移行されます。

4.  自動生成されたシステム テストおよびチューニング データはエクスポートされ、Custom Translator の新しいドキュメントとして作成されます。

トレーニングをすべてデプロイすると、Custom Translator でモデルが無料でトレーニングされます。 必要に応じて手動でデプロイすることができます。

>[!Note]
>トレーニングが成功するには、Custom Translator に最低 10,000 個の抽出された文が必要です。 抽出された文が[推奨最小数](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)よりも少ない場合、Custom Translator でトレーニングを実行することはできません。

デプロイされていないすべてのトレーニングが成功するように、Custom Translator ではドラフトとして移行されます。

## <a name="find-custom-translator-workspace-id"></a>Custom Translator ワークスペース ID を見つける

[Microsoft Translator Hub](https://hub.microsofttranslator.com/) ワークスペースを移行するには、Custom Translator の移行先のワークスペース ID が必要です。 Custom Translator の移行先ワークスペースは、すべての Hub ワークスペースとプロジェクトを移行される場所です。

Custom Translator の [設定] ページで、移行先のワークスペース ID が見つかります。 

1.  Custom Translator ポータルの [設定] ページに移動します。

2.  [基本情報] セクションにワークスペース ID があります。

    ![移行先のワークスペース ID を見つける方法](media/how-to/how-to-find-destination-ws-id.png)

3. 移行プロセス中に参照できるように、移行先のワークスペース ID をメモしておきます。

## <a name="migrate-workspace"></a>ワークスペースを移行する

完全な Hub ワークスペースを Custom Translator に移行すると、プロジェクト、ドキュメント、およびトレーニングが Custom Translator に移行されます。 移行する前に、デプロイしたトレーニングのみを移行するのか、成功したすべてのトレーニングを移行するかを選択する必要があります。

ワークスペースを移行するには:

1.  Microsoft Translator Hub にサインインします。

2.  [設定] ページに移動します。

3.  [設定] ページで [Migrate Workspace data to Custom Translator]\(ワークスペース データを Custom Translator に移行する\) をクリックします。

    ![Hub から移行する方法](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  次のページで、以下の 2 つのオプションのいずれかを選択します。

    a.  Deployed Trainings only (デプロイされたトレーニングのみ): このオプションを選択すると、デプロイされたシステムと関連するドキュメントのみが移行されます。

    b.  All Successful Trainings (成功したすべてのトレーニング): このオプションを選択すると、すべての成功したトレーニングと関連するドキュメントが移行されます。

    c.  Custom Translator の移行先のワークスペース ID を入力します。

    ![Hub から移行する方法](media/how-to/how-to-migrate-from-hub-screen.png)

5.  [要求の送信] をクリックします。

## <a name="migrate-project"></a>プロジェクトを移行する

プロジェクトを選択して移行する場合は、Microsoft Translator Hub にその機能があります。

1 つのプロジェクトを移行するには:

1.  Microsoft Translator Hub にサインインします。

2.  [プロジェクト] ページに移動します。

3.  適切なプロジェクトの [移行] リンクをクリックします。

    ![Hub から移行する方法](media/how-to/how-to-migrate-from-hub.png)

4.  次のページで、以下の 2 つのオプションのいずれかを選択します。

    a.  Deployed Trainings only (デプロイされたトレーニングのみ): このオプションを選択すると、デプロイされたシステムと関連するドキュメントのみが移行されます。 

    b.  All Successful Trainings (成功したすべてのトレーニング): このオプションを選択すると、すべての成功したトレーニングと関連するドキュメントが移行されます。

    c.  Custom Translator の移行先のワークスペース ID を入力します。

    ![Hub から移行する方法](media/how-to/how-to-migrate-from-hub-screen.png)

5.  [要求の送信] をクリックします。

## <a name="migration-history"></a>移行履歴

Hub からワークスペース/プロジェクトの移行を要求すると、Custom Translator の [設定] ページで移行履歴を確認できます。 

移行履歴を表示するには、次の手順を実行します。

1.  Custom Translator ポータルの [設定] ページに移動します。

2.  [設定] ページの [移行履歴] セクションで、[移行履歴] をクリックします。

    ![移行履歴](media/how-to/how-to-migration-history.png)

[移行履歴] ページには、要求した各移行の次の情報が概要として表示されます。

1.  Migrated By (移行したユーザー): この移行要求を送信したユーザーの名前とメール アドレス

2.  Migrated On (移行日): 移行の日付とタイム スタンプ

3.  プロジェクト: 移行が要求されたプロジェクトの数と、正常に移行されたプロジェクトの数。

4.  トレーニング: 移行が要求されたトレーニングの数と、正常に移行されたトレーニングの数。

5.  ドキュメント: 移行が要求されたドキュメントの数と、正常に移行されたドキュメントの数。

    ![移行履歴の詳細](media/how-to/how-to-migration-history-details.png)

プロジェクト、トレーニング、ドキュメントに関するより詳細な移行レポートが必要な場合は、詳細を CSV 形式でエクスポートするオプションがあります。

>[!Note]
>移行は、NMT 言語が存在する言語ペアでのみサポートされています。 現在[サポートされている NMT 言語](https://www.microsoft.com/translator/business/languages/)の一覧を確認してください。 NMT 言語が存在しない言語ペアの場合、データは Hub から Custom Translator に移動されますが、それらの言語ペアに対してトレーニングは実行できません。

## <a name="next-steps"></a>次の手順

- [モデルをトレーニングします](how-to-train-model.md)。
- [Microsoft Translator Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl) を介してデプロイされた翻訳モデルを使い始めます。