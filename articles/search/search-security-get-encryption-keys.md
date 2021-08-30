---
title: 暗号化キー情報を検索する
titleSuffix: Azure Cognitive Search
description: Azure Key Vault でキーを管理できるように、インデックスまたはシノニム マップで使用される暗号化キーの名前とバージョンを取得します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/21/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2f34d653a698a7ef2ee3dee21d46345ed9a7301a
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003820"
---
# <a name="find-encrypted-objects-and-information"></a>暗号化されたオブジェクトと情報を検索する

Azure Cognitive Search では、カスタマー マネージド暗号化キーが Azure Key Vault で作成、格納、管理されます。 オブジェクトが暗号化されているかどうかや、Azure Key Vault で使用されたキー名またはバージョンを調べる必要がある場合、REST API または Azure SDK を使用して、検索サービスのオブジェクトの定義から **encryptionKey** プロパティを取得します。

カスタマー マネージド キーで暗号化されていないオブジェクトは、**encryptionKey** プロパティが空になります。 それ以外の場合、定義は次の例のようになります。

```json
"encryptionKey": {
"keyVaultUri": "https://demokeyvault.vault.azure.net",
"keyVaultKeyName": "myEncryptionKey",
"keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
"accessCredentials": {
    "applicationId": "00000000-0000-0000-0000-000000000000",
    "applicationSecret": "myApplicationSecret"
    }
}
```

**encryptionKey** のコンストラクトは、暗号化されたすべてのオブジェクトで共通です。 オブジェクトの名前や説明と同じ第 1 レベルのプロパティとなります。

## <a name="get-the-admin-api-key"></a>管理 API キーを取得する

オブジェクトの定義を検索サービスから取得するには、あらかじめ管理 API キーを準備する必要があります。 オブジェクトの定義やメタデータを照会する要求では、管理 API キーが必要となります。 管理 API キーを取得する最も簡単な方法は、ポータルを使用することです。

1. [Azure portal](https://portal.azure.com/) にサインインして、検索サービスの概要ページを開きます。

1. 左側で **[キー]** をクリックし、管理 API をコピーします。 インデックスとシノニム マップを取得するには、管理者キーが必要です。

残りの手順では、PowerShell と REST API に切り替えます。 ポータルでは、どのオブジェクトの暗号化キー情報も表示されません。

## <a name="retrieve-object-properties"></a>オブジェクトのプロパティを取得する

PowerShell と REST から次のコマンドを実行して、変数を設定し、オブジェクトの定義を取得します。 

[.NET](/dotnet/api/azure.search.documents.indexes.searchindexclient.getindexes)、[Python](/python/api/azure-search-documents/azure.search.documents.indexes.searchindexclient)、[JavaScript](/javascript/api/@azure/search-documents/searchindexclient)、[Java](/java/api/com.azure.search.documents.indexes.searchindexclient.getindex) 用の Azure SDK を使用することもできます。

```powershell
<# Connect to Azure #>
$Connect-AzAccount

<# Provide the admin API key used for search service authentication  #>
$headers = @{
'api-key' = '<YOUR-ADMIN-API-KEY>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' }

<# List all existing synonym maps #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# List all existing indexes #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes?api-version=2020-06-30&$select=name'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific synonym map definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/synonyms/<YOUR-SYNONYM-MAP-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json

<# Return a specific index definition. The encryptionKey property is at the end #>
$uri= 'https://<YOUR-SEARCH-SERVICE>.search.windows.net/indexes/<YOUR-INDEX-NAME>?api-version=2020-06-30'
Invoke-RestMethod -Uri $uri -Headers $headers | ConvertTo-Json
```

## <a name="next-steps"></a>次のステップ

キーの使用状況を監視できるように、Azure Key Vault で[ログを有効にする](../key-vault/general/logging.md)ことをお勧めします。

Azure Key の使用またはカスタマー マネージド暗号化の構成について詳しくは、次の記事を参照してください。

+ [クイック スタート:PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-powershell.md)

+ [Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する](search-security-manage-encryption-keys.md)
