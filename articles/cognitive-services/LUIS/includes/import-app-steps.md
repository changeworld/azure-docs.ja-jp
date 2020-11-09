---
title: アプリのインポート手順
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130729"
---
1. [LUIS ポータル](https://www.luis.ai)の **[マイ アプリ]** ページで、 **[+ New app for conversation]\(+ 会話用の新しいアプリ\)** を選択し、次に **[Import as JSON]\(JSON としてインポート\)** を選択します。 前の手順で保存した JSON ファイルを見つけます。 アプリの名前を変更する必要はありません。 **[完了]** を選択します

1. **[管理]** セクションの **[バージョン]** タブで `0.1` バージョンを選択し、 **[複製]** を選択してそのバージョンを複製し、新たに名前 `ml-entity` を付けます。次に、 **[完了]** を選択して複製プロセスを終了します。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

    > [!TIP]
    > アプリを変更する前に、新しいバージョンにクローンすることをお勧めします。 バージョンの変更が完了したら、そのバージョンを (.json または .lu ファイル) としてエクスポートし、ソース管理システムにそのファイルをチェックインします。

1. 画面上部の **[ビルド]** を選択し、次に左側のナビゲーション メニューにある **[意図]** をクリックします。 LUIS アプリの主な構成要素である、アプリの意図が表示されます。
