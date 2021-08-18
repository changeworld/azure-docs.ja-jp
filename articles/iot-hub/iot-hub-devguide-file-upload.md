---
title: Azure IoT Hub ファイルのアップロードについて | Microsoft Docs
description: 開発者ガイド - IoT Hub のファイルのアップロード機能を使用して、デバイスから Azure Storage Blob コンテナーへのファイルのアップロードを管理します。
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd7adc3a1fdb45255bd13ee279d66e64bdf4c1d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728780"
---
# <a name="upload-files-with-iot-hub"></a>IoT Hub を使用したファイルのアップロード

デバイスから送信されたデータを、IoT Hub ですぐに受け入れられる、比較的小さい device-to-cloud メッセージに簡単にマップできない多くのシナリオがあります。 次に例を示します。
* 大きなイメージ ファイル
* ビデオ ファイル
* 高頻度でサンプリングされる振動データ
* 何らかの形式の前処理済みデータ

デバイスからそのようなファイルをアップロードする必要がある場合も、IoT Hub のセキュリティと信頼性を利用できます。 IoT Hub 自体を介してメッセージを仲介する代わりに、IoT Hub は、関連付けられている Azure Storage アカウントへのディスパッチャーとして機能します。 デバイスは、アップロードするファイルに固有のストレージ トークンを IoT Hub に要求します。 SAS URI を使用して、ファイルをストレージにアップロードし、アップロードが完了すると、IoT Hub に完了の通知を送信します。 IoT Hub では、ファイルのアップロードが完了したことが確認されます。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>使用する場合

ファイルのアップロードを使用して、断続的に接続されたデバイスでアップロードされた、または帯域幅を節約するために圧縮されたメディア ファイルや大容量のテレメトリ バッチを送信します。 報告プロパティ、Device-to-cloud メッセージ、またはファイルのアップロードのどれを使用するべきか不明な場合は、[デバイスからクラウドへの通信に関するガイダンス](iot-hub-devguide-d2c-guidance.md)を参照してください。

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Azure Storage アカウントと IoT Hub の関連付け

IoT ハブに関連付けられた Azure Storage アカウントを持っている必要があります。 

ポータルを使用して作成する方法については、「[ストレージ アカウントを作成する](../storage/common/storage-account-create.md)」を参照してください。 

[IoT Hub リソース プロバイダー REST API](/rest/api/iothub/iothubresource) を使用して、プログラムを使って作成することもできます。 

Azure Storage アカウントを IoT ハブに関連付けると、IoT ハブによって SAS URI が生成されます。 デバイスは、この SAS URI を使って安全にファイルを BLOB コンテナーにアップロードすることができます。

## <a name="create-a-container"></a>コンテナーを作成する

 ポータルを使用して BLOB コンテナーを作成するには、次のようにします。

1. ストレージ アカウントの左ペインで、 **[データ ストレージ]** の下の **[コンテナー]** を選択します。
1. [コンテナー] ブレードで、 **[+ コンテナー]** を選択します。
1. 開いた **[新しいコンテナー]** ペインで、コンテナーに名前を付け、 **[作成]** を選択します。

コンテナーを作成したら、「[Azure portal を使用してファイルのアップロードを構成する](iot-hub-configure-file-upload.md)」の手順に従います。 ご利用の IoT ハブに BLOB コンテナーが関連付けられていること、またファイル通知が有効になっていることを確認します。

[IoT Hub リソース プロバイダー REST API](/rest/api/iothub/iothubresource) を使用して、IoT Hub のストレージに関連付けられているコンテナーを作成することもできます。

## <a name="file-upload-using-an-sdk"></a>SDK を使用したファイルのアップロード

次の攻略ガイドでは、さまざまな SDK 言語でのファイルのアップロード プロセスに関する完全なチュートリアルを提供します。 これらのガイドでは、Azure portal を使用してストレージ アカウントを IoT ハブに関連付ける方法が示されます。 また、コード スニペットが含まれており、アップロード プロセスをガイドするサンプルを紹介します。

* [.NET](iot-hub-csharp-csharp-file-upload.md)
* [Java](iot-hub-java-java-file-upload.md)
* [Node.js](iot-hub-node-node-file-upload.md)
* [Python](iot-hub-python-python-file-upload.md)

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) では、共有アクセス署名 URI の取得、ファイルのアップロード、および IoT Hub へのアップロード完了の通知を自動的に処理します。 ファイアウォールによって BLOB ストレージ エンドポイントへのアクセスがブロックされていても、IoT Hub エンドポイントへのアクセスが許可されている場合、ファイルのアップロード プロセスは失敗し、IoT C# デバイス SDK に対して次のエラーが表示されます。
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> ファイルのアップロード機能を機能させるには、デバイスで IoT Hub エンドポイントと BLOB ストレージ エンドポイントの両方にアクセスできる必要があります。
> 


## <a name="initialize-a-file-upload-rest"></a>ファイルのアップロードを初期化する (REST)

SDK の 1 つではなく REST API を使用して、ファイルをアップロードできます。 IoT Hub には、ファイルをアップロードするためのストレージの SAS URI を要求する、特にデバイス向けのエンドポイントがあります。 ファイルのアップロード プロセスを開始するために、デバイスは以下の JSON 本文を含む POST 要求を `{iot hub}.azure-devices.net/devices/{deviceId}/files` に送信します。

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub は次のデータを返します。デバイスはこのデータを使用してファイルをアップロードします。

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>非推奨: ファイルのアップロードの初期化

> [!NOTE]
> このセクションでは、IoT Hub から SAS URI を 受信するための非推奨の機能について説明しています。 前述の POST メソッドを使用します。

IoT Hub には、ファイルのアップロードをサポートする 2 つの REST エンドポイントがあります。1 つは、ストレージの SAS URI を取得します。もう 1 つは、アップロードの完了を IoT Hub に通知します。 デバイスは、`{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` の IoT ハブに GET を送信して、ファイルのアップロード プロセスを開始します。 IoT Hub は以下を返します。

* アップロードするファイルに固有の SAS URI

* アップロードの完了後に使用される関連付け ID

## <a name="notify-iot-hub-of-a-completed-file-upload-rest"></a>IoT Hub にファイルのアップロード完了を通知する (REST)

デバイスは、Azure Storage SDK を使用してストレージにファイルをアップロードします。 アップロードが完了すると、デバイスは、次の JSON 本文を含む POST 要求を `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` に送信します。

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

`isSuccess` の値は、ファイルが正常にアップロードされたかどうかを示すブール値です。 状態コード `statusCode` は、ストレージに対するファイルのアップロードの状態であり、`statusDescription` は `statusCode` に対応します。

## <a name="reference-topics"></a>参照トピック:

以下の参照トピックは、デバイスからのファイルのアップロードに関する詳細情報を提供しています。

### <a name="file-upload-notifications"></a>ファイルのアップロード通知

必要に応じて、デバイスがアップロードの完了を IoT Hub に通知すると、IoT Hub によって通知メッセージが生成されます。 このメッセージには、ファイルの名前とストレージの場所が含まれています。

「[エンドポイント](iot-hub-devguide-endpoints.md)」で説明したように、IoT Hub はサービス向けエンドポイント (**/messages/servicebound/fileuploadnotifications**) 経由でメッセージとしてファイルのアップロード通知を配信します。 ファイルのアップロード通知の受信セマンティクスは cloud-to-device メッセージの場合と同様であり、[メッセージのライフ サイクル](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle)も同じです。 ファイルのアップロード通知エンドポイントから取得した各メッセージは、次のプロパティを持つ JSON レコードです。

| プロパティ | 説明 |
| --- | --- |
| EnqueuedTimeUtc |通知が作成された日時を示すタイムスタンプ。 |
| deviceId |**DeviceId** 。 |
| BlobUri |アップロードされたファイルの URI。 |
| BlobName |アップロードされたファイルの名前。 |
| LastUpdatedTime |ファイルが最後に更新された日時を示すタイムスタンプ。 |
| BlobSizeInBytes |アップロードされたファイルのサイズ。 |

**例**。 次の例は、ファイルのアップロード通知メッセージの本文を示しています。

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

### <a name="file-upload-notification-configuration-options"></a>ファイルのアップロード通知の構成オプション

各 IoT ハブには、ファイルのアップロード通知用に次の構成オプションが用意されています。

| プロパティ | 説明 | 範囲と既定値 |
| --- | --- | --- |
| **enableFileUploadNotifications** |ファイルのアップロード通知をファイル通知エンドポイントに書き込むかどうかを制御します。 |ブール値。 既定値: True。 |
| **fileNotifications.ttlAsIso8601** |ファイルのアップロード通知の既定の TTL。 |最大 48 時間の ISO_8601 書式による間隔 (最小 1 分)。 既定値: 1 時間。 |
| **fileNotifications.lockDuration** |ファイルのアップロード通知キューのロック期間。 |5 ～ 300 秒 (最小 5 秒)。 既定値: 60 秒。 |
| **fileNotifications.maxDeliveryCount** |ファイルのアップロード通知キューの最大配信数。 |1 ～ 100。 既定値: 100。 |

IoT ハブ上でこれらのプロパティを設定するには、Azure portal、Azure CLI、または PowerShell を使用します。 詳細については、[ファイル アップロードの構成](iot-hub-configure-file-upload.md)に関する記事を参照してください。

## <a name="additional-reference-material"></a>参考資料

IoT Hub 開発者ガイド内の他の参照トピックは次のとおりです。

* [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md): 実行時および管理操作用のさまざまな IoT Hub エンドポイントについて説明します。

* [スロットルとクォータ](iot-hub-devguide-quotas-throttling.md): IoT Hub サービスに適用されるクォータとスロットルの動作について説明します。

* [Azure IoT device SDK とサービス SDK](iot-hub-devguide-sdks.md): IoT Hub とやりとりするデバイスとサービス アプリの両方を開発する際に使用できるさまざまな言語の SDK を紹介します。

* [IoT Hub のクエリ言語](iot-hub-devguide-query-language.md): IoT Hub からデバイス ツインとジョブに関する情報を取得する際に使用できるクエリ言語について説明します。

* [IoT Hub の MQTT サポート](iot-hub-mqtt-support.md): IoT Hub での MQTT プロトコルのサポートについて詳しく説明します。

## <a name="next-steps"></a>次のステップ

IoT Hub を使用してデバイスからファイルをアップロードする方法を理解できたら、次の IoT Hub 開発者ガイドのトピックも参考にしてください。

* [IoT Hub でデバイス ID を管理する](iot-hub-devguide-identity-registry.md)

* [IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)

* [デバイス ツインを使って状態と構成を同期する](iot-hub-devguide-device-twins.md)

* [デバイスでダイレクト メソッドを呼び出す](iot-hub-devguide-direct-methods.md)

* [複数デバイスでのジョブをスケジュール設定する](iot-hub-devguide-jobs.md)

この記事で説明した概念を試すには、次の IoT Hub のチュートリアルをご覧ください。

* [IoT Hub でデバイスからクラウドにファイルをアップロードする方法](iot-hub-csharp-csharp-file-upload.md)
