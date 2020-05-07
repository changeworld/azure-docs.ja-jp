---
title: 既定の回答を取得する - QnA Maker
description: 規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097100"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>QnA Maker リソースの既定の回答を変更する

規定の回答は、質問に対する回答が見つからなかった場合に返されます。 標準の既定の回答から既定の応答を変更することもできます。

## <a name="change-default-answer-in-the-azure-portal"></a>Azure portal で既定の回答を変更する

1. [Azure Portal](https://portal.azure.com) に移動し、作成した QnA Maker サービスを表すリソース グループに移動します。

2. **[App Service]** をクリックして開きます。

    ![Azure portal で QnA Maker の App Service にアクセスする](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. **[アプリケーション設定]** をクリックし、**DefaultAnswer** フィールドを目的の既定の応答に編集します。 **[保存]** をクリックします。

    ![[アプリケーション設定] を選択し、QnA Maker の DefaultAnswer を編集します。](../media/qnamaker-concepts-confidencescore/change-response.png)

4. App Service を再起動します。

    ![DefaultAnswer の変更後、QnA Maker の App Service を再起動する](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>次のステップ

* [ナレッジ ベースの作成](../How-to/manage-knowledge-bases.md)