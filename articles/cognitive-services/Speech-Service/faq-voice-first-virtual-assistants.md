---
title: Direct Line Speech に関してよく寄せられる質問
titleSuffix: Azure Cognitive Services
description: Direct Line Speech チャネルを使用した音声優先仮想アシスタントに関してよく寄せられる質問の回答を紹介します。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: travisw
ms.openlocfilehash: 8427417c9b579c7dfa21f834ce1ca77099159eb2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65072620"
---
# <a name="voice-first-virtual-assistants-preview-frequently-asked-questions"></a>音声優先仮想アシスタントのプレビュー:よく寄せられる質問

疑問点への回答がこのドキュメントで見つからない場合は、[その他のサポート オプション](support.md)を確認してください。

## <a name="general"></a>全般

**Q:Direct Line Speech とは何ですか?**

**A:** Speech SDK の `SpeechBotConnector` は、Bot Framework の Direct Line Speech チャネルに接続されているボットに双方向の非同期通信を提供します。 このチャネルは、Azure Speech Services から音声変換とテキスト読み上げへの調整されたアクセスを提供します。これにより、ボットが音声入力、音声出力の会話エクスペリエンスに完全に対応します。 ウェイク ワードとウェイク ワード検証がサポートされているため、このソリューションを使用すると、高度にカスタマイズ可能な音声優先仮想アシスタントを自社のブランドや製品用に構築できます。

**Q:始めにどうすればよいですか。**

**A:** 音声優先仮想アシスタントの作成から始めるには、[基本的な Bot Framework ボットの作成](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)から始めることをお勧めします。 次に、ボットを [Direct Line Speech チャネル](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)に接続します。

## <a name="debugging"></a>デバッグ

**Q:接続時に 401 エラーが表示され、何も機能しません。音声サブスクリプション キーが有効であることはわかっています。どうなっているのでしょうか?**

**A:** プレビュー段階の Direct Line Speech には、使用されるサブスクリプションに対して非常に特別な制限があります。 **Cognitive Services** リソース (Microsoft.CognitiveServicesAllInOne、"All Cognitive Services") "*ではなく*"、**Speech** リソース (Microsoft.CognitiveServicesSpeechServices、"Speech") を使用していることを確認してください。 また、現在サポートされているのは **westus2** リージョンのみであることに注意してください。

![Direct Line Speech の正しいサブスクリプション](media/voice-first-virtual-assistants/faq-supported-subscription.png "互換性のある音声サブスクリプションの例")

**Q:Direct Line Speech から戻された認識テキストを受け取りますが、'1011' エラーが表示され、ボットからは何も表示されません。なぜですか?**

**A:** このエラーは、ボットと Direct Line Speech 間の通信に問題があることを示しています。 [Direct Line Speech チャネルを接続し](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)、(関連する Web ソケット サポートと共に) [ストリーミング プロトコル サポートをボットに追加し](https://aka.ms/botframework/addstreamingprotocolsupport)ていることを確認してから、ボットがチャネルからの受信要求に応答していることを確認します。

**Q:この方法でも解決しないか、SpeechBotConnector の使用時に別のエラーを受け取り、どうすればよいかわかりません。"*どうすれば*" よいですか。**

**A:** ファイルベースのログではより詳細な情報が提供され、サポート リクエストを迅速に行うことができます。 これを有効にするには、[ファイル ログの使用方法](how-to-use-logging.md)に関する記事を参照してください。

## <a name="next-steps"></a>次の手順

* [トラブルシューティング](troubleshooting.md)
* [リリース ノート](releasenotes.md)
