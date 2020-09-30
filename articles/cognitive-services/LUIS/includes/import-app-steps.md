---
title: アプリのインポート手順
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2020
ms.locfileid: "91545384"
---
1. [LUIS ポータル](https://www.luis.ai)の **[マイ アプリ]** ページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択し、次に **[Import as JSON]\(JSON としてインポート\)** を選択します。 前の手順で保存した JSON ファイルを見つけます。 アプリの名前を変更する必要はありません。 **[完了]** を選択します

1. **[管理]** セクションの **[バージョン]** タブで `0.1` バージョンを選択し、 **[複製]** を選択してそのバージョンを複製し、新たに名前 `ml-entity` を付けます。次に、 **[完了]** を選択して複製プロセスを終了します。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

    > [!TIP]
    > アプリを変更する前に、新しいバージョンにクローンすることをお勧めします。 バージョンの変更が完了したら、そのバージョンを (.json または .lu ファイル) としてエクスポートし、ソース管理システムにそのファイルをチェックインします。

1. **[ビルド]** を選択してから **[Intents]\(意図\)** を選択して、LUIS アプリのメインの構成要素である意図を確認します。

    ![[バージョン] ページから [Intents]\(意図\) ページに移動します。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
