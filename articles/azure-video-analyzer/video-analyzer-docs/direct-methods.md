---
title: Azure Video Analyzer でダイレクト メソッドを使用する - Azure
description: Azure Video Analyzer では、いくつかのダイレクト メソッドが公開されています。 ダイレクト メソッドは、このトピックで説明する規則に基づいています。
ms.topic: conceptual
ms.date: 05/06/2021
ms.openlocfilehash: 06d19047ae20fc752a09a28b2508cfdd2b08eb8d
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387311"
---
# <a name="azure-video-analyzer-direct-methods"></a>Azure Video Analyzer のダイレクト メソッド

Azure Video Analyzer の IoT Edge モジュール`avaedge`では、IoT Hub から呼び出すことができるダイレクト メソッドがいくつか公開されています。 ダイレクト メソッドは、デバイスとの要求/応答型通信を表し、すぐに要求の成功または失敗が確定する (ユーザーが指定したタイムアウト後) という点で HTTP 呼び出しに似ています。 この方法は、デバイスが応答できるかどうかに応じて即座に実行するアクションが異なるシナリオで便利です。 詳細については、「[IoT Hub からのダイレクト メソッドの呼び出しについて](../../iot-hub/iot-hub-devguide-direct-methods.md)」を参照してください。

このトピックでは、これらのメソッドと規則について説明します。

## <a name="conventions"></a>規則

ダイレクト メソッドは、次の規則に基づいています。

1. ダイレクト メソッドには、MAJOR.MINOR 形式で指定されたバージョンがあります (次の例を参照)。 この例では 、@apiVersion の数字は "1" が MAJOR で "0" は MINOR です。

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}",
        "properties": {
            // Desired Topology properties
        }
    }
  }
```

2. Video Analyzer モジュールの任意のバージョンでは、現在のバージョンまでのダイレクト メソッド呼び出しのすべてのマイナー バージョンがサポートされます。 メジャー バージョン間のサポートは保証されません。
3. すべてのダイレクト メソッドは同期的です。
4. エラーの結果は、[OData エラー スキーマ](http://docs.oasis-open.org/odata/odata-json-format/v4.01/odata-json-format-v4.01.html#sec_ErrorResponse)に基づいて行います。
5. 名前は、次の制約に従う必要があります。
    
    * 英数字とダッシュのみ。ただし、先頭と末尾にはダッシュを使用できません
    * スペースは使用できません
    * 最大 32 文字

### <a name="example-of-response-from-a-direct-method"></a>ダイレクト メソッドからの応答の例

```
-----------------------  Request: livePipelineList  --------------------------------------------------

{
  "@apiVersion": "1.0"
}

---------------  Response: livePipelineList - Status: 200  ---------------

{
  "value": []
}

--------------------------------------------------------------------------
```    
### <a name="error-codes"></a>エラー コード
次の例に示すように、ダイレクト メソッドからエラー応答を受け取った場合、トップ レベルのエラー コードが存在し、`details` の下に詳細な情報が表示されます。


```json
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "The request is invalid",
      "details": [
        {
          "code": "ApiVersionNotSupported",
          "message": "The API version '1.1' is not supported. Supported version(s): 1.0."
        }
      ]
    }
  }
}
```

トップ レベルで使用されるエラー コードを次に示します。

|Status |コード   |Message|
|---|---|---|
|400|   BadRequest| Request is not valid (要求は無効です)|
|400|   InvalidResource|    リソースが無効です|
|400|   InvalidVersion| API のバージョンが無効です|
|402|   ConnectivityRequired    |エッジ モジュールが適切に機能するには、クラウド接続が必要です。|
|404|   NotFound    |リソースが見つかりませんでした|
|409|   Conflict|   操作が競合しています|
|500|   InternalServerError|    内部サーバー エラー|
|503|   ServerBusy| サーバー ビジー|

詳細レベルで使用されるエラー コードの一部を次に示します。

|Status|    詳細コード   |説明|
|---|---|---|
|400|   PipelineValidationError|    サイクルやパーティション分割などの一般的なパイプライン エラー。|
|400|   ModuleValidationError|  モジュール固有の検証エラー。|
|409|   PipelineTopologyInUse|  パイプライン トポロジは、1 つ以上のライブ パイプラインによって引き続き参照されます。|
|409|   OperationNotAllowedInState| 要求された操作は、現在の状態では実行できません。|
|409|   ResourceValidationError|    参照されたリソース (例: ビデオ リソース) は、有効な状態ではありません。|

## <a name="supported-direct-methods"></a>サポートされているダイレクト メソッド  
Video Analyzer エッジ モジュールによって公開されるダイレクト メソッドを次に示します。

### <a name="pipelinetopologylist"></a>pipelineTopologyList

このダイレクト メソッドは、作成されたパイプライン トポロジの全一覧を示します。

#### <a name="request"></a>Request

```
{
  "@apiVersion": "1.0"
}
```
#### <a name="response"></a>Response

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },
      
      // first pipeline topology payload
      
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      
      // next pipeline topology payload
      
    }    
  ]
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code |
|--|--|
| エンティティが見つかりました | 200 |
| 一般的なユーザー エラー | 400 番台 |
| エンティティが見つかりません | 404 |
| 一般的なサーバー エラー | 500 番台 |

### <a name="pipelinetopologyset"></a>pipelineTopologySet

そのようなトポロジが存在しない場合は、指定された名前を持つパイプライントポロジを作成するか、その名前を持つ既存のトポロジを更新します。

重要な点:

* パイプライン トポロジは、それを参照するライブパイプラインがない場合は自由に更新できます。
* 次の場合に限り、参照しているすべてのライブ パイプラインを非アクティブ化すると、トポロジを自由に更新できます。

    * 新しく追加されるパラメーターが既定値である
    * 削除されるパラメーターがどのパイプラインによっても参照されていない
* ライブ パイプラインがアクティブな間は、一部のパイプライン トポロジの更新のみが許可されます。

#### <a name="request"></a>Request

```
  {
    "methodName": "pipelineTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}",
        "properties": {
            // Desired pipeline topology properties
        }
    }
  }
```

#### <a name="response"></a>Response

```
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{TopologyName}",
        "properties": {
            // Complete pipeline topology
        }
    }
  }
```
#### <a name="status-codes"></a>状態コード

|条件  |status code    |詳細エラー コード|
|---|---|---|
既存のエンティティが更新されました |200|   該当なし|
新しいエンティティが作成されました  |201|   該当なし|
一般的なユーザー エラー |400 番台  ||
Pipeline validation Errors  |400    |PipelineValidationError|
モジュール検証エラー|   400 |ModuleValidationError|
一般的なサーバー エラー   |500 番台  ||

### <a name="pipelinetopologyget"></a>pipelineTopologyGet

指定した名前のパイプライン トポロジが存在する場合は、それを取得します。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0",
        "name": "{TopologyName}"       
  }
```
#### <a name="response"></a>Response

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{TopologyName}",
        // Complete pipeline topology
      }
    ]
  }
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code |
|--|--|
| Success | 200 |
| 一般的なユーザー エラー | 400 番台 |
| 一般的なサーバー エラー | 500 番台 |

### <a name="pipelinetopologydelete"></a>pipelineTopologyDelete

1 つのパイプライン トポロジを削除します。

* [削除されるパイプライン トポロジを参照する](pipeline.md#pipeline-states)ライブ パイプラインは存在できないことに注意してください。 このようなライブ パイプラインがある場合は、`TopologyInUse` エラーが表示されます。

#### <a name="request"></a>Request

```
  {
    "methodName": "pipelineTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "{TopologyName}"
    }
  }
```
#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| エンティティが削除されました | 200 | 該当なし |
| エンティティが見つかりません | 204 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| パイプライン トポロジは、1 つ以上のパイプラインによって参照されます。 | 409 | PipelineTopologyInUse |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="livepipelinelist"></a>livePipelineList

すべてのライブ パイプラインを一覧表示します。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0"
  }
```
#### <a name="response"></a>Response

```
{
  "status": 200,
  "value": [
    {
      "systemData": {
        "createdAt": "2021-05-05T14:19:22.16Z",
        "lastModifiedAt": "2021-05-05T16:20:41.505Z"
      },      
      // first live pipeline payload  
    },
      "systemData": {
        "createdAt": "2021-05-06T14:19:22.16Z",
        "lastModifiedAt": "2021-05-06T16:20:41.505Z"
      },
      // next live pipeline payload
    }    
  ]
}
```
#### <a name="status-codes"></a>状態コード

| 条件 | status code |
|--|--|
| エンティティが見つかりました | 200 |
| 一般的なユーザー エラー | 400 番台 |
| エンティティが見つかりません | 404 |
| 一般的なサーバー エラー | 500 番台 |

### <a name="livepipelineset"></a>livePipelineSet

そのようなライブパイプラインが存在しない場合は、指定された名前でライブパイプラインを作成するか、その名前を持つ既存のライブパイプラインを更新します。

重要な点:

* ライブ パイプラインは、"非アクティブ化" 状態のときは自由に更新できます。 その他の状態では、一部の更新のみが許可されます。
* ライブ パイプラインは、すべての更新時に再検証されます。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}",
        "properties": {
            // Desired live pipeline properties
        }
  }
```
#### <a name="response"></a>Response

````
  {
    "status": 201,
    "payload": {
        "systemData": {
           "createdAt": "2021-05-11T18:16:46.491Z",
           "lastModifiedAt": "2021-05-11T18:16:46.491Z"
        },
        "name": "{livePipelineName}",
        "properties": {
            // Complete live pipeline
        }
    }
  }
````
#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| 既存のエンティティが更新されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 | 該当なし |
| ライブ パイプラインの検証エラー | 400 | PipelineValidationError |
| モジュール検証エラー | 400 | ModuleValidationError |
| リソース検証エラー | 409 | ResourceValidationError |
| 一般的なサーバー エラー | 500 番台 | 該当なし |

### <a name="livepipelinedelete"></a>livePipelineDelete

1 つのライブ パイプラインを削除します。

重要な点:

* 削除できるのは非アクティブ化されたライブ パイプラインのみです。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>Response

```
  {
    "status": 200,
    "payload": null
  }
```
#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| ライブ パイプラインが正常に削除されました | 200 | 該当なし |
| ライブ パイプラインが見つかりません | 204 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| パイプラインは「非アクティブ化」状態ではありません | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="livepipelineget"></a>livePipelineGet

これは liveTopologyGet に似ています。 指定した名前のライブ パイプラインが存在する場合は、それを取得します。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"       
  }
```
#### <a name="response"></a>Response

```
{
  "status": 200,
  "payload": {
    "value": [
      {
        "systemData": {
          "createdAt": "2021-05-06T10:28:04.560Z",
          "lastModifiedAt": "2021-05-06T10:28:04.560Z"
        },
        "name": "{livePipelineName}",
        // Complete live pipeline
      }
    ]
  }
}
```

#### <a name="status-codes"></a>状態コード

| 条件 | status code |
|--|--|
| Success | 200 |
| 一般的なユーザー エラー | 400 番台 |
| 一般的なサーバー エラー | 500 番台 |

### <a name="livepipelineactivate"></a>livePipelineActivate

ライブ パイプラインをアクティブ化します。 

重要な点

* ライブ パイプラインが「アクティブ化中」または「アクティブ化」状態の場合、メソッドにより返されます。 
* ライブ パイプラインに対する リスト/セット操作により、現在の状態が返されます。
* ライブ パイプラインが (一時的な) 「非アクティブ化中の」状態ではない限り、このメソッドを呼び出すことができます。
* べき等性:
    * 既に「アクティブ化」状態になっているライブ パイプラインでの `livePipelineActivate` 呼び出しは、ライブ パイプラインが非アクティブ化された場合と同じように動作します。
    * 「アクティブ」状態のパイプラインをアクティブ化すると、すぐに成功コードが返されます。

#### <a name="request"></a>Request

```
  {
        "@apiVersion": "1.0",
        "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>Response

```
{
    "status": 200,
    "payload": null
}
```
#### <a name="status-codes"></a>状態コード

| 条件 | status code | 詳細エラー コード |
|--|--|--|
| パイプラインが正常にアクティブ化されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| モジュール検証エラー | 400 | ModuleValidationError |
| リソース検証エラー | 409 | ResourceValidationError |
| ライブ パイプラインが非アクティブ化状態です | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

### <a name="livepipelinedeactivate"></a>livePipelineDeactivate

ライブ パイプラインを非アクティブ化します。 パイプラインを非アクティブ化すると、ビデオ処理が正常に非アクティブ化され、エッジに一時的に格納されたイベントとビデオがある場合は、すべてがクラウドに確実にコミットされます。

重要な点:

* メソッドからは、パイプラインが非アクティブになったときにだけ戻ります
* ライブ パイプラインが (一時的な) 「アクティブ化中の」状態ではない限り、このメソッドを呼び出すことができます。
* 非アクティブ化が行われている間、パイプラインは「非アクティブ化中」状態とみなされます。
    * ライブ パイプラインに対する リスト/セット操作により、現在の状態が返されます。
    * すべてのメディアがクラウドにアップロードされると、非アクティブ化が完了します (パイプラインに [ビデオシンク](pipeline.md#video-sink) ノードがある場合)。
* べき等性:
    * 既に「非アクティブ化中の」状態であるライブ パイプラインでの `livePipelineDeactivate` 呼び出しは、ライブ パイプラインが「アクティブ化」の状態であると同じように動作します。
    * 「不アクティブ」状態のパイプラインをア非クティブ化すると、すぐに成功コードが返されます。


#### <a name="request"></a>Request

```
  {
    "@apiVersion": "1.0",
    "name": "{livePipelineName}"
  }
```
#### <a name="response"></a>Response

```
{
 "status": 200,
 "payload": null
}
```
| 条件 | status code | 詳細エラー コード |
|--|--|--|
| パイプラインが正常にアクティブ化されました | 200 | 該当なし |
| 新しいエンティティが作成されました | 201 | 該当なし |
| 一般的なユーザー エラー | 400 番台 |  |
| パイプラインがアクティブ化中の状態です | 409 | OperationNotAllowedInState |
| 一般的なサーバー エラー | 500 番台 |  |

## <a name="next-steps"></a>次のステップ

[モジュール ツインの構成スキーマ](module-twin-configuration-schema.md)
