---
title: バージョンの管理
titleSuffix: Language Understanding - Azure Cognitive Services
description: バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別のバージョンに複製することをお勧めします。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a1594d3dfe449049258e5ee12bc173c413459f8a
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53132876"
---
# <a name="manage-versions"></a>バージョンの管理

バージョンを使用して、さまざまなモデルを構築および公開できます。 モデルに変更を加える前に、現在のアクティブなモデルをアプリの別の[バージョン](luis-concept-version.md)に複製することをお勧めします。 

バージョンを操作するには、**[マイ アプリ]** ページで名前を選択してアプリを開き、上部のバーにある **[管理]** を選択し、左側のナビゲーションで **[バージョン]** を選択します。 

バージョンの一覧には、公開されているバージョン、公開されている場所、現在アクティブであるバージョンが表示されます。 

[![[管理] セクションの [バージョン] ページ](./media/luis-how-to-manage-versions/versions-import.png "[管理] セクションの [バージョン] ページ")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>バージョンを複製する

1. 複製するバージョンを選択し、ツール バーから **[複製]** を選択します。 

2. **[Clone version]\(複製のバージョン\)** ダイアログ ボックスで、新しいバージョンの名前 ("0.2" など) を入力します。

   ![[Clone version]\(複製のバージョン\) ダイアログ ボックス](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > バージョン ID は、文字、数字、または '.' のみで構成できます。文字数は 10 文字までです。
 
 指定した名前の新しいバージョンが作成され、アクティブなバージョンとして設定されます。

## <a name="set-active-version"></a>アクティブなバージョンを設定する

一覧からバージョンを選択し、ツール バーから **[Make Active]\(アクティブにする\)** を選択します。 

[![[管理] セクションの [バージョン] ページ、バージョンの作成アクション](./media/luis-how-to-manage-versions/versions-other.png "[管理] セクションの [バージョン] ページ、バージョンの作成アクション")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>バージョンをインポートする

1. ツール バーから **[Import version]\(バージョンのインポート\)** を選択します。 

2. **[Import new version]\(新しいバージョンのインポート\)** ポップアップ ウィンドウで、新しい 10 文字のバージョン名を入力します。 JSON ファイルのバージョンが既にアプリに存在する場合、設定する必要があるのはバージョン ID のみです。

    ![[管理] セクションの [バージョン] ページ、新しいバージョンのインポート](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    バージョンをインポートすると、新しいバージョンがアクティブなバージョンになります。

<a name = "export-version"></a>

## <a name="other-actions"></a>その他のアクション

* バージョンを**削除**するには、一覧からバージョンを選択し、ツール バーから **[削除]** を選択します。 **[OK]** を選びます。 
* バージョンの**名前を変更**するには、一覧からバージョンを選択し、ツール バーから **[名前の変更]** を選択します。 新しい名前を入力して **[完了]** を選択します。 
* バージョンを**エクスポート**するには、一覧からバージョンを選択し、ツール バーから **[Export app]\(アプリのエクスポート\)** を選択します。 ファイルはローカル コンピューターにダウンロードされます。 

