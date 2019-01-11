---
title: Media Services で推奨されるライブ ストリーミング オンプレミス エンコーダーについて知る - Azure | Microsoft Docs
description: Media Services で推奨されるライブ ストリーミング オンプレミス エンコーダーについて知る
services: media-services
keywords: エンコード;エンコーダー;メディア
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 12/14/2018
ms.topic: article
ms.service: media-services
ms.openlocfilehash: d1110669bd0ca8c0ba0caf34ef41861c500bdd33
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790030"
---
# <a name="recommended-live-streaming-encoders"></a>おすすめのライブ ストリーミング エンコーダー

Media Services では、[LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) (チャネル) は、ライブ ストリーミング コンテンツを処理するためのパイプラインを表します。 LiveEvent は、次の 2 つの方法のいずれかでライブ入力ストリームを受信します。

* オンプレミスのライブ エンコーダーは、マルチビットレート RTMP またはスムーズ ストリーミング (フラグメント化 MP4) のストリームを、Media Services によるライブ エンコードの実行が無効な LiveEvent に送信します。 取り込んだストリームは、追加の処理なしで LiveEvent を通過します。 この方式は、 **パススルー**と呼ばれます。 ライブ エンコーダーではパススルー チャネルにシングルビットレート ストリームを送信することができますが、クライアントへのアダプティブ ビットレート ストリーミングができなくなるため、この構成は推奨されません。

  > [!NOTE]
  > パススルー方式を使用することが、ライブ ストリーミングを行う最も経済的な方法です。

* オンプレミスのライブ エンコーダーでは、次のいずれかの形式で、Media Services によるライブ エンコードが有効な LiveEvent に、シングル ビットレート ストリームが送信されます。RTMP またはスムーズ ストリーミング (フラグメント化 MP4)。 次に、受信したシングル ビットレート ストリームのマルチ ビットレート (アダプティブ) ビデオ ストリームへのライブ エンコードが LiveEvent で実行されます。

Media Services でのライブ エンコードの詳細については、[Media Services v3 でのライブ ストリーミング](live-streaming-overview.md)に関するページをご覧ください。

## <a name="live-encoders-that-output-rtmp"></a>RTMP を出力するライブ エンコーダー

Media Services では、RTMP を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `rtmp://` または `rtmps://` です。

> [!NOTE]
 > RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 1935 と 1936 が開いていることを確認します。<br/>
 RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信 TCP ポート 2935 と 2936 が開いていることを確認します。

- Adobe Flash Media Live Encoder 3.2
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast 8.1 以降
- Telestream Wirecast S
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream

## <a name="live-encoders-that-output-fragmented-mp4"></a>フラグメント化 MP4 を出力するライブ エンコーダー

Media Services では、マルチビットレートのスムーズ ストリーミング (フラグメント化 MP4) を使用した、次のいずれかのライブ エンコーダーを出力として使用することを推奨しています。 サポートされる URL スキームは `rtmp://` または `rtmps://` です。

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live
- Envivio 4Caster C4 Gen III
- Imagine Communications Selenio MCP3
- Media Excel Hero Live と Hero 4K (UHD/HEVC)

## <a name="how-to-become-an-on-premises-encoder-partner"></a>オンプレミス エンコーダー パートナーになる方法

Media Services は、Azure Media Services オンプレミス エンコーダー パートナーとして企業のお客様にエンコーダーを推奨することで、製品の販売を促進します。 オンプレミス エンコーダー パートナーになるには、オンプレミス エンコーダーと Media Services との互換性を確認する必要があります。 これを行うには、次の検証を実行します。

### <a name="pass-through-liveevent-verification"></a>パススルー LiveEvent 検証

1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします。
2. **パススルー** LiveEvent を作成し、起動します。
3. マルチビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します。
5. ライブ エンコーダーを約 10 分間実行します。
6. ライブ イベントを停止します。
7. ストリーミング エンドポイントを作成して開始し、[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) などのプレーヤーを使用して、すべての品質レベルで再生に目に見える異常がないことを確認するため、アーカイブされた資産を監視します (または、手順 6 の前のライブ セッションでプレビュー URL を使用して監視と検証を行います)。
8. 資産 ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
9. 各サンプルの作成後に、LiveEvent の状態をリセットします。
10. エンコーダーでサポートされているすべての構成について、手順 3 - 9 を繰り返します (必要に応じて、広告信号/キャプション/異なるエンコード速度を使用します)。

### <a name="live-encoding-liveevent-verification"></a>ライブ エンコード LiveEvent 検証

1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします。
2. **ライブ エンコード** LiveEvent を作成し、起動します。
3. シングルビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します。
5. ライブ エンコーダーを約 10 分間実行します。
6. ライブ イベントを停止します。
7. ストリーミング エンドポイントを作成して開始し、[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) などのプレーヤーを使用して、すべての品質レベルで再生に目に見える異常がないことを確認するため、アーカイブされた資産を監視します (または、手順 6 の前のライブ セッションでプレビュー URL を使用して監視と検証を行います)。
8. 資産 ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。
9. 各サンプルの作成後に、LiveEvent の状態をリセットします。
10. エンコーダーでサポートされているすべての構成について、手順 3 - 9 を繰り返します (必要に応じて、広告信号/キャプション/さまざまなエンコード速度を使用します)。

### <a name="longevity-verification"></a>持続時間の検証

1. Azure Media Services アカウントを作成するか、またはアカウントにアクセスします。
2. **パススルー** チャネルを作成し、起動します。
3. マルチビット レートのライブ ストリームをプッシュするようにエンコーダーを構成します。
4. 公開済みのライブ イベントを作成します。
5. ライブ エンコーダーを 1 週間以上実行します。
6. 再生に目に見える異常がないことを確認するため、[Azure Media Player](https://ampdemo.azureedge.net/azuremediaplayer.html) などのプレーヤーを使用して、時折ライブ ストリーミング (またはアーカイブされた資産) を監視します。
7. ライブ イベントを停止します。
8. 資産 ID、ライブ アーカイブの公開済みストリーミング URL、およびライブ エンコーダーから使用されている設定とバージョンを記録します。

最後に、すべての自己検証チェックに成功したことを知らせる通知として、amsstreaming@microsoft.com 宛の電子メールで記録した設定とライブ アーカイブ パラメーターを Azure Media Services に送信します。 また、すべてのフォローアップに関する自分の連絡先情報を含めます。 このプロセスに関して質問がある場合は、Azure Media Services チームに問い合わせることができます。

## <a name="next-steps"></a>次の手順

[Media Services v3 を使用したライブ ストリーミング](live-streaming-overview.md)
