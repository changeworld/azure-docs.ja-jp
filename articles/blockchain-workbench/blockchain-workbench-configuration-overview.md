---
title: Azure Blockchain Workbench 構成リファレンス
description: Azure Blockchain Workbench アプリケーションの構成の概要。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 3/20/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 6318b6aafda972242239ce91abb0f67a15721e1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2018
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 構成リファレンス

 Azure Blockchain Workbench アプリケーションは、構成メタデータとスマート コントラクト コードで定義されたマルチパーティ ワークフローです。 構成メタデータでは、ブロックチェーン アプリケーションの大まかなワークフローと対話モデルを定義します。 スマート コントラクトを使用して、ブロックチェーン アプリケーションのビジネス ロジックを定義します。 Workbench は、構成とスマート コントラクト コードを使用して、ブロックチェーン アプリケーションのユーザー エクスペリエンスを生成します。

構成のメタデータには、各ブロックチェーン アプリケーションの次の情報が指定されます。 

* ブロックチェーン アプリケーションの名前と説明
* ブロックチェーン アプリケーション内で操作または参加できるユーザーに固有のロール
* 1 つ以上のワークフロー。 各ワークフローは、ビジネス ロジックのフローを制御する状態マシンとして機能します。 ワークフローは独立している場合と、相互作用がある場合があります。

定義される各ワークフローには、次の情報が指定されます。

* ワークフローの名前と説明。
* ワークフローの状態。  各状態は、ビジネス ロジックの制御フローの 1 段階です。 
* 次の状態に遷移するアクション
* 各アクションの開始が許可されているユーザー ロール
* コード ファイル内のビジネス ロジックを表すスマート コントラクト

## <a name="application"></a>アプリケーション

ブロックチェーン アプリケーションには、構成メタデータ、ワークフロー、アプリケーション内で操作または参加できるユーザー ロールが含まれています。

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| ApplicationName | 一意のアプリケーション名。 対応するスマート コントラクトでは、該当するコントラクト クラスに対して同じ **ApplicationName** を使用する必要があります。  | [はい] |
| DisplayName | アプリケーションのわかりやすい表示名。 | [はい] |
| [説明] | アプリケーションの説明。 | いいえ  |
| ApplicationRoles | [ApplicationRoles](#application-roles) のコレクション。 アプリケーション内で操作または参加できるユーザー ロール。  | [はい] |
| ワークフロー | [Workflows](#workflows) のコレクション。 各ワークフローは、ビジネス ロジックのフローを制御する状態マシンとして機能します。 | [はい] |

例については、「[構成ファイルの例](#configuration-file-example)」を参照してください。

## <a name="workflows"></a>ワークフロー

アプリケーションのビジネス ロジックは、アクションを実行するとビジネス ロジックのフローが別の状態に移行するという、状態マシンとしてモデル化することができます。 ワークフローは、そのような状態とアクションのコレクションです。 各ワークフローは 1 つ以上のスマート コントラクトで構成されています。スマート コントラクトは、コード ファイル内のビジネス ロジックを表します。 実行可能なコントラクトは、ワークフローのインスタンスです。

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| Name | 一意のワークフロー名。 対応するスマート コントラクトでは、該当するコントラクト クラスに対して同じ **Name** を使用する必要があります。 | [はい] |
| DisplayName | ワークフローのわかりやすい表示名。 | [はい] |
| [説明] | ワークフローの説明。 | いいえ  |
| Initiators | [ApplicationRoles](#application-roles) のコレクション。 ワークフローでコントラクトを作成する権限を持つユーザーに割り当てられるロール。 | [はい] |
| StartState | ワークフローの初期状態の名前。 | [はい] |
| [プロパティ] | [識別子](#identifiers)のコレクション。 ユーザー エクスペリエンス ツールでオフチェーンの読み取りまたは視覚化を実行できるデータを表します。 | [はい] |
| コンストラクター | ワークフローのインスタンスを作成するための入力パラメーターを定義します。 | [はい] |
| Functions | ワークフローで実行できる[関数](#functions)のコレクション。 | [はい] |
| States | ワークフローの[状態](#states)のコレクション。 | [はい] |

例については、「[構成ファイルの例](#configuration-file-example)」を参照してください。

## <a name="type"></a>type

サポートされているデータ型。

| type | [説明] |
|-------|-------------|
| address  | *contracts*、*users* などのブロックチェーン アドレスの種類 |
| bool     | ブール データ型 |
| contract | 型のコントラクトのアドレス |
| int      | 整数データ型 |
| money    | 通貨データ型 |
| state    | ワークフローの状態 |
| 文字列   | 文字列データ型 |
| user     | 型ユーザーのアドレス |
| time     | 時間データ型 |
|`[ Application Role Name ]`| アプリケーション ロールに指定する任意の名前。 ユーザーをそのロールの種類に制限します。 |

## <a name="constructor"></a>コンストラクター

ワークフローのインスタンスの入力パラメーターを定義します。

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| parameters | スマート コントラクトを開始するために必要な[識別子](#identifiers)のコレクション。 | [はい] |

### <a name="constructor-example"></a>コンストラクターの例

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}

```

## <a name="functions"></a>Functions

ワークフローに対して実行できる関数を定義します。

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| Name | 関数の一意の名前。 対応するスマート コントラクトでは、該当する関数に対して同じ **Name** を使用する必要があります。 | [はい] |
| DisplayName | 関数のわかりやすい表示名。 | [はい] |
| [説明] | 関数の説明。 | いいえ  |
| parameters | 関数のパラメーターに対応する[識別子](#identifiers)のコレクション。 | [はい] |

### <a name="functions-example"></a>関数の例

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>States

ワークフロー内の一意の状態のコレクション。 各状態で、ビジネス ロジックの制御フローの手順がキャプチャされます。 

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| Name | 状態の一意の名前。 対応するスマート コントラクトでは、該当する状態に対して同じ **Name** を使用する必要があります。 | [はい] |
| DisplayName | 状態のわかりやすい表示名。 | [はい] |
| [説明] | 状態の説明。 | いいえ  |
| PercentComplete | Blockchain Workbench のユーザー インターフェイスに表示される整数値。ビジネス ロジックの制御フロー内の進行状況を示します。 | [はい] |
| Style | 状態が成功か失敗かを表すビジュアル ヒント。 有効な値は `Success` または `Failure` です。 | [はい] |
| 遷移 | 現在の状態から次の一連の状態への使用できる[遷移](#transitions)のコレクション。 | いいえ  |

### <a name="states-example"></a>状態の例

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been cancelled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>遷移

次の状態に使用できるアクションです。 1 つ以上のユーザー ロールが各状態でアクションを実行できます。このアクションで、ワークフロー内の状態が別の状態に遷移することがあります。 

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| AllowedRoles | 移行を開始できるアプリケーション ロールの一覧。 指定されたロールのすべてのユーザーがアクションを実行できる可能性があります。 | いいえ  |
| AllowedInstanceRoles | 遷移の開始が許可されているスマート コントラクトに参加している、または指定されているユーザー ロールのリスト。 インスタンス ロールは、ワークフロー内の **Properties** に定義されています。 これらのユーザーは、ロールの種類のすべてのユーザーではなく、スマート コントラクトに参加している、または指定されているユーザーを表します。 | いいえ  |
| DisplayName | 遷移のわかりやすい表示名。 | [はい] |
| [説明] | 遷移の説明。 | いいえ  |
| 関数 | 遷移を開始する関数の名前。 | [はい] |
| NextStates | 遷移が成功した後の次の状態候補のコレクション。 | [はい] |

### <a name="transitions-example"></a>遷移の例

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>アプリケーション ロール

アプリケーション ロールには、アプリケーション内で操作または参加するユーザーに割り当てることができる一連のロールを定義します。 アプリケーション ロールを使用して、ブロックチェーン アプリケーションとそれに対応するワークフロー内の操作と参加を制限することができます。 

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| Name | アプリケーション ロールの一意の名前。 対応するスマート コントラクトでは、該当するロールに対して同じ **Name** を使用する必要があります。 基本データ型は予約されています。 アプリケーション ロールに [Type](#type) と同じ名前を付けることはできません| [はい] |
| [説明] | アプリケーション ロールの説明。 | いいえ  |

### <a name="application-roles-example"></a>アプリケーション ロールの例

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>識別子

識別子は、ワークフローのプロパティ、コンストラクター、および関数のパラメーターを記述するために使用される情報のコレクションを表しています。 

| フィールド | [説明] | 必須 |
|-------|-------------|:--------:|
| Name | プロパティまたはパラメーターの一意の名前。 対応するスマート コントラクトでは、該当するプロパティまたはパラメーターに対して同じ **Name** を使用する必要があります。 | [はい] |
| DisplayName | プロパティまたはパラメーターのわかりやすい表示名。 | [はい] |
| [説明] | プロパティまたはパラメーターの説明。 | いいえ  |

### <a name="identifiers-example"></a>識別子の例

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>構成ファイルの例

次の例では、要求元が要求を送信し、レスポンダーが応答を要求に送信する基本的な要求/応答アプリケーションを定義します。

``` json
{
  "ApplicationName": "HelloBlockchain",
  "DisplayName": "Hello, Blockchain!",
  "Description": "A simple application to send request and get response",
  "ApplicationRoles": [
    {
      "Name": "Requestor",
      "Description": "A person sending a request."
    },
    {
      "Name": "Responder",
      "Description": "A person responding to a request"
    }
  ],
  "Workflows": [
    {
      "Name": "RequestResponse",
      "DisplayName": "Request Response",
      "Description": "A simple workflow to send a request and receive a response.",
      "Initiators": [ "Requestor" ],
      "StartState": "Request",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract.",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Requestor",
          "DisplayName": "Requestor",
          "Description": "A person sending a request.",
          "Type": {
            "Name": "Requestor"
          }
        },
        {
          "Name": "Responder",
          "DisplayName": "Responder",
          "Description": "A person sending a response.",
          "Type": {
            "Name": "Responder"
          }
        },
        {
          "Name": "RequestMessage",
          "DisplayName": "Request Message",
          "Description": "A request message.",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "ResponseMessage",
          "DisplayName": "Response Message",
          "Description": "A response message.",
          "Type": {
            "Name": "string"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "message",
            "Description": "...",
            "DisplayName": "Request Message",
            "Type": {
              "Name": "string"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "SendRequest",
          "DisplayName": "Request",
          "Description": "...",
          "Parameters": [
            {
              "Name": "requestMessage",
              "Description": "...",
              "DisplayName": "Request Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        },
        {
          "Name": "SendResponse",
          "DisplayName": "Response",
          "Description": "...",
          "Parameters": [
            {
              "Name": "responseMessage",
              "Description": "...",
              "DisplayName": "Response Message",
              "Type": {
                "Name": "string"
              }
            }
          ]
        }
      ],
      "States": [
        {
          "Name": "Request",
          "DisplayName": "Request",
          "Description": "...",
          "PercentComplete": 50,
          "Value": 0,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": ["Responder"],
              "AllowedInstanceRoles": [],
              "Description": "...",
              "Function": "SendResponse",
              "NextStates": [ "Respond" ],
              "DisplayName": "Send Response"
            }
          ]
        },
        {
          "Name": "Respond",
          "DisplayName": "Respond",
          "Description": "...",
          "PercentComplete": 90,
          "Value": 1,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": ["Requestor"],
              "Description": "...",
              "Function": "SendRequest",
              "NextStates": [ "Request" ],
              "DisplayName": "Send Request"
            }
          ]
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデプロイ](blockchain-workbench-deploy.md)

