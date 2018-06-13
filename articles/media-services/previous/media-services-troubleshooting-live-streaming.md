---
title: ライブ ストリーミングのトラブルシューティング ガイド | Microsoft Docs
description: このトピックでは、ライブ ストリーミングに関する問題のトラブルシューティング方法について推奨事項を説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 3a7f6c1d-ce57-4fa4-a7a6-edb526b3ffbf
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: 84e3e9fc18671d7199eeaf638377a6681cf09fb4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33940926"
---
# <a name="troubleshooting-guide-for-live-streaming"></a>ライブ ストリーミングのトラブルシューティング ガイド
この記事では、一部のライブ ストリーミングの問題のトラブルシューティング方法に関する推奨事項を説明します。

## <a name="issues-related-to-on-premises-encoders"></a>オンプレミスのエンコーダーに関連する問題
このセクションでは、ライブ エンコードが有効になっている AMS チャネルに、単一ビットレートのストリームを送信するよう構成したオンプレミスのエンコーダーに関連する問題のトラブルシューティング方法について推奨事項を説明します。

### <a name="problem-would-like-to-see-logs"></a>問題: ログを確認したい
* **潜在的な問題**: 問題のデバッグに役立つエンコーダーのログが見つかりません。
  
  * **Telestream Wirecast**: 通常、ログは C:\Users\{username}\AppData\Roaming\Wirecast\ にあります。 
  * **Elemental Live**: 管理ポータルでログへのリンクを見つけることができます。 **[統計]** をクリックし、**[ログ]** をクリックします。 **[ログ ファイル]** ページに、すべての LiveEvent 項目のログの一覧が表示されます。現在のセッションに一致するものを選択します。 
  * **Flash Media Live Encoder**: **[エンコーディング ログ]** タブに移動すると、**[ログ ディレクトリ...]** があります。

### <a name="problem-there-is-no-option-for-outputting-a-progressive-stream"></a>問題: プログレッシブ ストリームを出力するためのオプションがない
* **潜在的な問題**: 使用しているエンコーダーでは、自動的にインターレースが解除されません。 
  
    **トラブルシューティングの手順**: エンコーダーのインターフェイス内でインターレースを解除するオプションを探します。 インターレースの解除を有効にしたら、プログレッシブ出力の設定をもう一度確認します。 

### <a name="problem-tried-several-encoder-output-settings-and-still-unable-to-connect"></a>問題: エンコーダーの出力設定をいくつか試したが、まだ接続できない
* **潜在的な問題**: Azure のエンコード チャネルが正しくリセットされませんでした。 
  
    **トラブルシューティングの手順**: エンコーダーが AMS にプッシュしなくなったことを確認してから、チャネルを停止してリセットします。 再度実行されたら、新しい設定でエンコーダーを接続します。 それでも問題が解決しない場合は、まったく新しいチャネルを作成します。場合によっては、何回か失敗した後に、チャネルが破損している可能性があります。  
* **潜在的な問題**: GOP サイズまたはキーフレームの設定が最適でない 
  
    **トラブルシューティングの手順**: 推奨する GOP サイズまたはキーフレームの間隔は 2 秒です。 エンコーダーには、この設定の計算にフレーム数を使用するものと、秒を使用するものがあります。 例: 30 fps を出力する場合、GOP サイズは 60 フレームとなり、これは 2 秒に相当します。  
* **潜在的な問題**: 閉じたポートが、ストリームをブロックしています。 
  
    **トラブルシューティングの手順**: RTMP を使用してデータをストリーミングしている場合は、ファイアウォールまたはプロキシ、あるいはその両方の設定で、送信ポート 1935 と 1936 が開いていることを確認します。 

> [!NOTE]
> このトラブルシューティングの手順に従っても正常にストリーミングできない場合は、Azure Portal を使用して、サポート チケットを送信してください。
> 
> 

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

