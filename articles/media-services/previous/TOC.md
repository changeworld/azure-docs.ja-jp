# [Media Services のドキュメント](index.md)

# [概要](media-services-overview.md)
## [シナリオと可用性](scenarios-and-availability.md)
## [概念](media-services-concepts.md)

# 作業開始
## [アカウントの作成と管理](media-services-portal-create-account.md)
## [開発環境のセットアップ](media-services-set-up-computer.md)
### [.NET](media-services-dotnet-how-to-use.md)
### [REST](media-services-rest-how-to-use.md)  
## [AAD 認証を使用して API にアクセスする](media-services-use-aad-auth-to-access-ams-api.md)
### [ポータルを使用して AAD 認証を管理する](media-services-portal-get-started-with-aad.md)
### [.NET で API にアクセスする](media-services-dotnet-get-started-with-aad.md)
### [REST で API にアクセスする](media-services-rest-connect-with-aad.md)
### [Azure CLI を使用して AAD アプリを作成および構成する](media-services-cli-create-and-configure-aad-app.md)
### [Azure PowerShell を使用して AAD アプリを作成および構成する](media-services-powershell-create-and-configure-aad-app.md)

## ビデオ オン デマンドの配信
### [Azure Portal](media-services-portal-vod-get-started.md)
### [.NET SDK](media-services-dotnet-get-started.md)
### [Java](media-services-java-how-to-use.md)
### [REST ()](media-services-rest-get-started.md)
## ライブ ストリーミングの実行
### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)

# 方法
## [管理]
### エンティティ
#### [.NET](media-services-dotnet-manage-entities.md)
#### [REST](media-services-rest-manage-entities.md)
### [ストリーミング エンドポイント](media-services-streaming-endpoints-overview.md)
#### [Azure Portal](media-services-portal-manage-streaming-endpoints.md)
#### [.NET](media-services-dotnet-manage-streaming-endpoints.md)
### Storage
#### [ストレージ アクセス キーをローリングした後に Media Services を更新する](media-services-roll-storage-access-keys.md)
#### [複数のストレージ アカウントで資産を管理する](meda-services-managing-multiple-storage-accounts.md)
### [クォータと制限](media-services-quotas-and-limitations.md)
## [Postman の構成](media-rest-apis-with-postman.md)
### [オンデマンド ストリーミングのコレクション](postman-collection.md)
### [ライブ ストリーミングのコレクション](postman-live-streaming-collection.md)
### [Environment](postman-environment.md)
## コンテンツをアップロードする
### ファイルをアカウントにアップロードする
#### [Azure Portal](media-services-portal-upload-files.md)
#### [.NET](media-services-dotnet-upload-files.md)
#### [REST](media-services-rest-upload-files.md)
### [Aspera を使用した大きなファイルのアップロード](media-services-upload-files-with-aspera.md)
### [StorSimple を使用したファイルのアップロード](media-services-upload-files-from-storsimple.md)
### [既存の BLOB のコピー](media-services-copying-existing-blob.md)

## [コンテンツのエンコード](media-services-encode-asset.md)
### [エンコーダーの比較](media-services-compare-encoders.md)
### [エンコードの速度と同時実行の管理](media-services-manage-encoding-speed.md)
### Media Encoder Standard (MES)
#### [Media Encoder Standard の形式とコーデック](media-services-media-encoder-standard-formats.md)
#### [MES を使用したビットレート ラダーの自動生成](media-services-autogen-bitrate-ladder-with-mes.md)
#### Media Encoder Standard を使用したエンコード
##### [Azure Portal](media-services-portal-encode.md)
##### [.NET](media-services-dotnet-encode-with-media-encoder-standard.md)
##### [REST](media-services-rest-encode-asset.md)
#### [MES による高度なエンコード](media-services-advanced-encoding-with-mes.md)
##### [Media Encoder Standard のプリセットのカスタマイズ](media-services-custom-mes-presets-with-dotnet.md)
##### [.NET で Media Encoder Standard を使用してサムネイルを生成する方法](media-services-dotnet-generate-thumbnail-with-mes.md)
##### [Media Encoder Standard を使用してビデオをトリミングする](media-services-crop-video.md)
#### MES スキーマ
##### [Media Encoder Standard スキーマ](media-services-mes-schema.md)
##### [入力メタデータ](media-services-input-metadata-schema.md)
##### [出力メタデータ](media-services-output-metadata-schema.md)
#### [MES のプリセット](media-services-mes-presets-overview.md) 
##### [H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md)
##### [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md)
##### [H264 Multiple Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md)
##### [H264 Multiple Bitrate 16x9 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md)
##### [H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md)
##### [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md)
##### [H264 Multiple Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md)
##### [H264 Multiple Bitrate 4x3 for iOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md)
##### [H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md)
##### [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md)
##### [H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md)
##### [H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md)
##### [H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md)
##### [H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md)
##### [H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md)
##### [H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md)
##### [H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md)
##### [H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md)
##### [H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md)
##### [H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md)
##### [H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md)
##### [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md)
##### [H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md)
### メディア エンコーダー Premium ワークフロー
#### [メディア エンコーダー プレミアム ワークフローの形式とコーデック](media-services-premium-workflow-encoder-formats.md)
#### メディア エンコーダー プレミアム ワークフローを使用したエンコード
##### [メディア エンコーダー Premium ワークフロー](media-services-encode-with-premium-workflow.md)
##### [メディア エンコーダー Premium ワークフローのチュートリアル](media-services-media-encoder-premium-workflow-tutorials.md)
##### [ワークフロー デザイナーを使用して高度なエンコード ワークフローを作成する](media-services-workflow-designer.md)
##### [複数入力の場合の Premium ワークフロー](media-services-media-encoder-premium-workflow-multiplefilesinput.md)
### [fMP4 チャンクを生成するタスクを作成する](media-services-generate-fmp4-chunks.md)
### メディア プロセッサ
#### [.NET](media-services-get-media-processor.md)
#### [REST](media-services-rest-get-media-processor.md)
### [エラー コード](media-services-encoding-error-codes.md)
### 非推奨
#### [静的パッケージと暗号化](media-services-static-packaging.md)

## [ライブ ストリーミング](media-services-manage-channels-overview.md)
### [オンプレミス エンコーダー](media-services-live-streaming-with-onprem-encoders.md)
#### [推奨のオンプレミス エンコーダー](media-services-recommended-encoders.md)
#### [Azure Portal](media-services-portal-live-passthrough-get-started.md)
#### [.NET](media-services-dotnet-live-encode-with-onpremises-encoders.md)
### [クラウド エンコーダーを使用したライブ ストリーミング](media-services-manage-live-encoder-enabled-channels.md)
#### [Azure Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
#### [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
### [オンプレミス エンコーダーをクラウド エンコーダーと使用するための構成](media-services-live-encoders-overview.md)
#### [FMLE エンコーダー](media-services-configure-fmle-live-encoder.md)
#### [Haivision KB エンコーダー](media-services-configure-kb-live-encoder.md)
#### [NewTek TriCaster エンコーダー](media-services-configure-tricaster-live-encoder.md)
#### [Wirecast エンコーダー](media-services-configure-wirecast-live-encoder.md)
### [長時間にわたって実行される操作の処理](media-services-dotnet-long-operations.md)
### [フラグメント化 MP4 ライブ取り込みの仕様](media-services-fmp4-live-ingest-overview.md)

## [クリップ コンテンツ](media-services-azure-media-clipper-overview.md)
### [使用の開始](media-services-azure-media-clipper-getting-started.md)
### [動画の読み込み](media-services-azure-media-clipper-load-assets.md)
### [キーボード ショートカットの構成](media-services-azure-media-clipper-keyboard-shortcuts.md)
### [ローカライズの構成](media-services-azure-media-clipper-localization.md)
### [クリッピング ジョブの送信](media-services-azure-media-clipper-submit-job.md)
### [Azure Portal](media-services-azure-media-clipper-portal.md)

## [コンテンツの保護](media-services-content-protection-overview.md)
### [ストレージ暗号化](media-services-rest-storage-encryption.md)
### [AES-128 暗号化](media-services-protect-with-aes128.md)
### [ストリーミング用 PlayReady/Widevine](media-services-protect-with-playready-widevine.md)
### [ストリーミング用 FairPlay](media-services-protect-hls-with-fairplay.md)
### [Windows 10 用のオフラインの PlayReady](https://blogs.msdn.microsoft.com/playready4/2016/10/26/does-azure-media-services-support-offline-mode/)
### [iOS 用のオフラインの FairPlay](media-services-protect-hls-with-offline-fairplay.md)
### [Android 用のオフラインの Widevine](offline-widevine-for-android.md)
### [Azure Portal での構成](media-services-portal-protect-content.md)
### [DRM ライセンスの配布](media-services-deliver-keys-and-licenses.md)
### ContentKeys を作成する
#### [.NET](media-services-dotnet-create-contentkey.md)
#### [REST](media-services-rest-create-contentkey.md)
### ライセンス テンプレートの概要
#### [PlayReady ライセンス テンプレート](media-services-playready-license-template-overview.md)
#### [Widevine ライセンス テンプレート](media-services-widevine-license-template-overview.md)
### 資産配信ポリシーを構成する
#### [.NET](media-services-dotnet-configure-asset-delivery-policy.md)
#### [REST](media-services-rest-configure-asset-delivery-policy.md)
### コンテンツ キー承認ポリシーを構成する
#### [Azure Portal](media-services-portal-configure-content-key-auth-policy.md)
#### [.NET](media-services-dotnet-configure-content-key-auth-policy.md)
#### [REST](media-services-rest-configure-content-key-auth-policy.md)
### [認証トークンを AMS に渡す](media-services-pass-authentication-tokens.md)
### 参照設計
#### [DRM システムのハイブリッド設計](hybrid-design-drm-sybsystem.md)
#### [マルチ DRM の参照設計](media-services-cenc-with-multidrm-access-control.md)

## [分析](media-services-analytics-overview.md)
### [Azure Portal を使用したメディアの分析](media-services-portal-analyze.md)
### [Indexer 2 による処理](media-services-process-content-with-indexer2.md)
### [Indexer による処理](media-services-index-content.md)
#### [タスク プリセット](indexer-task-preset.md)
### [Hyperlapse による処理](media-services-hyperlapse-content.md)
### [Face Detector による処理](media-services-face-and-emotion-detection.md)
### [Motion Detector による処理](media-services-motion-detection.md)
### [Face Redactor による処理](media-services-face-redaction.md)
#### [Face Redactor のチュートリアル](media-services-redactor-walkthrough.md)
### [Video Thumbnails による処理](media-services-video-summarization.md)
### [OCR による処理](media-services-video-optical-character-recognition.md)
### [Content Moderator による処理](media-services-content-moderation.md)

## [テレメトリの構成](media-services-telemetry-overview.md)
###[.NET](media-services-dotnet-telemetry.md)
###[REST](media-services-rest-telemetry.md)

## スケール
### [メディア処理](media-services-scale-media-processing-overview.md)
#### [Azure Portal](media-services-portal-scale-media-processing.md)
#### [.NET](media-services-dotnet-encoding-units.md)
### ストリーミング エンドポイント
#### [Azure Portal](media-services-portal-scale-streaming-endpoints.md)

## [コンテンツの配信](media-services-deliver-content-overview.md)
### [ダイナミック パッケージ](media-services-dynamic-packaging-overview.md)
### [フィルターと動的マニフェストの概要](media-services-dynamic-manifest-overview.md)
#### [.NET を使用したフィルターの作成](media-services-dotnet-dynamic-manifest.md)
#### [REST を使用したフィルターの作成](media-services-rest-dynamic-manifest.md)
### [Media Services Extension での CDN キャッシュ ポリシー](../../cdn/cdn-caching-policy.md?toc=%2fazure%2fmedia-services%2ftoc.json)
### コンテンツを発行する
#### [Azure Portal](media-services-portal-publish.md)
#### [.NET](media-services-deliver-streaming-content.md)
#### [REST](media-services-rest-deliver-streaming-content.md)
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
### [Microsoft Smooth Streaming Client Porting Kit のライセンス](media-services-sspk.md)

## 統合
### [Media Services を使用する Azure 関数の使用](media-services-dotnet-how-to-use-azure-functions.md)
### [Media Services を使用する Azure 関数の例](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)

## 監視
### ジョブの進行状況をチェックする
#### [REST](media-services-rest-check-job-progress.md)
#### [Azure Portal](media-services-portal-check-job-progress.md)
#### [.NET](media-services-check-job-progress.md)
### [Queue Storage を使用したジョブ通知の監視](media-services-dotnet-check-job-progress-with-queues.md)
### [webhook を使用したジョブ通知の監視](media-services-dotnet-check-job-progress-with-webhooks.md)

## トラブルシューティング
### [よく寄せられる質問](media-services-frequently-asked-questions.md)
### [ライブ ストリーミングのトラブルシューティング ガイド](media-services-troubleshooting-live-streaming.md)
### [エラー コード](media-services-error-codes.md)
### [再試行ロジック](media-services-retry-logic-in-dotnet-sdk.md)

# リファレンス
## [コード サンプル](https://azure.microsoft.com/resources/samples/?service=media-services)
## [Azure PowerShell (Resource Manager)](/powershell/module/azurerm.media)
## [Azure PowerShell (サービス管理)](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)
## [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client)
## [REST](/rest/api/media/mediaservice)
## 仕様
### [ライブ取り込み - フラグメント化 MP4 ライブ取り込みの仕様](media-services-fmp4-live-ingest-overview.md)
### [ライブ取り込み - ライブ ストリーミングでの時限メタデータのシグナル発信](media-services-specifications-live-timed-metadata.md)
### [スムーズ ストリーミング HEVC](media-services-specifications-ms-sstr-amendment-hevc.md)

# リソース
## [Azure Media Services コミュニティ](media-services-community.md)
## [Azure のロードマップ](https://azure.microsoft.com/roadmap/?category=web-mobile)
## [料金](https://azure.microsoft.com/pricing/details/media-services/)
## [料金計算ツール](https://azure.microsoft.com/pricing/calculator/)
## [リリース ノート](media-services-release-notes.md)
## [ビデオ](https://azure.microsoft.com/resources/videos/index/?services=media-services)
