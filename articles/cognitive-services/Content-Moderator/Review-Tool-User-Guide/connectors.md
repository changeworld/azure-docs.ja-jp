---
title: Azure Content Moderator のコネクタを使用して他の API に接続する | Microsoft Docs
description: Content Moderator ワークフローが他の API にアクセスできるようにコネクタを使用する方法を説明します。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 06/22/2017
ms.author: sajagtap
ms.openlocfilehash: d8114457e7079ca8772cab830bd011dcddf372f5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35373032"
---
# <a name="connectors"></a>コネクタ

Azure Content Moderator ワークフローは、Content Moderator API に加えて他のワークフローも使用できます。 Content Moderator でコネクタを使用することで他の API にアクセスします。 コネクタがその他の API へのリンクを提供します。

Content Moderator には次の既定コネクタが含まれています。

* Emotion API
* Face API
* PhotoDNA Cloud Service

![Content Moderator の使用可能なコネクタ](images/connectors-1.png)

## <a name="verify-your-credentials"></a>資格情報を確認する 

ワークフローを定義する前に、使用するコネクタ API に対して有効な資格情報があることを確認します。

1.  レビュー ツールのダッシュボードで、**[設定]** > **[コネクタ]** を選択します。

  ![Content Moderator でのコネクタの選択](images/connectors-2.png)

2.  資格情報を確認するコネクタの横にある**編集**記号を選択します。

  ![Content Moderator での編集記号の選択](images/connectors-3.png)

3.  サブスクリプション キーが表示されます。 編集する場合は、終了時に **[保存]** を選択します。

  ![Content Moderator のコネクタ編集ページ](images/connectors-4-1.png)
 
## <a name="add-a-connector"></a>コネクタを追加する

1.  コネクタを追加する前にサブスクリプション キーが必要です。 レビュー ツールのダッシュボードで、**[設定]** > **[資格情報]** を選択します。 **[Ocp-Admin-Subscription-Key]** ボックスにある値を選択してコピーします。

2.  **[コネクタ]** を選択します。 レビュー ツールのダッシュボードに表示される使用可能なコネクタの 1 つを選択します。 次に、**[接続]** を選択します。 

  ![Content Moderator の [コネクタの追加] ページ](images/connectors-5.png)

3.  **[Ocp-Admin-Subscription-Key]** ボックスに、コピーしたキーを貼り付けます。 次に、**[保存]** を選択します。

## <a name="next-steps"></a>次の手順

* コネクタを使用して[カスタム ワークフローを定義](workflows.md)する方法を学びます。
