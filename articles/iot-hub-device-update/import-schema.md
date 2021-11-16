---
title: Device Update for IoT Hub への更新プログラムのインポート - スキーマおよびその他の情報 | Microsoft Docs
description: Device Update for IoT Hub に更新プログラムをインポートする際に使用されるスキーマおよびその他の関連情報 (オブジェクトを含む)。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 09dc0bd3afb2b9bfc99313ad38d5c7ad19086cb8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061042"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Device Update for IoT Hub への更新プログラムのインポート - スキーマおよびその他の情報
Device Update for IoT Hub に更新プログラムにインポートする場合は、まず、[概念](import-concepts.md)と[攻略ガイド](import-update.md) を必ず確認しておいてください。 インポート マニフェストの構築時に使用されるスキーマの詳細、または関連するオブジェクトの情報に関心がある場合は、以下を参照してください。

## <a name="import-manifest-schema"></a>インポート マニフェスト スキーマ

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` オブジェクト | 更新プログラム ID。 |
| UpdateType | string | 更新の種類: <br/><br/> * 参照エージェントを使用してパッケージベースの更新を実行する場合は、`microsoft/apt:1` を指定します。<br/> * 参照エージェントを使用してイメージベースの更新を実行する場合は、`microsoft/swupdate:1` を指定します。<br/> * サンプル エージェント シミュレーターを使用する場合は、`microsoft/simulator:1` を指定します。<br/> * カスタム エージェントを開発する場合は、カスタムの種類を指定します。 | 形式: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 最大 32 文字 |
| InstalledCriteria | string | 更新プログラムが正常に適用されたかどうかを判断するために、エージェントによって解釈される文字列。  <br/> * 更新の種類 `microsoft/swupdate:1` には SWVersion の **値** を指定します。<br/> * 更新の種類 `microsoft/apt:1` には `{name}-{version}` を指定します。これは、APT ファイルから取得した名前とバージョンです。<br/> * カスタム エージェントを開発する場合は、カスタムの文字列を指定します。<br/> | 最大 64 文字 |
| 互換性 | `CompatibilityInfo` [オブジェクト](#compatibilityinfo-object)の配列 | この更新プログラムと互換性のあるデバイスの互換性情報。 | 最大 10 個の項目 |
| CreatedDateTime | 日付/時刻 | 更新プログラムが作成された日時。 | ISO 8601 の、区切られた日付と時刻の形式 (UTC) |
| ManifestVersion | string | マニフェスト スキーマのバージョンをインポートします。 `2.0` を指定します。これは、`urn:azureiot:AzureDeviceUpdateCore:1` インターフェイスおよび `urn:azureiot:AzureDeviceUpdateCore:4` インターフェイスと互換性があります。 | `2.0` である必要があります。 |
| ファイル | `File` オブジェクトの配列 | ペイロード更新ファイル | 最大 5 個のファイル |

## <a name="updateid-object"></a>UpdateId オブジェクト

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| プロバイダー | string | 更新プログラム ID のプロバイダー部分 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| 名前 | string | 更新プログラム ID の名前部分 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| Version | version | 更新プログラム ID のバージョン部分。 | 2 から 4 のパート、ドットで区切られたバージョン番号。 ドットで区切られた _各_ 部分の合計数は、0 ～ 2147483647 の範囲で指定できます。 先頭の 0 はサポートされていません。

## <a name="file-object"></a>File オブジェクト

| 名前 | Type | 説明 | 制限 |
| --------- | --------- | --------- | --------- |
| ファイル名 | string | ファイルの名前 | 255 文字以内でなければなりません。 更新プログラム内で一意である必要があります |
| SizeInBytes | Int64 | ファイルのサイズ (バイト単位)。 | 個々のファイルあたり、および更新プログラムあたりの合計の最大サイズについては、「[Device Update の制限](./device-update-limits.md)」を参照してください。 |
| ハッシュ | `Hashes` オブジェクト | ファイルのハッシュを含む JSON オブジェクト |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo オブジェクト

| 名前 | Type | 説明 | 制限 |
| --- | --- | --- | --- |
| DeviceManufacturer | string | 更新プログラムと互換性があるデバイスの製造元。 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |
| DeviceModel | string | 更新プログラムと互換性があるデバイスのモデル。 | 1 から 64 文字 (英数字、ドット、ダッシュ)。 |

## <a name="hashes-object"></a>Hashes オブジェクト

| 名前 | 必須 | Type | 説明 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | string | SHA-256 アルゴリズムを使用した、ファイルの Base64 エンコード ハッシュ。 |

## <a name="example-import-request-body"></a>インポート要求本文の例

「[新しい更新プログラムの追加方法](./import-update.md#review-the-generated-import-manifest)」ページのサンプル インポート マニフェストの出力を使用していて、Device Update [REST API](/rest/api/deviceupdate/updates) を直接呼び出してインポートを実行したい場合、対応する要求本文は次のようになります。

```json
{
  "importManifest": {
    "url": "http://<your Azure Storage location file path>/importManifest.json",
    "sizeInBytes": <size of import manifest file>,
    "hashes": {
      "sha256": "<hash of import manifest file>"
    }
  },
  "files": [
    {
      "filename": "file1.json",
      "url": "http://<your Azure Storage location file path>/file1.json"
    },
    {
          "filename": "file2.zip",
          "url": "http://<your Azure Storage location file path>/file2.zip"
    },
  ]
}
```

## <a name="oauth-authorization-when-calling-device-update-apis"></a>デバイス更新 API を呼び出す際の OAuth 認可

**azure_auth**

Azure Active Directory OAuth2 Flow Type: oauth2 Flow: any 

認可 URL: https://login.microsoftonline.com/common/oauth2/authorize

**スコープ**

| 名前 | Description |
| --- | --- |
| `https://api.adu.microsoft.com/user_impersonation` | ユーザー アカウントの借用 |
| `https://api.adu.microsoft.com/.default`  | クライアント資格情報フロー |


**アクセス許可**

Azure AD アプリケーションを使用してユーザーのサインイン処理を行う場合、スコープには /user_impersonation が含まれている必要があります。 

Azure Device Update API を使用するためのアクセス許可を ([Azure AD アプリケーション] ビューの [API のアクセス許可] タブで) Azure AD アプリに追加する必要があります。 Azure Device Update ([所属する組織で使用している API] にあります) への API アクセス許可を要求し、委任された user_impersonation アクセス許可を付与してください。

ADU は、Azure AD でサポートされているいずれかのフローを使用してトークンを受け入れ、ユーザー、アプリケーション、またはマネージド ID のトークンを取得します。 ただし、別途 Azure AD アプリケーションのセットアップが必要なフローもあります。 

* パブリック クライアント フローでは、モバイル フローとデスクトップ フローを有効にする必要があります。
* 暗黙的フローでは、Web プラットフォームを追加し、承認エンドポイントの [アクセス トークン] を選択する必要があります。

**Azure CLI を使用する例:**

```azurecli
az login

az account get-access-token --resource 'https://api.adu.microsoft.com/'
```

**PowerShell MSAL ライブラリを使用してトークンを取得する例:**

_ユーザーの資格情報を使用する_ 

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope
```

_ユーザーの資格情報とデバイス コードを使用する_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -Interactive -DeviceCode
```

_アプリの資格情報を使用する_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$cert = '<client_certificate>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/.default'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -ClientCertificate $cert
```

## <a name="next-steps"></a>次のステップ

[インポートの概念](./import-concepts.md)について詳しく説明します。

準備ができたら、インポート プロセスの詳しい手順が示されている[インポート攻略ガイド](./import-update.md)に進んでください。
