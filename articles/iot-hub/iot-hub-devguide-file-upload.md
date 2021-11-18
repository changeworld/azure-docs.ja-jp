---
title: Azure IoT Hub ファイルのアップロードについて | Microsoft Docs
description: 開発者ガイド - IoT Hub のファイルのアップロード機能を使用して、デバイスから Azure Storage Blob コンテナーへのファイルのアップロードを管理します。
author: eross-msft
ms.author: lizross
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/25/2021
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 36ecdb4fc1442447e1f0e80019db6039556e75e4
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718208"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub を使用したファイルのアップロード

デバイスデータを、IoT Hub が受け入れる比較的小さいデバイスからクラウドへのメッセージに簡単にマップできないシナリオは多数あります。 たとえば、大きなメディアファイルを送信したり、断続的に接続されたデバイスによってアップロードされた、または帯域幅を節約するために集約および圧縮された大きなテレメトリバッチを送信したりします。

デバイスから大きなファイルをアップロードする必要がある場合、IoT Hub のセキュリティと信頼性も利用できます。 IoT Hub 自体を介してメッセージを仲介する代わりに、IoT Hub は、関連付けられている Azure Storage アカウントへのディスパッチャーとして機能します。 また IoT Hub は、デバイスがファイルのアップロードを完了したときにバックエンドサービスに通知を提供することもできます。

報告されるプロパティ、デバイスからクラウドへのメッセージ、またはファイルのアップロードをいつ使用するかの決定については、「[デバイスからクラウドへの通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)」を参照してください。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="file-upload-overview"></a>ファイルのアップロードの概要

IoT hub は、接続されているデバイスからのファイルのアップロードを容易にします。これには、ハブで事前に構成されている blob コンテナーと Azure storage アカウントに対して、アップロードごとに shared access signature (SAS) Uri を指定します。 IoT Hub によるファイルのアップロードの使用には、次の3つの部分があります。 IoT hub での Azure storage アカウントと blob コンテナーの事前構成、デバイスからのファイルのアップロード、および必要に応じて、完了したファイルのアップロードのバックエンドサービスへの通知を行います。

IoT Hub でファイルのアップロード機能を使用するには、最初に [Azure storage アカウント](../storage/common/storage-account-overview.md) と [BLOB コンテナー](../storage/blobs/storage-blobs-introduction.md) を IoT ハブに関連付ける必要があります。 また、Azure  storage を使用した認証方法、IoT hub がデバイスに送信する SAS Uri の有効期限 (TTL)、バックエンドサービスへのファイルアップロード通知の IoT Hub を制御する設定を構成することもできます。 詳細については、「[Azure storage アカウントを IoT Hub に関連付ける](#associate-an-azure-storage-account-with-iot-hub)」を参照してください。

デバイスは、3つの手順からなるプロセスに従って、関連付けられた blob コンテナーにファイルをアップロードします：

1. デバイスは、IoT hub を使用したファイルのアップロードを開始します。 要求に blob の名前を渡し、SAS URI と相関 ID を取得します。 SAS URI には、blob コンテナー内の要求された blob に対する読み取り/書き込みアクセス許可をデバイスに付与する Azure storage の SAS トークンが含まれています。 詳細については、「[デバイス: ファイルのアップロードの初期化](#device-initialize-a-file-upload)」を参照してください。

1. デバイスは、SAS URI を使用して Azure blob storage Api を安全に呼び出し、blob コンテナーにファイルをアップロードします。 詳細については、「[デバイス: Azure storage APIs を使用したアップロードファイル](#device-upload-file-using-azure-storage-apis)」を参照してください。

1. ファイルのアップロードが完了すると、デバイスは、アップロードを開始したときに IoT Hub から受け取った関連付け ID を使用して、IoT hub に完了状態を通知します。 詳細については、「[デバイス: 完了したファイルのアップロードの IoT Hub 通知](#device-notify-iot-hub-of-a-completed-file-upload)」を参照してください。

バックエンドサービスは、IoT hub のサービス向けファイルアップロード通知エンドポイントでファイルアップロード通知をサブスクライブできます。 IoT hub でこれらの通知を有効にした場合、ファイルのアップロードが完了したことをデバイスがハブに通知するたびに、このエンドポイントに通知が配信されます。 サービスは、これらの通知を使用して、blob データをさらにトリガーすることができます。 詳細については、「[サービス: ファイルアップロード通知](#service-file-upload-notifications)」を参照してください。

ファイルのアップロードは、Azure IoT デバイスとサービスのSDKs によって完全にサポートされています。 詳細については、「[SDK を使用したファイルのアップロード](#file-upload-using-an-sdk)」を参照してください。

### <a name="file-upload-quotas-and-limits"></a>ファイルアップロードのクォータと制限

IoT Hub では、指定された期間内に開始できるファイルアップロード数にスロットリング制限が課されます。 しきい値は、IoT hub の SKU とユニット数に基づいています。 さらに、各デバイスは一度に10個のアクティブファイルの同時アップロードに制限されています。 情報の詳細については、「[スロットリングとクォータ](iot-hub-devguide-quotas-throttling.md)」を参照してください。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage アカウントと IoT Hub の関連付け

ファイルアップロード機能を使用するには、Azure storage アカウントと sblob コンテナーを IoT hub に関連付ける必要があります。 IoT hub に登録されているデバイスからのすべてのファイルアップロードは、このコンテナーに送られます。 IoT hub でストレージアカウントと blob コンテナーを構成するには、「[Azure portal を使用したファイルのアップロードの構成](iot-hub-configure-file-upload.md)」、「[Azure CLI を使用したファイルの アップロードの構成](iot-hub-configure-file-upload-cli.md)」、または「[PowerShell を使用したファイルのアップロードの構成](iot-hub-configure-file-upload-powershell.md)」をご覧ください。 また、IoT Hub 管理 APIs を使用して、プログラムでファイルのアップロードを構成することもできます。

ポータルを使用する場合は、構成中にストレージアカウントとコンテナーを作成できます。 それ以外の場合、ストレージアカウントを作成するには、Azure storage のドキュメントの「[ストレージアカウントの作成](../storage/common/storage-account-create.md)」を参照してください。 ストレージアカウントを作成したら、[Azure blob storage のクイックスタート](../storage/blobs/storage-quickstart-blobs-portal.md)で blob コンテナーを作成する方法を確認できます。

ファイルのアップロードやファイルのアップロード通知の動作を制御する他の設定がいくつかあります。 次のセクションでは、使用可能なすべての設定を示します。 Azure portal、Azure CLI、PowerShell、または管理 APIs のいずれを使用してファイルのアップロードを構成するかによって、これらの設定の一部が使用できなくなる場合があります。 ファイルのアップロードが完了したときにバックエンドサービスに通知が送信されるようにする場合は、 **ファイルアップロード通知有効** 設定を必ず設定してください。

### <a name="iot-hub-storage-and-authentication-settings"></a>Iot Hub のストレージと認証の設定

次の設定では、ストレージアカウントとコンテナーを IoT hub に関連付け、ハブが Azure storage でどのように認証されるかを制御します。 これらの設定は、Azure storage でのデバイスの認証方法には影響しません。 デバイスは常に、IoT Hub から取得した SAS URI に示されている SAS トークンを使用して認証されます。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **storageEndpoints.$default.authenticationType** | IoT Hub が Azure storage でどのように認証されるかを制御します。 | 使用可能な値は keyBased と identity Based です。 既定値は keyBased です。 |
| **storageEndpoints. $default. connectionString** | ファイルのアップロードに使用する Azure ストレージアカウントへの接続文字列。 | 既定値 : 空文字列。 |
| **storageEndpoints.$default.containerName** | ファイルのアップロード先となるコンテナーの名前。 | 既定値 : 空文字列。 |
| **storageEndpoints.$default.identity** | Id ベースの認証に使用するマネージ id。 | 使用できる値は `[system]`システムによって割り当てられたマネージド id、またはユーザーが割り当てられたマネージド id のリソース ID です。 この値は、キーベースの認証には使用されません。 既定値 : NULL。 |

### <a name="file-upload-settings"></a>ファイルアップロードの設定

次の設定は、デバイスからのファイルのアップロードを制御します。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **storageEndpoints.$default.ttlAsIso8601** | IoT Hub によって生成される SAS Uri の既定の TTL。 | ISO_8601 間隔は最大48時間（最小1分）。 既定値: 1 時間。 |

### <a name="file-upload-notification-settings"></a>ファイルアップロードの通知設定

次の設定は、バックエンドサービスへのファイルアップロード通知を制御します。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **enableFileUploadNotifications** |ファイルのアップロード通知をファイル通知エンドポイントに書き込むかどうかを制御します。 |ブール値。 既定値は False です。 |
| **fileNotifications.ttlAsIso8601** |ファイルのアップロード通知の既定の TTL。 |ISO_8601 間隔は最大48時間（最小1分）。 既定値: 1 時間。 |
| **fileNotifications.lockDuration** |ファイルのアップロード通知キューのロック期間。 |5 から 300 秒です。 既定値: 60 秒。 |
| **fileNotifications.maxDeliveryCount** |ファイルのアップロード通知キューの最大配信数。 |1 ～ 100。 既定値: 100。 |

## <a name="file-upload-using-an-sdk"></a>SDK を使用したファイルのアップロード

次のハウツーガイドは、Azure IoTデバイスとサービスSDKを使用してファイルをアップロードするための完全なステップバイステップの手順を提供します。 Azure portal を使用してストレージ アカウントを IoT ハブに関連付ける方法を示し、コード スニペットが含まれているか、アップロードをガイドするサンプルを参照します。

| ハウツー ガイド | デバイス SDK の例 | サービス SDK の例 |
|---------|--------|---------|
| [.NET](iot-hub-csharp-csharp-file-upload.md) | はい | はい |
| [Java](iot-hub-java-java-file-upload.md) | はい | はい |
| [Node.js](iot-hub-node-node-file-upload.md) | はい | はい |
| [Python](iot-hub-python-python-file-upload.md) | はい | いいえ (サポートされていません） |

> [!NOTE]
> C デバイス SDK は、デバイスクライアントで 1 回の呼び出しを使用してファイルのアップロードを実行します。 詳細については、「[IoTHubDeviceClient_UploadToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadtoblobasync)」および「[IoTHubDeviceClient_UploadMultipleBlocksToBlobAsync()](/azure/iot-hub/iot-c-sdk-ref/iothub-device-client-h/iothubdeviceclient-uploadmultipleblockstoblobasync)」を参照してください。 これらの関数は、1 回の呼び出しでファイルアップロードのすべてのアスペクトを実行します。つまり、アップロードを開始し、ファイルを Azure Storage にアップロードし、完了したら IoT Hub に通知します。 つまり、デバイスが IoT Hub との通信に使用しているプロトコルに加えて、これらの関数が Azure Storage API を呼び出すので、HTTPS 経由で Azure Storage と通信できる必要があります。

## <a name="device-initialize-a-file-upload"></a>デバイス: ファイルアップロードを初期化する

デバイスは、1つのデバイス SDKsで[アップロード SAS URI](/rest/api/iothub/device/create-file-upload-sas-uri) REST APIまたは同等の API を呼び出して、ファイルのアップロードを開始します。

**サポートされているプロトコル**: AMQP、AMQP-WS、MQTT、MQTT-WS、及びHTTPS <br/>
**エンドポイント**: {iot hub}.azure-devices.net/devices/{deviceId}/files <br/>
**メソッド**: POST

```json
{
    "blobName":"myfile.txt"
}

```

| プロパティ | 説明 |
|----------|-------------|
| BlobName | SAS URI を生成する BLOB の名前。 |

 IoT Hub、関連付け ID と、デバイスが Azure Storage での認証に使用できる SAS URI の要素で応答します。 この応答には、ターゲット IoT hubのスロットリング制限とデバイスごとのアップロード制限が適用されます。

```json
{
    "correlationId":"MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "hostName":"contosostorageaccount.blob.core.windows.net",
    "containerName":"device-upload-container",
    "blobName":"mydevice/myfile.txt",
    "sasToken":"?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw"
}

```

| プロパティ | 説明 |
|----------|-------------|
| correlationId | ファイルアップロードが完了した通知をIoT Hubに送信するときに使用するデバイスの識別子。 |
| hostName | IoT hubで構成されたストレージアカウントのAzure storage アカウントのホスト名 |
| containerName | IoT hubで構成されている BLOB コンテナーの名前。 |
| BlobName | Blob がコンテナーに保存される場所。 名前は次のフォーマットにあります：`{device ID of the device making the request}/{blobName in the request}` |
| sasToken | Azure Storage を使用して BLOB に対する読み取り/書き込みアクセスを許可する SAS トークン。 トークンはIoT Hub によって生成および署名されます。 |

応答を受信すると、デバイスは:

* アップロードが完了したら、IoT hubへのファイルアップロード完了通知に含める関連付け ID を保存します。

* 他のプロパティを使用して、Azure Storage での認証に使用する BLOB の SAS URI を作成します。 SAS URI には、要求された BLOB のリソース URI と SAS トークンが含まれています。 次の形式を取る: `https://{hostName}/{containerName}/{blobName}{sasToken}`(応答の`sasToken`プロパティには先頭に '?' 文字が含まれます)。中かっこは含まれません。

    たとえば、上記のサンプルで返された値の場合、SAS URI は、`https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw`

    SAS URI と SAS トークンの詳細については、Azure Storage ドキュメントの[サービス SASの作成](/rest/api/storageservices/create-service-sas)に関するページを参照してください。

## <a name="device-upload-file-using-azure-storage-apis"></a>デバイス: Azure storage APIs を使用してファイルをアップロードする

デバイスは、[Azure Blob Storage REST APIs](/rest/api/storageservices/blob-service-rest-api) または同等の Azure Storage SDK APIs を使用して、Azure Storage 内の BLOB にファイルをアップロードします。

**サポートされるプロトコル**: HTTPS

次の例は、小さなブロックBLOB を作成または更新する [Put BLOB](/rest/api/storageservices/put-blob)要求を示しています。 この要求に使用されるURI は、前のセクションで指定した要求によってIoT Hub SAS URI です。 `x-ms-blob-type` ヘッダーは、この要求がブロック BLOB に対する要求を示します。 要求が成功すると、Azure Storage からが返されます `201 Created`。

```http
PUT https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt?sv=2018-03-28&sr=b&sig=mBLiODhpKXBs0y9RVzwk1S...l1X9qAfDuyg%3D&se=2021-07-30T06%3A11%3A10Z&sp=rw HTTP/1.1
Content-Length: 11
Content-Type: text/plain; charset=UTF-8
Host: contosostorageaccount.blob.core.windows.net
x-ms-blob-type: BlockBlob

hello world
```

Azure storage APIs の操作は、この記事の範囲を超えています。 このセクションで前にリンクした Azure Blob Storage REST API に加えて、次のドキュメントを確認して、使用を開始できます：

* Azure Storage での BLOB の操作の詳細については、[Azure Blob Storage](../storage/blobs/index.yml) のドキュメントを参照 してください。

* Azure Storage クライアント SDKs を使用して BLOB をアップロードする方法については、「[Azure Blob Storage API リファレンス](../storage/blobs/reference.md)」を参照してください。  

## <a name="device-notify-iot-hub-of-a-completed-file-upload"></a>デバイス: 完了したファイルアップロードをIoT Hubに通知します

デバイスは、ファイルのアップロードが完了するとき、1つのデバイス SDKsで[更新ファイルアップロード状態](/rest/api/iothub/device/update-file-upload-status) REST APIまたは同等の API を呼び出します。 デバイスは、アップロードが成功するか失敗するかに関係なく、IoT Hubファイルのアップロード状態を更新する必要があります。

**サポートされているプロトコル**: AMQP、AMQP-WS、MQTT、MQTT-WS、及びHTTPS <br/>
**エンドポイント**:  {iot hub}.azure-devices.net/devices/{deviceId}/files/notifications <br/>
**メソッド**: POST 

```json
{
    "correlationId": "MjAyMTA3MzAwNjIxXzBiNjgwOGVkLWZjNzQtN...MzYzLWRlZmI4OWQxMzdmNF9teWZpbGUudHh0X3ZlcjIuMA==",
    "isSuccess": true,
    "statusCode": 200,
    "statusDescription": "File uploaded successfully"
}

```

 プロパティ | 説明 |
|----------|-------------|
| correlationId | 初期 SAS URI 要求で受信した関連付け ID。 |
| isSuccess | ファイルのアップロードが成功したかどうかを示すブール値。 |
| statusCode | ファイルのアップロードの状態コードを表す整数。 通常は 3 桁の数字です。たとえば、200 または 201 です。 |
| statusDescription | ファイルのアップロード状態の説明。 |

デバイスからファイルのアップロード完了通知を受信したら、IoT Hub：

* ファイルのアップロード通知が構成されている場合は、バックエンド サービスへのファイルのアップロード通知をトリガーします。

* ファイルのアップロードに関連付けられているリソースを解放します。 通知を受信しない場合IoT Hubはアップロードに関連付けられている SAS URI Time-to-Live (TTL) の有効期限が切れるまでリソースが保持されます。

## <a name="service-file-upload-notifications"></a>サービス：ファイルのアップロード通知

IoT hub でファイルのアップロード通知が有効になっている場合、ファイルのアップロードが完了したという通知をデバイスから受信すると、バックエンド サービスの通知メッセージが生成されます。 IoT Hubはサービスに接続するエンドポイントを介してこれらのファイル アップロード通知を配信します。 ファイルのアップロード通知の受信セマンティクスは cloud-to-device メッセージの場合と同様であり、[メッセージのライフ サイクル](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)も同じです。 サービス SDKs は、ファイルのアップロード通知を処理する API を公開します。

**サポートされているプロトコル** AMQP、AMQP-WS <br/>
**エンドポイント**:  {iot hub}.azure-devices.net/messages/servicebound/fileuploadnotifications <br/>
**メソッド** GET

ファイルのアップロード通知エンドポイントから取得した各メッセージは、次のプロパティを持つ JSON レコードです：

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://contosostorageaccount.blob.core.windows.net/device-upload-container/mydevice/myfile.txt",
    "blobName":"mydevice/myfile.txt",
    "lastUpdatedTime":"2021-07-31T00:26:50+00:00",
    "blobSizeInBytes":11,
    "enqueuedTimeUtc":"2021-07-31T00:26:51.5134008Z"
}
```

| プロパティ | 説明 |
| --- | --- |
| enqueuedTimeUtc | 通知が作成された日時を示すタイムスタンプ。 |
| deviceId | ファイルをアップロードしたデバイスのデバイス ID。 |
| BlobUri | アップロードされたファイルの URI。 |
| BlobName | アップロードされたファイルの名前。 名前は次のフォーマットにあります： `{device ID of the device}/{name of the blob}`|
| lastUpdatedTime |ファイルが最後に更新された日時を示すタイムスタンプ。 |
| BlobSizeInBytes | アップロードされたファイルのサイズをバイト単位で表す整数。 |

サービスは、通知を使用してアップロードを管理できます。 たとえば、blob データの独自の処理をトリガーしたり、他の Azure サービスを使用して blob データの処理をトリガーしたり、後でレビューするためにファイルのアップロード通知をログに記録したりすることができます。

## <a name="next-steps"></a>次の手順

* [ファイル アップロードのハウツー ガイド](iot-hub-csharp-csharp-file-upload.md)

* [IoT Hub でファイルのアップロードを構成する方法](iot-hub-configure-file-upload.md)

* [IoT Hub のマネージド ID を構成する方法](iot-hub-managed-identity.md)

* [Azure Blob Storage のドキュメント](../storage/blobs/index.yml)

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。