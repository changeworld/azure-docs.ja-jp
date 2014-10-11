<properties linkid="develop-media-services-how-to-guides-ios-media-player-framework" urlDisplayName="iOS Media Player Framework" pageTitle="Use the iOS Media Player Framework with Azure Media Services" metaKeywords="" description="Learn how to use the Media Services iOS Media Player Framework library to create rich, dynamic apps.," metaCanonical="" services="media-services" documentationCenter="" title="How to use the Azure Media Services iOS Media Player Framework" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="mobile-ios" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Azure Media Services iOS Media Player フレームワークを使用する方法

Azure メディア サービス iOS Media Player フレームワーク ライブラリを使用すると、iPod、iPhone、iPad の開発者は、ビデオとオーディオのストリームをその場で作成およびミックスするダイナミックなリッチ クライアント アプリケーションを容易に作成できます。たとえば、スポーツ コンテンツを表示するアプリケーションでは、選んだ箇所に広告を容易に挿入することができ、メイン コンテンツが巻き戻された場合も含めて、広告を表示する頻度を制御できます。教育用アプリケーションでも同じ機能を使用して、たとえば、メイン コンテンツに戻る前に、メインの講義に応じた余談 (または補足説明) を加えるようなコンテンツを作成することができます。

一般に、アプリケーションとユーザーとの対話に基づいてコンテンツ ストリームを作成するアプリケーションを構築する作業は、かなり煩雑です。通常は、ストリーム全体を 1 から作成して、事前にサーバーに保存しておく必要があります。iOS Media Player フレームワークを使用すると、これらをすべて処理できるクライアント アプリケーションを作成できます。メイン コンテンツ ストリームに対する制御や変更は必要ありません。そのための方法は次のとおりです。

-   クライアント デバイス上で、事前にコンテンツ ストリームのスケジュールを設定する。
-   プリロール広告または挿入広告のスケジュールを設定する。
-   ポストロール広告または挿入広告のスケジュールを設定する。
-   ミッドロール広告または挿入広告のスケジュールを設定し、Ad Pod を作成する。
-   ミッドロール広告または挿入広告をコンテンツのタイムラインが巻き戻されるたびに再生するか、1 度だけ再生して自身をタイムラインから削除するかを制御する。
-   ユーザーがボタンを押すか、アプリケーションがサービスから通知を受け取るなど、なんらかのイベントの結果として動的にコンテンツを直接タイムラインに挿入する。たとえば、ニュース コンテンツのプログラムからニュース速報の通知が送信されると、アプリケーションがメイン コンテンツを "一時停止" し、ニュース速報のストリームを動的に読み込むなど。

iOS デバイスのメディア再生設備にこれらの機能を組み合わせると、非常にリッチなメディア エクスペリエンスを非常に短時間に、縮小したリソースで構築できます。

この SDK には、iOS アプリケーションの構築方法を示す SamplePlayer アプリケーションが含まれています。これには、コンテンツ ストリームをその場で作成し、ユーザーがボタンを押すことによって動的に挿入広告をトリガーするためのほとんどの機能が使用されています。このチュートリアルでは、SamplePlayer アプリケーションのメイン コンポーネントについて説明し、これをアプリケーションの開始点として使用する方法を示します。

## SamplePlayer アプリケーションの紹介

次の手順では、アプリケーションの入手方法を示し、フレームワークを活用したアプリケーションの各部について説明します。

1.  Git リポジトリを複製します。

    `git clone https://github.com/WindowsAzure/azure-media-player-framework`

2.  `azure-media-player-framework/src/iOS/HLSClient/` にあるプロジェクト **SamplePlayer.xcodeproj** を開きます。

3.  SamplePlayer の構造は、次のとおりです。

![HLS サンプル コードの構造][]

1.  iPad フォルダーには 2 つの .xib ファイル、**SeekbarViewController** と **SamplePlayerViewController** があります。これらは、iPad アプリケーションの UI レイアウトを作成します。iPhone フォルダーにも 2 つの .xib ファイルがあり、これらはシーク バーとコントローラーを定義しています。

2.  メイン アプリケーションのロジックは、`Shared` フォルダー内の **SamplePlayerViewController.m** に記述されています。以下に示すコード スニペットのほとんどは、このファイルに含まれています。

## UI レイアウトについて

プレーヤーのインターフェイスは、2 つの .xib ファイルで定義されています (以下の説明では例として iPad のレイアウトを使用していますが、iPhone でもレイアウトはこれとよく似ており、基本的な概念は同じです)。

### SamplePlayerViewController\_iPad.xib

![Sample Player のアドレス バー][]

-   **[Media URL] (メディアの URL)** は、メディア ストリームの読み込みに使用する URL です。アプリケーションには、使用できるメディア URL の一覧があらかじめ用意されており、[URL Selection] (URL 選択) ボタンを使用して選択できます。独自の Http ライブ ストリーミング (HLS) コンテンツの URL を入力することもできます。このメディア コンテンツは、1 つ目のメイン コンテンツとして使用されます。
    **注: この URL を空にしておくことはできません。**

-   **[URL Selection]** (URL の選択) ボタンを使用すると、メディア URL の一覧から、別の URL を選択できます。

### SeekbarViewController\_iPad.xib

![シーク バー コント ローラー][]

-   **[Play]** (再生) ボタンは、メディアの再生と一時停止に使用します。

-   **Seek bar** (シーク バー) は、再生タイムライン全体を表します。シーク時には、シーク バーのシーク ボタンを押したまま希望の位置までドラッグして放します。

**メモ**: シーク中に広告の位置に達すると、広告の長さの新しいシーク バーが表示されます。メイン シーク バーが表示されるのは、メイン コンテンツの時間内のみです (つまり、メイン シーク バーでは広告の長さが 0 になります)。

-   **[Player time]** (プレーヤー時間) コントロールには、2 つの時間 (`Label:playerTime`) が 00:23/02:10 のように表示されます。この場合、00:23 は現在の再生時間、02:10 はメディア全体の長さを表します。

-   **[SkipFroward/SkipBackward]** (前方へのスキップ/後方へのスキップ) ボタンは現在、正しく動作しません。更新バージョンが間もなくリリースされます。

-   メイン コンテンツの再生中に **[SkipFroward/SkipBackward]** (ここにスケジュール設定) ボタンを押すと、広告が挿入されます (広告ソースの URL はコード ビハインドで定義できます)。注: 現在のバージョンでは、広告の再生中に他の広告をスケジュール設定することはできません。

### メイン コンテンツをスケジュールする方法

コンテンツ クリップの 0 秒から 80 秒までをスケジュールしました。

    //Schedule the main content
    MediaTime *mediaTime = [[[MediaTime alloc] init] autorelease];
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
        
    int clipId = 0;
    if (![framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

このサンプル コードの内容は次のとおりです。

-   **MediaTime** オブジェクトは、メイン コンテンツとしてスケジュールされるビデオ クリップを制御します。上の例では、ビデオ クリップの長さが 80 秒間 (0 秒目から 80 秒目まで) になるようにスケジュールされます。
-   **clipBeginMediaTime** は、ビデオ再生の開始位置を時間で表します。たとえば、**clipBeginMediaTime** = 5 の場合、このビデオ クリップはビデオ クリップ内の 5 秒目から開始されます。
-   **clipEndMediaTime** は、ビデオ再生の終了位置を時間で表します。**clipEndMediaTime**=100 の場合、ビデオの再生はビデオ クリップ内の 100 秒目で終了します。
    \***MediaTime** のスケジュール設定は、フレームワークに対して **appendContentClip** を指示することによって行っています。上の例では、メイン コンテンツの URL が `[NSURL URLWithString:url]` で指定され、そのメディアのスケジュールが **withMedia** を使用して設定されています (`[framework appendContentClip:[NSURL URLWithString:url] withMediaTime:mediaTime andGetClipId:&clipId])`)。

**注:** メイン コンテンツのスケジュール設定は、必ず (プリロール広告の場合も含めて) 広告のスケジュール設定より前に行ってください。

### バリエーション: 2 つのメイン コンテンツ クリップを再生する場合、次のコードを使用して 2 つ目のクリップを 1 つ目の後にスケジュールすることもできます。

    //Schedule second content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 30;
    mediaTime.clipEndMediaTime = 80;
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }

前のコードに続いてこの処理を行うと、2 つのコンテンツ ストリームがメイン コンテンツのタイムライン上でスケジュールされます。1 つ目のコンテンツは `URLWithString:url` に基づいてスケジュールされ、2 つ目のコンテンツは `URLWithString:secondContent` に基づいてスケジュールされます。2 つ目のコンテンツは、メディア ストリームの先頭から 30 秒の位置から開始され、80 秒の位置で終了します。

## 広告のスケジュール設定

現在のリリースでは、**pauseTimeline=false** の広告のみがサポートされています。これは、広告の終了後に、メイン コンテンツが中断された箇所から再生が再開されることを意味します。

次に重要なポイントをいくつか示します。

-   広告をスケジュールする際に、**LinearTime.duration** はすべて 0 にする必要があります。
-   **clipEndMediaTime** が広告の長さを超える場合、クリップの末尾より後に再生が終了し、例外はスローされません。広告によるビジネス チャンスを逃すことのないよう、広告の自然な長さがレンダリング時間 (**clipEndMediaTime**) に収まるかどうかを確認することをお勧めします。
-   プリロール、ミッドロール、およびポストロールの広告がサポートされています。プリロール広告は、すべてのコンテンツの先頭のみにスケジュールできます。たとえば、ラフ カット編集 (RCE) のシナリオで、プリロール広告を 2 番目のコンテンツとしてスケジュールすることはできません。
-   Sticky 広告および Play-once 広告がサポートされており、これらをプリロール、ミッドロール、およびポストロール広告と組み合わせて使用することができます。
-   広告の形式としては、.Mp4 または HLS を使用できます。

</p>
### プリロール広告、ミッドロール広告、ポストロール広告、Ad Pod をスケジュールする方法

#### プリロール広告のスケジュール設定

    LinearTime *adLinearTime = [[[LinearTime alloc] init] autorelease];
    NSString *adURLString = @"http://smoothstreamingdemo.blob.core.windows.net/videoasset/WA-BumpShort_120530-1.mp4";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.clipBeginMediaTime = 0;
    adInfo.mediaTime.clipEndMediaTime = 5;
    adInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

**AdInfo** オブジェクトは、広告クリップに関するすべての情報を表します。

-   **ClipURL** はクリップ ソースの URL です。
-   **mediaTime** プロパティは、広告を再生する時間の長さを示します (**clipBeginMediaTime** は広告の開始時間、**clipEndMediaTime** は広告の終了時間です)。上のサンプル コードでは、クリップの 0 秒目から 5 秒目まで、5 秒間の広告をスケジュールしています。
-   **Policy** オブジェクトは、現在、フレームワークでは使用されていません。
-   Ad Pod ではない場合は、**appendTo** の値を -1 に設定する必要があります。
-   **type** の値には、プリロール、ミッドロール、ポストロール、または Ad Pod を指定できます。プリロールまたはポストロールの場合、関連付けられているタイミングがないため、type の指定が必要です。

#### ミッドロール広告のスケジュール設定

`adLinearTime.startTime = 23;` を追加すると、広告の再生はメイン コンテンツのタインムラインの 23 秒目から開始されます。

#### ポストロール広告のスケジュール設定

    //Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    postAdInfo.mediaTime.clipEndMediaTime = 5;
    postAdInfo.policy = [[[PlaybackPolicy alloc] init] autorelease];
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

プリロール広告のスケジュール設定との唯一の相違は、`postAdInfo.type = AdType_Postroll;` です。上のコードでは、ポストロールとして 5 秒間の広告をスケジュール設定しています。

#### Ad Pod のスケジュール設定

Ad Pod は、複数の広告が連続して再生される広告ブレークです。2 つの広告を 1 つの Ad Pod 内にスケジュール設定するコードを次に示します。

    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    adpodInfo1.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.mediaTime = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    int32_t adIndex = 0;
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
        
    NSString *adpodSt2=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-532531b8-fca4-4c15-86f6-45f9f45ec980/Windows%208_%20Sign%20in%20with%20a%20Smile.mp4?st=2012-11-28T16%3A35%3A26Z&se=2014-11-28T16%3A35%3A26Z&sr=c&si=c6ede35c-f212-4ccd-84da-805c4ebf64be&sig=zcWsj1JOHJB6TsiQL5ZbRmCSsEIsOJOcPDRvFVI0zwA%3D";
    AdInfo *adpodInfo2 = [[[AdInfo alloc] init] autorelease];
    adpodInfo2.clipURL = [NSURL URLWithString:adpodSt2];
    adpodInfo2.mediaTime = [[[ManifestTime alloc] init] autorelease];
    adpodInfo2.mediaTime.clipBeginMediaTime = 0;
    adpodInfo2.mediaTime.clipEndMediaTime = 17;
    adpodInfo2.policy = [[[PlaybackPolicy alloc] init] autorelease];
    adpodInfo2.type = AdType_Pod;
    adpodInfo2.appendTo = adIndex;
    if (![framework scheduleClip:adpodInfo2 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }

いくつかの点について説明します。

-   1 つ目のクリップに関する **appendTo** は -1 です。`[framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex]` を呼び出すと、`adIndex` は、Ad Pod 内で 1 つ目であるこのクリップの終わりを示す一意の値を受け取ります。 Ad Pod 内の 2 つ目のクリップについては、**appendTo** を `adpodInfo2.appendTo = adIndex;` (1 つ目の終了位置を 2 つ目のクリップの開始位置とする) として設定することにより、2 つ目の広告の先頭と 1 つ目の末尾を揃えています。
-   さらに、type を `AdType_Pod` として設定することにより、Ad Pod であることを示す必要があります。

### Play-Once 広告または "Sticky" 広告をスケジュールする方法

    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    oneTimeInfo.deleteAfterPlay = YES;

上のコード例で示されているように、**deleteAfterPlay** を **YES** に設定すると、この広告は 1 度だけ再生されます。**deleteAfterPlay** を **NO** に設定すると、この広告は連続して再生されます。これを Sticky 広告といいます。

### 詳細については、[Azure Media Player フレームワークの wiki][] を参照してください。

  [HLS サンプル コードの構造]: http://mingfeiy.com/wp-content/uploads/2013/01/HLS-Structure.png
  [Sample Player のアドレス バー]: http://mingfeiy.com/wp-content/uploads/2013/01/addressbar.png
  [シーク バー コント ローラー]: http://mingfeiy.com/wp-content/uploads/2013/01/controller.png
  [Azure Media Player フレームワークの wiki]: https://github.com/WindowsAzure/azure-media-player-framework/wiki
