---
title: 既定の回答を取得する - QnA Maker
description: 規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843278"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>ナレッジ ベースの既定の回答を設定する

規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。

## <a name="change-default-answer"></a>既定の回答の変更

1. [Azure Portal](https://portal.azure.com) に移動し、作成した QnA Maker サービスを表すリソース グループに移動します。

2. **[App Service]** をクリックして開きます。

    ![Azure portal で QnA Maker の App Service にアクセスする](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **[アプリケーション設定]** をクリックし、**DefaultAnswer** フィールドを目的の既定の応答に編集します。 **[保存]** をクリックします。

    ![[アプリケーション設定] を選択し、QnA Maker の DefaultAnswer を編集します。](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service を再起動します。

    ![DefaultAnswer の変更後、QnA Maker の App Service を再起動する](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>次のステップ

* [QnA Maker と LUIS を使用したボットの作成](../tutorials/integrate-qnamaker-luis.md)