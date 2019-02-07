---
title: Azure Media Services を使用したライブ ストリーミングの概要 | Microsoft Docs
description: この記事では、Azure Media Services v3 を使用したライブ ストリーミングの概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/27/2019
ms.author: juliako
ms.openlocfilehash: a3e4821d9deb7ceee815d804f58d0b1ba14925b4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55103566"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Azure Media Services v3 を使用したライブ ストリーミング

Azure Media Services では、Azure クラウドで顧客にライブ イベントを配信することができます。 Media Services でライブ イベントをストリーミングするには、以下が必要です。  

- ライブ イベントをキャプチャするために使用するカメラ。<br/>設定のアイデアについては、「[Simple and portable event video gear setup (シンプルでポータブルなイベント ビデオ機器の設定)]( https://link.medium.com/KNTtiN6IeT)」を参照してください。
- カメラ (またはノート PC などのデバイス) からの信号を Media Services に送信される投稿フィードに変換するライブ ビデオ エンコーダー。 投稿フィードでは、SCTE-35 マーカーなどの広告に関連する信号を含めることができます。<br/>推奨されるライブ ストリーミング エンコーダーの一覧については、[ライブ ストリーミング エンコーダー](recommended-on-premises-live-encoders.md)に関するページを参照してください。 また、ブログ「[Live streaming production with OBS (OBS を使用したライブ ストリーミングの製作)](https://link.medium.com/ttuwHpaJeT)」も参照してください。
- コンテンツの取り込み、プレビュー、パッケージ、録画、暗号化、ライブ イベントのブロードキャスト (顧客のほか、CDN に配信して広域配信することもできる) を可能にする、Media Services のコンポーネント。

Media Services では、**ダイナミック パッケージ**を利用して、サービスに送信する投稿フィードからのライブ ストリームを [MPEG DASH、HLS、および Smooth Streaming 形式](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)でプレビューしてブロードキャストできます。 視聴者は、HLS、DASH、またはスムーズ ストリーミングと互換性のある任意のプレーヤーを使用して、ライブ ストリームを再生できます。 これらいずれかのプロトコルでストリームを配信するために、Web またはモバイル アプリケーションの [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) を使用することができます。

Media Services では、Advanced Encryption Standard (AES-128) または主要な 3 つのデジタル著作権管理 (DRM) システム (Microsoft PlayReady、Google Widevine、および Apple FairPlay) によって動的に暗号化 (**動的暗号化**) されたコンテンツを配信できます。 Media Services では、承認されたクライアントに AES キーと DRM ライセンスを配信するためのサービスも提供しています。 Media Services でコンテンツを暗号化する方法の詳細については、「[コンテンツ保護の概要](content-protection-overview.md)」を参照してください。

動的フィルターを適用することもできます。動的フィルターを使用することで、プレーヤーに送信されるトラック数、形式、ビットレート、プレゼンテーションの時間枠を制御することができます。 詳細については、「 [フィルターと動的マニフェスト](filters-dynamic-manifest-overview.md)」を参照してください。

この記事では、Media Services を使用したライブ ストリーミングの概要とガイダンスを説明します。

## <a name="prerequisites"></a>前提条件

Media Services v3 のライブ ストリーミング ワークフローを理解するには、次の概念を確認して理解する必要があります。 

- [ストリーミング エンドポイント](streaming-endpoint-concept.md)
- [ライブ イベントとライブ出力](live-events-outputs-concept.md)

## <a name="live-streaming-workflow"></a>ライブ ストリーミング ワークフロー

ライブ ストリーミング ワークフローの手順は次のとおりです。

1. Media Services アカウントに移動して、**ストリーミング エンドポイント**が実行されていることを確認します。 
2. **ライブ イベント**を作成します。 <br/>イベントの作成時に、そのイベントを自動開始するように設定できます。 または、ストリーミングを開始する準備ができたら、イベントを開始できます。<br/> 自動開始が true に設定されている場合、ライブ イベントは作成の直後に開始されます。 つまり、ライブ イベントの実行が開始されるとすぐに課金が開始されます。 それ以上の課金を停止するには、ライブ イベント リソースの Stop を明示的に呼び出す必要があります。 詳細については、[ライブ イベントの状態と課金](live-event-states-billing.md)に関するページを参照してください。
3. 取り込み URL を取得し、URL を使用して投稿フィードを送信するようにオンプレミス エンコーダーを構成します。<br/>「[おすすめのライブ エンコーダー](recommended-on-premises-live-encoders.md)」を参照してください。
4. プレビュー URL を取得し、それを使用して、エンコーダーからの入力が実際に受信されていることを確認します。
5. 新しい**資産**オブジェクトを作成します。
6. **ライブ出力**を作成し、作成した資産の名前を使用します。<br/>**ライブ出力**により、ストリームが**資産**にアーカイブされます。
7. 組み込みの**ストリーミング ポリシー** タイプで**ストリーミング ロケーター**を作成します。<br/>コンテンツを暗号化する場合は、「[コンテンツ保護の概要](content-protection-overview.md)」を確認してください。
8. 使用する URL を返すためのパスを**ストリーミング ロケーター**に列挙します (これらは決定論的です)。
9. ストリーミングする**ストリーミング エンドポイント**のホスト名を取得します。
10. 手順 8.の URL と手順 9 のホスト名を組み合わせて、完全な URL を取得します。
11. **ライブ イベント**の公開をやめる場合は、イベントのストリーミングを停止し、**ストリーミング ロケーター**を削除する必要があります。

## <a name="other-important-articles"></a>その他の重要な記事

- [おすすめのライブ エンコーダー](recommended-on-premises-live-encoders.md)
- [クラウド DVR の使用](live-event-cloud-dvr.md)
- [ライブ イベントの種類の機能の比較](live-event-types-comparison.md)
- [状態と課金](live-event-states-billing.md)
- [待機時間](live-event-latency.md)

## <a name="next-steps"></a>次の手順

* [ライブ ストリーミングのチュートリアル](stream-live-tutorial-with-api.md)
* [Media Services v2 から v3 への移行のガイダンス](migrate-from-v2-to-v3.md)
