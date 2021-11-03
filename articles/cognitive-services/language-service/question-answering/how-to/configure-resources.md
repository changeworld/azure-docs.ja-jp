---
title: 質問と回答サービスを構成する
description: このドキュメントでは、カスタムの質問と回答が有効なリソースの高度な構成について説明します。
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2eae7e38422aa597c9b093dbefaef553cb084d09
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092151"
---
# <a name="configure-custom-question-answering-enabled-resources"></a>カスタムの質問と回答が有効なリソースを構成する

異なる Cognitive Search リソースを使うように質問と回答を構成できます。

## <a name="change-cognitive-search-resource"></a>Cognitive Search リソースを変更する

> [!WARNING]
> 言語リソースに関連付けられている Azure Search サービスを変更した場合、その中に既に存在しているすべてのプロジェクトとナレッジ ベースにアクセスできなくなります。 Azure Search サービスを変更する前に、既存のプロジェクトをエクスポートしてください。

Azure portal を使って言語リソースとその依存関係 (Search など) を作成すると、Search サービスが自動的に作成されて、言語リソースにリンクされます。 これらのリソースが作成されたら、 **[機能]** タブで Search リソースを更新することができます。

1.  Azure portal で言語リソースに移動します。

2.  **[Features]\(機能\)** を選び、言語リソースにリンクしたい Azure Cognitive Search サービスを選びます。

> [!div class="mx-imgBorder"]
> ![[Add QnA to TA]\(TA に QnA を追加する\)](../media/configure-resources/update-custom-feature.png)

3.  <bpt id="p1">**</bpt>[保存]<ept id="p1">**</ept> を選択します。

## <a name="next-steps"></a>次のステップ

* [保存データを暗号化](./encrypt-data-at-rest.md)
