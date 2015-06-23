<properties 
	pageTitle="Media Services リリース ノート" 
	description="Media Services リリース ノート" 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="juliako"/>


# Azure Media Services リリース ノート

このリリース ノートには、以前のリリースからの変更と既知の問題が要約されています。

>[AZURE.NOTE] 製品に関するご意見、ご要望をお寄せください。お客様に影響する問題の修正に尽力しています。問題の報告または質問を行うには、[Azure Media Services MSDN フォーラム]に投稿してください。

- [現在の既知の問題](#issues)
- [REST API バージョン履歴](#rest_version_history)
- [2015 年 3 月のリリース](#march_changes_15)
- [2015 年 2 月のリリース](#february_changes_15)
- [2015 年 1 月のリリース](#january_changes_15)
- [2014 年 12 月のリリース](#december_changes_14)
- [2014 年 11 月のリリース](#november_changes_14)
- [2014 年 10 月のリリース](#october_changes_14)
- [2014 年 9 月のリリース](#september_changes_14)
- [2014 年 8 月のリリース](#august_changes_14)
- [2014 年 7 月のリリース](#july_changes_14)
- [2014 年 5 月のリリース](#may_changes_14)
- [2014 年 4 月のリリース](#april_changes_14) 
- [2014 年 1 月と 2 月のリリース](#jan_feb_changes_14) 
- [2013 年 12 月のリリース](#december_changes_13)
- [2013 年 11 月のリリース](#november_changes_13)
- [2013 年 8 月のリリース](#august_changes_13)
- [2013 年 6 月のリリース](#june_changes_13)
- [2012 年 12 月のリリース](#december_changes_12)
- [2012 年 11 月のリリース](#november_changes_12)
- [2012 年 6 月のプレビュー リリース](#june_changes_12)


## <a id="issues"></a>現在の既知の問題

### <a id="general_issues"></a>Media Services の全般的な問題

<table border="1">
<tr><th>問題</th><th>説明</yt></tr>
<tr><td>REST API で一般的な HTTP ヘッダーがいくつか提供されていない。</td><td>REST API を使用して  Media Services  アプリケーションを開発している場合、いくつかの一般的な HTTP フィールド (CLIENT-REQUEST-ID、REQUEST-ID、RETURN-CLIENT-REQUEST-ID を含む) がサポートされていないことに気付きます。ヘッダーは、今後の更新プログラムで追加される予定です。</td></tr>
<tr><td>エスケープ文字を含むファイル名 (たとえば、%20) のアセットをエンコードすると、"MediaProcessor :File not found." が返され失敗する。</td><td>アセットに付加し、その後エンコードする予定のファイル名には、英数字とスペースのみを使用する必要があります。問題は、今後の更新プログラムで修正される予定です。</td></tr>
<tr><td>Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドが失敗する。</td><td>Media Services は、<a href="http://msdn.microsoft.com/library/azure/dn592123.aspx">2012-02-12</a> バージョンに基づいて SAS URL を生成します。Azure Storage SDK を使用して、BLOB コンテナー内の BLOB を一覧する場合は、Azure Storage SDK Version 2.x に含まれる <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> メソッドを使用してください。Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドは失敗します。</td></tr>
<tr><td>Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。サービスが「サービスを利用できません」(503) HTTP 状態コードを返すことがある。</td><td>詳細については、「<a href="http://msdn.microsoft.com/library/azure/dn168949.aspx">Azure Media Services エラー コード</a>」の 503 HTTP 状態コードの説明をご覧ください。</td></tr>
</table><br/>

### <a id="dotnet_issues"></a>Azure Media Services SDK for .NET の問題

<table border="1">
<tr><th>問題</th><th>説明</yt></tr>
<tr><td>SDK 内の  Media Services オブジェクトをシリアル化できず、その結果、Azure Cache と連携動作しない。</td><td>SDK AssetCollection オブジェクトをシリアル化して、Azure Cache に追加しようとすると、例外がスローされます。</td></tr>
</table><br/>

## <a id="rest_version_history"></a>REST API バージョン履歴

Media Services REST API バージョン履歴の詳細については、「[Azure Media Services REST API リファレンス]」のトピックをご覧ください。

## <a id="march_changes_15"></a>2015 年 3 月のリリース

### Media Services の全般的な更新

- Media Services で、シームレスな Azure CDN 統合が提供されるようになりました。統合をサポートするために、**CdnEnabled** プロパティが **StreamingEndpoint** に追加されました。**CdnEnabled** はバージョン 2.9 以降の REST API で使用できます (詳細については「[StreamingEndpoint](https://msdn.microsoft.com/ja-jp/library/azure/dn783468.aspx)」をご覧ください)。**CdnEnabled** はバージョン 3.1.0.2 以降の .NET SDK で使用できます (詳細については「[StreamingEndpoint](https://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mediaservices.client.istreamingendpoint(v=azure.10).aspx」をご覧ください)」。
- **メディア エンコーダー プレミアム ワークフロー**についてのお知らせ。詳細については、「[Azure Media Services へのプレミアム エンコードの導入 (ブログの投稿)](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)」をご覧ください。
 


## <a id="february_changes_15"></a>2015 年 2 月のリリース

### Media Services の全般的な更新

Media Services REST API は、現在、バージョン 2.9 です。このバージョンより、Azure CDN をストリーミング エンドポイントと統合できます。詳細については、「[StreamingEndpoint](https://msdn.microsoft.com/library/dn783468.aspx)」をご覧ください。

## <a id="january_changes_15"></a>2015 年 1 月のリリース

### Media Services の全般的な更新

動的な暗号化によるコンテンツを保護する General Availability (GA) についてのお知らせ。詳細については、[Azure Media Services の一般的な可用性の DRM 技術とストリーミングのセキュリティを強化する](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/) をご覧ください。

### Media Services .NET SDK の更新

Azure Media Services .NET SDK が 3.1.0.1 にバージョン アップしました。

このリリースでは、既定の Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate コンストラクターが旧式としてマークされています。新しいコンス トラクターは、TokenType を引数として受け取ります。

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


## <a id="december_changes_14"></a>2014 年 12 月のリリース

### Media Services の全般的な更新

- Azure Indexer メディア プロセッサに、いくつかの更新と新機能が追加されています。詳細については、「[Azure Media Indexer Version 1.1.6.7 のリリース ノート](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/)」をご覧ください。
- エンコーディング占有ユニットを更新する新しい REST API が追加されました。[REST を 使用した EncodingReservedUnitType](http://msdn.microsoft.com/library/azure/dn859236.aspx)。
- キー配信サービスの CORS サポートが追加されました。
- 承認ポリシー オプションのクエリ パフォーマンスが向上しています。
- (他のデータ センターと同様) 中国のデータ センターにおいても、[キー配信 URL](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) が顧客単位となりました。
- HLS 自動ターゲット期間が追加されました。ライブ ストリーミングの実行中、HLS は常に動的にパッケージ化されます。既定では、Live エンコーダーから送信されるキーフレーム間隔 (KeyFrameInterval) に基づき Media Services  が自動的に HLS セグメント パッケージ化率 (FragmentsPerSegment) を計算します。これは GOP (Group of Pictures) と呼ばれることもあります。詳細については、[Azure Media Services ライブ ストリーミングの操作]をご覧ください。
 
### Media Services .NET SDK の更新

- [Azure Media Services .NET SDK](http://www.nuget.org/packages/windowsazure.mediaservices/) が 3.1.0.0 にバージョン アップしました。
- .Net SDK の依存関係が .NET 4.5 Framework にアップグレードされました。
- エンコード占有ユニットを更新できる新しい API が追加されました。詳細については、[.NET を使用して占有ユニットの種類を更新したりエンコーディング占有ユニットを拡張したりする方法](http://msdn.microsoft.com/library/azure/jj129582.aspx)に関するページをご覧ください。
- トークン認証に JWT (JSON Web Token) が新たにサポートされています。詳細については、[Azure Media Services と動的暗号化における JWT トークン認証](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)に関するページをご覧ください。
- PlayReady ライセンス テンプレートに BeginDate と ExpirationDate の相対オフセットが追加されました。


## <a id="november_changes_14"></a>2014 年 11 月のリリース

- ライブ スムーズ ストリーミング (FMP4) コンテンツの SSL 接続での取り込みに Media Services  が対応しました。SSL 経由で取り込むには、取り込み URL を HTTPS に更新する必要があります。ライブ ストリーミングの詳細については、[Azure Media Services ライブ ストリーミングの操作]をご覧ください。
- RTMP ライブ ストリームについては現在、SSL 接続で取り込むことはできません。
- SSL 接続経由でコンテンツのストリーミングもできます。そのためには、ストリーミング URL の先頭が HTTPS になっていることをご確認ください。
- SSL 経由でのストリーミングを実行できるのは、コンテンツの配信元となるストリーミング エンドポイントが 2014 年 9 月 10 日より後に作成されている場合のみです。ストリーミング URL の基になるストリーミング エンドポイントの作成日が 9 月 10 日より後である場合、URL に "streaming.mediaservices.windows.net" (新形式) が含まれています。"origin.mediaservices.windows.net" (旧形式) を含んだストリーミング URL では、SSL がサポートされません。URL が旧形式である場合、SSL ストリーミングに対応するためには、[新しいストリーミング エンドポイントを作成](media-services-manage-origins.md)してください。SSL でコンテンツをストリーミングするには、新しいストリーミング エンドポイントに基づいて作成された URL を使用する必要があります。
   
## <a id="october_changes_14"></a>2014 年 10 月のリリース

### <a id="new_encoder_release"></a>Media Services エンコーダーのリリース

Media Services Azure メディア エンコーダーの新規リリースが発表されています。最新の Azure メディア エンコーダーでは、出力 GB 分しか課金されません。それ以外では、新規エンコーダーの機能は Windows Azure メディア エンコーダーと互換性があります。詳細については、[Media Services の料金詳細]に関するページをご覧ください。

### <a id="oct_sdk"></a>Media Services .NET SDK 

Media Services SDK for .NET Extensions は、現在、バージョン 2.0.0.3 です。

Media Services SDK for .NET は、現在、バージョン 3.0.0.8 です。

変更点を次に示します。

- 再試行ポリシーのクラスをリファクタリング。
- ユーザー エージェント文字列を http 要求ヘッダーに追加。
- Nuget のビルド復元ステップを追加。
- リポジトリからの x509 証明書を使用するようにシナリオ テストを修正。
- チャンネルとストリーミング エンドを更新するときの設定を検証。
 

### Media Services のサンプルをホストする新しい GitHub リポジトリ

サンプルは、[Azure Media Services のサンプル GitHub リポジトリ](https://github.com/Azure/Azure-Media-Services-Samples)に置かれています。


## <a id="september_changes_14"></a>2014 年 9 月のリリース

Media Services REST メタデータは、現在、バージョン 2.7 です。最新の REST 更新プログラムの詳細については、「[Azure Media Services REST API リファレンス]」のトピックをご覧ください。

Media Services SDK for .NET は、現在、バージョン 3.0.0.7 です。
 
### <a id="sept_14_breaking_changes"></a>重大な変更

* **オリジン**の名前が [StreamingEndpoint] に変更されました。
* **Azure 管理ポータル**で MP4 ファイルをエンコードし、その後、発行する際の既定の動作が変更されました。 

	以前は、管理ポータルを使用して単一ファイルの MP4 ビデオ アセットを発行すると、SAS URL が作成されました (SAS URL を使用してビデオを BLOB ストレージからダウンロードできます)。現在は、管理ポータルを使用して、単一ファイルの MP4 ビデオアセットをエンコードし、その後、発行すると、生成された URL は Azure Media Services ストリーミング エンドポイントを指します。この変更は、Media Services に直接アップロードされ、Azure Media Services によってエンコードされずに発行された MP4 ビデオには影響しません。
	
	現在、問題を解決するための次の 2 つのオプションがあります。 
	
	* ストリーミング ユニットを有効にし、動的パッケージを使用して、.mp4 アセットを Smooth Streaming プレゼンテーションとしてストリーミングします。
	
	* .mp4 をダウンロード (または、漸次的に再生) するための SAS url を作成します。SAS ロケーターの作成方法の詳細については、[コンテンツの配信]をご覧ください。 


### <a id="sept_14_GA_changes"></a>一般提供リリースに含まれる新機能とシナリオ

* **インデクサー メディア プロセッサ**。詳細については、[Azure Media Indexer によるメディア ファイルのインデックス作成]をご覧ください。

* 現在、[StreamingEndpoint] エンティティを使用して、カスタム ドメイン (ホスト) 名を追加できます。

	Media Services ストリーミング エンドポイント名として使用されるカスタム ドメイン名の場合、カスタム ホスト名をストリーミング エンドポイントに追加する必要があります。Media Services REST API または .NET SDK を使用して、カスタム ホスト名を追加します。
	
	次の考慮事項が適用されます。
	
	* カスタム ドメイン名の所有権を保持している必要があります。
	
	* ドメイン名の所有権は、Azure Media Services によって検証する必要があります。ドメインを検証するには、<MediaServicesAccountId>.<parent domain> を verifydns.<mediaservices-dns-zone> にマップする CName を作成します。 
	
	* カスタム ホスト名 (たとえば、sports.contoso.com) を  Media Services StreamingEndpont のホスト名 (たとえば、amstest.streaming.mediaservices.windows.net) にマップする別の CName を作成する必要があります。


	詳細については、[StreamingEndpoint] のトピックの **CustomHostNames** プロパティをご覧ください。

### <a id="sept_14_preview_changes"></a>パブリック プレビュー リリースに含まれる新機能とシナリオ

* ライブ ストリーミングのプレビュー。詳細については、[Azure Media Services ライブ ストリーミングの操作]をご覧ください。

* キー配信サービス。詳細については、[AES-128 動的暗号化とキー配信サービスの使用]をご覧ください。

* AES 動的暗号化。詳細については、[AES-128 動的暗号化とキー配信サービスの使用]をご覧ください。

* PlayReady ライセンス配信サービス。詳細については、[PlayReady 動的暗号化とライセンス提供サービスの使用]をご覧ください。

* PlayReady 動的暗号化。詳細については、[PlayReady 動的暗号化とライセンス提供サービスの使用]をご覧ください。

* Media Services PlayReady ライセンス テンプレート。詳細については、[Media Services PlayReady ライセンス テンプレートの概要]をご覧ください。

* ストリーミング ストレージ暗号化アセット。詳細については、[ストレージ暗号化コンテンツの配信]をご覧ください。

## <a id="august_changes_14"></a>2014 年 8 月のリリース

アセットをエンコードすると、エンコード ジョブの完了時に出力アセットが生成されます。このリリースになるまで、Azure Media Services エンコーダーは出力アセットに関するメタデータを生成していました。このリリースから、エンコーダーは入力アセットに関するメタデータも生成します。詳細については、[入力メタデータ]と[出力メタデータ]をご覧ください。


## <a id="july_changes_14"></a>2014 年 7 月のリリース

Azure Media Services パッケージと暗号化機能で次のバグが修正されました。

* ライブ アーカイブ アセットを HTTP ライブ ストリーミングに変換するとオーディオしか再生されない - これは修正され、現在はオーディオとビデオの両方が再生されます。

* アセットを HTTP ライブ ストリーミングと AES 128 ビット エンベローブ暗号化にパッケージ化すると、パッケージ化されたストリームが Android デバイス上で再生されない - このバグは修正され、パッケージ化されたストリームは HTTP ライブ ストリーミングをサポートする Android デバイス上で再生されます。

## <a id="may_changes_14"></a>2014 年 5 月のリリース

### <a id="may_14_changes"></a>Media Services の全般的な更新

現在、[動的パッケージ]を使用して、HTTP ライブ ストリーミング (HLS) v3 をストリーミングできます。HLS v3 をストリーミングするには、オリジン ロケーター パスに次の形式を追加します。*.ism/manifest(format=m3u8-aapl-v3). 詳細については、「[Nick Drouin's Blog (Nick Drouin のブログ)]」をご覧ください。

現在、動的パッケージは、PlayReady によるスムーズ ストリーミング静的暗号化に基づく、PlayReady による HLS (v3 と v4) 暗号化の配信もサポートしています。PlayReady によるスムーズ ストリーミングの暗号化方法の詳細については、[PlayReady で静的暗号化を使用して Smooth Streaming と MPEG DASH を保護する]をご覧ください。

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK の更新

Media Services .NET SDK 3.0.0.5 リリースでは、次の改善が加えられました。

* メディア アセットのアップロードとダウンロードの迅速化と復元性の向上。

* 再試行ロジックと一時的な例外の処理の改善: 

	* ファイルのクエリ、保存、変更、アップロード、またはダウンロードによって引き起こされる例外に関する一時的なエラーの検出と再試行ロジックが改善されました。 
	
	* Web の例外が返される際に (たとえば、ACS トークンの要求時)、致命的なエラーが以前より早く失敗を通知することに気付きます。

詳細については、[Media Services SDK for .NET の再試行ロジック]をご覧ください。

## <a id="april_changes_14"></a>2014 年 4 月のエンコーダー リリース

### <a name="april_14_enocer_changes"></a>Media Services エンコーダーの更新

* Grass Valley HQ/HQX コーデックを使用してビデオをすばやく圧縮する Grass Valley EDIUS 非線形エディターを使用して作成された AVI ファイルの取り込みがサポートされるようになりました。詳細については、「[Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]」をご覧ください。

* メディア エンコーダーによって生成されたファイルの名前付け規則を指定できるようになりました。詳細については、[Media Services エンコーダーの出力ファイル名の制御]をご覧ください。

* ビデオ オーバーレイとオーディオ オーバーレイのサポートが追加されました。詳細については、[オーバーレイの作成]をご覧ください。

* 複数のビデオ セグメントを結合できるようになりました。詳細については、[ビデオのセグメントの結合]をご覧ください。

* オーディオが MPEG-1 Audio layer 3 (MP3) でエンコードされるという、MP4 のコード変換に関するバグが修正されました。


## <a id="jan_feb_changes_14"></a>2014 年 1 月と 2 月のリリース

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1、3.0.0.2、3.0.0.3

3.0.0.1、3.0.0.2 における変更:

* OrderBy ステートメントでの LINQ クエリの使用に関する問題が修正されました。

* [Github] のテスト ソリューションをユニットベースのテストとシナリオベースのテストに分割しました。

変更の詳細については、[Azure Media Services .NET SDK 3.0.0.1、3.0.0.2 リリース]に関するページをご覧ください。

3.0.0.3 では次の点が変更されました。

* バージョン 3.0.3.0 を使用するように Azure Storage の依存関係がアップグレードされました。 

* 3.0.*.* リリースの下位互換性の問題が修正されました。 


## <a id="december_changes_13"></a>2013 年 12 月のリリース

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

>[AZURE.NOTE] 3.0.x.x リリースには、2.4.x.x リリースとの下位互換性がありません。

現在、Media Services SDK の最新バージョンは 3.0.0.0 です。Nuget から最新パッケージをダウンロードするか、[Github] からビットを取得できます。

Media Services SDK Version 3.0.0.0 から、[Azure Active Directory Access Control Service (ACS)] トークンを再利用できます。詳細については、[Media Services SDK による  Media Services への接続]の「アクセス制御サービス トークンの再利用」をご覧ください。

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0

Azure Media Services .NET SDK Extensions は、コードを簡素化し、Azure Media Services による開発を容易にする一連の拡張メソッドとヘルパー機能です。[Azure Media Services .NET SDK Extensions] から最新のビットを取得できます。

## <a id="november_changes_13"></a>2013 年 11 月のリリース

### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK の変更点

このバージョンから、Media Services SDK for .NET は、Media Services REST API レイヤーの呼び出し時発生することのある一時的な障害エラーを処理します。

## <a id="august_changes_13"></a>2013 年 8 月のリリース

### <a name="aug_13_powershell_changes"></a>Azure Sdk ツールに含まれる Media Services PowerShell コマンドレット

現在、次の Media Services PowerShell コマンドレットが [azure-sdk-tools] に含まれています。

* Get-AzureMediaServices 

	例:  `Get-AzureMediaServicesAccount`

* New-AzureMediaServicesAccount 

	例: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`.

* New-AzureMediaServicesKey 

	例:  `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount 

	例:  `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`.

## <a id="june_changes_13"></a>2013 年 6 月のリリース

### <a name="june_13_general_changes"></a>Azure Media Services の変更点

ここで説明する変更点は、2013 年 6 月の Media Services  リリースに含まれている更新内容です。

* 複数のストレージ アカウントを Media Services アカウントにリンクする機能。 

	StorageAccount
	
	Asset.StorageAccountName と Asset.StorageAccount

* Job.Priority を更新する機能。 

* エンティティとプロパティ関連の通知: 

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Job

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK の変更点

2013 年 6 月の Media Services SDK リリースには、次の変更点が加えられました。最新の Media Services SDK は、GitHub から入手可能です。

* Media Services SDK Version 2.3.0.0 から、複数のストレージ アカウントの  Media Services アカウントへのリンクがサポートされています。次の API がこの機能をサポートしています。
	
	IStorageAccount 型。
	
	Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts プロパティ。
	
	StorageAccount プロパティ。
	
	StorageAccountName プロパティ。
	
	詳細については、[複数のストレージ アカウントでの  Media Services アセットの管理]をご覧ください。

* API 関連の通知。バージョン 2.2.0.0 から、Azure キュー ストレージ通知をリッスンできます。詳細については、[Media Services ジョブ通知の処理]をご覧ください。
	
	Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions プロパティ
	
	Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 型
	
	Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 型
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 型
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 型
	
	Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 型

* Azure Storage クライアント SDK 2.0 への依存関係 (Microsoft.WindowsAzure.StorageClient.dll)。

* OData 5.5 への依存関係 (Microsoft.Data.OData.dll)。


## <a id="december_changes_12"></a>2012 年 12 月のリリース

### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK の変更点

* Intellisense:多くの型で不足していた Intellisense ドキュメントが追加されました。

* Microsoft.Practices.TransientFaultHandling.Core:SDK が、引き続きこのアセンブリの旧バージョンへ依存していた問題が修正されました。現在、SDK はこのアセンブリのバージョン 5.1.1209.1 を参照しています。

2012 年 11 月の SDK で見つかった問題が修正されました。

* IAsset.Locators.Count:このカウントは、現在、すべてのロケーターが削除された後の新しい IAsset インターフェイスで正しく報告されます。

* IAssetFile.ContentFileSize:この値は、現在、IAssetFile.Upload(filepath) よるアップロード後に適切に設定されます。

* IAssetFile.ContentFileSize:このプロパティは、現在、アセット ファイルの作成時に設定できます。以前は、読み取り専用でした。

* IAssetFile.Upload(filepath):この同期アップロード メソッドが、複数のファイルをアセットにアップロードしたときに次のエラーをスローする問題は修正されました。エラーは、「Server failed to authenticate the request.Make sure the value of Authorization header is formed correctly including the signature.(サーバーが要求を認証できませんでした。認証ヘッダーの値が、署名を含め正しく形成されていることをご確認ください。)」

* IAssetFile.UploadAsync:5 個のファイルを同時にアップロードできないという問題は修正されました。

* IAssetFile.UploadProgressChanged:このイベントは、現在、SDK によって提供されています。

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken):このメソッド オーバーロードは、現在、提供されています。

* IAssetFile.DownloadAsync:5 個のファイルを同時にダウンロードできないという問題は修正されました。

* IAssetFile.Delete():IAssetFile のファイルがアップロードされていない場合に、delete 呼び出しで例外がスローされる問題は修正されました。

* Jobs:ジョブ テンプレートを使用して「MP4 とスムーズ ストリーム間変換タスク」や「PlayReady 保護タスク」を連結してもまったくタスクが作成されないという問題は修正されました。

* EncryptionUtils.GetCertificateFromStore():証明書検出の障害は証明書構成の問題に基づいていたため、このメソッドは、現在、ヌル参照例外をスローしません。

## <a id="november_changes_12"></a>2012 年 11 月のリリース

こおで説明する変更点は、2012 年 11 月 (バージョン 2.0.0.0) SDK に含まれている更新内容です。これらの変更では、2012 年 6 月のプレビュー SDK リリース向けに記述されたコードの変更または書き換えが必要です。

* アセット
	
	IAsset.Create(assetName) は、唯一のアセット作成関数です。IAsset.Create は、現在、メソッド呼び出しの一部としてファイルをアップロードしません。アップロードには IAssetFile を使用してください。
	
	IAsset.Publish メソッドと AssetState.Publish 列挙値は、サービス SDK から削除されました。尾の値に依存しているコードはすべて書き換える必要があります。

* FileInfo

	このクラスは削除され、IAssetFile によって置き換えられました。

	IAssetFiles

	IAssetFile は FileInfo にとって代わり、異なる動作を実行します。使用するには、IAssetFiles オブジェクトをインスタント化し、Media Services SDK または Azure Storage SDK を使用してファイルをアップロードします。次の IAssetFile.Upload オーバーロードを使用できます。

	* IAssetFile.Upload(filePath):スレッドをブロックする同期メソッドは、単一のファイルをアップロードする場合のみ推奨されます。
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken):非同期メソッド。これは、推奨されるアップロード メカニズムです。 

	既知のバグ: cancellationToken を使用すると、本多王にアップロードがキャンセルされます。ただし、タスクのキャンセル状態は複数の状態のいずれかになります。例外を適切にキャッチし、処理する必要があります。

* ロケーター
	
	オリジン固有のバージョンは削除されました。SAS 固有の context.Locators.CreateSasLocator(asset, accessPolicy) は非推奨化されます。つまり、GA までに廃止されます。更新後の動作については、新機能について説明するページの Locators のセクションをご覧ください。


## <a id="june_changes_12"></a>2012 年 6 月のプレビュー リリース

以下は、SDK の 11 月のリリースでの新機能です。

* エンティティの削除

	IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey オブジェクトは、Collection、つまり cloudMediaContext.ObjCollection.Delete(objInstance) で削除する必要がありましたが、現在は代わりに、オブジェクト レベルの IObject.Delete() で削除されます。

* ロケーター

	現在、ロケーターは CreateLocator メソッドを使用して作成し、LocatorType.SAS または LocatorType.OnDemandOrigin 列挙値を、作成する特定の種類のロケーターの引数として使用する必要があります。

	コンテンツ用に使用できる URI の取得をより容易にするためのプロパティがロケーターに追加されました。ロケーターのこの再設計により、将来のサードパーティの拡張性を確保するための柔軟性が向上し、メディア クライアント アプリケーションの使いやすさが改善されました。

* 非同期メソッドのサポート

	すべてのメソッドに非同期のサポートが追加されました。

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Azure Media Services MSDN フォーラム]: http://social.msdn.microsoft.com/forums/azure/home?forum=MediaServices
[Azure Media Services REST API リファレンス]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Media Services の料金詳細]: http://azure.microsoft.com/pricing/details/media-services/
[入力メタデータ]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[出力メタデータ]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[コンテンツの配信]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Azure Media Indexer によるメディア ファイルのインデックス作成]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Azure Media Services ライブ ストリーミングの操作]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[AES-128 動的暗号化とキー配信サービスの使用]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[PlayReady 動的暗号化とライセンス提供サービスの使用]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[プレビュー機能]: http://azure.microsoft.com/services/preview/
[Media Services PlayReady ライセンス テンプレートの概要]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[ストレージ暗号化コンテンツの配信]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Azure 管理ポータル]: https://manage.windowsazure.com
[動的パッケージ]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Nick Drouin's Blog (Nick Drouin のブログ)]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[PlayReady で静的暗号化を使用して Smooth Streaming と MPEG DASH を保護する]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Media Services SDK for .NET の再試行ロジック]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Media Services Encoder の出力ファイル名の制御]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[オーバーレイの作成]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[ビデオのセグメントの結合]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 リリース]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/library/hh147631.aspx
[Media Services SDK による Media Services への接続]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[複数のストレージ アカウントでの Media Services アセットの管理]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Media Services ジョブ通知の処理]: http://msdn.microsoft.com/library/azure/dn261241.aspx

[Media Services の料金詳細]: http://azure.microsoft.com/pricing/details/media-services/

[Media Services エンコーダーの出力ファイル名の制御]: http://msdn.microsoft.com/library/azure/dn303341.aspx


[Azure Media Services .NET SDK 3.0.0.1、3.0.0.2 リリース]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/


[Media Services SDK による  Media Services への接続]: http://msdn.microsoft.com/library/azure/jj129571.aspx


[複数のストレージ アカウントでの  Media Services アセットの管理]: http://msdn.microsoft.com/library/azure/dn271889.aspx

<!--HONumber=52--> 