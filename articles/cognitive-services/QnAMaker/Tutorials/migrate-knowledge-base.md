---
title: ナレッジ ベースの移行 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902032"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>エクスポート/インポートを使用したナレッジ ベースの移行

ナレッジ ベースを移行するには、一方のナレッジ ベースからエクスポートしてから他方のナレッジ ベースにインポートする必要があります。

## <a name="prerequisites"></a>前提条件

* 始める前に、[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成します。
* 新しい [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)をセットアップします

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>QnA Maker からのナレッジ ベースの移行
1. [QnA Maker ポータル](https://qnamaker.ai)にサインインします。
1. 移行する元のナレッジ ベースを選択します。

1. **[SETTINGS]\(設定\)** ページで **[Export knowledge base]\(ナレッジ ベースのエクスポート\)** をクリックして、元のナレッジ ベースのコンテンツを含む .tsv ファイルをダウンロードします。質問、回答、メタデータ、追加プロンプト、データ ソース名などは、このファイルから抽出されました。

1. 上部のメニューから **[Create a knowledge base]\(ナレッジ ベースの作成\)** を選択し、_空の_ ナレッジ ベースを作成します。 これは空です。これを作成すると、URL やファイルは追加されません。 これらは、作成後にインポート ステップで追加されます。

    ナレッジ ベースを構成します。 新しいナレッジ ベース名のみを設定します。 名前は重複しても構いません。特殊文字も使用できます。

    ステップ 4 の値は、ファイルをインポートするときに上書きされるため、選択しないでください。

1. ステップ 5 で、**作成**を選択します。

1. この新しいナレッジ ベースで、 **[SETTINGS]\(設定\)** タブを開いて、 **[Import knowledge base]\(ナレッジ ベースのインポート\)** を選択します。 これにより質問、回答、およびメタデータ、追加プロンプトがインポートされ、抽出元のデータ ソースの名前は保持されます。

   > [!div class="mx-imgBorder"]
   > [![ナレッジ ベースのインポート](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. テスト パネルを使用して、新しいナレッジ ベースを**テスト**します。 [ナレッジ ベースのテスト](../How-To/test-knowledge-base.md)方法を確認してください。
1. ナレッジ ベースを**公開**します。 [ナレッジ ベースの公開](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)方法を確認してください。
1. お使いのアプリケーションまたはボット コードで、エンドポイントを使用します。 ここで、[QnA ボットの作成](../Tutorials/create-qna-bot.md)方法を参照してください。

    ![QnA Maker の値](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    この時点で、ナレッジ ベースのすべてのコンテンツ、つまり、質問、回答、メタデータと、ソース ファイルの名前、URL が新しいナレッジ ベースにインポートされています。

## <a name="chat-logs-and-alterations"></a>チャットログと変更
大文字と小文字が区別されない変更 (シノニム) は、自動的にはインポートされません。 [V4 API](https://go.microsoft.com/fwlink/?linkid=2092179) を使用して、新しいナレッジ ベースに変更を移行します。

チャットログを移行することはできません。新しいナレッジ ベースでは Application Insights を使ってチャットログが格納されているためです。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースを編集する](../How-To/edit-knowledge-base.md)
