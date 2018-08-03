---
title: Azure の LUIS アプリでのバージョン管理 | Microsoft Docs
description: Language Understanding (LUIS) アプリケーションでバージョンを管理する方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/29/2017
ms.author: diberry
ms.openlocfilehash: 4941cf533f1b860ead07a416d5af6f62a1978305
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226596"
---
# <a name="manage-versions"></a>バージョンの管理

モデルを編集するたびに、別の[バージョン](luis-concept-version.md)のアプリを作成します。 

## <a name="set-active-version"></a>アクティブなバージョンを設定する
バージョンを操作するには、**[マイ アプリ]** ページで選択して開き、上部のバーにある **[設定]** を選択します。

![[バージョン] ページ](./media/luis-how-to-manage-versions/settings.png)

**[設定]** ページでは、バージョンやコラボレーターなど、アプリ全体の設定を構成することができます。 

## <a name="clone-a-version"></a>バージョンを複製する
1. **[設定]** ページで、[アプリケーション設定] セクションと [コラボレーター] セクションの後にある、複製するバージョンの行を見つけます。 右端にある省略記号 (***...***) ボタンを選択します。 

    ![バージョン行のプロパティ](./media/luis-how-to-manage-versions/version-section.png)

2. リストから **[複製]** を選択します。

    ![バージョン行のプロパティの選択](./media/luis-how-to-manage-versions/version-three-dots-modal.png)

3. **[Clone version]\(複製のバージョン\)** ダイアログ ボックスで、新しいバージョンの名前 ("0.2" など) を入力します。

   ![[Clone version]\(複製のバージョン\) ダイアログ ボックス](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
 > [!NOTE]
 > バージョン ID は、文字、数字、または '.' のみで構成できます。文字数は 10 文字までです。
 
 指定した名前の新しいバージョンが作成され、アクティブなバージョンとして設定されます。
 
  ![バージョンが作成され、リストに追加されます。](./media/luis-how-to-manage-versions/new-version.png)

 > [!NOTE]
 > 前の図のように、発行されたバージョンは、色分けされたマークと関連付けられ、発行されたスロットの種類を示します (運用 (緑)、ステージング (赤)、両方 (黒))。 トレーニングと発行の日付は、発行されたバージョンごとに表示されます。

## <a name="set-active-version"></a>アクティブなバージョンを設定する
1. **[設定]** ページの **[バージョン]** 一覧で、右端にある省略記号 (***...***) ボタンを選択します。

2. ポップアップ リストから **[アクティブとして設定]** を選択します。

    ![アクティブなバージョンを設定する](./media/luis-how-to-manage-versions/set-active-version.png)

    アクティブなバージョンは、次のスクリーンショットのように、薄い青色で強調表示されます。

    ![アクティブなバージョン](./media/luis-how-to-manage-versions/set-active-version-done.png) 


## <a name="import-version"></a>バージョンをインポートする
JSON ファイルからバージョンをインポートできます。 バージョンをインポートすると、新しいバージョンがアクティブなバージョンになります。

**バージョンをインポートするには:**

1. **[設定]** ページで、**[Import new version]\(新しいバージョンのインポート\)** ボタンを選択します。

    ![[インポート] ボタン](./media/luis-how-to-manage-versions/import-version.png)

2. **[参照]** を選択し、JSON ファイルを選択します。

    ![バージョンのインポート ダイアログ](./media/luis-how-to-manage-versions/import-version-dialog.png)

JSON ファイルのバージョンが既にアプリに存在する場合、設定する必要があるのはバージョン ID のみです。

## <a name="export-version"></a>バージョンをエクスポートする
バージョンを JSON ファイルにエクスポートできます。

**バージョンをエクスポートするには:**

1. **[設定]** ページの **[バージョン]** 一覧で、右端にある省略記号 (***...***) ボタンを選択します。

2. アクションのポップアップ リストで **[エクスポート]** を選択し、ファイルを保存する場所を選択します。

## <a name="delete-a-version"></a>バージョンを削除する
バージョンは削除できますが、少なくとも 1 つのバージョンのアプリを保存する必要があります。 アクティブなバージョンを除くすべてのバージョンを削除できます。 

1. **[設定]** ページの **[バージョン]** 一覧で、右端にある省略記号 (***...***) ボタンを選択します。

2. アクションのポップアップ リストで **[削除]** を選択し、ファイルを保存する場所を選択します。

    ![バージョンの削除の確認](./media/luis-how-to-manage-versions/delete-menu.png) 


## <a name="rename-a-version"></a>バージョンの名前を変更する
既に使用されていないバージョンの名前の場合にのみ、その名前を変更できます。  

1. **[設定]** ページの **[バージョン]** 一覧で、右端にある省略記号 (***...***) ボタンを選択します。

2. アクションのポップアップ リストで **[名前の変更]** を選択します。

3. 新しいバージョンの名前を入力し、**[完了]** を選択します。

    ![バージョンの名前変更の確認](./media/luis-how-to-manage-versions/rename-popup.png) 
