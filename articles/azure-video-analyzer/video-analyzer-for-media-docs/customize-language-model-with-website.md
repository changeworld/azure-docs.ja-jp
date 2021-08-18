---
title: Azure Video Analyzer for Media (旧称 Video Indexer) Web サイトを使用して言語モデルをカスタマイズする
titleSuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media (旧称 Video Indexer) Web サイトを使用して言語モデルをカスタマイズする方法について説明します。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: 67610ef51a11aca59b65dd60a8a70dbf10d59e50
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121456"
---
# <a name="customize-a-language-model-with-the-video-analyzer-for-media-website"></a>Video Analyzer for Media Web サイトを使用して言語モデルをカスタマイズする

Azure Video Analyzer for Media (旧称 Video Indexer) では、カスタム言語モデルを作成し、適応テキストをアップロードすることで音声認識をカスタマイズすることができます。適応テキストとは、エンジンを適応させたいボキャブラリがあるドメインから取得したテキストのことです。 モデルをトレーニングすると、適応テキスト内に含まれる新しい単語が認識されるようになります。

カスタム言語モデルの詳細な概要とベスト プラクティスについては、「[Video Analyzer for Media を使用して言語モデルをカスタマイズする](customize-language-model-overview.md)」を参照してください。

このトピックで説明しているように、Video Analyzer for Media Web サイトを使用すると、アカウント内にカスタム言語モデルを作成し、編集することができます。 また、API を使用することもできます (「[API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)」で説明しています)。

## <a name="create-a-language-model"></a>言語モデルの作成

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトに移動して、サインインします。
1. アカウント内のモデルをカスタマイズするには、ページの左にある **[コンテンツ モデルのカスタマイズ]** ボタンを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Video Analyzer for Media でコンテンツ モデルをカスタマイズする":::
1. **[言語]** タブを選択します。

    サポートされている言語の一覧が表示されます。
1. 目的の言語の下で、 **[モデルを追加]** を選択します。
1. 言語モデルの名前を入力し、Enter キーを押します。

    この手順によってモデルが作成され、モデルにテキスト ファイルをアップロードするオプションが提供されます。
1. テキスト ファイルを追加するには、 **[ファイルの追加]** を選択します。 ファイル エクスプローラーが開かれます。
1. テキスト ファイルの場所に移動し、ファイルを選択します。 言語モデルには複数のテキスト ファイルを追加することができます。

    言語モデルの右側にある **[...]** ボタンを選択し、 **[ファイルの追加]** を選択してテキスト ファイルを追加することもできます。
1. テキスト ファイルのアップロードが完了したら、緑色の **[トレーニング]** オプションを選択します。

トレーニング プロセスには数分かかることがあります。 トレーニングが完了すると、モデルの横に **[トレーニング済み]** と表示されます。 ユーザーは、モデル内のファイルをプレビュー、ダウンロード、削除することができます。

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="モデルのトレーニング":::

### <a name="using-a-language-model-on-a-new-video"></a>新しいビデオに言語モデルを使用する

新しいビデオに対して言語モデルを使用するには、以下のいずれかの操作を行います。

* ページ上部の **[アップロード]** ボタンを選択します。

    ![Video Analyzer for Media の [アップロード] ボタン](./media/customize-language-model/upload.png)
* オーディオまたはビデオ ファイルをドロップするか、ファイルを参照します。

**ビデオのソース言語** を選択するオプションが使用可能になります。 ドロップダウン リストを選択し、一覧から、作成した言語モデルを選択します。 一覧には、言語モデルの言語と、それに付けた名前 (かっこ内) が表示されます。 例:

![ビデオのソース言語を選択する — Video Analyzer for Media でビデオのインデックスを再作成する](./media/customize-language-model/reindex.png)

ページ下部の **[アップロード]** オプションを選択すると、その言語モデルを使用して、新しいビデオにインデックスが作成されます。

### <a name="using-a-language-model-to-reindex"></a>言語モデルを使用してインデックスを再作成する

言語モデルを使用し、コレクションのビデオのインデックスを再作成するには、次の手順に従います。

1. [Video Analyzer for Media](https://www.videoindexer.ai/) ホーム ページにサインインします。
1. ビデオの **[...]** ボタンをクリックし、 **[インデックスの再作成]** を選択します。
1. ビデオのインデックスの再作成に使用する、**ビデオのソース言語** を選択するオプションが使用可能になります。 ドロップダウン リストを選択し、一覧から、作成した言語モデルを選択します。 一覧には、言語モデルの言語と、それに付けた名前 (かっこ内) が表示されます。
1. **[インデックスの再作成]** ボタンを選択すると、その言語モデルを使用してビデオのインデックスが再作成されます。

## <a name="edit-a-language-model"></a>言語モデルの編集

言語モデルは、名前を変更したり、ファイルを追加したり、ファイルを削除したりして編集することができます。

言語モデルに対してファイルを追加または削除する場合、緑色の **[トレーニング]** オプションを選択して、モデルを再度トレーニングする必要があります。

### <a name="rename-the-language-model"></a>言語モデルの名前変更

言語モデルの右側にある省略記号 ( **[...]** ) ボタンを選択し、 **[名前の変更]** を選択すると、言語モデルの名前を変更できます。

新しい名前を入力し、Enter キーを押します。

### <a name="add-files"></a>ファイルの追加

テキスト ファイルを追加するには、 **[ファイルの追加]** を選択します。 ファイル エクスプローラーが開かれます。

テキスト ファイルの場所に移動し、ファイルを選択します。 言語モデルには複数のテキスト ファイルを追加することができます。

言語モデルの右側にある省略記号 ( **[...]** ) ボタンを選択し、 **[ファイルの追加]** を選択すると、テキスト ファイルも追加できます。

### <a name="delete-files"></a>ファイルの削除

言語モデルからファイルを削除するには、テキスト ファイルの右側にある省略記号 ( **[...]** ) ボタンを選択し、 **[削除]** を選択します。 新しいウィンドウが開き、削除は元に戻せないというメッセージが表示されます。 新しいウィンドウで **[削除]** オプションを選択します。

これにより、言語モデルからファイルが完全に削除されます。

## <a name="delete-a-language-model"></a>言語モデルの削除

アカウントから言語モデルを削除するには、言語モデルの右側にある省略記号 ( **[...]** ) ボタンを選択し、 **[削除]** を選択します。

新しいウィンドウが開き、削除は元に戻せないというメッセージが表示されます。 新しいウィンドウで **[削除]** オプションを選択します。

これにより、アカウントから言語モデルが完全に削除されます。 削除された言語モデルを使用していたすべてのビデオでは、ユーザーがビデオのインデックスを再作成するまで同じインデックスが保持されます。 ビデオのインデックスを再作成すると、ビデオに新しい言語モデルを割り当てることができます。 それ以外の場合、Video Analyzer for Media では既定のモデルを使用して、ビデオのインデックスが再作成されます。

## <a name="customize-language-models-by-correcting-transcripts"></a>トランスクリプトの変更による言語モデルのカスタマイズ

Video Analyzer for Media では、ユーザーがそのビデオの文字起こしに対して行う実際の変更に基づく、言語モデルの自動カスタマイズがサポートされています。

1. トランスクリプトを変更するには、自分のアカウントのビデオから、編集したいビデオを開きます。 **[タイムライン]** タブを選択します。

    ![言語モデルをカスタマイズする、[タイムライン] タブ — Video Analyzer for Media](./media/customize-language-model/timeline.png)

1. 鉛筆アイコンを選択して、文字起こしのトランスクリプトを編集します。

    ![言語モデルをカスタマイズする、文字起こしの編集 — Video Analyzer for Media](./media/customize-language-model/edits.png)

    Video Analyzer for Media によって、ビデオの文字起こし内の変更した行がすべてキャプチャされ、"From transcript edits" というテキスト ファイルにそれらが自動的に追加されます。 これらの編集は、このビデオにインデックスを付けるために使用された特定の言語モデルを再トレーニングするために使用されます。 
    
    [ウィジェット](video-indexer-embed-widgets.md)のタイムラインで行われた編集も含まれています。
    
    このビデオのインデックス作成時に言語モデルを指定しなかった場合、このビデオのすべての編集内容は、ビデオの検出された言語内の、"アカウント適応" と呼ばれる既定の言語モデルに格納されます。
    
    同じ行に対して複数の編集が加えられた場合、変更された行の最新バージョンのみが、言語モデルの更新に使用されます。  
    
    > [!NOTE]
    > カスタマイズに使用されるのは、テキストの変更のみです。 実際の単語に関係しない変更 (句読点やスペースなど) は含められません。
    
1. [コンテンツ モデルのカスタマイズ] ページの [言語] タブに、トランスクリプトの変更が表示されます。

   自分の各言語モデルの "From transcript edits" ファイルを確認するには、そのファイルを選択して開きます。

    ![トランスクリプトの編集から — Video Analyzer for Media](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>次のステップ

[API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)
