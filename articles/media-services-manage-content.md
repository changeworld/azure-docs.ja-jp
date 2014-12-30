<properties urlDisplayName="How to manage media content" pageTitle="メディア コンテンツの管理方法 - Azure Media Services" metaKeywords="" description="Learn how to manage your media content in Azure Media Services." metaCanonical="" services="media-services" documentationCenter="" title="How to Manage Content in Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





# メディア サービスのコンテンツを管理する方法 #
Azure メディア サービスのコンテンツ ビューを利用して、メディア サービス アカウントのメディア コンテンツを管理できます。

現在は、次のコンテンツ操作をポータルから直接実行できます。

- コンテンツ情報の表示 (公開状態、公開 URL、サイズ、日付、最終更新日時など) を表示することもできます。
- 新しいコンテンツのアップロード
- コンテンツのエンコード
- コンテンツ ビデオの再生
- コンテンツの発行および発行の取り消し
- コンテンツの削除


## 方法:コンテンツをアップロードする ##


1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[メディア サービス]** をクリックし、目的のメディア サービス アカウント名をクリックします。
2. [コンテンツ] ページを選択します。 
3. ページまたはポータルの下部にある **[アップロード]** をクリックします。 
4. **[コンテンツのアップロード]** ダイアログで、目的のアセット ファイルを見つけます。ファイルをクリックして、**[開く]** をクリックするか、**Enter** キーを押します。

	![UploadContentDialog][uploadcontent]

5. [コンテンツのアップロード] ダイアログで、チェック ボタンをクリックして、ファイル名とコンテンツ名をそのまま使用します。
6. アップロードが開始され、進捗状況はポータルの下部で確認できます。  

	![JobStatus][status]

アップロードが完了すると、コンテンツ一覧に新しいアセットが表示されます。通常、名前の末尾に "**-Source**" が付加され、エンコード タスクのソース コンテンツとして新しいコンテンツを見つけやすくなっています。

アップロード処理が終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これによりアセットのファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。	


## 方法:コンテンツのエンコード

1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[メディア サービス]** をクリックし、目的のメディア サービス アカウント名をクリックします。 
2. ページの上部にある [コンテンツ] ページをクリックします。
3. エンコード ジョブで使用する目的のソース ビデオをクリックし、ページの下部にある **[エンコード]** をクリックします。
4. [Azure メディア エンコーダー] ダイアログで、主なプリセットまたは詳細なプリセットからエンコードを 1 つ選択します。

	**主なプリセット**

	+ **PC/Mac での再生 (Flash/Silverlight を使用)**。このプリセットは、次のような特徴を持つスムーズ ストリーミング アセットを作成します。AAC を使用して 96 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 3,400 ～ 400 kbps の範囲で 6 ビットレートで CBR エンコードされた 720p ビデオ、および 2 秒の GOP。
	+ **HTML5 (IE/Chrome/Safari) を使用した再生**。このプリセットは、次のような特徴を持つ単一の MP4 ファイルを作成します。AAC を使用して 128 kbps で CBR エンコードされた 44.1 kHz 16 ビット/サンプルのステレオ オーディオ、および H.264 Main Profile を使用して 4,500 kbps で CBR エンコードされた 720p ビデオ。
	+ **iOS デバイスおよび PC/Mac での再生**。このプリセットは、スムーズ ストリーミング アセット (上述) と同じ特徴を持つアセットを作成しますが、Apple HLS ストリームを iOS デバイスに配信するときに使用できる形式で作成します。 
	+ **PlayReady コンテンツ保護を使用したエンコード**。このプリセットは、PlayReady コンテンツ保護を使用してエンコードされたアセットを生成します。このプリセットを稼働するには、PlayReady ライセンス配信サービスを有効にする必要があります。有効にするには、**[コンテンツ保護]** タブで、ブランディング レポート テーブルに行を追加します。Media Services PlayReady ライセンス サービスは、[保存] をクリックして数分後に有効になります。   
	
	
		既定では、Media Services PlayReady ライセンス サービスが使用されます。クライアントが PlayReady 暗号化コンテンツを再生するためにライセンスを取得可能な他のサービスを指定するには、REST または Media Services .NET SDK API を使用します。詳細については、「[Using Static Encryption to Protect your Content (静的暗号化を使用したコンテンツの保護)]」を参照し、Media Encryptor プリセットの **licenseAcquisitionUrl** プロパティを設定してください。代わりに、「**PlayReady 動的暗号化とライセンス提供サービスの使用**」に説明されているように、動的暗号化を使用して [PlayReadyLicenseAcquisitionUrl](http://go.microsoft.com/fwlink/?LinkId=507720 ) プロパティを設定することもできます。 
		
		このオプションは、PlayReady コンテンツ保護プレビュー機能にサインアップしている場合にのみ表示される点に注意してください。プレビュー機能にサインアップするには、「[プレビュー機能](http://azure.microsoft.com/ja-jp/services/preview/)」で説明されている手順を実行する必要があります。  
	
		
	**詳細なプリセット**
	
	+ 「[Azure Media Encoder 用のタスク プリセット文字列](http://go.microsoft.com/fwlink/?LinkId=270865) 」トピックでは、[詳細なプリセット] リスト内の各プリセットの意味について説明されています。 


	![EncoderDialog][encoder]

	現在、ポータルではメディア エンコーダーがサポートするエンコード形式がすべてサポートされているわけではありません。また、メディア アセットの暗号化と復号化もサポートされていません。これらのタスクをプログラムで実行できます。詳細については、「[Building Applications with the Media Services SDK for .NET (Media Services SDK for .NET によるアプリケーション構築)](http://go.microsoft.com/fwlink/?LinkId=270866) 」および「[Azure Media Encoder 用のタスク プリセット文字列](http://go.microsoft.com/fwlink/?LinkId=270865)」を参照してください。


5. [Azure メディア エンコーダー] ダイアログで、わかりやすい出力コンテンツ名を入力するか、既定値をそのまま使用します。チェック ボタンをクリックすると、エンコード処理が開始され、進捗状況はポータルの下部で確認できます。

	エンコードが終了すると、ビューは下図のようになります。 

	![PortalViewUploadCompleted][portaloverview]


	エンコードが終了してもファイル サイズの値が更新されない場合は、**[メタデータの同期]** をクリックします。これにより出力アセットのファイル サイズがストレージの実際のファイル サイズと同期され、[コンテンツ] ページに表示される値が更新されます。	

## 方法:コンテンツを発行する

1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[メディア サービス]** をクリックし、目的のメディア サービス アカウント名をクリックします。
2. ページの上部にある [コンテンツ] ページをクリックします。
3. 発行されていないアセットをクリックします。[発行] をクリックしてパブリック URL に発行します。コンテンツが URL に発行されると、エンコードされたコンテンツを描画できるクライアント プレーヤーでその URL を開くことができるようになります。

 ![PublishedContent][publishedcontent]

## 方法:ポータルでコンテンツを再生する


1. [管理ポータル](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409)で、**[メディア サービス]** をクリックし、目的のメディア サービス アカウント名をクリックします。
2. ページの上部にある [コンテンツ] ページをクリックします。
3. 目的のビデオ コンテンツをクリックし、ポータルの下部にある **[再生]** をクリックします。ポータルから再生できるのは、発行されたコンテンツだけです。また、エンコード方法がブラウザーでサポートされている必要があります。


<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png

<!--HONumber=35_1-->
