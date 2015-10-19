<properties
	pageTitle="Azure ポータルを使用したオンデマンド コンテンツ配信の概要 | Microsoft Azure"
	description="このチュートリアルでは、Azure Media Services と Azure ポータルを使用したビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装手順を紹介します。"
	services="media-services"
	documentationCenter=""
	authors="Juliako"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/05/2015"
	ms.author="juliako"/>


# Azure ポータルを使用したオンデマンド コンテンツ配信の概要


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


このチュートリアルでは、Azure ポータルを使用した基本的なビデオ オン デマンド (VoD) コンテンツ配信アプリケーションの実装について、手順を追って説明します。

> [AZURE.NOTE]このチュートリアルを完了するには、Azure アカウントが必要です。アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。詳細については、「<a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Azure の無料試用版サイト</a>」を参照してください。

このチュートリアルに含まれるタスクは次のとおりです。

1.  Azure Media Services アカウントを作成する
2.  ストリーミング エンドポイントを構成する
1.  ビデオ ファイルをアップロードする
1.  一連のアダプティブ ビットレート MP4 ファイルにソース ファイルをエンコードします。
1.  資産を発行してストリーミング URL とプログレッシブ ダウンロード URL を取得する  
1.  コンテンツの再生


## Azure Media Services アカウントの作成

1. [Azure ポータル](https://manage.windowsazure.com/)で、**[新規]**、**[メディア サービス]**、**[簡易作成]** の順にクリックします。

	![Media Services の簡易作成](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. **[名前]** ボックスに新しいアカウントの名前を入力します。Media Services アカウント名に使用できる文字は、小文字または数字のみで、空白を含めることはできません。長さは 3 ～ 24 文字です。

3. **[リージョン]** ボックスで、Media Services アカウントのメタデータ レコードを保存するリージョンを選択します。ドロップダウン リストのボックスには、利用可能な Media Services リージョンのみが表示されます。

4. **[ストレージ アカウント]** ボックスで、Media Services アカウントのメディア コンテンツの BLOB ストレージとなるストレージ アカウントを選択します。Media Services アカウントと同じリージョンにある既存のストレージ アカウントを選択することも、新しいストレージ アカウントを作成することもできます。新しいストレージ アカウントは同じリージョンに作成されます。

5. 新しいストレージ アカウントを作成した場合は、**[新しいストレージ アカウント名]** ボックスにストレージ アカウントの名前を入力します。ストレージ アカウントの命名規則は、Media Services アカウントと同じです。

6. フォームの下部にある **[簡易作成]** をクリックします。

	処理の状態はウィンドウの下部にあるメッセージ領域で監視できます。

	アカウントの作成に成功すると、ステータスが [アクティブ] に変化します。

	ページ下部に **[キーの管理]** ボタンが表示されます。このボタンをクリックすると、Media Services アカウント名、プライマリ キー、セカンダリ キーを示すダイアログ ボックスが表示されます。Media Services アカウントにプログラムからアクセスするには、アカウント名とプライマリ キーの情報が必要です。

	![[Media Services] ページ](./media/media-services-portal-get-started/wams-mediaservices-page.png)

	アカウント名をダブルクリックすると、既定で [クイック スタート] ページが表示されます。このページでは、ポータルの別のページでも実行できる管理タスクをいくつか実行できます。たとえば、ビデオ ファイルのアップロードは、このページから実行することも、[コンテンツ] ページから実行することもできます。


## ポータルを使用したストリーミング エンドポイントの構成

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。  
- コンテンツ配信元となる*ストリーミング エンドポイント*のストリーミング ユニットを少なくとも 1 つ取得します。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

ストリーミング予約ユニットの数を変更するには、以下の手順を実行します。

1. [Azure ポータル](https://manage.windowsazure.com/)で、**[Media Services]** をクリックします。次に、メディア サービスの名前をクリックします。

2. [ストリーミング エンドポイント] ページを選択します。次に、変更するストリーミング エンドポイントをクリックします。

3. ストリーミング ユニットの数を指定するには、**[スケール]** タブを選択し、**[占有容量]** スライダーを動かします。

	![[スケール] ページ](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. **[保存]** をクリックして、変更を保存します。

	新しいユニットの割り当ては完了するまでに約 20 分かかります。

	>[AZURE.NOTE]現在のところ、ストリーミング ユニットの数を正の値からゼロに戻すと、ストリーミングが最大 1 時間無効になります。
	>
	> コストの計算時には、24 時間の期間内に指定されたユニットの最大数が使用されます。料金設定の詳細については、「[Azure 料金早見表](http://go.microsoft.com/fwlink/?LinkId=275107)」を参照してください。

## コンテンツをアップロードする


1. [Azure ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[Media Services]** をクリックし、目的の Media Services アカウント名をクリックします。
2. [コンテンツ] ページを選択します。
3. ページまたはポータルの下部にある **[アップロード]** をクリックします。
4. **[コンテンツのアップロード]** ダイアログ ボックスで、目的の資産ファイルを見つけます。ファイルをクリックして、**[開く]** をクリックするか、Enter キーを押します。

	![UploadContentDialog][uploadcontent]

5. **[コンテンツのアップロード]** ダイアログ ボックスで、チェック ボタンをクリックして、**ファイル**と**コンテンツ名**をそのまま使用します。
6. アップロードが開始され、進捗状況はポータルの下部で確認できます。  

	![JobStatus][status]

アップロードが完了すると、コンテンツ一覧に新しい資産が表示されます。通常、名前の末尾に "**-Source**" が付加され、エンコード タスクのソース コンテンツとして新しいコンテンツを見つけやすくなっています。

![ContentPage][contentpage]

アップロード処理が終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** を選択します。これにより資産のファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。


## コンテンツのエンコード

### 概要
インターネット経由でデジタル ビデオを配信するには、メディアを圧縮する必要があります。Media Services には、コンテンツのエンコード方法 (使用するコーデック、ファイル形式、解像度、ビットレートなど) を指定できる Media Encoder が用意されています。

クライアントに対するアダプティブ ビットレート ストリーミング配信は、Azure Media Services の代表的な用途の 1 つです。アダプティブ ビットレート ストリーミングでは、現在のネットワーク帯域幅、CPU 使用率などの条件に基づいてビデオが表示されるため、高低のビットレート ストリームの切り替えをクライアント側で行うことができます。Media Services でサポートされるアダプティブ ビットレート ストリーミング テクノロジは、HTTP ライブ ストリーミング (HLS)、スムーズ ストリーミング、MPEG DASH、HDS (Adobe PrimeTime/Access のライセンスが必要) です。

Media Services には動的パッケージ化機能があり、アダプティブ ビットレート MP4 またはスムーズ ストリーミングでエンコードされたコンテンツを、Media Services でサポートされるストリーミング形式 (MPEG DASH、HLS、スムーズ ストリーミング、または HDS) でそのまま配信することができます。つまり、これらのストリーミング形式に再度パッケージ化する必要がありません。

動的パッケージ化機能を利用するには、次の作業が必要となります。

- メザニン (ソース) ファイルを一連のアダプティブ ビットレート MP4 ファイルまたはアダプティブ ビットレート スムーズ ストリーミング ファイルにエンコードします (エンコードの手順は、このチュートリアルの後半で説明しています)。
- コンテンツに配信するストリーミング エンドポイントの 1 つ以上のオンデマンド ストリーミング ユニットを取得します。詳細については、「[オンデマンド ストリーミング予約ユニットのスケールの変更方法](media-services-manage-origins.md#scale_streaming_endpoints/)」をご覧ください。

動的パッケージ化機能を使用した場合、保存と課金の対象となるのは、単一のストレージ形式のファイルのみです。Media Services がクライアントからの要求に応じて適切な応答を構築して返します。

動的パッケージ化機能を使用できることに加え、オンデマンド ストリーミング予約ユニットを使用すると、専用の送信容量を 200 Mbps 単位で購入できます。既定では、オンデマンド ストリーミングは、サーバー リソース (コンピューティング、送信容量など) を他のユーザーと共有する共有インスタンス モデルとして構成されます。オンデマンド ストリーミングのスループットを高めるために、オンデマンド ストリーミング予約ユニットの購入をお勧めします。

### エンコード

ここでは、Azure ポータルから Azure Media Encoder でコンテンツをエンコードするための手順について説明します。

1.  エンコードの対象となるファイルを選択します。そのファイル タイプのエンコードがサポートされている場合、[コンテンツ] ページの一番下にある **[プロセス]** ボタンが有効になります。
4. **[プロセス]** ダイアログ ボックスで **[Azure Media Encoder]** プロセッサを選択します。
5. いずれかの**エンコーディング構成**を選択します。

	![Process2][process2]

	**[アダプティブ ストリーミング用のプリセット (動的パッケージング)]**、**[プログレッシブ ダウンロード用のプリセット]**、**[アダプティブ ストリーミング用のレガシー プリセット]** の各プリセットの意味については、「[Azure Media Encoder 用のタスク プリセット文字列](https://msdn.microsoft.com/library/azure/dn619392.aspx)」をご覧ください。

	以下、**[その他]** の構成について説明します。

	+ **PlayReady コンテンツ保護を使用したエンコード**。このプリセットは、PlayReady Content Protection を使用してエンコードされた資産を生成します。  

		既定では、Media Services PlayReady ライセンス サービスが使用されます。クライアントが PlayReady 暗号化コンテンツを再生するためにライセンスを取得可能な他のサービスを指定するには、REST または Media Services .NET SDK API を使用します。詳細については、「[静的暗号化を使用したコンテンツの保護]()」を参照し、Media Encryptor プリセットの **licenseAcquisitionUrl** プロパティを設定してください。代わりに、「**Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)**」に説明されているように、動的暗号化を使用して [PlayReadyLicenseAcquisitionUrl](http://go.microsoft.com/fwlink/?LinkId=507720) プロパティを設定することもできます。
	+ **[PC/Mac での再生 (Flash/Silverlight を使用)]**。このプリセットは、次のような特徴を持つスムーズ ストリーミング資産を作成します。AAC を使用して 96 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 3400 ～ 400 kbps の範囲で 6 ビットレートで CBR エンコードされた 720p ビデオ、および 2 秒の GOP。
	+ **[HTML5 (IE/Chrome/Safari) を使用した再生]**。このプリセットは、次のような特徴を持つ単一の MP4 ファイルを作成します。AAC を使用して 128 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 4500 kbps で CBR エンコードされた 720p ビデオ。
	+ **[iOS デバイスおよび PC/Mac での再生]**。このプリセットは、スムーズ ストリーミング資産 (上述) と同じ特徴を持つ資産を作成しますが、Apple HLS ストリームを iOS デバイスに配信するときに使用できる形式で作成します。

5. 次に、わかりやすい出力コンテンツ名を入力するか、既定値をそのまま使用します。チェック ボタンをクリックすると、エンコード処理が開始され、進捗状況はポータルの下部で確認できます。
6. **[OK]** を選択します。

	エンコードが完了すると、[コンテンツ] ページにエンコード済みのファイルが表示されます。

	エンコード ジョブの進行状況を確認するには、**[ジョブ]** ページに切り替えます。

	エンコードが終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** を選択します。これにより出力資産のファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。


## コンテンツを発行する

### 概要

ストリーミングかダウンロードに使用できる URL を提供するには、まず、ロケーターを作成して資産を "発行" する必要があります。資産に含まれているファイルには、ロケーターを通じてアクセスできます。Media Services では、2 種類のロケーターがサポートされています。OnDemandOrigin ロケーターはメディアのストリーミング (MPEG DASH、HLS、スムーズ ストリーミングなど) に、Access Signature (SAS) ロケーターはメディア ファイルのダウンロードに使用します。

資産の発行に Azure ポータルを使用するとロケーターが作成され、OnDemand ベースの URL (資産に .ism ファイルが含まれている場合) または SAS URL が提供されます。

SAS URL には次の形式があります。

	{blob container name}/{asset name}/{file name}/{SAS signature}

ストリーミング URL には次の形式があり、スムーズ ストリーミング資産の再生に使用できます。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS ストリーミング URL を作成するには、(format=m3u8-aapl) を URL に追加します。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH ストリーミング URL を作成するには、(format=mpd-time-csf) を URL に追加します。

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


ロケーターには、有効期限があります。ポータルを使用して資産を発行すると、有効期限が 100 年のロケーターが作成されます。

>[AZURE.NOTE]2015 年 3 月より前にポータルを使用してロケーターを作成した場合、有効期限が 2 年のロケーターが作成されています。

ロケーターの有効期限を更新するには、[REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) API または [.NET](http://go.microsoft.com/fwlink/?LinkID=533259) API を使用します。SAS ロケーターの有効期限を更新すると、URL が変更されることにご注意ください。

### Publish

ポータルを使用して資産を発行するには、次の操作を行います。

1. 資産を選択します。
2. 次に発行ボタンをクリックします。

 ![PublishedContent][publishedcontent]


## ポータルでコンテンツを再生する

ビデオは、Azure ポータルにあるコンテンツ プレーヤーを使用してテストできます。

目的のビデオをクリックし、ポータルの下部にある **[再生]** をクリックします。

いくつかの考慮事項が適用されます。

- ビデオが発行されたことを確認します。
- **Media Services コンテンツ プレーヤー**を既定のストリーミング エンドポイントから再生します。既定以外のストリーミング エンドポイントから再生する場合は、別のプレーヤーを使用します([Azure Media Services プレーヤーなど](http://amsplayer.azurewebsites.net/azuremediaplayer.html))。


![AMSPlayer][AMSPlayer]



##次のステップ

###Media Services のラーニング パス

AMS のラーニング パスについては、以下を参照してください。

- [AMS のライブ ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/)
- [AMS のオンデマンド ストリーミング ワークフロー](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/)

### 他の情報をお探しですか。

このトピックに必要な情報が含まれていないか、不足しているか、あるいはニーズを満たしていない場合は、以下の Disqus スレッドを使用してフィードバックをお送りください。

### その他のリソース
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - 今すぐビデオをオンラインにしましょう!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - 動的パッケージ化機能とモバイル デバイス</a>


<!-- Anchors. -->


<!-- URLs. -->
[Azure portal]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png

<!---HONumber=Oct15_HO2-->