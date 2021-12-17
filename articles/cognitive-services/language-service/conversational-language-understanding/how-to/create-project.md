---
title: Conversational Language Understanding でプロジェクトを作成する方法
titleSuffix: Azure Cognitive Services
description: この記事では、Conversational Language Understanding でプロジェクトを作成する方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c837912ea60b0caf91e2d04382af343bd4b6a96a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092185"
---
# <a name="how-to-create-projects-in-conversational-language-understanding"></a>Conversational Language Understanding でプロジェクトを作成する方法

Conversational Language Understanding を使うと、**会話** と **オーケストレーション ワークフロー** の 2 種類のプロジェクトを作成できます。

## <a name="sign-in-to-language-studio"></a>Language Studio にサインインする
始めるには、最初に [Language Studio](https://aka.ms/languageStudio) にサインインして、言語リソースを作成する必要があります。 選択が終わったら、 **[完了]** を選びます。

## <a name="navigate-to-conversational-language-understanding"></a>Conversational Language Understanding に移動する

Language Studio で、 **[Understand conversational language]\(会話言語を理解する\)** セクションを見つけて、 **[Conversational language understanding]\(会話言語の理解\)** を選びます。

Conversational Language Understanding プロジェクトのページが表示されます。

:::image type="content" source="../media/projects-page.png" alt-text="Conversational Language Understanding プロジェクトのページを示すスクリーンショット。" lightbox="../media/projects-page.png":::

## <a name="create-a-conversation-project"></a>会話プロジェクトを作成する
会話を選んだ後、次の詳細を指定する必要があります。
- [名前]: プロジェクトの名前
- [説明]: 省略可能なプロジェクトの説明
- [Text primary language]\(テキストのプライマリ言語\): プロジェクトの第一言語です。 トレーニング データは、主にこの言語にする必要があります。
- [Enable multiple languages]\(複数の言語を有効にする\): 複数の言語の同時サポートをプロジェクトで有効にするかどうか。

:::image type="content" source="../media/clu-project-modal.png" alt-text="Conversational Language Understanding の会話プロジェクト作成ウィンドウを示すスクリーンショット。" lightbox="../media/clu-project-modal.png":::

終わったら、[次へ] をクリックし、詳細を確認してから、[プロジェクトの作成] をクリックしてプロセスを完了します。 

## <a name="create-an-orchestration-workflow-project"></a>オーケストレーション ワークフロー プロジェクトを作成する

オーケストレーションを選んだ後、次の詳細を指定する必要があります。
- [名前]: プロジェクトの名前
- [説明]: 省略可能なプロジェクトの説明
- [Text primary language]\(テキストのプライマリ言語\): プロジェクトの第一言語です。 トレーニング データは、主にこの言語にする必要があります。
- [Enable multiple languages]\(複数の言語を有効にする\): 複数の言語の同時サポートをプロジェクトで有効にするかどうか。

終わると、オーケストレーションを行う他のプロジェクトやサービスに接続するためのオプションが表示されるようになります。 各接続は、その種類と関連データで表されます。 意図には、**名前**、**プロジェクトの種類** (LUIS、カスタムの質問への回答 (QnA)、または Conversational Language Understanding)、そして名前による接続先のプロジェクトの選択が必要です。 

> [!NOTE]
> 接続できるプロジェクトの一覧は、オーケストレーション プロジェクトの作成に使用しているのと同じ言語リソースによって所有されているプロジェクトに限られます。

このステップは省略でき、プロジェクトを作成した後で意図の接続を追加することもできます。

:::image type="content" source="../media/orchestration-project-detail.png" alt-text="Conversational Language Understanding のオーケストレーション ワークフロー プロジェクトのモーダルを示すスクリーンショット。" lightbox="../media/orchestration-project-detail.png":::

## <a name="import-a-project"></a>プロジェクトをインポートする

いつでも、会話プロジェクトのページに移動し、プロジェクトを選択して、 **[エクスポート]** をクリックすることで、Conversational Language Understanding プロジェクトを JSON ファイルとしてエクスポートできます。
そのプロジェクトを新しいプロジェクトとしてインポートし直すことができます。 まったく同じ名前でプロジェクトをインポートすると、プロジェクトのデータが新しくインポートしたプロジェクトのデータに置き換えられます。

:::image type="content" source="../media/export.png" alt-text="Conversational Language Understanding のエクスポート ボタンを示すスクリーンショット。" lightbox="../media/export.png":::

既存の LUIS アプリケーションがある場合は、LUIS アプリケーションの JSON を Conversational Language Understanding に直接 "_インポート_" することができます。それにより、現在使用できるすべての要素 (意図、ML エンティティ、発話) を使って会話プロジェクトが作成されます。 詳しくは、[LUIS との下位互換性](../concepts/backwards-compatibility.md)に関する記事をご覧ください。

**[新しいプロジェクトの作成]** の横にある矢印ボタンをクリックし、 **[インポート]** を選んでから、LUIS または Conversational Language Understanding の JSON ファイルを選びます。

:::image type="content" source="../media/import.png" alt-text="Conversational Language Understanding のインポート ボタンを示すスクリーンショット。" lightbox="../media/import.png":::

## <a name="next-steps"></a>次の手順

[スキーマの作成](./build-schema.md)
