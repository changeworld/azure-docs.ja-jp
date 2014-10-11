<properties linkid="manage-services-mediaservices-manage-media-services-content" urlDisplayName="How to manage media content" pageTitle="How to manage media content - Azure Media Services" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# メディア サービスのコンテンツの管理方法に関するページ

Azure メディア サービスのコンテンツ ビューを利用して、メディア サービス アカウントのメディア コンテンツを管理できます。

現在は、次のコンテンツ操作をポータルから直接実行できます。

-   コンテンツ情報の表示 (公開状態、公開 URL、サイズ、日付、最終更新日時など) を表示することもできます。
-   新しいコンテンツのアップロード
-   コンテンツのエンコード
-   コンテンツ ビデオの再生
-   コンテンツの発行および発行の取り消し
-   コンテンツの削除

## 方法: コンテンツをアップロードする

1.  [管理ポータル][]で、**[メディア サービス]** をクリックし、目的の Media Services アカウント名をクリックします。
2.  [コンテンツ] ページを選択します。
3.  ページまたはポータルの下部にある **[アップロード]** をクリックします。
4.  **[コンテンツのアップロード]** ダイアログで、目的のアセット ファイルを見つけます。ファイルをクリックして、**[開く]** をクリックするか、**Enter** キーを押します。

    ![UploadContentDialog][]

5.  [コンテンツのアップロード] ダイアログで、チェック ボタンをクリックして、ファイル名とコンテンツ名をそのまま使用します。
6.  アップロードが開始され、進捗状況はポータルの下部で確認できます。

    ![JobStatus][]

アップロードが完了すると、コンテンツ一覧に新しいアセットが表示されます。通常、名前の末尾に "**-Source**" が付加され、エンコード タスクのソース コンテンツとして新しいコンテンツを見つけやすくなっています。

アップロード処理が終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これによりアセットのファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。

## 方法: コンテンツのエンコード

1.  [管理ポータル][]で、**[メディア サービス]** をクリックし、目的の Media Services アカウント名をクリックします。
2.  ページの上部にある [コンテンツ] ページをクリックします。
3.  エンコード ジョブで使用するソース ビデオをクリックし、ページの下部にある **[エンコード]** をクリックします。
4.  [Azure メディア エンコーダー] ダイアログで、主なプリセットまたは詳細なプリセットからエンコードを 1 つ選択します。

    **主なプリセット**

    -   [PC/Mac での再生 (Flash/Silverlight を使用)]\*\*。このプリセットは、次のような特徴を持つスムーズ ストリーミング アセットを作成します。AAC を使用して 96 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 3,400 ～ 400 kbps の範囲で 6 ビットレートで CBR エンコードされた 720p ビデオ、および 2 秒の GOP。
    -   [HTML5 (IE/Chrome/Safari) を使用した再生]\*\*。このプリセットは、次のような特徴を持つ単一の MP4 ファイルを作成します。AAC を使用して 128 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 4,500 kbps で CBR エンコードされた 720p ビデオ。
    -   [iOS デバイスおよび PC/Mac での再生]\*\*。このプリセットは、スムーズ ストリーミング アセット (上述) と同じ特徴を持つアセットを作成しますが、Apple HLS ストリームを iOS デバイスに配信するときに使用できる形式で作成します。

    **詳細なプリセット**

    -   「[Media Services Encoder 用のタスク プリセット][]」に、詳細なプリセットのリストに表示されているプリセットの意味が説明されています。

    ![EncoderDialog][]

    現在、ポータルではメディア エンコーダーがサポートするエンコード形式がすべてサポートされているわけではありません。また、メディア アセットの暗号化と復号化もサポートされていません。このようなタスクはプログラムによって実行できます。詳細については、「[Media Services SDK for .NET を使用したアプリケーション構築][]」および「[Windows Azure Media Encoder 用のタスク プリセット文字列][Media Services Encoder 用のタスク プリセット]」を参照してください。

5.  [Azure メディア エンコーダー] ダイアログで、わかりやすい出力コンテンツ名を入力するか、既定値をそのまま使用します。チェック ボタンをクリックすると、エンコード処理が開始され、進捗状況はポータルの下部で確認できます。

エンコードが終了すると、ビューは下図のようになります。

    ![PortalViewUploadCompleted][portaloverview]

エンコードが終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これにより出力アセットのファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。

## 方法: コンテンツを発行する

1.  [管理ポータル][]で、**[メディア サービス]** をクリックし、目的の Media Services アカウント名をクリックします。
2.  ページの上部にある [コンテンツ] ページをクリックします。
3.  発行されていないアセットをクリックします。[発行] をクリックしてパブリック URL に発行します。コンテンツが URL に発行されると、エンコードされたコンテンツを描画できるクライアント プレーヤーでその URL を開くことができるようになります。

![PublishedContent][]

## 方法: ポータルでコンテンツを再生する

1.  [管理ポータル][]で、**[メディア サービス]** をクリックし、目的の Media Services アカウント名をクリックします。
2.  ページの上部にある [コンテンツ] ページをクリックします。
3.  目的のビデオ コンテンツをクリックし、ポータルの下部にある **[再生]** をクリックします。ポータルから再生できるのは、発行されたコンテンツだけです。また、エンコード方法がブラウザーでサポートされている必要があります。

<!-- Images -->

  [管理ポータル]: http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409
  [UploadContentDialog]: ./media/media-services-manage-content/UploadContent.png
  [JobStatus]: ./media/media-services-manage-content/Status.png
  [Media Services Encoder 用のタスク プリセット]: http://go.microsoft.com/fwlink/?LinkId=270865
  [EncoderDialog]: ./media/media-services-manage-content/EncoderDialog2.png
  [Media Services SDK for .NET を使用したアプリケーション構築]: http://go.microsoft.com/fwlink/?LinkId=270866
  [PublishedContent]: ./media/media-services-manage-content/media-services-upload-content-published.png
