---
title: Azure Blockchain Workbench のメッセージ統合の概要
description: Azure Blockchain Workbench でのメッセージの使用の概要。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/12/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: f8f3584475415cf9ca19458f6da78d34df37f438
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614363"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench のメッセージング統合

Azure Blockchain Workbench は、REST API を提供するだけでなく、メッセージング ベースの統合も提供します。 Workbench は Azure Event Grid を介して台帳中心のイベントを発行し、ダウンストリームの消費者がこれらのイベントに基づいてデータを取り込んだり、行動を起こしたりすることができるようにします。 信頼性の高いメッセージングを必要とするクライアントの場合、Azure Blockchain Workbench は Azure Service Bus エンドポイントにもメッセージを送信します。

## <a name="input-apis"></a>入力 API

ユーザーの作成、コントラクトの作成、およびコントラクトの更新のために外部システムからトランザクションを開始する場合は、メッセージング入力 API を使用して、台帳に対するトランザクションを実行できます。 入力 API の使用方法を示すサンプルについては、[メッセージング統合のサンプル](https://aka.ms/blockchain-workbench-integration-sample)をご覧ください。

次に示すのは、現在使用可能な入力 API です。

### <a name="create-user"></a>ユーザーの作成

新しいユーザーを作成します。

要求には次のフィールドが必要です。

| **名前**             | **説明**                                      |
|----------------------|------------------------------------------------------|
| requestId            | クライアントによって指定された GUID                                |
| firstName            | ユーザーの名                              |
| lastName             | ユーザーの姓                               |
| emailAddress         | ユーザーの電子メール アドレス                           |
| externalId           | ユーザーの Azure AD オブジェクト ID                      |
| connectionId         | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン                            |
| messageName          | **CreateUserRequest**                               |

例:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **名前**              | **説明**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | クライアントによって指定された GUID |
| userId                | 作成されたユーザーの ID |
| userChainIdentifier   | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの **オンチェーン**  アドレスです。 |
| connectionId          | ブロックチェーン接続の一意識別子|
| messageSchemaVersion  | メッセージング スキーマ バージョン |
| messageName           | **CreateUserUpdate** |
| status                | ユーザー作成要求の状態。  成功した場合、値は **Success** です。 失敗した場合、値は **Failure** です。     |
| additionalInformation | 状態に基づいて提供される追加情報 |

**ユーザー作成**が成功した場合の Blockchain Workbench からの応答例:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>コントラクトの作成

新しいコントラクトを作成します。

要求には次のフィールドが必要です。

| **名前**             | **説明**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | クライアントによって指定された GUID |
| userChainIdentifier  | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの**オン チェーン** アドレスです。 |
| applicationName      | アプリケーションの名前 |
| workflowName         | ワークフローの名前 |
| parameters           | コントラクト作成のために入力されたパラメーター |
| connectionId         | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion | メッセージング スキーマ バージョン |
| messageName          | **CreateContractRequest** |

例:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer", 
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **名前**                 | **説明**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | クライアントによって指定された GUID                                                             |
| contractId               | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| contractLedgerIdentifier | 台帳のコントラクトのアドレス                                            |
| connectionId             | ブロックチェーン接続の一意識別子                               |
| messageSchemaVersion     | メッセージング スキーマ バージョン                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | コントラクト作成要求の状態。  使用される値: **Submitted**、**Committed**、**Failure**。  |
| additionalInformation    | 状態に基づいて提供される追加情報                              |

**コントラクト作成**が送信された後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

**コントラクト作成**がコミットされた後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>コントラクト アクションの作成

新しいコントラクト アクションを作成します。

要求には次のフィールドが必要です。

| **名前**                 | **説明**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | クライアントによって指定された GUID |
| userChainIdentifier      | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、これはユーザーの**オン チェーン** アドレスです。 |
| contractLedgerIdentifier | 台帳のコントラクトのアドレス |
| workflowFunctionName     | ワークフロー関数の名前 |
| parameters               | コントラクト作成のために入力されたパラメーター |
| connectionId             | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion     | メッセージング スキーマ バージョン |
| messageName              | **CreateContractActionRequest** |

例:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench は、次のフィールドを含む応答を返します。

| **名前**              | **説明**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | クライアントによって指定された GUID|
| contractId            | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| connectionId          | ブロックチェーン接続の一意識別子 |
| messageSchemaVersion  | メッセージング スキーマ バージョン |
| messageName           | **CreateContractActionUpdate** |
| status                | コントラクト アクション要求の状態。 使用される値: **Submitted**、**Committed**、**Failure**。                         |
| additionalInformation | 状態に基づいて提供される追加情報 |

**コントラクト アクション作成**が送信された後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

**コントラクト アクション作成**がコミットされた後の Blockchain Workbench からの応答例。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

要求が成功しなかった場合は、追加情報にエラーの詳細が示されます。

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>入力 API のエラー コードとメッセージ

**エラー コード 4000: 無効な要求のエラー**
- Invalid connectionId \(無効な connectionId です\)
- CreateUserRequest deserialization failed \(CreateUserRequest のシリアル化を解除できませんでした\)
- CreateContractRequest deserialization failed \(CreateContractRequest のシリアル化を解除できませんでした\)
- CreateContractActionRequest deserialization failed \(CreateContractActionRequest のシリアル化を解除できませんでした\)
- Application {identified by application name} does not exist \({アプリケーション名によって識別された} アプリケーションが存在しません\)
- Application {identified by application name} does not have workflow \({アプリケーション名によって識別された} アプリケーションにワークフローがありません\)
- UserChainIdentifier does not exist \(UserChainIdentifier が存在しません\)
- Contract {identified by ledger identifier} does not exist \({台帳識別子によって識別された} コントラクトが存在しません\)
- Contract {identified by ledger identifier} does not have function {workflow function name} \({台帳識別子によって識別された} コントラクトには、{ワークフロー関数名} 関数がありません\)
- UserChainIdentifier does not exist \(UserChainIdentifier が存在しません\)

**エラー コード 4090: 競合のエラー**
- ユーザーは既に存在します
- Contract already exists \(コントラクトは既に存在します\)
- Contract action already exists \(コントラクト アクションは既に存在します\)

**エラー コード 5000: 内部サーバー エラー**
- 例外メッセージ

## <a name="event-notifications"></a>イベント通知

イベント通知を使用すると、Blockchain Workbench およびそれが接続されているブロックチェーン ネットワークで発生するイベントをユーザーとダウンストリーム システムに通知することができます。 イベント通知は、コードで直接使用するか、Logic Apps や Flow などのツールでダウンストリーム システムへのデータのフローをトリガーするために使用されます。

受け取ることができるさまざまなメッセージの詳細については、「[通知メッセージのリファレンス](#notification-message-reference)」を参照してください。

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Functions での Event Grid イベントの使用

Blockchain Workbench で発生したイベントについて、ユーザーが Event Grid による通知を望む場合は、Azure Functions を使用して、Event Grid からのイベントを利用することができます。

1. Azure Portal で **Azure 関数アプリ**を作成します。
2. 新しい関数を作成します。
3. Event Grid のテンプレートを探します。 メッセージを読み取るための基本的なテンプレート コードが表示されます。 必要に応じてコードを変更します。
4. 関数を保存します。 
5. Blockchain Workbench のリソース グループから Event Grid を選択します。

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps での Event Grid イベントの使用

1.  Azure Portal で新しい **Azure Logic App** を作成します。
2.  ポータルで Azure Logic App を開くときに、トリガーの選択を求めるメッセージが表示されます。 **[Azure Event Grid -- When a resource event occurs]\(Azure Event Grid -- リソース イベントが発生するとき\)** を選択します。
3. ワークフロー デザイナーが表示されるときに、サインインを求められます。
4. サブスクリプションを選択します。 リソースとして **Microsoft.EventGrid.Topics** を選択します。 Azure Blockchain Workbench リソース グループのリソースの名前の中から、**リソース名**を選択します。
5. Blockchain Workbench のリソース グループから Event Grid を選択します。

## <a name="using-service-bus-topics-for-notifications"></a>通知の Service Bus トピックの使用

Service Bus トピックは、Blockchain Workbench で発生するイベントについてユーザーに通知するために使用できます。 

1.  Workbench のリソース グループ内の Service Bus に移動します。
2.  **[トピック]** を選択します。
3.  **[workbench-external]** を選択します。
4.  このトピックに新しいサブスクリプションを作成します。 それのキーを取得します。
5.  このサブスクリプションからのイベントにサブスクライブするプログラムを作成します。

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps による Service Bus メッセージの使用

1. Azure Portal で新しい **Azure Logic App** を作成します。
2. ポータルで Azure Logic App を開くときに、トリガーの選択を求めるメッセージが表示されます。 検索ボックスに「**Service Bus**」と入力し、Service Bus との対話の種類に適したトリガーを選択します。 たとえば、**[Service Bus -- メッセージがトピック サブスクリプションに着信したとき (オート コンプリート)]** です。
3. ワークフロー デザイナーが表示されたら、Service Bus の接続情報を指定します。
4. サブスクリプションを選択し、**workbench-external** のトピックを指定します。
5. このトリガーからのメッセージを利用するアプリケーションのロジックを開発します。

## <a name="notification-message-reference"></a>通知メッセージのリファレンス

**OperationName** に応じて、通知メッセージは次のいずれかの種類になります。

### <a name="accountcreated"></a>AccountCreated

新しいアカウントが、指定されたチェーンに追加されるように要求されたことを示します。

| Name    | 説明  |
|----------|--------------|
| UserId  | 作成されたユーザーの ID |
| ChainIdentifier | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、ユーザーの **オン チェーン** アドレスです。 |

``` csharp
public class NewAccountRequest : MessageModelBase
{
  public int UserID { get; set; }
  public string ChainIdentifier { get; set; }
}
```

### <a name="contractinsertedorupdated"></a>ContractInsertedOrUpdated

分散型台帳で契約を挿入または更新する要求が行われたことを示します。

| Name | 説明 |
|-----|--------------|
| ChainID | 要求に関連付けられているチェーンの一意の識別子 |
| BlockId | 台帳のブロックの一意の識別子 |
| ContractId | コントラクトの一意識別子 |
| ContractAddress |       台帳のコントラクトのアドレス |
| TransactionHash  |     台帳のトランザクションのハッシュ |
| OriginatingAddress |   トランザクションの発信者のアドレス |
| ActionName       |     アクションの名前 |
| IsUpdate        |      これが更新であるかどうかを示します |
| parameters       |     アクションに送信されるパラメーターの名前、値、およびデータ型を識別するオブジェクトの一覧 |
| TopLevelInputParams |  契約が 1 つ以上の他の契約に接続されているシナリオでは、これらは最上位の契約のパラメーターです。 |

``` csharp
public class ContractInsertOrUpdateRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public int ContractId { get; set; }
    public string ContractAddress { get; set; }
    public string TransactionHash { get; set; }
    public string OriginatingAddress { get; set; }
    public string ActionName { get; set; }
    public bool IsUpdate { get; set; }
    public List<ContractProperty> Parameters { get; set; }
    public bool IsTopLevelUpdate { get; set; }
    public List<ContractInputParameter> TopLevelInputParams { get; set; }
}
```

#### <a name="updatecontractaction"></a>UpdateContractAction

分散型台帳の特定の契約に対するアクションを実行する要求が行われたことを示します。

| Name                     | 説明                                                                                                                                                                   |
|--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ContractActionId         | このコントラクト アクションの一意の識別子 |
| ChainIdentifier          | チェーンの一意の識別子 |
| ConnectionId             | 接続の一意の識別子 |
| UserChainIdentifier      | ブロックチェーン ネットワーク上に作成されたユーザーのアドレス。 Ethereum では、このアドレスはユーザーの**オン チェーン** アドレスです。 |
| ContractLedgerIdentifier | 台帳のコントラクトのアドレス |
| WorkflowFunctionName     | ワークフロー関数の名前 |
| WorkflowName             | ワークフローの名前 |
| WorkflowBlobStorageURL   | BLOB ストレージ内のコントラクトの URL |
| ContractActionParameters | コントラクト アクションのパラメーター |
| TransactionHash          | 台帳のトランザクションのハッシュ |
| プロビジョニング状態      | アクションの現在のプロビジョニング状態。</br>0 - 作成済み</br>1 - 進行中</br>2 - 完了</br> "完了" は、これが正常に追加されたことが台帳で確認されたことを示します |

```csharp
public class ContractActionRequest : MessageModelBase
{
    public int ContractActionId { get; set; }
    public int ConnectionId { get; set; }
    public string UserChainIdentifier { get; set; }
    public string ContractLedgerIdentifier { get; set; }
    public string WorkflowFunctionName { get; set; }
    public string WorkflowName { get; set; }
    public string WorkflowBlobStorageURL { get; set; }
    public IEnumerable<ContractActionParameter> ContractActionParameters { get; set; }
    public string TransactionHash { get; set; }
    public int ProvisioningStatus { get; set; }
}
```

### <a name="updateuserbalance"></a>UpdateUserBalance

特定の分散型台帳でユーザー残高を更新する要求が行われたことを示します。

> [!NOTE]
> このメッセージは、口座の資金調達を必要とする台帳のみに生成されます。
> 

| Name    | 説明                              |
|---------|------------------------------------------|
| Address | 資金調達されたユーザーのアドレス |
| Balance | ユーザー残高の残高         |
| ChainID | チェーンの一意の識別子     |


``` csharp
public class UpdateUserBalanceRequest : MessageModelBase
{
    public string Address { get; set; }
    public decimal Balance { get; set; }
    public int ChainID { get; set; }
}
```

### <a name="insertblock"></a>InsertBlock

メッセージは、分散型台帳でブロックを追加する要求が行われたことを示します。

| Name           | 説明                                                            |
|----------------|------------------------------------------------------------------------|
| ChainId        | ブロックが追加されたチェーンの一意の識別子             |
| BlockId        | Azure Blockchain Workbench 内のブロックの一意の識別子 |
| BlockHash      | ブロックのハッシュ                                                 |
| BlockTimeStamp | ブロックのタイムスタンプ                                            |

``` csharp
public class InsertBlockRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string BlockHash { get; set; }
    public int BlockTimestamp { get; set; }
}
```

### <a name="inserttransaction"></a>InsertTransaction

メッセージは、分散型台帳で取引を追加するために要求の詳細を提供します。

| Name            | 説明                                                            |
|-----------------|------------------------------------------------------------------------|
| ChainId         | ブロックが追加されたチェーンの一意の識別子             |
| BlockId         | Azure Blockchain Workbench 内のブロックの一意の識別子 |
| TransactionHash | トランザクションのハッシュ                                           |
| ソース            | トランザクションの発信者のアドレス                      |
| ターゲット              | トランザクション対象の受信者のアドレス              |
| 値           | トランザクションに含まれている値                                 |
| IsAppBuilderTx  | これが Blockchain Workbench トランザクションであるかどうかを示します                         |

``` csharp
public class InsertTransactionRequest : MessageModelBase
{
    public int ChainId { get; set; }
    public int BlockId { get; set; }
    public string TransactionHash { get; set; }
    public string From { get; set; }
    public string To { get; set; }
    public decimal Value { get; set; }
    public bool IsAppBuilderTx { get; set; }
}
```

### <a name="assigncontractchainidentifier"></a>AssignContractChainIdentifier

契約に対するチェーン識別子の割り当てについての詳細を提供します。 たとえば、Ethereum ブロックチェーンでは、台帳の契約のアドレス。

| Name            | 説明                                                                       |
|-----------------|-----------------------------------------------------------------------------------|
| ContractId      | Azure Blockchain Workbench 内のコントラクトの一意の識別子 |
| ChainIdentifier | チェーン上のコントラクトの識別子                             |

``` csharp
public class AssignContractChainIdentifierRequest : MessageModelBase
{
    public int ContractId { get; set; }
    public string ChainIdentifier { get; set; }
}
```

## <a name="classes-used-by-message-types"></a>メッセージの種類で使用されるクラス

### <a name="messagemodelbase"></a>MessageModelBase

すべてのメッセージの基本モデル。

| Name          | 説明                          |
|---------------|--------------------------------------|
| OperationName | 操作の名前           |
| RequestId     | 要求の一意識別子 |

``` csharp
public class MessageModelBase
{
    public string OperationName { get; set; }
    public string RequestId { get; set; }
}
```

### <a name="contractinputparameter"></a>ContractInputParameter

名前、値、パラメーターの型が含まれています。

| Name  | 説明                 |
|-------|-----------------------------|
| Name  | パラメーターの名前  |
| 値 | パラメーターの値 |
| type  | パラメーターの型  |

``` csharp
public class ContractInputParameter
{
    public string Name { get; set; }
    public string Value { get; set; }
    public string Type { get; set; }
}
```

#### <a name="contractproperty"></a>ContractProperty

ID、名前、値、パラメーターの型が含まれています。

| Name  | 説明                |
|-------|----------------------------|
| ID    | プロパティの ID    |
| Name  | プロパティの名前  |
| 値 | プロパティの値。 |
| type  | プロパティの型  |

``` csharp
public class ContractProperty
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Value { get; set; }
    public string DataType { get; set; }
}
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [スマート コントラクトの統合パターン](integration-patterns.md)