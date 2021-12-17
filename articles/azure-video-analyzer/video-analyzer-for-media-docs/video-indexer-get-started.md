---
title: Azure Video Analyzer for Media (旧称 Video Indexer) にサインアップして最初のビデオをアップロードする - Azure
description: Azure Video Analyzer for Media (旧称 Video Indexer) ポータルにサインアップし、それを使用して最初のビデオをアップロードする方法について説明します。
ms.topic: quickstart
ms.subservice: azure-video-analyzer-media
ms.date: 01/25/2021
ms.author: juliako
ms.openlocfilehash: bf8b8f640fbcb6155092a50fdbd6e1af66060e07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636156"
---
# <a name="quickstart-how-to-sign-up-and-upload-your-first-video"></a>クイック スタート:サインアップして最初のビデオをアップロードする方法

この入門クイック スタートでは、Azure Video Analyzer for Media (旧称 Video Indexer) Web サイトにサインインする方法と、最初のビデオをアップロードする方法を示します。

Video Analyzer for Media アカウントを作成するときに、無料試用アカウント (一定分数の無料インデックス作成を利用可能) または有料オプション (クォータによる制限なし) を選択できます。 無料試用アカウントで Video Analyzer for Media 使用する場合、Web サイト ユーザーは最大 600 分間の無料インデックス作成を利用でき、API ユーザーは最大 2,400 分間の無料インデックス作成を利用できます。 有料オプションでは、[ご使用の Azure サブスクリプションと Azure Media Services アカウントに接続される](connect-to-azure.md) Video Analyzer for Media アカウントを作成します。 インデックス作成にかかった時間 (分) に対して支払います。詳細については、「[Media Services の価格](https://azure.microsoft.com/pricing/details/media-services/)」を参照してください。 

## <a name="sign-up-for-video-analyzer-for-media"></a>Video Analyzer for Media にサインアップする

Video Analyzer for Media を使用した開発を開始するには、[Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトにアクセスして、サインアップします。

Video Analyzer for Media の使用を開始すると、保存されているデータおよびアップロードされたコンテンツはすべて、Microsoft で管理されるキーを使用して保存時に暗号化されます。

> [!NOTE]
> 「[Video Analyzer for Media Web サイトの認証に予定されている変更](./release-notes.md#planned-video-analyzer-for-media-website-authenticatication-changes)」を確認してください。

## <a name="upload-a-video-using-the-video-analyzer-for-media-website"></a>Video Analyzer for Media Web サイトを使用してビデオをアップロードする

### <a name="supported-file-formats-for-video-analyzer-for-media"></a>Video Analyzer for Media でサポートされるファイル形式

Video Analyzer for Media で使用できるファイル形式の一覧については、「[入力コンテナー/ファイル形式](../../media-services/latest/encode-media-encoder-standard-formats-reference.md)」を参照してください。

### <a name="upload-a-video"></a>ビデオをアップロードする

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ビデオをアップロードするには、 **[アップロード]** ボタンまたはリンクを押します。

    > [!NOTE]
    > ビデオの名前は、80 文字以下にする必要があります。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/video-indexer-upload.png" alt-text="アップロード":::
1. ビデオがアップロードされると、Video Analyzer for Media によってビデオのインデックス作成と分析が開始されます。 進行状況が表示されます。 

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/progress.png" alt-text="アップロードの進行状況":::
1. Video Analyzer for Media で分析が完了すると、ビデオへのリンクとビデオの内容の簡単な説明が記載されたメールが届きます。 人、話されたり書かれたりしている単語、トピック、名前付きエンティティなどです。
1. 後でライブラリの一覧でビデオを検索し、さまざまな操作を実行できます。 検索、インデックスの再作成、編集などです。

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/video-indexer-get-started/uploaded.png" alt-text="アップロードの完了":::

## <a name="supported-browsers"></a>サポートされているブラウザー

詳細については、「[サポートされているブラウザー](video-indexer-overview.md#supported-browsers)」を参照してください。

## <a name="see-also"></a>関連項目

詳細については、[ビデオのアップロードとインデックス作成](upload-index-videos.md)に関するページを参照してください。

ビデオをアップロードしてインデックスを作成したら、[Video Analyzer for Media Web サイト](video-indexer-view-edit.md)または [Video Analyzer for Media 開発者ポータル](video-indexer-use-apis.md)を使用して、ビデオの分析情報を表示できます。 

[API の使用開始](video-indexer-use-apis.md)

## <a name="next-steps"></a>次の手順

詳しい紹介記事については、[概要ラボ](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/IntroToVideoIndexer.md)を参照してください。 

ワークショップを終えるころには、ビデオやオーディオ コンテンツから抽出できる情報の種類についての理解が深まります。また、コンテンツ インテリジェンスに関連した好機を発見したり、Azure 上のビデオ AI を売り込んだり、Video Analyzer for Mediar に関するさまざまなシナリオをデモンストレーションしたりするための気運も高まることでしょう。

