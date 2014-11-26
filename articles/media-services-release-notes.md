<properties pageTitle="Media Services リリース ノート" metaKeywords="Azure Media Services" description="Media Services リリース ノート" metaCanonical="" services="media-services" documentationCenter="Media" title="Media Services リリース ノート" authors="juliako" solutions="media" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako" />

# Azure Media Services リリース ノート

このリリース ノートには、以前のリリースからの変更と既知の問題が要約されています。

> [AZURE.NOTE] 製品に関するご意見、ご要望をお寄せください。お客様に影響する問題の修正に尽力しています。問題の報告または質問を行うには、[Azure Media Services MSDN フォーラム][Azure Media Services MSDN フォーラム]に投稿してください。

-   [現在の既知の問題][現在の既知の問題]
-   [REST API バージョン履歴][REST API バージョン履歴]
-   [2014 年 10 月のリリース][2014 年 10 月のリリース]
-   [2014 年 9 月のリリース][2014 年 9 月のリリース]
-   [2014 年 8 月のリリース][2014 年 8 月のリリース]
-   [2014 年 7 月のリリース][2014 年 7 月のリリース]
-   [2014 年 5 月のリリース][2014 年 5 月のリリース]
-   [2014 年 4 月のリリース][2014 年 4 月のリリース]
-   [2014 年 1 月と 2 月のリリース][2014 年 1 月と 2 月のリリース]
-   [2013 年 12 月のリリース][2013 年 12 月のリリース]
-   [2013 年 11 月のリリース][2013 年 11 月のリリース]
-   [2013 年 8 月のリリース][2013 年 8 月のリリース]
-   [2013 年 6 月のリリース][2013 年 6 月のリリース]
-   [2012 年 12 月のリリース][2012 年 12 月のリリース]
-   [2012 年 11 月のリリース][2012 年 11 月のリリース]
-   [2012 年 6 月のプレビュー リリース][2012 年 6 月のプレビュー リリース]

## <span id="issues"></span></a>現在の既知の問題

### <span id="general_issues"></span></a>Media Services の全般的な問題

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">問題</th>
<th align="left">説明</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">REST API で一般的な HTTP ヘッダーがいくつか提供されていない。</td>
<td align="left">REST API を使用して Media Services アプリケーションを開発している場合、いくつかの一般的な HTTP フィールド (CLIENT-REQUEST-ID、REQUEST-ID、および RETURN-CLIENT-REQUEST-ID を含む) がサポートされていないことに気付きます。ヘッダーは、今後の更新プログラムで追加される予定です。</td>
</tr>
<tr class="even">
<td align="left">エスケープ文字を含むファイル名 (たとえば、%20) のアセットをエンコードすると、&quot;MediaProcessor :File not found.&quot; が返され失敗する。</td>
<td align="left">アセットに付加し、その後エンコードする予定のファイル名には、英数字とスペースのみを使用する必要があります。問題は、今後の更新プログラムで修正される予定です。</td>
</tr>
<tr class="odd">
<td align="left">Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドが失敗する。</td>
<td align="left">Media Services は、<a href="http://msdn.microsoft.com/ja-jp/library/azure/dn592123.aspx">2012-02-12</a> バージョンに基づいて SAS URL を生成します。Azure Storage SDK を使用して、BLOB コンテナー内の BLOB を一覧する場合は、Azure Storage SDK Version 2.x に含まれる <a href="http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> メソッドを使用してください。Azure Storage SDK Version 3.x に含まれる ListBlobs メソッドは失敗します。</td>
</tr>
<tr class="even">
<td align="left">Media Services 調整メカニズムが、サービスに対して過剰な要求を作成するアプリケーションのリソース使用を制限する。サービスが「サービスを利用できません」(503) HTTP 状態コードを返すことがある。</td>
<td align="left">詳細については、「<a href="http://msdn.microsoft.com/ja-jp/library/azure/dn168949.aspx">Azure Media Services エラー コード</a>」の 503 HTTP 状態コードの説明を参照してください。</td>
</tr>
</tbody>
</table>

### <span id="dotnet_issues"></span></a>Azure Media Services SDK for .NET の問題

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">問題</th>
<th align="left">説明</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">SDK 内の Media Services オブジェクトをシリアル化できず、その結果、Azure Cache と連携動作しない。</td>
<td align="left">SDK AssetCollection オブジェクトをシリアル化して、Azure Cache に追加しようとすると、例外がスローされます。</td>
</tr>
</tbody>
</table>

## <span id="rest_version_history"></span></a>REST API バージョン履歴

Media Services REST API バージョン履歴の詳細については、「[Azure モバイル サービス REST API リファレンス][Azure モバイル サービス REST API リファレンス]」を参照してください。

## <span id="october_changes_14"></span></a>2014 年 10 月のリリース

### <span id="new_encoder_release"></span></a>Media Services エンコーダーのリリース

Media Services Azure メディア エンコーダーの新規リリースが発表されています。最新の Azure メディア エンコーダーでは、出力 GB 分しか課金されません。それ以外では、新規エンコーダーの機能は Windows Azure メディア エンコーダーと互換性があります。詳細については、「[Media Services の料金詳細][Media Services の料金詳細]」を参照してください。

### <span id="oct_sdk"></span></a>Media Services .NET SDK

Media Services SDK for .NET は、現在、バージョン 3.0.0.8 です。

Media Services SDK for .NET Extensions は、現在、バージョン 2.0.0.3 です。

## <span id="september_changes_14"></span></a>2014 年 9 月のリリース

Media Services REST メタデータは、現在、バージョン 2.7 です。最新の REST 更新プログラムの詳細については、「[Azure モバイル サービス REST API リファレンス][Azure モバイル サービス REST API リファレンス]」を参照してください。

Media Services SDK for .NET は、現在、バージョン 3.0.0.7 です。

### <span id="sept_14_breaking_changes"></span></a>重大な変更

-   **オリジン**の名前が[ストリーミング エンドポイント][ストリーミング エンドポイント]に変更されました。
-   **Azure 管理ポータル**で MP4 ファイルをエンコードし、その後、発行する際の既定の動作が変更されました。

    以前は、管理ポータルを使用して単一ファイルの MP4 ビデオ アセットを発行すると、SAS URL が作成されました (SAS URL を使用してビデオを BLOB ストレージからダウンロードできます)。現在は、管理ポータルを使用して、単一ファイルの MP4 ビデオ アセットをエンコードし、その後、発行すると、生成された URL は Azure Media Services ストリーミング エンドポイントを指します。この変更は、Media Services に直接アップロードされ、Azure Media Services によってエンコードされずに発行された MP4 ビデオには影響しません。

    現在、問題を解決するための次の 2 つのオプションがあります。

    -   ストリーミング ユニットを有効にし、動的パッケージを使用して、.mp4 アセットを Smooth Streaming プレゼンテーションとしてストリーミングします。

    -   .mp4 をダウンロード (または、漸次的に再生) するための SAS url を作成します。SAS ロケーターの作成方法の詳細については、「[コンテンツの配信][コンテンツの配信]」を参照してください。

### <span id="sept_14_GA_changes"></span></a>一般提供リリースに含まれる新機能およびシナリオ

-   **インデクサー メディア プロセッサ**。詳細については、「[Azure Media Indexer によるメディア ファイルのインデックス作成][Azure Media Indexer によるメディア ファイルのインデックス作成]」を参照してください。

-   現在、[StreamingEndpoint][ストリーミング エンドポイント] エンティティを使用して、カスタム ドメイン (ホスト) 名を追加できます。

    Media Services ストリーミング エンドポイント名として使用されるカスタム ドメイン名の場合、カスタム ホスト名をストリーミング エンドポイントに追加する必要があります。Media Services REST API または .NET SDK を使用して、カスタム ホスト名を追加します。

    次の考慮事項が適用されます。

    -   カスタム ドメイン名の所有権を保持している必要があります。

    -   ドメイン名の所有権は、Azure Media Services によって検証する必要があります。

    詳細については、「[StreamingEndpoint][ストリーミング エンドポイント]」の **CustomHostNames** プロパティを参照してください。

### <span id="sept_14_preview_changes"></span></a>パブリック プレビュー リリースに含まれる新機能およびシナリオ

-   ライブ ストリーミングのプレビュー。詳細については、「[Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)][Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]」を参照してください。

-   キー配信サービス。詳細については、「[AES-128 動的暗号化とキー配信サービスの使用][AES-128 動的暗号化とキー配信サービスの使用]」を参照してください。

-   AES 動的暗号化。詳細については、「[AES-128 動的暗号化とキー配信サービスの使用][AES-128 動的暗号化とキー配信サービスの使用]」を参照してください。

-   PlayReady ライセンス配信サービス。詳細については、「[Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)][Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]」を参照してください。

-   PlayReady 動的暗号化。詳細については、「[Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)][Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]」を参照してください。

-   Media Services PlayReady ライセンス テンプレート。詳細については、「[Media Services PlayReady ライセンス テンプレートの概要][Media Services PlayReady ライセンス テンプレートの概要]」を参照してください。

-   ストリーミング ストレージ暗号化アセット。詳細については、「[Streaming Storage Encrypted Content (ストリーミング ストレージ暗号化コンテンツ)][Streaming Storage Encrypted Content (ストリーミング ストレージ暗号化コンテンツ)]」を参照してください。

## <span id="august_changes_14"></span></a>2014 年 8 月のリリース

アセットをエンコードすると、エンコード ジョブの完了時に出力アセットが生成されます。このリリースになるまで、Azure Media Services エンコーダーは出力アセットに関するメタデータを生成していました。このリリースから、エンコーダーは入力アセットに関するメタデータも生成します。詳細については、「[Input Metadata (入力メタデータ)][Input Metadata (入力メタデータ)]」および「[Output Metadata (出力メタデータ)][Output Metadata (出力メタデータ)]」を参照してください。

## <span id="july_changes_14"></span></a>2014 年 7 月のリリース

Azure Media Services パッケージおよび暗号化機能で次のバグが修正されました。

-   ライブ アーカイブ アセットを HTTP ライブ ストリーミングに変換するとオーディオしか再生されない – これは修正され、現在はオーディオとビデオの両方が再生されます。

-   アセットを HTTP ライブ ストリーミングおよび AES 128 ビット エンベローブ暗号化にパッケージ化すると、パッケージ化されたストリームが Android デバイス上で再生されない – このバグは修正され、パッケージ化されたストリームは HTTP ライブ ストリーミングをサポートする Android デバイス上で再生されます。

## <span id="may_changes_14"></span></a>2014 年 5 月のリリース

### <span id="may_14_changes"></span></a>Media Services の全般的な更新

現在、[動的パッケージ][動的パッケージ]を使用して、HTTP ライブ ストリーミング (HLS) v3 をストリーミングできます。HLS v3 をストリーミングするには、オリジン ロケーター パスに\*.ism/manifest(format=m3u8-aapl-v3) 形式を追加します。詳細については、「[Nick Drouin's Blog (Nick Drouin のブログ)][Nick Drouin's Blog (Nick Drouin のブログ)]」を参照してください。

現在、動的パッケージは、PlayReady によるスムーズ ストリーミング静的暗号化に基づく、PlayReady による HLS (v3 および v4) 暗号化の配信もサポートしています。PlayReady によるスムーズ ストリーミングの暗号化方法の詳細については、「[PlayReady によるスムーズ ストリーミングおよび MPEG DASH の保護][PlayReady によるスムーズ ストリーミングおよび MPEG DASH の保護]」を参照してください。

### <a name="may_14_donnet_changes"></a>Media Services .NET SDK の更新

Media Services .NET SDK 3.0.0.5 リリースでは、次の改善が加えられました。

-   メディア アセットのアップロードおよびダウンロードの迅速化と復元性の向上。

-   再試行ロジックと一時的な例外の処理の改善:

    -   ファイルのクエリ、保存、変更、アップロード、またはダウンロードによって引き起こされる例外に関する一時的なエラーの検出と再試行ロジックが改善されました。

    -   Web の例外が返される際に (たとえば、ACS トークンの要求時)、致命的なエラーが以前より早く失敗を通知することに気付きます。

詳細については、「[RMedia Services SDK for .NET の再試行ロジック][RMedia Services SDK for .NET の再試行ロジック]」を参照してください。

## <span id="april_changes_14"></span></a>2014 年 4 月のエンコーダー リリース

### <a name="april_14_enocer_changes"></a>Media Services エンコーダーの更新

-   Grass Valley HQ/HQX コーデックを使用してビデオをすばやく圧縮する Grass Valley EDIUS 非線形エディターを使用して作成された AVI ファイルの取り込みがサポートされるようになりました。詳細については、「[Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)][Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]」を参照してください。

-   メディア エンコーダーによって生成されたファイルの名前付け規則を指定できるようになりました。詳細については、「[Media Services Encoder の出力ファイル名の制御][Media Services Encoder の出力ファイル名の制御]」を参照してください。

-   ビデオ オーバーレイおよびオーディオ オーバーレイのサポートが追加されました。詳細については、「[オーバーレイの作成][オーバーレイの作成]」を参照してください。

-   複数のビデオ セグメントを結合できるようになりました。詳細については、「[ビデオのセグメントの結合][ビデオのセグメントの結合]」を参照してください。

-   オーディオが MPEG-1 Audio layer 3 (MP3) でエンコードされるという、MP4 のコード変換に関するバグが修正されました。

## <span id="jan_feb_changes_14"></span></a>2014 年 1 月と 2 月のリリース

### <a name="jan_fab_14_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.1、3.0.0.2、および 3.0.0.3

3.0.0.1 および 3.0.0.2 における変更:

-   OrderBy ステートメントでの LINQ クエリの使用に関する問題が修正されました。

-   [Github][Github] のテスト ソリューションをユニットベースのテストとシナリオベースのテストに分割しました。

変更の詳細については、「[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases (Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 リリース)][Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases (Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 リリース)]」を参照してください。

3.0.0.3 では次の点が変更されました。

-   バージョン 3.0.3.0 を使用するように Azure Storage の依存関係がアップグレードされました。

-   3.0.*.* リリースの下位互換性の問題が修正されました。

## <span id="december_changes_13"></span></a>2013 年 12 月のリリース

### <a name="dec_13_donnet_changes"></a>Azure Media Services .NET SDK 3.0.0.0

> [AZURE.NOTE] 3.0.x.x リリースには、2.4.x.x リリースとの下位互換性がありません。

現在、Media Services SDK の最新バージョンは 3.0.0.0 です。Nuget から最新パッケージをダウンロードするか、[Github][Github] からビットを取得できます。

Media Services SDK Version 3.0.0.0 から、[Azure Active Directory Access Control Service (ACS)][Azure Active Directory Access Control Service (ACS)] トークンを再利用できます。詳細については、「[Media Services SDK による Media Services への接続][Media Services SDK による Media Services への接続]」の「アクセス制御サービス トークンの再利用」を参照してください。

### <a name="dec_13_donnet_ext_changes"></a>Azure Media Services .NET SDK Extensions 2.0.0.0

Azure Media Services .NET SDK Extensions は、コードを簡素化し、Azure Media Services による開発を容易にする一連の拡張メソッドおよびヘルパー機能です。[Azure Media Services .NET SDK Extensions][Azure Media Services .NET SDK Extensions] から最新のビットを取得できます。

## <span id="november_changes_13"></span></a>2013 年 11 月のリリース

### <a name="nov_13_donnet_changes"></a>Azure Media Services .NET SDK の変更点

このバージョンから、Media Services SDK for .NET は、Media Services REST API レイヤーの呼び出し時発生することのある一時的な障害エラーを処理します。

## <span id="august_changes_13"></span></a>2013 年 8 月のリリース

### <a name="aug_13_powershell_changes"></a>Azure Sdk ツールに含まれる Media Services PowerShell コマンドレット

現在、次の Media Services PowerShell コマンドレットが [azure-sdk-tools][azure-sdk-tools] に含まれています。

-   Get-AzureMediaServices

    たとえば、「`Get-AzureMediaServicesAccount`」のように入力します。

-   New-AzureMediaServicesAccount

    たとえば、「`New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`」のように入力します。

-   New-AzureMediaServicesKey

    たとえば、「`New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`」のように入力します。

-   Remove-AzureMediaServicesAccount

    たとえば、「`Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`」のように入力します。

## <span id="june_changes_13"></span></a>2013 年 6 月のリリース

### <a name="june_13_general_changes"></a>Azure Media Services の変更点

ここで説明する変更点は、2013 年 6 月の Media Services リリースに含まれている更新内容です。

-   複数のストレージ アカウントを Media Service アカウントにリンクする機能。

    StorageAccount

    Asset.StorageAccountName および Asset.StorageAccount

-   Job.Priority を更新する機能。

-   エンティティおよびプロパティ関連の通知:

    JobNotificationSubscription

    NotificationEndPoint

    Job

-   Asset.Uri

-   Locator.Name

### <a name="june_13_dotnet_changes"></a>Azure Media Services .NET SDK の変更点

2013 年 6 月の Media Services SDK リリースには、次の変更点が加えられました。最新の Media Services SDK は、GitHub から入手可能です。

-   Media Services SDK Version 2.3.0.0 から、複数のストレージ アカウントの Media Services アカウントへのリンクがサポートされています。次の API がこの機能をサポートしています。

    IStorageAccount 型。

    Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts プロパティ

    StorageAccount プロパティ

    StorageAccountName プロパティ

    詳細については、「[複数のストレージ アカウントでの Media Services アセットの管理][複数のストレージ アカウントでの Media Services アセットの管理]」を参照してください。

-   API 関連の通知。バージョン 2.2.0.0 から、Azure キュー ストレージ通知をリッスンできます。詳細については、「[Media Services ジョブ通知の処理][Media Services ジョブ通知の処理]」を参照してください。

    Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions プロパティ

    Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint 型

    Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription 型

    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection 型

    Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType 型

    Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState 型

-   Azure Storage クライアント SDK 2.0 への依存関係 (Microsoft.WindowsAzure.StorageClient.dll)

-   OData 5.5 への依存関係 (Microsoft.Data.OData.dll)

## <span id="december_changes_12"></span></a>2012 年 12 月のリリース

### <a name="dec_12_dotnet_changes"></a>Azure Media Services .NET SDK の変更点

-   Intellisense:多くの型で不足していた Intellisense ドキュメントが追加されました。

-   Microsoft.Practices.TransientFaultHandling.Core:SDK が、引き続きこのアセンブリの旧バージョンへ依存していた問題が修正されました。現在、SDK はこのアセンブリのバージョン 5.1.1209.1 を参照しています。

2012 年 11 月の SDK で見つかった問題が修正されました。

-   IAsset.Locators.Count:このカウントは、現在、すべてのロケーターが削除された後の新しい IAsset インターフェイスで正しく報告されます。

-   IAssetFile.ContentFileSize:この値は、現在、IAssetFile.Upload(filepath) よるアップロード後に適切に設定されます。

-   IAssetFile.ContentFileSize:このプロパティは、現在、アセット ファイルの作成時に設定できます。以前は、読み取り専用でした。

-   IAssetFile.Upload(filepath):この同期アップロード メソッドが、複数のファイルをアセットにアップロードしたときに次のエラーをスローする問題は修正されました。エラーは、「Server failed to authenticate the request.Make sure the value of Authorization header is formed correctly including the signature.(サーバーが要求を認証できませんでした。認証ヘッダーの値が、署名を含め正しく形成されていることを確認してください。)」

-   IAssetFile.UploadAsync:5 個のファイルを同時にアップロードできないという問題は修正されました。

-   IAssetFile.UploadProgressChanged:このイベントは、現在、SDK によって提供されています。

-   IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken):このメソッド オーバーロードは、現在、提供されています。

-   IAssetFile.DownloadAsync:5 個のファイルを同時にダウンロードできないという問題は修正されました。

-   IAssetFile.Delete():IAssetFile のファイルがアップロードされていない場合に、delete 呼び出しで例外がスローされる問題は修正されました。

-   Jobs:ジョブ テンプレートを使用して「MP4 とスムーズ ストリーム間変換タスク」と「PlayReady 保護タスク」を連結してもまったくタスクが作成されないという問題は修正されました。

-   EncryptionUtils.GetCertificateFromStore():証明書検出の障害は証明書構成の問題に基づいていたため、このメソッドは、現在、ヌル参照例外をスローしません。

## <span id="november_changes_12"></span></a>2012 年 11 月のリリース

こおで説明する変更点は、2012 年 11 月 (バージョン 2.0.0.0) SDK に含まれている更新内容です。これらの変更では、2012 年 6 月のプレビュー SDK リリース向けに記述されたコードの変更または書き換えが必要です。

-   アセット

    IAsset.Create(assetName) は、唯一のアセット作成関数です。IAsset.Create は、現在、メソッド呼び出しの一部としてファイルをアップロードしません。アップロードには IAssetFile を使用してください。

    IAsset.Publish メソッドと AssetState.Publish 列挙値は、サービス SDK から削除されました。尾の値に依存しているコードはすべて書き換える必要があります。

-   FileInfo

    このクラスは削除され、IAssetFile によって置き換えられました。

    IAssetFiles

    IAssetFile は FileInfo にとって代わり、異なる動作を実行します。使用するには、IAssetFiles オブジェクトをインスタント化し、Media Services SDK または Azure Storage SDK を使用してファイルをアップロードします。次の IAssetFile.Upload オーバーロードを使用できます。

    -   IAssetFile.Upload(filePath):スレッドをブロックする同期メソッドは、単一のファイルをアップロードする場合のみ推奨されます。

    -   IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken):非同期メソッド。これは、推奨されるアップロード メカニズムです。

    既知のバグ:cancellationToken を使用すると、本多王にアップロードがキャンセルされます。ただし、タスクのキャンセル状態は複数の状態のいずれかになります。例外を適切にキャッチし、処理する必要があります。

-   ロケーター

    オリジン固有のバージョンは削除されました。SAS 固有の context.Locators.CreateSasLocator(asset, accessPolicy) は、廃止としてマークされるか、一般提供バージョンで削除されます。更新された動作については、「新機能」の「ロケーター」の項を参照してください。

## <span id="june_changes_12"></span></a>2012 年 6 月のプレビュー リリース

以下は、SDK の 11 月のリリースでの新機能です。

-   エンティティの削除

    IAsset、IAssetFile、ILocator、IAccessPolicy、IContentKey オブジェクトは、Collection、つまり cloudMediaContext.ObjCollection.Delete(objInstance) で削除する必要がありましたが、現在は代わりに、オブジェクト レベルの IObject.Delete() で削除されます。

-   ロケーター

    現在、ロケーターは CreateLocator メソッドを使用して作成し、LocatorType.SAS または LocatorType.OnDemandOrigin 列挙値を、作成する特定の種類のロケーターの引数として使用する必要があります。

    コンテンツ用に使用できる URI の取得をより容易にするためのプロパティがロケーターに追加されました。ロケーターのこの再設計により、将来のサードパーティの拡張性を確保するための柔軟性が向上し、メディア クライアント アプリケーションの使いやすさが改善されました。

-   非同期メソッドのサポート

    すべてのメソッドに非同期のサポートが追加されました。

  

  [Azure Media Services MSDN フォーラム]: http://social.msdn.microsoft.com/forums/azure/ja-jp/home?forum=MediaServices
  [現在の既知の問題]: #issues
  [REST API バージョン履歴]: #rest_version_history
  [2014 年 10 月のリリース]: #october_changes_14
  [2014 年 9 月のリリース]: #september_changes_14
  [2014 年 8 月のリリース]: #august_changes_14
  [2014 年 7 月のリリース]: #july_changes_14
  [2014 年 5 月のリリース]: #may_changes_14
  [2014 年 4 月のリリース]: #april_changes_14
  [2014 年 1 月と 2 月のリリース]: #jan_feb_changes_14
  [2013 年 12 月のリリース]: #december_changes_13
  [2013 年 11 月のリリース]: #november_changes_13
  [2013 年 8 月のリリース]: #august_changes_13
  [2013 年 6 月のリリース]: #june_changes_13
  [2012 年 12 月のリリース]: #december_changes_12
  [2012 年 11 月のリリース]: #november_changes_12
  [2012 年 6 月のプレビュー リリース]: #june_changes_12
  [Azure モバイル サービス REST API リファレンス]: http://msdn.microsoft.com/ja-jp/library/azure/hh973617.aspx
  [Media Services の料金詳細]: http://azure.microsoft.com/ja-jp/pricing/details/media-services/
  [ストリーミング エンドポイント]: http://msdn.microsoft.com/ja-jp/library/azure/dn783468.aspx
  [コンテンツの配信]: http://msdn.microsoft.com/ja-jp/library/azure/hh973618.aspx
  [Azure Media Indexer によるメディア ファイルのインデックス作成]: http://msdn.microsoft.com/ja-jp/library/azure/dn783455.aspx
  [Working with Azure Media Services Live Streaming (Azure Media Services ライブ ストリーミングでの動作)]: http://msdn.microsoft.com/ja-jp/library/azure/dn783466.aspx
  [AES-128 動的暗号化とキー配信サービスの使用]: http://msdn.microsoft.com/ja-jp/library/azure/dn783457.aspx
  [Using PlayReady Dynamic Encryption and License Delivery Service (PlayReady 動的暗号化とライセンス配信サービスの使用)]: http://msdn.microsoft.com/ja-jp/library/azure/dn783467.aspx
  [Media Services PlayReady ライセンス テンプレートの概要]: http://msdn.microsoft.com/ja-jp/library/azure/dn783459.aspx
  [Streaming Storage Encrypted Content (ストリーミング ストレージ暗号化コンテンツ)]: http://msdn.microsoft.com/ja-jp/library/azure/dn783451.aspx
  [Input Metadata (入力メタデータ)]: http://msdn.microsoft.com/ja-jp/library/azure/dn783120.aspx
  [Output Metadata (出力メタデータ)]: http://msdn.microsoft.com/ja-jp/library/azure/dn783217.aspx
  [動的パッケージ]: http://msdn.microsoft.com/ja-jp/library/azure/jj889436.aspx
  [Nick Drouin's Blog (Nick Drouin のブログ)]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
  [PlayReady によるスムーズ ストリーミングおよび MPEG DASH の保護]: http://msdn.microsoft.com/ja-jp/library/azure/dn189154.aspx
  [RMedia Services SDK for .NET の再試行ロジック]: http://msdn.microsoft.com/ja-jp/library/azure/dn745650.aspx
  [Grass Valley Announces EDIUS 7 Streaming Through the Cloud (Grass Valley 社、クラウドを介した EDIUS 7 ストリーミングを発表)]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
  [Media Services Encoder の出力ファイル名の制御]: http://msdn.microsoft.com/ja-jp/library/azure/dn303341.aspx
  [オーバーレイの作成]: http://msdn.microsoft.com/ja-jp/library/azure/dn640496.aspx
  [ビデオのセグメントの結合]: http://msdn.microsoft.com/ja-jp/library/azure/dn640504.aspx
  [Github]: https://github.com/Azure/azure-sdk-for-media-services
  [Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases (Azure Media Services .NET SDK 3.0.0.1 および 3.0.0.2 リリース)]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
  [Azure Active Directory Access Control Service (ACS)]: http://msdn.microsoft.com/ja-jp/library/hh147631.aspx
  [Media Services SDK による Media Services への接続]: http://msdn.microsoft.com/ja-jp/library/azure/jj129571.aspx
  [Azure Media Services .NET SDK Extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
  [azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
  [複数のストレージ アカウントでの Media Services アセットの管理]: http://msdn.microsoft.com/ja-jp/library/azure/dn271889.aspx
  [Media Services ジョブ通知の処理]: http://msdn.microsoft.com/ja-jp/library/azure/dn261241.aspx
