---
title: Go を使用して Azure Event Hubs からイベントを受信する | Microsoft Docs
description: Go を使用して Event Hubs からイベントの受信を開始する
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/23/2018
ms.author: shvija
ms.openlocfilehash: eaea6adbaef7baf9bb1e617ba0a709cf14edf781
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005580"
---
# <a name="receive-events-from-event-hubs-using-go"></a>Go を使用して Event Hubs からイベントを受信する

Azure Event Hubs は、拡張性の高いイベント管理システムで、1 秒あたり何百万ものイベントを処理することができます。そのためアプリケーションは、接続されているデバイスや他のシステムによって生成された大量のデータを処理し、分析できます。 イベント ハブに収集されたイベントは、インプロセス ハンドラーを使用するか、他の分析システムに転送して、受信し処理することができます。

Event Hubs の詳細については、[Event Hubs の概要][Event Hubs overview]に関する記事を参照してください。

このチュートリアルでは、Go アプリケーションでイベント ハブからイベントを受信する方法について説明します。 イベントを送信する方法については、[対応する送信の記事](event-hubs-go-get-started-send.md)を参照してください。

このチュートリアルのコードは、[これらの GitHub サンプル](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/eventhubs)から取得されたもので、import ステートメントや変数の宣言など、アプリケーションが完全に機能するかを確認することができます。

その他の例については、[Event Hubs パッケージのリポジトリ](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples)から入手できます。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件が必要です。

* Go がローカルにインストールされていること。 必要に応じて、[こちらの手順](https://golang.org/doc/install)に従います。
* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][] を作成してください。
* メッセージを受信するには、ターゲットのイベント ハブにメッセージがある必要があります。 メッセージの送信方法については、[送信チュートリアル](event-hubs-go-get-started-send.md)で学習してください。
* 既存のイベント ハブ (次のセクションを参照)。
* 既存のストレージ アカウントとコンテナー (次のセクション後のセクションを参照)。

### <a name="create-an-event-hub"></a>イベント ハブの作成

このチュートリアルでは、既存の Event Hubs 名前空間とイベント ハブを使用して開始します。 これらのエンティティは、[こちらの記事](event-hubs-create.md)の手順に従って作成できます。

### <a name="create-a-storage-account-and-container"></a>Storage アカウントとコンテナーの作成

パーティションのリースやイベント ストリーム内のチェックポイントなどの状態は、Azure Storage コンテナーを使用して受信者間で共有されます。 Go SDK を使用してストレージ アカウントとコンテナーを作成できますが、「[Azure ストレージ アカウントについて](../storage/common/storage-create-storage-account.md)」の手順に従って作成することもできます。

Go SDK を使用して Storage の成果物を作成するためのサンプルは、[Go のサンプル リポジトリ](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/storage)と、このチュートリアルに対応するサンプルで入手できます。

## <a name="receive-messages"></a>メッセージを受信する

メッセージを受信するには、`go get` または `dep` 使用して Event Hubs 用の Go パッケージを取得します。

```bash
go get -u github.com/Azure/azure-event-hubs-go/...
go get -u github.com/Azure/azure-amqp-common-go/...
go get -u github.com/Azure/go-autorest/...

# or

dep ensure -add github.com/Azure/azure-event-hubs-go
dep ensure -add github.com/Azure/azure-amqp-common-go
dep ensure -add github.com/Azure/go-autorest
```

## <a name="import-packages-in-your-code-file"></a>コード ファイルにパッケージをインポートする

Go パッケージをインポートするには、次のコード例を使用します。

```go
import (
    aad "github.com/Azure/azure-amqp-common-go/aad"
    eventhubs "github.com/Azure/azure-event-hubs-go"
    eph "github.com/Azure/azure-event-hubs-go/eph"
    storageLeaser "github.com/Azure/azure-event-hubs-go/storage"
    azure "github.com/Azure/go-autorest/autorest/azure"
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

## <a name="get-metadata-struct"></a>メタデータ構造体の取得

Azure Go SDK を使用して、Azure 環境に関するメタデータを含む構造体を取得します。 その後の操作で、この構造体を使用して正しいエンドポイントを見つけます。

```go
azureEnv, err := azure.EnvironmentFromName("AzurePublicCloud")
if err != nil {
    log.Fatalf("could not get azure.Environment struct: %s\n", err)
}
```

## <a name="create-credential-helper"></a>資格情報ヘルパーを作成する 

前の Azure Active Directory (AAD) の資格情報を使用して Storage 用に Shared Access Signature (SAS) 資格情報を作成する資格情報ヘルパーを作成します。 最後のパラメーターは、このコンストラクターに以前に使用したのと同じ環境変数を使用するように指示します。

```go
cred, err := storageLeaser.NewAADSASCredential(
    subscriptionID,
    resourceGroupName,
    storageAccountName,
    storageContainerName,
    storageLeaser.AADSASCredentialWithEnvironmentVars())
if err != nil {
    log.Fatalf("could not prepare a storage credential: %s\n", err)
}
```

## <a name="create-checkpointer-and-leaser"></a>Checkpointer と Leaser を作成する 

特定の受信者へのパーティションのリースを担当する **Leaser** と、他の受信者が適切なオフセットから読み取りを開始できるように、メッセージ ストリームへのチェックポイントの書き込みを担当する **Checkpointer** を作成します。

現時点では、同じ Storage コンテナーを使用してリースとチェックポイントの両方を管理する、1 つの **StorageLeaserCheckpointer** を使用できます。 **StorageLeaserCheckpointer** がコンテナーに正しくアクセスするためには、ストレージ アカウントとコンテナー名の他に、前の手順で作成した資格情報と Azure 環境の構造体が必要です。

```go
leaserCheckpointer, err := storageLeaser.NewStorageLeaserCheckpointer(
    cred,
    storageAccountName,
    storageContainerName,
    azureEnv)
if err != nil {
    log.Fatalf("could not prepare a storage leaserCheckpointer: %s\n", err)
}
```

## <a name="construct-event-processor-host"></a>イベント プロセッサ ホストを構築する

これで、次のような、EventProcessorHost の構築に必要な部分が揃いました。 前述したように、同じ **StorageLeaserCheckpointer** が、Leaser と Checkpointer の両方に使用されます。

```go
ctx := context.Background()
p, err := eph.New(
    ctx,
    nsName,
    hubName,
    tokenProvider,
    leaserCheckpointer,
    leaserCheckpointer)
if err != nil {
    log.Fatalf("failed to create EPH: %s\n", err)
}
defer p.Close(context.Background())
```

## <a name="create-handler"></a>ハンドラーを作成する 

次に、ハンドラーを作成し、それをイベント プロセッサ ホストに登録します。 ホストは、起動すると、このハンドラーとその他の指定されたハンドラーを受信メッセージに適用します。

```go
handler := func(ctx context.Context, event *eventhubs.Event) error {
    fmt.Printf("received: %s\n", string(event.Data))
    return nil
}

// register the handler with the EPH
_, err := p.RegisterHandler(ctx, handler)
if err != nil {
    log.Fatalf("failed to register handler: %s\n", err)
}
```

## <a name="receive-messages"></a>メッセージを受信する

設定のたびに、`Start(context)` を使用してイベント プロセッサ ホストを起動して永続的に実行させることも、`StartNonBlocking(context)` を使用してメッセージが使用可能な間だけ実行することもできます。

このチュートリアルでは、次のように起動して実行します。`StartNonBlocking` を使用した例については、GitHub のサンプルを参照してください。

```go
ctx := context.Background()
err = p.Start()
if err != nil {
    log.Fatalf("failed to start EPH: %s\n", err)
}
```

## <a name="notes"></a>メモ

このチュートリアルでは、 **EventProcessorHost**の単一のインスタンスを使用します。 スループットと信頼性を向上させるには、**EventProcessorHost** の複数のインスタンスを異なるシステムで実行する必要があります。 Leaser システムにより、確実に 1 人の受信者だけが関連付けられ、指定された時間に指定したパーティションからメッセージを受信するようにします。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のページを参照してください。

* [Go を使用してイベントを送信する](event-hubs-go-get-started-send.md)
* [Event Hubs の概要](event-hubs-about.md)
* [Event Hub を作成する](event-hubs-create.md)
* [Event Hubs の FAQ](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[無料アカウント]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
