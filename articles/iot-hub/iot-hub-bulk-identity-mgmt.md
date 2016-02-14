<properties
 pageTitle="IoT Hub デバイス ID のインポートとエクスポート | Microsoft Azure"
 description="IoT Hub デバイス ID の一括管理に関する概念および .NET コード スニペット"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/03/2016"
 ms.author="dobett"/>

# IoT Hub デバイス ID の一括管理

各 IoT Hub には、転送中の C2D メッセージを含むキューなど、サービスでデバイスごとのリソースを作成するのに使用し、デバイス向けのエンドポイントにアクセスできるデバイス ID レジストリがあります。この記事では、デバイス ID レジストリとの間でデバイス ID を一括でインポートおよびエクスポートする方法について説明します。

インポートおよびエクスポート操作は、ユーザーが IoT Hub に対する一括サービス操作を実行するのを可能にする*ジョブ*のコンテキストで行われます。

**RegistryManager** クラスには、**ジョブ** フレームワークを使用する **ExportDevicesAsync** および **ImportDevicesAsync** メソッドが含まれています。これらのメソッドを使用すると、IoT Hub デバイス レジストリ全体のエクスポート、インポート、および同期化を行うことができます。

## ジョブとは

デバイス ID レジストリの操作では、次の場合に**ジョブ** システムを使用します。

*  操作の実行時間が、標準のランタイム操作と比べて長くなる可能性がある。
*  操作で大量のデータがユーザーに返される。

このような場合、操作では、結果が得られるまで単一の API 呼び出しを待機させたりブロックしたりするのでなく、該当する IoT Hub 用に**ジョブ**を非同期に作成し、その後すぐに **JobProperties** オブジェクトを返します。

次の C# コード スニペットでは、エクスポート ジョブの作成方法を示します。

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

**RegistryManager** クラスを使用すると、返された **JobProperties** メタデータを基に **ジョブ** の状態を照会することができます。

次の C# コード スニペットでは、5 秒ごとにポーリングして、ジョブの実行が完了したかどうかを確認する方法を示します。

```
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## デバイスのエクスポート

**ExportDevicesAsync** メソッドでは、[Shared Access Signature](https://msdn.microsoft.com/library/ee395415.aspx) を使用して IoT Hub デバイス レジストリ全体を [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB コンテナーにエクスポートすることができます。

このメソッドでは、制御対象の BLOB コンテナーにデバイス情報のバックアップを確実に作成することができます。

**ExportDevicesAsync** メソッドには、次の 2 つのパラメーターが必要です。

*  BLOB コンテナーの URI が格納される *文字列*。この URI には、コンテナーに対する書き込みアクセスを付与する SAS トークンを含める必要があります。ジョブでは、デバイスのシリアル化されたエクスポート データを格納するために、このコンテナー内にブロック BLOB を作成します。SAS トークンには、次のアクセス許可を含める必要があります。
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

*  エクスポート データから認証キーを除外するかどうかを示す*ブール*。**false** の場合は、認証キーがエクスポート出力に含められます。true の場合、キーは **null** としてエクスポートされます。

次の C# コード スニペットでは、エクスポート ジョブを開始し、ジョブの完了をポーリングする方法を示します。

```
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

ジョブは、その出力を、指定された BLOB コンテナー内に **devices.txt** という名前のブロック BLOB として格納します。出力データは、JSON のシリアル化されたデバイス データで構成され、1 行につき 1 つのデバイスが配置されます。

出力データの例を次に示します。

```
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

コード内のこのデータにアクセスする必要がある場合、**ExportImportDevice** クラスを使用すると、データを簡単に逆シリアル化できます。次の C# コード スニペットでは、以前にブロック BLOB にエクスポートしたデバイス情報を読み取る方法を示します。

```
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), RequestOptions, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

> [AZURE.NOTE]  また、**RegistryManager** クラスの **GetDevicesAsync** メソッドを使用して、デバイスの一覧を取得することができます。ただし、この方法では、返されるデバイス オブジェクトの数は 1000 に制限されます。**GetDevicesAsync** メソッドの用途は、開発シナリオでデバッグを支援することを想定しており、運用環境のワークロードに対しての使用はお勧めできません。

## デバイスのインポート

**RegistryManager** クラスの **ImportDevicesAsync** メソッドを使用すると、IoT Hub デバイス レジストリの一括インポートおよび同期化操作を実行することができます。**ExportDevicesAsync** メソッドと同様に、**ImportDevicesAsync** メソッドでもジョブ フレームワークを使用します。

**ImportDevicesAsync** メソッドを使用する場合は注意が必要です。このメソッドでは、デバイス ID レジストリ内に新しいデバイスをプロビジョニングするほか、既存のデバイスを更新および削除する可能性もあるからです。

> [AZURE.WARNING]  インポート操作は元に戻すことができません。デバイス ID レジストリに対して一括変更を加える場合は、必ず事前に **ExportDevicesAsync** メソッドを使用して既存のデータを別の BLOB コンテナーにバックアップしておく必要があります。

**ImportDevicesAsync** メソッドには、次の 2 つのパラメーターが必要です。

*  [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB コンテナーの URI を、ジョブへの*入力*として格納する*文字列*。この URI には、コンテナーに対する読み取りアクセスを付与する SAS トークンを含める必要があります。このコンテナーには、デバイス ID レジストリにインポートするシリアル化されたデバイス データが入っている **devices.txt** という名前の BLOB を含める必要があります。インポート データには、**ExportImportDevice** ジョブの場合と同じ JSON 形式でデバイス情報を含める必要があります。これは、SAS トークンには、次のアクセス許可を含める必要があります。

    ```
    SharedAccessBlobPermissions.Read
    ```

*  [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) BLOB コンテナーの URI を、ジョブからの*出力*として格納する*文字列*。ジョブは、このコンテナー内にブロック BLOB を作成して、完了したインポート **ジョブ**から返されたエラー情報を格納します。SAS トークンには、次のアクセス許可を含める必要があります。
    
    ```
    SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
    ```

> [AZURE.NOTE]  この 2 つのパラメーターは、同じ BLOB コンテナーを指すことができます。出力コンテナーで追加のアクセス許可が必要な場合は、個々のパラメーターでデータのより細かな制御を簡単に実現できます。

次の C# コード スニペットでは、インポート ジョブの開始方法を示します。

```
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

## インポートの動作

**ImportDevicesAsync** メソッドを使用して、デバイス ID レジストリで次の一括操作を実行することができます。

-   新しいデバイスの一括登録
-   既存のデバイスの一括削除
-   状態の一括変更 (デバイスの有効化または無効化)
-   新しいデバイス認証キーの一括割り当て
-   デバイス認証キーの一括自動再生成

上記の操作の任意の組み合わせを、1 回の **ImportDevicesAsync** 呼び出しで実行できます。たとえば、新しいデバイスの登録と、既存のデバイスの削除または更新とを同時に行うことができます。**ExportDevicesAsync** メソッドと一緒に使用すると、すべてのデバイスを IoT Hub 間で完全に移行できます。

デバイスごとにインポート プロセスを制御するには、デバイスごとのインポート シリアル化データにオプションの **importMode** プロパティを使用します。**importMode** プロパティには、次のオプションが用意されています。

| importMode | 説明 |
| -------- | ----------- |
| **createOrUpdate** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合、既存の情報は、**ETag** 値に関係なく、指定した入力データで上書きされます。 |
| **create** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合は、エラーがログ ファイルに書き込まれます。 |
| **update** | 指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値に関係なく、既存の情報は指定した入力データで上書きされます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **updateIfMatchETag** | 指定した **ID** を持つデバイスが既に存在する場合、**ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。<br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |
| **createOrUpdateIfMatchETag** | 指定した **ID** を持つデバイスが存在しない場合は、新たに登録されます。<br/>該当するデバイスが既に存在する場合、**ETag** 値が一致した場合に限り、既存の情報は指定した入力データで上書きされます。<br/>**ETag** 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |
| **delete** | 指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値に関係なく削除されます。<br/>該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。 |
| **deleteIfMatchETag** | 指定した **ID** を持つデバイスが既に存在する場合、そのデバイスは **ETag** 値が一致した場合に限り削除されます。該当するデバイスが存在しない場合は、エラーがログ ファイルに書き込まれます。<br/>ETag 値が不一致である場合は、ログ ファイルにエラーが書き込まれます。 |

> [AZURE.NOTE] シリアル化データで、デバイスの **importMode** フラグが明示的に定義されていない場合、インポート操作中、既定値では **createOrUpdate** が使用されます。

## デバイスのインポートの例 – デバイスの一括プロビジョニング 

次の C# コード サンプルでは、認証キーを含む複数のデバイス ID を生成し、そのデバイス情報を Azure Storage ブロック BLOB に書き込み、さらにそれらのデバイスをデバイス ID レジストリにインポートする方法を示します。

```
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
// Create a new ExportImportDevice
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to existing list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write this list to the Azure storage blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the same storage blob to add new devices!
// This normally takes 1 minute per 100 devices the normal way
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## デバイスのインポートの例 – 一括削除

次のコード サンプルでは、前述のコード サンプルを使用して追加したデバイスを削除する方法を示します。

```
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same storage blob to delete all devices!
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}

```

## コンテナーの SAS URI の取得


次のコード サンプルでは、BLOB コンテナーに対する読み取り、書き込み、および削除アクセス許可を使用して [SAS URI](../storage/storage-dotnet-shared-access-signature-part-2.md) を生成する方法を示します。

```
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}

```

## 次のステップ

この記事では、IoT Hub のデバイス ID レジストリに対して一括操作を実行する方法について説明しました。IoT Hub の機能や、他の IoT のシナリオを次の記事でさらに詳しく説明しています。

- [C# プログラムを使って IoT Hub を作成する](iot-hub-rm-template.md)
- [IoT Hub usage metrics (IoT Hub の使用状況に関するメトリック)](iot-hub-metrics.md)
- [IoT Hub 操作の監視](iot-hub-operations-montoring.md)

<!---HONumber=AcomDC_0204_2016-->