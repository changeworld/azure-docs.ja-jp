# [概要](media-services-overview.md)
## [概念](media-services-concepts.md)


# 作業開始
## [アカウントの作成と管理](media-services-portal-create-account.md)
## [開発環境のセットアップ](media-services-set-up-computer.md)
## ビデオ オン デマンドの配信
### [ポータル](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST ()](media-services-rest-get-started.md)
## ライブ ストリーミングの実行
### [ポータル](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 方法
## [管理]
### [ポータルでストリーミング エンドポイントを管理する](media-services-portal-manage-streaming-endpoints.md)
### エンティティを管理する
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST ()](media-services-rest-manage-entities.md)
### [PowerShell を使用したアカウントの管理](media-services-manage-with-powershell.md)
### [Media Encoder Standard を使用してビデオをトリミングする](media-services-crop-video.md)
### [方法: ストレージ アクセス キーをローリングした後に Media Services を更新する](media-services-roll-storage-access-keys.md)
### [クォータと制限](media-services-quotas-and-limitations.md)
### フィルター
#### [Azure Media Services .NET SDK を使用したフィルターの作成](media-services-dotnet-dynamic-manifest.md)
#### [Media Encoder Standard を使用して資産をエンコードする方法](media-services-rest-encode-asset.md)
### プログラムによる接続
#### [.NET](media-services-dotnet-connect-programmatically.md)
#### [REST ()](media-services-rest-connect-programmatically.md)

## コンテンツをアップロードする
### ファイルをアカウントにアップロードする
#### [ポータル](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST ()](media-services-rest-upload-files.md)
### [既存の BLOB のコピー](media-services-copying-existing-blob.md)

## エンコード
### [コンテンツ](media-services-encode-asset.md)
#### Media Encoder Standard を使用して資産をエンコードする
##### [ポータル](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
#### [.NET で Media Encoder Standard を使用してサムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
#### [高度なエンコード](media-services-advanced-encoding-with-mes.md)
##### [メディア エンコーダー Premium ワークフロー](media-services-encode-with-premium-workflow.md)
##### [メディア エンコーダー Premium ワークフローのチュートリアル](media-services-media-encoder-premium-workflow-tutorials.md)
##### [ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する](media-services-workflow-designer.md)
##### [複数入力の場合の Premium ワークフロー](media-services-media-encoder-premium-workflow-multiplefilesinput.md)

#### スキーマ 
#####[Media Encoder Standard](media-services-mes-schema.md)
#####[入力メタデータ](media-services-input-metadata-schema.md)
#####[出力メタデータ](media-services-output-metadata-schema.md)

#### 従来のエンコーダー
##### [Azure Media Packager の使用](media-services-static-packaging.md)

### [ライブ ストリーム](media-services-manage-channels-overview.md)
#### [オンプレミス エンコーダー](media-services-live-streaming-with-onprem-encoders.md)
#### オンプレミス エンコーダーのチュートリアル
##### [ポータル](media-services-portal-live-passthrough-get-started.md)
##### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
#### [クラウド エンコーダーを使用したライブ ストリーミング](media-services-manage-live-encoder-enabled-channels.md)
#### クラウド エンコーダーのチュートリアル
##### [ポータル](media-services-portal-creating-live-encoder-enabled-channel.md)
##### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
#### [オンプレミス エンコーダーをクラウド エンコーダーと使用するための構成](media-services-live-encoders-overview.md)
#### [長時間にわたって実行される操作の処理](media-services-dotnet-long-operations.md)
#### [フラグメント化 MP4 ライブ取り込みの仕様](media-services-fmp4-live-ingest-overview.md)
#### [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)

### メディア処理
#### [.NET](media-services-get-media-processor.md)
#### [REST ()](media-services-rest-get-media-processor.md)

### 単一ビットレートのライブ ストリームのエンコーダーを構成する
#### [Elemental Live エンコーダー](media-services-configure-elemental-live-encoder.md)
#### [FMLE エンコーダー](media-services-configure-fmle-live-encoder.md)
#### [NewTek TriCaster エンコーダー](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast エンコーダー](media-services-configure-wirecast-live-encoder.md)

## [保護](media-services-content-protection-overview.md)
### [ポータルでコンテンツ保護を構成する](media-services-portal-protect-content.md)
### [ストリームの AES-128 クリア キーを構成する](media-services-protect-with-aes128.md)
### [AMS REST API を使用したストレージ暗号化によるコンテンツの暗号化](media-services-rest-storage-encryption.md)
### [Media Services PlayReady ライセンス テンプレートの概要](media-services-playready-license-template-overview.md)
### [DRM ライセンス配信](media-services-deliver-keys-and-licenses.md)
### [パートナーを使用して Azure Media Services に Widevine ライセンスを配信する](media-services-licenses-partner-integration.md)
### [PlayReady または Widevine の動的共通暗号化を使用する](media-services-protect-with-drm.md)
### [Azure Media Services を使用して Apple FairPlay で保護された HLS コンテンツをストリーミングする](media-services-protect-hls-with-fairplay.md)
### [CENC とマルチ DRM および Access Control: Azure および Azure Media Services での参照設計と実装](media-services-cenc-with-multidrm-access-control.md)

### 資産の配信
#### 資産配信ポリシーを構成する
##### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
##### [REST ()](media-services-rest-configure-asset-delivery-policy.md)
### ContentKeys を作成する
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST ()](media-services-rest-create-contentkey.md)
### コンテンツ キー承認ポリシーを構成する
#### [ポータル](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST ()](media-services-rest-configure-content-key-auth-policy.md)

## [分析](media-services-analytics-overview.md)
### [Indexer 2 による処理](media-services-process-content-with-indexer2.md)
### [Indexer による処理](media-services-index-content.md)
### [Hyperlapse による処理](media-services-hyperlapse-content.md)
### [Face Detector による処理](media-services-face-and-emotion-detection.md)
### [Motion Detector による処理](media-services-motion-detection.md)
### [Face Redactor による処理](media-services-face-redaction.md)
### [Video Thumbnails による処理](media-services-video-summarization.md)
### [OCR による処理](media-services-video-optical-character-recognition.md)

## スケール
### [メディア処理](media-services-scale-media-processing-overview.md)
#### [ポータル](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
#### [REST ()](https://msdn.microsoft.com/library/azure/dn859236.aspx)
### ストリーミング エンドポイント
#### [ポータル](media-services-portal-scale-streaming-endpoints.md)

## [コンテンツの配信](media-services-deliver-content-overview.md)
### [フィルターと動的マニフェストの概要](media-services-dynamic-manifest-overview.md)
### フィルターの作成
#### [.NET](media-services-dotnet-dynamic-manifest.md)
#### [REST ()](media-services-rest-dynamic-manifest.md)
### コンテンツを発行する
#### [ポータル](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST ()](media-services-rest-deliver-streaming-content.md)
### [ダウンロードで配信する](media-services-deliver-asset-download.md)
### [フェールオーバー ストリーミング シナリオ](media-services-implement-failover.md)

## 使用
### [既存のプレーヤーを使用したメディアの再生](media-services-playback-content-with-existing-players.md)
### [Media Player を使用したメディアの再生](media-services-develop-video-players.md)
### その他の再生オプション
#### [Smooth Streaming Windows ストア アプリケーション](media-services-build-smooth-streaming-apps.md)
#### [DASH.js を使用した HTML5 アプリケーション](media-services-embed-mpeg-dash-in-html5.md)
#### [Adobe Open Source Media Framework プレーヤー](media-services-use-osmf-smooth-streaming-client-plugin.md)
### [クライアント側での広告の挿入](media-services-inserting-ads-on-client-side.md)

## 統合
### [Media Services Extension での CDN キャッシュ ポリシー](../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### [Licensing Microsoft†" Smooth Streaming Client Porting Kit](media-services-sspk.md)
### [複数のストレージ アカウントで資産を管理する](meda-services-managing-multiple-storage-accounts.md)
### [Axinom を使用して Azure Media Services に Widevine ライセンスを配信する](media-services-axinom-integration.md)
### [castLabs を使用して Azure Media Services に Widevine ライセンスを配信する](media-services-castlabs-integration.md)
### [Widevine ライセンス テンプレートの概要](media-services-widevine-license-template-overview.md)

## 監視
### ジョブの進行状況をチェックする
#### [REST ()](media-services-rest-check-job-progress.md)
#### [ポータル](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [ジョブの通知を監視する Queue Storage](media-services-dotnet-check-job-progress-with-queues.md)

## トラブルシューティング
### [よく寄せられる質問](media-services-frequently-asked-questions.md)
### [ライブ ストリーミングのトラブルシューティング ガイド](media-services-troubleshooting-live-streaming.md)
###[エラー コード](media-services-error-codes.md)
###[再試行ロジック](media-services-retry-logic-in-dotnet-sdk.md)

# リファレンス
## [リリース ノート](media-services-release-notes.md)
## [.NET](media-services-dotnet-how-to-use.md)
## [REST ()](media-services-rest-how-to-use.md)
## [メディア エンコーダー プレミアム ワークフローの形式とコーデック](media-services-premium-workflow-encoder-formats.md)
## [Media Encoder Standard の形式とコーデック](media-services-media-encoder-standard-formats.md)

# リソース
## [料金](https://azure.microsoft.com/pricing/details/media-services/)
## [Azure Media Services コミュニティ](media-services-community.md)











<!--HONumber=Nov16_HO4-->


