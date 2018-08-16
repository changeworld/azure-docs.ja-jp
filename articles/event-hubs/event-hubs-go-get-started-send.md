---
title: Go を使用して Azure Event Hubs にイベントを送信する | Microsoft Docs
description: Go を使用して Event Hubs へのイベントの送信を開始する
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: 40b3aa82c3e9e8ab9a30362c0a41998877655725
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005599"
---
# <a name="send-events-to-event-hubs-using-go"></a>Go を使用して Event Hubs にイベントを送信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。

Event Hubs の詳細については、[Event Hubs の概要][Event Hubs overview]に関する記事を参照してください。

このチュートリアルでは、Go で記述されたアプリケーションからイベント ハブにイベントを送信する方法について説明します。 イベントを受信するには、[対応する受信の記事](event-hubs-go-get-started-receive-eph.md)で説明されているように、**Go eph** (イベント プロセッサ ホスト) パッケージを使用します。

このチュートリアルのコードは、[これらの GitHub サンプル](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)から取得されたもので、import ステートメントや変数の宣言など、アプリケーションが完全に機能するかを確認することができます。

その他の例については、[Event Hubs パッケージのリポジトリ](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)から入手できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* Go がローカルにインストールされていること。 必要に応じて、[こちらの手順](https://golang.org/doc/install)に従います。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。
* 既存の Azure Event Hubs 名前空間とイベント ハブ。 これらのエンティティは、[こちらの記事](event-hubs-create.md)の手順に従って作成できます。

## <a name="install-go-package"></a>Go パッケージをインストールする

`go get` または `dep` を使用して、Event Hubs 用の Go パッケージを取得します。 例: 

```bash
go get -u github.com/Azure/azure-event-hubs-go
go get -u github.com/Azure/azure-amqp-common-go/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
```

## <a name="import-packages-in-your-code-file"></a>コード ファイルにパッケージをインポートする

Go パッケージをインポートするには、次のコード例を使用します。

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
)
```

## <a name="create-service-principal"></a>サービス プリンシパルの作成

「[Azure CLI 2.0 で Azure サービス プリンシパルを作成する](/cli/azure/create-an-azure-service-principal-azure-cli)」の手順に従って、新しいサービス プリンシパルを作成します。 次の名前を使用して指定された資格情報を環境内に保存します。 Azure SDK for Go と Event Hubs パッケージは、どちらもこれらの変数名を検索するために事前に構成されています。

```bash
export AZURE_CLIENT_ID=
export AZURE_CLIENT_SECRET=
export AZURE_TENANT_ID=
export AZURE_SUBSCRIPTION_ID= 
```

次に、これらの資格情報を使用する Event Hubs クライアントに対して、認証プロバイダーを作成します。

```go
tokenProvider, err := aad.NewJWTProvider(aad.JWTProviderWithEnvironmentVars())
if err != nil {
    log.Fatalf("failed to configure AAD JWT provider: %s\n", err)
}
```

## <a name="create-event-hubs-client"></a>Event Hubs クライアントを作成する

次のコードは、Event Hubs クライアントを作成します。

```go
hub, err := eventhubs.NewHub("namespaceName", "hubName", tokenProvider)
ctx := context.WithTimeout(context.Background(), 10 * time.Second)
defer hub.Close(ctx)
if err != nil {
    log.Fatalf("failed to get hub %s\n", err)
}
```

## <a name="send-messages"></a>メッセージを送信する

次のスニペットでは、(1) を使用してターミナルから対話形式でメッセージを送信する、または (2) を使用してプログラム内でメッセージを送信します。

```go
// 1. send messages at the terminal
ctx = context.Background()
reader := bufio.NewReader(os.Stdin)
for {
    fmt.Printf("Input a message to send: ")
    text, _ := reader.ReadString('\n')
    hub.Send(ctx, eventhubs.NewEventFromString(text))
}

// 2. send messages within program
ctx = context.Background()
hub.Send(ctx, eventhubs.NewEventFromString("hello Azure!")
```

## <a name="extras"></a>追加

イベント ハブのパーティションの ID を取得します。

```go
info, err := hub.GetRuntimeInformation(ctx)
if err != nil {
    log.Fatalf("failed to get runtime info: %s\n", err)
}
log.Printf("got partition IDs: %s\n, info.PartitionIDs)
```

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のページを参照してください。

* [EventProcessorHost を使用してイベントを受信する](event-hubs-go-get-started-receive-eph.md)
* [Event Hubs の概要][Event Hubs overview]
* [イベント ハブの作成](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
