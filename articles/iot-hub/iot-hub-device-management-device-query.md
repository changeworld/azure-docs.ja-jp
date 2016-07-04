<properties
	pageTitle="IoT Hub デバイス管理のツインのクエリ | Microsoft Azure"
	description="Azure IoT Hub デバイス管理のチュートリアルで、クエリを使用してデバイス ツインを検索する方法を説明します。"
	services="iot-hub"
	documentationCenter=".net"
	authors="juanjperez"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# チュートリアル: C# でクエリを使用したデバイス ツインの検索方法 (プレビュー)

[AZURE.INCLUDE [iot-hub-device-management-query-selector](../../includes/iot-hub-device-management-query-selector.md)]

Azure IoT デバイス管理では、デバイス ツイン (物理デバイスのサービス表現) をクエリを使って検索できます。クエリは、デバイス ツインのデバイス プロパティ、サービス プロパティ、またはタグに基づいて実行できます。タグやプロパティを使用してクエリを実行できます。

-   タグを使用してデバイス ツインのクエリを実行するには、文字列の配列を渡します。クエリは、これらの文字列のすべてにタグ付けされているデバイスのセットを返します。

-   サービス プロパティまたはデバイス プロパティを使用してデバイス ツインのクエリを実行するには、JSON クエリ式を使用します。

デバイス ツインとクエリの詳細については、「[Azure IoT Hub デバイス管理の概要][lnk-dm-overview]」を参照してください。

## デバイス クエリのサンプルを実行する

次の例では、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」チュートリアル機能が拡張されます。まず別々のシミュレートされたデバイスを実行してから、クエリを使用して特定のデバイスを検索します。

### 前提条件 

このサンプルを実行する前に、「[Azure IoT Hub デバイス管理の使用][lnk-get-started]」の手順を完了する必要があります。つまり、シミュレートされたデバイスを実行する必要があります。事前にプロセスを完了済みの場合は、ここでシミュレートされたデバイスを再起動してください。

### サンプルを開始する

サンプルを開始するには、**Query.exe** プロセスを実行する必要があります。これにより、いくつかの異なるクエリが実行されます。次の手順に従って、サンプルを開始してください。

1.  シミュレートされたデバイスを少なくとも 1 分間実行します。これにより、ツインのデバイス プロパティが物理デバイスと同期します。同期の詳細については、「[チュートリアル: デバイス ツインの使用方法][lnk-twin-tutorial]」を参照してください。

2.  **azure-iot-sdks** リポジトリを複製したルート フォルダーから **azure-iot-sdks\\csharp\\service\\samples\\bin** フォルダーに移動します。

3.  `Query.exe <IoT Hub Connection String>` を実行します。

タグ、サービス プロパティ、デバイス プロパティを使用したデバイス オブジェクトのクエリ結果を示す出力がコマンド ライン ウィンドウに表示されます。

## クエリの構造 (JSON)

デバイス プロパティおよびサービス プロパティに対するクエリは、クエリ自体を表す JSON 文字列により実行されます。JSON 文字列は 4 つの部分から構成されます。以下に、各部分の説明と、正しい JSON 文字列にシリアル化できる C# オブジェクトを示します。

- **Project**: クエリ結果セットに含めるデバイス オブジェクトからのフィールドを指定する式 (SQL の SELECT に相当)。

  ```
	  var query = JsonConvert.SerializeObject(
		  new
		  {
			  project = new
			  {
				  all = false,
				  properties = new[]
				  {
					  new
					  {
					  name = "CustomerId",
					  type = "service"
					  },
					  new
					  {
					  name = "Weight",
					  type = "service"
					  }
				  }
			  }
		  }
	  );
	```

- **Filter**: クエリ結果セットに含めるデバイス オブジェクトを制限する式 (SQL の WHERE に相当)。

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
        filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = "123456",
          comparisonOperator = "eq",
          type = "comparison"
        }
      }
  );
  ```

- **Aggregate**: クエリ結果セットをグループ化する方法を決定する式 (SQL の GROUPBY に相当)。

  ```
  var query = JsonConvert.SerializeObject(
      new
      {
      filter = new
        {
          property = new
          {
            name = "CustomerId",
            type = "service"
          },
          value = (string)null,
          comparisonOperator = "ne",
          type = "comparison"
        },
        aggregate = new
        {
          keys = new[]
          {
            new
            {
              name = "CustomerId",
              type = "service"
            }
          },
          properties = new[]
          {
            new
            {
              @operator = "avg",
              property = new
              {
                name = "Weight",
                type = "service"
              },
              columnName = "TotalWeight"
            }
          }
        }
      }
  );
  ```

- **Sort**: クエリ結果セットの並べ替えに使用するプロパティの式の定義 (SQL の ORDER BY に相当)。sort が null の場合は、既定で **deviceID** が使用されます。

  ```
  var query = JsonConvert.SerializeObject(
    new
    {
      sort = new[]
      {
        new
        {
          property = new
          {
            name = "QoS",
            type = "service"
          },
          order = "asc"
        }
      }
    }
  );
  ```

### 制限事項

パブリック プレビューのクエリの実装には、いくつかの制限事項があります。

-   クエリ式の検証はありません。

-   クエリでは大文字と小文字が区別されます。

-   サービス プロパティまたはデバイス プロパティでクエリを実行するクエリ式の場合は、100 個のデバイスのみが返されます。ページングの実装例については、[クエリ ライブラリ][lnk-query-samples]を参照してください。

JSON の構文と使用可能なフィールドの詳細については、[こちら][lnk-query-expression-guide]を参照してください。また、[クエリ式ライブラリ][lnk-query-samples]にはサンプル クエリも含まれています。

### デバイス プロパティとサービス プロパティを使用したクエリ

JSON クエリ式を作成したら、デバイス ツインのクエリを実行できます。**QueryDeviceJsonAsync** を呼び出し、aggregate クエリについては **AggregateResult** フィールド、その他すべてのクエリについては **Result** フィールドをチェックします。**Result** には、クエリに一致するデバイス ツインを表すデバイス オブジェクトの一覧が含まれています。**AggregateResult** のディクショナリの配列には、結果の行が含まれています。

これらのクエリは **Query** プロジェクトの **Program.cs** で使用されています。

```
var foundDevices = (await registryManager.QueryDevicesJsonAsync(query)).Result;

var results = (await registryManager.QueryDevicesJsonAsync(query)).AggregateResult;
```

### タグを使用したクエリ

タグによるクエリでは、JSON クエリ式を使用せずにデバイス オブジェクトを検索することができます。複数のタグが渡された場合は、すべてのタグを持つデバイス オブジェクトのみが返されます。2 番目のパラメーター **maxCount** は、返されるデバイスの最大数を表します。**maxCount** の最大値は 1000 です。

```
var foundDevices = await registryManager.QueryDevicesAsync(new[] { "bacon" }, 100);
```

### デバイスの実装

クエリは、Azure IoT Hub デバイス管理クライアント ライブラリによって有効になります。デバイス プロパティが同期している限り (「[チュートリアル: デバイス ツインの使用方法][lnk-twin-tutorial]」を参照)、それらのプロパティでクエリを実行できます。デバイス プロパティは、物理デバイスが IoT Hub に接続され、初期値が指定された後にのみ使用できます。

## 次のステップ

Azure IoT Hub デバイス管理機能の詳細については、次のチュートリアルに進んでください。

- [デバイス ツインの使用方法][lnk-twin-tutorial]
- [デバイスのジョブを使用して、デバイスのファームウェアを更新する方法][lnk-jobs-tutorial]



<!-- images and links -->
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twin-tutorial]: iot-hub-device-management-device-twin.md
[lnk-jobs-tutorial]: iot-hub-device-management-device-jobs.md
[lnk-query-spec]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-query-expression-guide]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/node/service/devdoc/query_expression_requirements.md

<!---HONumber=AcomDC_0622_2016-->