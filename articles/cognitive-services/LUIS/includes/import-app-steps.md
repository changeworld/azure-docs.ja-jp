---
title: アプリのインポート手順
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806417"
---
1. [プレビューの LUIS ポータル](https://preview.luis.ai)の **[マイ アプリ]** ページで **[インポート]** を選択し、**JSON としてインポート**します。 前の手順で保存した JSON ファイルを見つけます。 アプリの名前を変更する必要はありません。 **[完了]** を選択します

1. **[管理]** セクションの **[バージョン]** タブでバージョンを選択し、 **[Clone]\(クローン\)** を選択してそのバージョンをクローンし、新たに 10 文字の名前を付けます。次に、 **[完了]** を選択してクローン プロセスを終了します。 バージョン名は URL ルートの一部として使用されるため、URL 内で有効ではない文字を名前に含めることはできません。

    > [!TIP]
    > アプリを変更する前に、バージョンを新しいバージョンにクローンすることをお勧めします。 あるバージョンが完成したら、そのバージョンを (.json または .lu ファイル) としてエクスポートし、お使いのソース管理システムにファイルをチェックインします。

1. **[ビルド]** を選択してから **[Intents]\(意図\)** を選択して、LUIS アプリのメインの構成要素である意図を確認します。

    ![[バージョン] ページから [Intents]\(意図\) ページに移動します。](../media/tutorial-machine-learned-entity/new-version-imported-app.png)