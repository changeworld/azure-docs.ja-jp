---
title: カスタマー マネージド キーを使用してデータを暗号化する
titleSuffix: Azure Cognitive Search
description: Azure Key Vault 内で作成・管理するキーを使用して、Azure Cognitive Search 内でインデックスとシノニム マップによるサーバー側の暗号化を補完します。
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 9dc1244c6747ea5616bee59ccf9b972a89d0c4a0
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "130042184"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Azure Cognitive Search のデータ暗号化のためにカスタマー マネージド キーを構成する

Azure Cognitive Search 使用時、コンテンツは[サービス マネージド キー](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components)を使用して自動的に暗号化されます。 保護を強化する必要がある場合は、Azure Key Vault 内で作成して管理するキーを使用して、既定の暗号化を追加の暗号化レイヤーで補完できます。 暗号化できるオブジェクトには、インデックス、シノニム リスト、インデクサー、データ ソース、スキルセットがあります。

この記事では、カスタマー マネージド キーの暗号化を設定する手順について説明します。 次の点にご注意ください。

+ カスタマー マネージド キーの暗号化は、[Azure Key Vault](../key-vault/general/overview.md) に依存しています。 独自の暗号化キーを作成してキー コンテナーに格納したり、Azure Key Vault の API を使用して暗号化キーを生成したりできます。

+ カスタマー マネージド キーを使用した暗号化は、オブジェクトが作成されるときに、オブジェクトごとに有効になります。 既存のコンテンツを暗号化することはできません。

暗号化すると復号に評価コストがかかるため、機密性の高いコンテンツのみが暗号化されます。 これには、インデックスとシノニム リスト内のすべてのコンテンツが含まれます。 インデクサー、データ ソース、スキルセットについては、接続文字列、説明、キー、ユーザー入力を格納するフィールドのみが暗号化されます。 たとえば、スキルセットには Cognitive Services キーがあり、一部のスキルではカスタム エンティティなどのユーザー入力が受け取られます。 キーとスキルへのユーザー入力は暗号化されます。

## <a name="double-encryption"></a>二重暗号化

二重暗号化は、カスタマー マネージド キー (CMK) 暗号化の拡張機能です。 CMK 暗号化は、データ ディスクに書き込まれる長期ストレージに適用されます。 "*二重暗号化*" という用語は、(一時ディスクに書き込まれたコンテンツの) 短期ストレージに対する追加の暗号化を指します。 手動で構成する必要はありません。 CMK をオブジェクトに適用すると、二重暗号化が自動的に呼び出されます。

二重暗号化は、すべてのリージョンで使用できます。ただし、サポートは 2 つのフェーズでロール アウトされました。 最初のロール アウトは 2020 年 8 月であり、以下に示す 5 つのリージョンが含まれていました。 2021 年 5 月の 2 回目のロール アウトでは、残りのすべてのリージョンに二重暗号化が拡張されました。 古いサービスで CMK を使用し、二重暗号化が必要な場合は、選択したリージョンに新しい検索サービスを作成する必要があります。

| リージョン | サービスの作成日 |
|--------|-----------------------|
| 米国西部 2 | 2020 年 8 月 1 日以降 |
| 米国東部 | 2020 年 8 月 1 日以降 |
| 米国中南部  | 2020 年 8 月 1 日以降 |
| US Gov バージニア州  | 2020 年 8 月 1 日以降 |
| US Gov アリゾナ  | 2020 年 8 月 1 日以降 |
| [サポートされているその他のすべてのリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=search#select-product) | 2021 年 5 月 13 日以降 |

## <a name="prerequisites"></a>前提条件

このシナリオでは、以下のツールとサービスが使用されます。

+ [請求可能なレベル](search-sku-tier.md#tier-descriptions) (Basic 以上、任意のリージョン) の [Azure Cognitive Search](search-create-service-portal.md)。
+ [Azure Key Vault](../key-vault/general/overview.md)。[Azure portal](../key-vault//general/quick-create-portal.md)、[Azure CLI](../key-vault//general/quick-create-cli.md)、または [Azure PowerShell](../key-vault//general/quick-create-powershell.md) を使用して、キー コンテナーを作成できます (Azure Cognitive Search と同じサブスクリプション内)。 キー コンテナーでは、**論理的な削除** と **消去保護** が有効になっている必要があります。
+ [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)。 それがない場合は、[新しいテナントをセットアップ](../active-directory/develop/quickstart-create-new-tenant.md)してください。

暗号化されたオブジェクトを作成できる検索アプリケーションが必要です。 このコード内では、キー コンテナー キーと Active Directory 登録情報を参照します。 このコードは、動作しているアプリや、[C# コードサンプル DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) などのプロトタイプ コードなどになります。

> [!TIP]
> [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、または [Azure PowerShell](search-get-started-powershell.md) を使用して、暗号化キー パラメーターを含むインデックスおよびシノニム マップを作成する REST API を呼び出すことができます。 Azure SDK を使用することもできます。 インデックスまたはシノニム マップにポータルでキーを追加することはサポートされていません。

## <a name="key-vault-tips"></a>Key Vault に関するヒント

Azure Key Vault を初めて使用する場合は、このクイック スタートを参照して、基本的なタスクについてご確認ください: 「[PowerShell を使用して Azure Key Vault との間でシークレットの設定と取得を行う](../key-vault/secrets/quick-create-powershell.md)」。 Key Vault を使用するときのヒントを次に示します。

+ 必要な数のキー コンテナーを使用します。 マネージド キーは異なるキー コンテナーに配置できます。 それぞれが異なるカスタマー マネージド暗号化キーで暗号化されて異なるキー コンテナーに格納されている、複数の暗号化されたオブジェクトに 1 つの検索サービスで対応できます。

+ キーの使用状況を監視できるように、Key Vault で[ログを有効にします](../key-vault/general/logging.md)。

+ キー コンテナー キーと Active Directory アプリケーション シークレットの定期的なローテーションおよび登録時には、必ず厳密な手順に従います。 必ず、[暗号化されたコンテンツ](search-security-get-encryption-keys.md)すべてを新しいシークレットとキーを使用するように更新してから、古いものを削除します。 この手順を実行しないと、コンテンツの暗号化を解除できません。

## <a name="1---enable-purge-protection"></a>1 - 消去保護の有効化

最初のステップとして、キー コンテナーで[論理的な削除](../key-vault/general/soft-delete-overview.md)と[消去保護](../key-vault/general/soft-delete-overview.md#purge-protection)が有効になっていることを確認します。 カスタマー マネージド キーを使用する暗号化の性質上、Azure Key Vault キーが削除された場合、データを取得できません。 

Key Vault キーの誤った削除によるデータ損失を防ぐため、キー コンテナーで論理的な削除と消去保護を有効にする必要があります。 論理的な削除は既定で有効になっているため、この機能を意図的に無効にした場合にのみ問題が発生します。 消去保護は既定では有効になりませんが、Cognitive Search のカスタマー マネージド キーの暗号化には必須です。 

ポータル、PowerShell、または Azure CLI コマンドを使用して、両方のプロパティを設定できます。

### <a name="using-azure-portal"></a>Azure Portal の使用

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。

1. **[概要]** ページの **[Essentials]** で、 **[論理的な削除]** と **[消去保護]** を有効にします。

### <a name="using-powershell"></a>PowerShell の使用

1. `Connect-AzAccount` を実行して Azure の資格情報を設定します。

1. 次のコマンドを実行してキー コンテナーに接続し、`<vault_name>` を有効な名前に置き換えます。

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. Azure Key Vault は、論理的な削除が有効な状態で作成されます。 これがコンテナーで無効になっている場合は、次のコマンドを実行します。

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. 消去保護を有効にします。

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. 更新内容を保存します。

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Azure CLI の使用

+ [Azure CLI のインストール](/cli/azure/install-azure-cli)がある場合は、次のコマンドを実行して必要なプロパティを有効にすることができます。

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2 - キー コンテナー内のキーの作成

使用する Azure Key Vault にキーが既に存在するが、キー識別子を収集する場合は、キーの生成をスキップします。 この情報は、暗号化されたオブジェクトを作成するときに必要になります。

1. [Azure portal にサインイン](https://portal.azure.com)して、キー コンテナーの概要ページを開きます。

1. 左側の **[キー]** を選択し、 **[+ 生成/インポート]** を選択します。

1. **[キーの作成]** ウィンドウで、 **[オプション]** 一覧から、キーの作成に使用する方法を選択します。 新しいキーを **生成** したり、既存のキーを **アップロード** したり、キーのバックアップを選択して **バックアップを復元** したりできます。

1. キーの **[名前]** を入力し、必要に応じてその他のキー プロパティを選択します。

1. **[作成]** を選択してデプロイを開始します。

1. キー識別子を書き留めてください。これは、**キー値の URI**、**キー名**、および **キー バージョン** で構成されます。 Azure Cognitive Search で暗号化されたインデックスを定義するために識別子が必要になります。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="新しい Key Vault キーを作成する":::

## <a name="3---register-an-app"></a>3 - アプリの登録

1. [Azure portal](https://portal.azure.com) で、サブスクリプションの Azure Active Directory リソースを検索します。

1. 左の **[管理]** で、 **[アプリの登録]** を選択し、次に **[新しい登録]** を選択します。

1. 登録に名前、おそらく検索アプリケーション名に似た名前を付けます。 **[登録]** を選択します。

1. アプリの登録が作成されたら、アプリケーション ID をコピーします。 この文字列をアプリケーションに指定する必要があります。 

   [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) をステップ実行している場合は、この値を **appsettings.json** ファイルに貼り付けます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="[Essentials] セクションの [アプリケーション ID]":::

1. 次に、左側の **[証明書とシークレット]** を選択します。

1. **[新しいクライアント シークレット]** を選択します。 シークレットに表示名を付け、 **[追加]** を選択します。

1. アプリケーション シークレットをコピーします。 サンプルをステップ実行している場合は、この値を **appsettings.json** ファイルに貼り付けます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="アプリケーション シークレット":::

## <a name="4---grant-permissions"></a>4 - アクセス許可の付与

この手順では、キー コンテナーのアクセス ポリシーを作成します。 このポリシーにより、登録したアプリケーションに、カスタマー マネージド キーを使用するための Active Directory アクセス許可を付与します。

アクセス許可はいつでも取り消すことができます。 取り消すと、そのキー コンテナーを使用する検索サービスのインデックスまたはシノニム マップを使用できなくなります。 キー コンテナーのアクセス許可を後から復元すると、インデックス/シノニム マップへのアクセスが復元されます。 詳細については、「[キー コンテナーへのアクセスをセキュリティで保護する](../key-vault/general/security-features.md)」を参照してください。

1. Azure portal にまだいる場合は、キー コンテナーの **[概要]** ページを開きます。 

1. 左側の **[アクセス ポリシー]** を選択し、 **[+ アクセス ポリシーの追加]** を選択します。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="新しい Key Vault アクセス ポリシーを追加する":::

1. **[プリンシパルの選択]** を選択し、Active Directory に登録したアプリケーションを選択します。 それを名前で検索できます。

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Key Vault アクセス ポリシーのプリンシパルを選択する":::

1. **[キーのアクセス許可]** で *[取得]* 、 *[キーの折り返しを解除]* と *[キーを折り返す]* を選択します。

1. **[シークレットのアクセス許可]** で、 *[取得]* を選択します。

1. **[証明書のアクセス許可]** で、 *[取得]* を選択します。

1. **[追加]** を選択し、次に **[保存]** を選択します。

> [!Important]
> Azure Cognitive Search 内の暗号化されたコンテンツは、特定の **バージョン** の特定の Azure Key Vault キーを使用するように構成されます。 キーまたはバージョンを変更する場合は、以前のキー/バージョンを削除する **前** に、新しいキー/バージョンを使用するようにインデックスまたはシノニム マップを更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5 - コンテンツの暗号化

インデックス、シノニム マップ、インデクサー、データ ソース、またはスキルセットにカスタマー マネージド キーを追加するには、[Search REST API](/rest/api/searchservice/) または Azure SDK を使用して、暗号化が有効になっているオブジェクトを作成します。 ポータルでは、シノニム マップや暗号化プロパティが公開されません。 

1. 作成の API を呼び出して **encryptionKey** プロパティを指定します。

   + [インデックスの作成](/rest/api/searchservice/create-index)
   + [シノニム マップの作成](/rest/api/searchservice/create-synonym-map)
   + [インデクサーの作成](/rest/api/searchservice/create-indexer)
   + [データ ソースの作成](/rest/api/searchservice/create-data-source)
   + [スキルセットの作成](/rest/api/searchservice/create-skillset)。

1. オブジェクト定義に encryptionKey コンストラクトを挿入します。 このプロパティは、名前および説明と同じレベルにある第 1 レベルのプロパティです。 [次の例](#rest-examples)は、プロパティの配置を示しています。 同じキー コンテナー、キー、およびバージョンを使用している場合は、暗号化を有効にする各オブジェクトに同じ encryptionKey コンストラクトを貼り付けることができます。

   次の JSON の例は、Azure Key Vault と Azure Active Directory でのアプリケーション登録のプレースホルダー値を含む encryptionKey を示しています。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "accessCredentials": {
          "applicationId": "00000000-0000-0000-0000-000000000000",
          "applicationSecret": "myApplicationSecret"
        }
      }
    }
    ```

暗号化されたオブジェクトを検索サービス上で作成したら、それをその種類の他のオブジェクトと同様に使用できます。 暗号化は、ユーザーと開発者に対して透過的です。

> [!Note]
> Key Vault のこれらの詳細はシークレットとは見なされず、Azure portal 内の関連する Azure Key Vault キーのページを参照して簡単に取得できます。

## <a name="rest-examples"></a>REST の例

このセクションでは、オブジェクト定義内の `encryptionKey` の場所を確認できるよう、複数のオブジェクトの JSON を示します。

### <a name="index-encryption"></a>インデックスの暗号化

REST API を使用した新しいインデックスの作成の詳細については、[インデックスの作成 (REST API)](/rest/api/searchservice/create-index) に関するページを参照してください。こことの唯一の違いは、インデックス定義の一部として暗号化キーの詳細が指定されている点です。

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true}
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、インデックス作成要求を送信し、インデックスの使用を正常に開始できます。

### <a name="synonym-map-encryption"></a>シノニム マップの暗号化

[シノニム マップの作成 (Azure Cognitive Search REST API)](/rest/api/searchservice/create-synonym-map) に関する記事を使用して、暗号化されたシノニム マップを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、シノニム マップ作成要求を送信し、シノニム マップの使用を正常に開始できます。

### <a name="data-source-encryption"></a>データ ソースの暗号化

[データ ソースの作成 (REST API)](/rest/api/searchservice/create-data-source) に関する記事を使用して、暗号化されたデータ ソースを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、データ ソース作成要求を送信すると、データ ソースの正常な使用を開始できます。

### <a name="skillset-encryption"></a>スキルセットの暗号化

[スキルセットの作成 REST API](/rest/api/searchservice/create-skillset) に関する記事を使用して、暗号化されたスキルセットを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
    "name": "skillset1",
    "skills":  [ omitted for brevity ],
    "cognitiveServices": { omitted for brevity },
      "knowledgeStore":  { omitted for brevity  },
    "encryptionKey": (optional) { 
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "accessCredentials": {
            "applicationId": "00000000-0000-0000-0000-000000000000",
            "applicationSecret": "myApplicationSecret"}
    }
}
```

これで、スキルセット作成要求を送信すると、データ ソースの正常な使用を開始できます。

### <a name="indexer-encryption"></a>インデクサーの暗号化

[インデクサーの作成 REST API](/rest/api/searchservice/create-indexer) に関する記事を使用して、暗号化されたインデクサーを作成します。 使用する暗号化キーを指定するには、`encryptionKey` プロパティを使用します。

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

これで、インデクサー作成要求を送信すると、データ ソースの正常な使用を開始できます。

>[!Important]
> `encryptionKey` を既存の Search インデックスまたはシノニム マップに追加することはできませんが、3 つのキー コンテナーの詳細のいずれかに別の値を指定して更新することはできます (たとえば、キー バージョンの更新)。 新しいキー コンテナー キーまたは新しいキー バージョンに変更する場合は、以前のキー/バージョンを削除する **前** に、まずキーを使用するすべての Search キー インデックスまたはシノニム マップを、新しいキー/バージョンを使用するように更新する必要があります。 そうしないと、インデックスまたはシノニム マップが使用できない状態になり、キー アクセスが失われた場合にコンテンツを暗号化解除できません。 キー コンテナーのアクセス許可を後から復元すると、コンテンツへのアクセスが復元されます。

## <a name="simpler-alternative-trusted-service"></a>簡単な代替方法:信頼できるサービス

テナントの構成と認証の要件によっては、キー コンテナー キーにアクセスするためのより簡単なアプローチを実装できる場合があります。 Active Directory アプリケーションを作成して使用する代わりに、検索サービスのシステム マネージド ID を有効にすることで、それを信頼されたサービスにするか、ユーザー割り当てマネージド ID を検索サービスに割り当てることができます。 その後、AD に登録されたアプリケーションではなく、その信頼された検索サービスまたはユーザー割り当てマネージド ID をセキュリティ プリンシパルとして使用して、キー コンテナー キーにアクセスします。

どちらの方法でも、アプリケーション登録とアプリケーション シークレットの手順を省略し、暗号化キー定義を簡略化できます。

### <a name="system-assigned-managed-identity"></a>システム割り当てマネージド ID

通常、マネージド ID を使用すると、コードに資格情報 (ApplicationID や ApplicationSecret) を格納することなく、検索サービスで Azure Key Vault に対する認証を行うことができます。 この種類のマネージド ID のライフサイクルは、検索サービスのライフサイクルに関連付けられます。そのため、割り当てることのできるマネージド ID は 1 つだけです。 マネージド ID が機能する方法について詳しくは、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

1. 検索サービスを信頼されたサービスにします。
   ![システム割り当てマネージド ID をオンにする](./media/search-managed-identities/turn-on-system-assigned-identity.png "システム割り当てマネージド ID をオンにする")

1. Azure Key Vault でアクセス ポリシーを設定する場合は、信頼された検索サービスをプリンシパルとして選択します (AD に登録されたアプリケーションではなく)。 アクセス キーのアクセス許可の付与の手順に示されている同じアクセス許可 (複数の GET、WRAP、UNWRAP) を割り当てます。

1. Active Directory のプロパティを省略する `encryptionKey` の簡略化された構成を使用します。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

この簡略化されたアプローチを採用できない条件には、次のようなものがあります。

+ 検索サービスのアクセス許可をキー コンテナーに直接付与することはできません (たとえば、検索サービスが Azure Key Vault ではなく別の Active Directory テナント内にある場合などです)。

+ 暗号化された複数の (別のキー コンテナーからの異なるキーをそれぞれが使用する) インデックス/シノニム マップをホストするには、1 つの検索サービスが必要です。各キー コンテナーでは **異なる ID** を使用して認証を行う必要があります。 検索サービスはマネージド ID を 1 つしか持つことができないため、複数の ID の要件によってシナリオの簡略化されたアプローチが不適格になります。  

### <a name="user-assigned-managed-identity-preview"></a>ユーザー割り当てマネージド ID (プレビュー)

> [!IMPORTANT] 
> ユーザー割り当てマネージド ID のサポートは、[追加の使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)のもとでパブリック プレビュー段階にあります。
> 
> REST API バージョン 2021-04-30-Preview および[管理 REST API 2021-04-01-Preview](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update) でこの機能が提供されます。

ユーザー割り当てマネージド ID を検索サービスに割り当てると、コードに資格情報 (ApplicationID や ApplicationSecret) を格納することなく、検索サービスで Azure Key Vault に対する認証を行うことができます。 この種類のマネージド ID のライフサイクルは、検索サービスのライフサイクルとは独立しています。 マネージド ID が機能する方法について詳しくは、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

1. ユーザー割り当てマネージド ID をまだ作成していない場合は、作成する必要があります。 ユーザー割り当てマネージド ID を作成するには、次の手順に従います。

    1. [Azure portal](https://portal.azure.com/) にサインインします。
    1. **[+ 新しいリソースを作成する]** を選択します。
    1. "サービスとマーケットプレースの検索" の検索バーで、"ユーザー割り当てマネージド ID" を検索し、 **[作成]** を選択します。
    1. ID にわかりやすい名前を付けます。

1. 次に、ユーザー割り当てマネージド ID を検索サービスに割り当てます。 これは、[2021-04-01-preview](/rest/api/searchmanagement/management-api-versions) 管理 API を使用して行うことができます。

    ID プロパティには、種類と 1 つ以上の完全修飾ユーザー割り当て ID を使用できます。
    
    * **type** は、リソースに使用される ID の種類です。 種類 "SystemAssigned, UserAssigned" には、システムによって作成された 1 個の ID と、ユーザー割り当て ID のセットの両方が含まれます。 種類 "None" の場合は、サービスからすべての ID が削除されます。
    * **userAssignedIdentities** には、ユーザー割り当てマネージド ID の詳細が含まれます。
        * ユーザー割り当てマネージド ID の形式: 
            * /subscriptions/**サブスクリプション ID**/resourcegroups/**リソース グループ名**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**マネージド ID 名**
    
    ユーザー割り当てマネージド ID を検索サービスに割り当てる方法の例:
    
    ```http
    PUT https://management.azure.com/subscriptions/subid/resourceGroups/rg1/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
    Content-Type: application/json

    {
      "location": "[region]",
      "sku": {
        "name": "[sku]"
      },
      "properties": {
        "replicaCount": [replica count],
        "partitionCount": [partition count],
        "hostingMode": "default"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]": {}
        }
      }
    } 
    ```

1. Azure Key Vault でアクセス ポリシーを設定する場合は、ユーザー割り当てマネージド ID をプリンシパルとして選択します (AD に登録されたアプリケーションではなく)。 アクセス キーのアクセス許可の付与の手順に示されている同じアクセス許可 (複数の GET、WRAP、UNWRAP) を割り当てます。

1. Active Directory のプロパティを省略する `encryptionKey` の簡略化された構成を使用し、ID のプロパティを追加します。 必ず 2021-04-30-preview REST API バージョンを使用してください。

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://[key vault name].vault.azure.net",
        "keyVaultKeyName": "[key vault key name]",
        "keyVaultKeyVersion": "[key vault key version]",
        "identity" : { 
            "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
            "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]"
        }
      }
    }
    ```

## <a name="work-with-encrypted-content"></a>暗号化されたコンテンツを使用する

カスタマー マネージド キーの暗号化を使用すると、暗号化/暗号化解除の処理が増えるため、インデックス作成とクエリの両方で待ち時間があることに気付きます。 Azure Cognitive Search では、暗号化アクティビティはログに記録されませんが、キー コンテナーのログを通してキーへのアクセスを監視できます。 キー コンテナー構成の一環として[ログを有効](../key-vault/general/logging.md)にすることが推奨されます。

時間の経過と共に、キーのローテーションが発生すると予想されます。 キーのローテーションのたびに、次の手順に従うことが重要です。

1. [インデックスやシノニム マップ](search-security-get-encryption-keys.md)によって使用されるキーを確認します。
1. [キー コンテナー内に新しいキーを作成します](../key-vault/keys/quick-create-portal.md)が、元のキーは使用可能なままにしておきます。
1. インデックスまたはシノニム マップの [encryptionKey プロパティを更新](/rest/api/searchservice/update-index)して新しい値を使用します。 別の値を使用するように更新できるのは、当初このプロパティを使用して作成されたオブジェクトだけです。
1. キー コンテナーで以前のキーを無効にするか削除します。 キー アクセスを監視して、新しいキーが使用されていることを確認します。

パフォーマンス上の理由で、検索サービスでは、キーが最大で数時間キャッシュされます。 新しいキーを指定せずにキーを無効にした、または削除した場合、キャッシュの有効期限が切れるまで、クエリは一時的に機能し続けます。 ただし、検索サービスでコンテンツの暗号化解除ができなくなると、次のメッセージが表示されます。"アクセスが禁止されています。 使用されたクエリ キーが失効している可能性があります - もう一度お試しください。" 

## <a name="next-steps"></a>次のステップ

Azure セキュリティ アーキテクチャを使い慣れていない場合は、[Azure のセキュリティのドキュメント](../security/index.yml)を確認してください。具体的には次の記事です。

> [!div class="nextstepaction"]
> [保存時のデータ暗号化](../security/fundamentals/encryption-atrest.md)
