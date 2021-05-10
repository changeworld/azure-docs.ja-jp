---
title: Azure Communication Services でのチャットの概念
titleSuffix: An Azure Communication Services concept document
description: Communication Services のチャットの概念について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 292f430a1b08d59efdf05405437b3d1aa49ea2b7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168597"
---
# <a name="chat-concepts"></a>チャットに関する概念 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services Chat SDK を使用して、アプリケーションにリアルタイムのテキスト チャットを追加できます。 このページには、チャットの主な概念と機能がまとめられています。    

特定の SDK の言語と機能の詳細については、[Communication Services Chat SDK の概要](./sdk-features.md)に関するページを参照してください。  

## <a name="chat-overview"></a>チャットの概要    

チャットの会話は、**チャット スレッド** 内で行われます。 チャット スレッドには次の特徴があります。

- チャット スレッドは、それぞれの `ChatThreadId` によって一意に識別されます。 
- チャット スレッドには、メッセージを送信できる参加者として 1 人以上のユーザーを含めることができます。 
- ユーザーは、1 つ以上のチャット スレッドに参加できます。 
- 特定のチャット スレッドにアクセスできるのは、そのスレッドの参加者のみで、彼らだけがチャット スレッドの操作を実行できます。 これらの操作には、メッセージの送受信、参加者の追加、参加者の削除が含まれます。 
- ユーザーは、自分が作成したすべてのチャット スレッドに参加者として自動的に追加されます。

### <a name="user-access"></a>ユーザー アクセス
通常、スレッドの作成者と参加者は、スレッドに対して同じレベルのアクセス権限を持っており、SDK で利用可能なすべての関連する操作 (スレッドの削除を含む) を実行できます。 参加者には、他の参加者が送信したメッセージに対する書き込みアクセス権限がありません。つまり、送信済みメッセージを更新または削除できるのは、そのメッセージの送信者だけです。 別の参加者がその操作を行おうとすると、エラーが発生します。 

一連のユーザーのチャット機能へのアクセスを制限する場合は、信頼されたサービスの一環としてアクセスを構成することができます。 信頼されたサービスとは、チャット参加者の認証と承認を調整するサービスです。 これについては、以下で詳しく説明します。  

### <a name="chat-data"></a>チャット データ 
Communication Services は、明示的に削除されるまで、チャットの履歴を保存します。 チャット スレッドの参加者は、`ListMessages` を使用して、特定のスレッドのメッセージの履歴を表示できます。 チャット スレッドから削除されたユーザーは、以前のメッセージの履歴を表示することはできますが、新しいメッセージをそのチャット スレッドの一部として送受信することはできません。 参加者のいない、完全にアイドル状態のスレッドは、30 日後に自動的に削除されます。 Communication Services で格納されるデータの詳細については、[プライバシー](../privacy.md)に関するドキュメントを参照してください。  

### <a name="service-limits"></a>サービスの制限  
- チャット スレッドで許可される参加者の最大数は 250 人です。   
- 許可されている最大メッセージ サイズは約 28 KB です。  
- 20 名を超える参加者がいるチャット スレッドの場合は、開封確認メッセージと入力インジケーターの機能はサポートされません。    

## <a name="chat-architecture"></a>チャットのアーキテクチャ    

チャットのアーキテクチャには、次の2つの主要な部分があります。1) 信頼されたサービスと 2) クライアント アプリケーション。    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Communication Services のチャットのアーキテクチャを示す図。"::: 

 - **信頼されたサービス:** チャット セッションを適切に管理するには、リソース接続文字列を使用して Communication Services に接続できるようにするサービスが必要です。 このサービスには、チャット スレッドの作成、参加者の追加と削除、ユーザーへのアクセス トークンの発行を行う役割があります。 アクセス トークンの詳細については、[アクセス トークン](../../quickstarts/access-tokens.md)のクイックスタートを参照してください。  
 - **クライアント アプリ:** クライアント アプリケーションが信頼されたサービスに接続し、ユーザーが直接 Communication Services に接続するために使用するアクセス トークンを受け取ります。 信頼されたサービスによってチャット スレッドが作成され、ユーザーが参加者として追加されたら、クライアント アプリを使用してチャット スレッドに接続し、メッセージを送信できるようになります。 クライアント アプリでリアルタイム通知機能 (以下で説明) を使用し、他の参加者によるメッセージおよびスレッドの更新情報を受信登録します。
    
        
## <a name="message-types"></a>メッセージの種類    

チャットでは、メッセージの履歴の一部として、システムによって生成されたメッセージだけでなく、ユーザーによって生成されたメッセージも共有されます。 システム メッセージは、チャット スレッドが更新されたときに生成され、参加者が追加または削除された日時やチャット スレッドのトピックが更新された日時を特定するのに役立ちます。 チャット スレッド上で `List Messages` または `Get Messages` を呼び出すと、その結果には、両方の種類のメッセージが時系列順に表示されます。

ユーザーによって生成されたメッセージの場合、チャット スレッドにメッセージを送信する際に `SendMessageOptions` でメッセージの種類を設定できます。 値が指定されていない場合、Communication Services の既定値である `text` 型になります。 HTML を送信する場合、この値の設定は重要です。 `html` を指定すると、コンテンツは Communication Services によってサニタイズされ、クライアント デバイス上で安全にレンダリングされるようになります。
 - `text`: チャット スレッドの一部としてユーザーが作成および送信するプレーンテキスト メッセージ。 
 - `html`: チャット スレッドの一部としてユーザーが作成および送信する、HTML を使用して書式設定されたメッセージ。 

システム メッセージの種類は次のとおりです。 
 - `participantAdded`:1 人以上の参加者がチャット スレッドに追加されたことを示すシステム メッセージ。
 - `participantRemoved`:参加者がチャット スレッドから削除されたことを示すシステム メッセージ。
 - `topicUpdated`: スレッド トピックが更新されたことを示すシステム メッセージ。

## <a name="real-time-notifications"></a>リアルタイム通知  

一部の SDK (JavaScript Chat SDK など) では、リアルタイム通知がサポートされています。 これにより、クライアントは API をポーリングしなくても、Communication Services のチャット スレッドに対する更新情報や受信メッセージをリアルタイムでリッスンできます。 クライアント アプリは、次のイベントをサブスクライブできます。
 - `chatMessageReceived` - 参加者によってチャット スレッドに新しいメッセージが送信されたとき。
 - `chatMessageEdited` - チャット スレッドでメッセージが編集されたとき。 
 - `chatMessageDeleted` - チャット スレッドでメッセージが削除されたとき。   
 - `typingIndicatorReceived` - 別の参加者によってチャット スレッドに入力インジケーターが送信されたとき。    
 - `readReceiptReceived` - 別の参加者によって開封済みのメッセージに対して開封確認メッセージが送信されたとき。  
 - `chatThreadCreated` - Communication Services ユーザーによってチャット スレッドが作成されたとき。    
 - `chatThreadDeleted` - Communication Services ユーザーによってチャット スレッドが削除されたとき。    
 - `chatThreadPropertiesUpdated` - チャット スレッドのプロパティが更新されたとき。現在は、スレッドのトピックの更新のみがサポートされています。 
 - `participantsAdded` - ユーザーがチャット スレッドの参加者として追加されたとき。     
 - `participantsRemoved` - 既存の参加者がチャット スレッドから削除されたとき。

リアルタイム通知を使用すると、ユーザーにリアルタイムのチャット エクスペリエンスを提供できます。 Communication Services は、ユーザーが不在のため見逃したメッセージに対してプッシュ通知を送信するために Azure Event Grid と統合されており、自分のカスタム アプリ通知サービスに接続できるチャット関連のイベント (投稿操作) を発行します。 詳細については、[サーバー イベント](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)に関する記事をご覧ください。


## <a name="build-intelligent-ai-powered-chat-experiences"></a>AI を活用したインテリジェントなチャット エクスペリエンスを構築する   

[Azure Cognitive API シリーズ](../../../cognitive-services/index.yml)と Chat SDK を使用して、次のようなユース ケースを構築できます。

- ユーザーがさまざまな言語で互いにチャットできるようにする。  
- サポート エージェントがチケットの優先順位を設定するために、顧客から受信したメッセージから否定的なセンチメントを検出できるように支援する。 
- 受信メッセージのキー検出とエンティティ認識を分析し、メッセージの内容に基づいてアプリ内でユーザーに関連情報を表示する。

これを実現する 1 つの方法として、信頼されたサービスをチャット スレッドの参加者として機能させることができます。 たとえば、言語の翻訳を有効にするとします。 このサービスは、他の参加者が交換するメッセージをリッスンし [1]、Cognitive API を呼び出してその内容を目的の言語に翻訳し [2、3]、翻訳結果をチャット スレッドのメッセージとして送信する [4] という役割を担います。

これにより、メッセージ履歴には元のメッセージと翻訳されたメッセージの両方が含まれます。 クライアント アプリケーションに、元のメッセージまたは翻訳されたメッセージを表示するロジックを追加できます。 Cognitive API を使用してテキストを別の言語に翻訳する方法については、[こちらのクイックスタート](../../../cognitive-services/translator/quickstart-translator.md)をご覧ください。 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Communication Services と対話する Cognitive Services を示す図。"::: 

## <a name="next-steps"></a>次の手順   

> [!div class="nextstepaction"] 
> [チャットを開始する](../../quickstarts/chat/get-started.md)    

次のドキュメントもご覧ください。  
- [Chat SDK](sdk-features.md) について理解を深める
