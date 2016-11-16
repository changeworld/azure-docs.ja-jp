---
title: "Relay ハイブリッド接続の概要 | Microsoft Docs"
description: "ハイブリッド接続用の C# コンソール アプリケーションを作成する方法"
services: service-bus
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: d1386900-b942-4abf-acfc-38d2ef826253
ms.service: service-bus
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/28/2016
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 92d7935596ab1dede6dc1d613cb635c32d52e3ab


---
# <a name="get-started-with-relay-hybrid-connections"></a>Relay ハイブリッド接続の概要
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

## <a name="what-will-be-accomplished"></a>作業内容
ハイブリッド接続ではクライアントとサーバーの 2 つのコンポーネントが必要になります。このため、このチュートリアルでは 2 つのコンソール アプリケーションを作成します。 手順は次のようになります。

1. Azure Portal を使用した Relay 名前空間の作成
2. Azure Portal を使用したハイブリッド接続の作成
3. メッセージを受信するサーバー コンソール アプリケーションの作成
4. メッセージを送信するクライアント コンソール アプリケーションの作成

## <a name="prerequisites"></a>前提条件
1. [Visual Studio 2013 または Visual Studio 2015](http://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2015 を使用します。
2. Azure サブスクリプション。

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1.Azure ポータルを使用した名前空間の作成
Relay 名前空間を既に作成してある場合には、「[Azure Portal を使用したハイブリッド接続の作成](#2-create-a-hybrid-connection-using-the-azure-portal)」セクションに進んでください。

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="2-create-a-hybrid-connection-using-the-azure-portal"></a>2.Azure Portal を使用したハイブリッド接続の作成
ハイブリッド接続を既に作成してある場合には、「[サーバー アプリケーションの作成](#3-create-a-server-application-listener)」セクションに進んでください。

[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="3-create-a-server-application-listener"></a>3.サーバー アプリケーション (リスナー) の作成
Relay からのメッセージをリッスンおよび受信するために、Visual Studio を使って C# コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-server](../../includes/relay-hybrid-connections-dotnet-get-started-server.md)]

## <a name="4-create-a-client-application-sender"></a>4.クライアント アプリケーション (センダー) の作成
Relay にメッセージを送信するために、Visual Studio を使って C# コンソール アプリケーションを作成します。

[!INCLUDE [relay-hybrid-connections-dotnet-get-started-client](../../includes/relay-hybrid-connections-dotnet-get-started-client.md)]

## <a name="5-run-the-applications"></a>5.アプリケーションの実行
1. サーバー アプリケーションを実行します。
2. クライアント アプリケーションを実行し、何かテキストを入力します。
3. サーバー アプリケーション コンソールに、クライアント アプリケーションで入力したテキストが出力されることを確認します。

![アプリケーションの実行](./media/relay-hybrid-connections-dotnet-get-started/running-applications.png)

これで、エンド ツー エンドのハイブリッド接続アプリケーションを作成できました。

## <a name="next-steps"></a>次のステップ:
* [Relay に関する FAQ](relay-faq.md)
* [名前空間を作成する](relay-create-namespace-portal.md)
* [Node を使って作業を開始する](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Nov16_HO2-->


