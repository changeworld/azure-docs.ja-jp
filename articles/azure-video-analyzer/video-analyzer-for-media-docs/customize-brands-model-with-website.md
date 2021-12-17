---
title: Azure Video Analyzer for Media (旧称 Video Indexer) Web サイトを使用してブランド モデルをカスタマイズする
titleSuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media (旧称 Video Indexer) Web サイトを使用してブランド モデルをカスタマイズする方法について説明します。
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 12/15/2019
ms.author: kumud
ms.openlocfilehash: 5bb1a6317ad946da4e7fc00068a7fac40c45d772
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121447"
---
# <a name="customize-a-brands-model-with-the-video-analyzer-for-media-website"></a>Video Analyzer for Media Web サイトを使用してブランド モデルをカスタマイズする

Azure Video Analyzer for Media (旧 Video Indexer) では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで言及されている場合や、ビデオ内の視覚テキストに表示される場合、Video Analyzer for Media では Microsoft がそのコンテンツ内のブランドとして検出されます。

カスタム ブランド モデルを使用すると、以下のことが可能です。

- Video Analyzer for Media で Bing のブランド データベースに含まれるブランドを検出するかどうかを選択します。
- Video Analyzer for Media で特定のブランドが検出されないようにするかどうかを選択します (基本的に、ブランドの拒否リストを作成します)。
- Video Analyzer for Media で、Bing のブランド データベースに含まれていない可能性があり、モデルの一部である必要があるブランドを含めるかどうかを選択します (基本的に、ブランドの受け入れリストを作成します)。

より詳しい概要については、こちらの[概要](customize-brands-model-overview.md)に関するページを参照してください。

このトピックで説明されているように、Video Analyzer for Media Web サイトを使用して、ビデオで検出されるカスタム ブランド モデルを作成、使用、および編集することができます。 また、API を使用することもできます (「[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)」で説明しています)。

> [!NOTE]
> ブランド追加に先立ってビデオにインデックスを付けた場合、インデックスを再作成する必要があります。 動画に関連付けられているドロップダウン メニューに **[インデックスの再作成]** 項目があります。 **[詳細設定オプション]** 、 **[ブランドのカテゴリ]** の順に選択し、 **[すべてのブランド]** をオンにします。

## <a name="edit-brands-model-settings"></a>ブランド モデルの設定を編集する

Bing ブランド データベースからのブランドを検出するかどうかを設定するオプションもあります。 このオプションを設定するには、ブランド モデルの設定を編集する必要があります。 次の手順に従います。

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトに移動して、サインインします。
1. アカウント内のモデルをカスタマイズするには、ページの左にある **[コンテンツ モデルのカスタマイズ]** ボタンを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Video Analyzer for Media でコンテンツ モデルをカスタマイズする":::
1. ブランドを編集するには、 **[ブランド]** タブを選択します。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-brand-model/customize-brand-model.png" alt-text="[コンテンツ モデルのカスタマイズ] ダイアログ ボックスの [ブランド] タブを示すスクリーンショット":::
1. Video Analyzer for Media で、Bing によって提案されたブランドを検出する場合は、 **[Bing で提案されたブランドを表示する]** オプションをオンにします。そうしない場合は、チェック ボックスをオフのままにします。

## <a name="include-brands-in-the-model"></a>モデルにブランドを含める

**[ブランドを含める]** セクションは、Bing によって提案されていなくても Video Analyzer for Media で検出するカスタム ブランドを表します。  

### <a name="add-a-brand-to-include-list"></a>含めるリストにブランドを追加する

1. **[+ Create new brand]\(+ 新しいブランドの作成\)** を選択します。

    名前 (必須)、カテゴリ (省略可能)、説明 (省略可能)、参照 URL (省略可能) を指定します。
    カテゴリ フィールドは、ブランドをタグ付けするためのものです。 このフィールドは、Video Analyzer for Media API を使用している場合に、ブランドの "*タグ*" として表示されます。 たとえば、ブランドが "Azure" の場合、"クラウド" としてタグを付けたり、分類したりすることができます。

    参照用の URL フィールドは、Wikipedia ページへのリンクなど、ブランドの任意の参照 Web サイトにすることができます。

2. **[保存]** を選択すると、 **[ブランドを含める]** の一覧にブランドが追加されます。

### <a name="edit-a-brand-on-the-include-list"></a>含めるリストのブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンを選択します。

    ブランドのカテゴリ、説明、参照 URL を更新できます。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。

2. **[更新]** ボタンをクリックし、新しい情報でブランドを更新します。

### <a name="delete-a-brand-on-the-include-list"></a>含めるリストのブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンを選択します。
2. **[削除]** をクリックすると、そのブランドは *[ブランドを含める]* の一覧に表示されなくなります。

## <a name="exclude-brands-from-the-model"></a>モデルからのブランドを除外する

**[ブランドの除外]** セクションは、Video Analyzer for Media で検出しないブランドを表します。

### <a name="add-a-brand-to-exclude-list"></a>除外リストにブランドを追加する

1. **[+ Create new brand]\(+ 新しいブランドの作成\)** を選択します。

    名前 (必須)、カテゴリ (省略可能) を指定します。

2. **[保存]** を選択すると、 *[ブランドの除外]* の一覧にブランドが追加されます。

### <a name="edit-a-brand-on-the-exclude-list"></a>除外リストのブランドを編集する

1. 編集するブランドの横にある鉛筆アイコンを選択します。

    更新できるのは、ブランドのカテゴリのみです。 ブランドの名前は一意であるため、変更することはできません。 ブランド名を変更する必要がある場合は、ブランド全体を削除し (次のセクションを参照してください)、新しい名前でブランドを新規作成してください。

2. **[更新]** ボタンをクリックし、新しい情報でブランドを更新します。

### <a name="delete-a-brand-on-the-exclude-list"></a>除外リストのブランドを削除する

1. 削除するブランドの横にあるごみ箱アイコンを選択します。
2. **[削除]** をクリックすると、そのブランドは *[ブランドの除外]* の一覧に表示されなくなります。

## <a name="next-steps"></a>次のステップ

[API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)
