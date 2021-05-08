---
title: Azure Communication Services の Chat SDK の概要
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services Chat SDK について説明します。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500687"
---
# <a name="chat-sdk-overview"></a>Chat SDK の概要 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services Chat SDK を使用して、アプリケーションに高度なリアルタイム チャットを追加できます。
    
## <a name="chat-sdk-capabilities"></a>Chat SDK の機能    

次の一覧は、Communication Services Chat SDK で現在使用できる機能のセットを示しています。  

| 機能のグループ | 機能 | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| コア機能 | 2 人以上のユーザー間のチャット スレッドを作成する                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | チャット スレッドのトピックを更新する                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | チャット スレッドの参加者を追加または削除する                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | 追加する参加者とチャット メッセージの履歴を共有するかどうかを選択する                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | チャット スレッドの参加者の一覧を取得する                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | チャット スレッドを削除する                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Communication ユーザーを指定して、そのユーザーが属するチャット スレッドの一覧を取得する                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 特定のチャット スレッドに関する情報を取得する                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | チャット スレッドでメッセージを送受信する                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | 送信したメッセージの内容を更新する                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | 以前に送信したメッセージを削除する                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | チャットの他の参加者が確認済みメッセージの開封確認                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | 参加者がチャット スレッドにメッセージを入力中であることを示す通知を受け取る                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | チャット スレッド内のすべてのメッセージを取得する                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | メッセージ コンテンツの一部として Unicode の絵文字を送信する                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|リアルタイムの通知 (独自のシグナリング パッケージで対応**)|  チャット クライアントは、受信メッセージや、チャット スレッドで発生しているその他の操作のリアルタイム更新を取得するためにサブスクライブできます。 リアルタイム通知でサポートされている更新の一覧を確認するには、「[チャットに関する概念](concepts.md#real-time-notifications)」をご覧ください                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Azure Event Grid との統合             | Azure Event Grid で使用可能なチャット イベントを使用して、カスタム通知サービスを接続する、またはそのイベントを Webhook に投稿して、チャットの完了後に CRM レコードを更新するなどのビジネス ロジックを実行する   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| レポート </br>(この情報は、Azure portal のお使いの Communication Services リソースの [監視] タブにあります)      | Azure メトリックス エクスプローラーで公開されたメトリックを監視してチャット アプリからの API トラフィックを理解し、アラートを設定して異常を検出する     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | お使いのリソースの診断ログを有効にして、お使いの Communication Services ソリューションを監視およびデバッグする    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**独自のシグナリング パッケージは、Web ソケットを使用して実装します。 Web ソケットがサポートされていない場合は、ロング ポーリングにフォールバックされます。  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>OS とブラウザーによる JavaScript Chat SDK のサポート    

次の表は、現在使用可能な、サポートされているブラウザーとバージョンのセットを示しています。
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat SDK** | Firefox *、Chrome*、新しい Microsoft Edge | Firefox *、Chrome*、Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

\* 前の 2 つのリリースに加えて最新バージョンがサポートされていることに注意してください。<br/>   

## <a name="next-steps"></a>次の手順   

> [!div class="nextstepaction"] 
> [チャットを開始する](../../quickstarts/chat/get-started.md)    

次のドキュメントもご覧ください。  
- [チャットの概念](../chat/concepts.md)について理解する
- チャットの[価格](../pricing.md#chat)について理解する
