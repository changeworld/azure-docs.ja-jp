---
title: さまざまなデータ ソースから Metrics Advisor に接続する
titleSuffix: Azure Cognitive Services
description: Metrics Advisor にさまざまなデータ フィードを追加する
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 05/26/2021
ms.author: mbullwin
ms.openlocfilehash: 005886c0399a89fe2e58aa669a8f7c0d33821cd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026640"
---
# <a name="how-to-connect-different-data-sources"></a>方法: さまざまなデータ ソースを接続する

この記事を使用して、さまざまな種類のデータ ソースを Azure Metrics Advisor に接続するための設定と要件を理解してください。 Metrics Advisor でデータを使用する方法の詳細については、[データのオンボード](how-tos/onboard-your-data.md)に関するページを参照してください。 

## <a name="supported-authentication-types"></a>サポートされている認証の種類

| 認証の種類 | 説明 |
| ---------------------|-------------|
|**Basic** | データ ソースにアクセスするための基本パラメーターを指定する必要があります。 たとえば、接続文字列またはパスワードを使用できます。 データ フィード管理者は、これらの資格情報を表示できます。 |
| **Azure マネージド ID** | Azure リソースの[マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) は、Azure Active Directory (Azure AD) の機能です。 これにより、Azure AD で自動的に管理される ID が Azure サービスに提供されます。 ID を使用して、Azure AD 認証をサポートする任意のサービスに対する認証を行うことができます。|
| **Azure SQL 接続文字列**| AzureSQL 接続文字列を資格情報エンティティとして Metrics Advisor に保存し、メトリック データをインポートするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、認可された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。 |
| **Azure Data Lake Storage Gen2 共有キー**| データ レイク アカウント キーを資格情報エンティティとして Metrics Advisor に保存し、メトリック データをインポートするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、認可された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。|
| **サービス プリンシパル**| [サービス プリンシパル](../../active-directory/develop/app-objects-and-service-principals.md)を資格情報エンティティとして Metrics Advisor に保存し、メトリック データをインポートするたびにそれを直接使用します。 資格情報を表示できるのは資格情報エンティティの管理者だけですが、認可された閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。|
| **キー コンテナーからのサービス プリンシパル**|[キー コンテナーのサービス プリンシパル](/azure-stack/user/azure-stack-key-vault-store-credentials)を資格情報エンティティとして Metrics Advisor に保存し、メトリック データをインポートするたびにそれを直接使用します。 これらの資格情報を表示できるのは資格情報エンティティの管理者だけですが、閲覧者は、資格情報の詳細を知らなくてもデータ フィードを作成できます。 |


## <a name="data-sources-and-corresponding-authentication-types"></a>データ ソースおよび対応する認証の種類

| データ ソース | 認証の種類 |
|-------------| ---------------------|
|[Application Insights](#appinsights) | 基本 |
|[Azure Blob Storage (JSON)](#blob) | 基本<br>マネージド ID |
|[Azure Cosmos DB (SQL)](#cosmosdb) | 基本 |
|[Azure Data Explorer (Kusto)](#kusto) | 基本<br>マネージド ID<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル |
|[Azure Data Lake Storage Gen2](#adl) | 基本<br>Data Lake Storage Gen2 共有キー<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル |
|[Azure Event Hubs](#eventhubs) | 基本 |
|[Azure Monitor ログ](#log) | 基本<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル |
|[Azure SQL Database / SQL Server](#sql) | 基本<br>マネージド ID<br>サービス プリンシパル<br>キー コンテナーからのサービス プリンシパル<br>Azure SQL 接続文字列 |
|[Azure Table Storage](#table) | 基本 | 
|[InfluxDB (InfluxQL)](#influxdb) | 基本 |
|[MongoDB](#mongodb) | 基本 |
|[MySQL](#mysql) | 基本 |
|[PostgreSQL](#pgsql) | 基本|
|[ローカル ファイル (CSV)](#csv) | 基本|

次のセクションでは、さまざまなデータ ソースのシナリオ内のすべての認証の種類に必要なパラメーターを指定します。 

## <a name="span-idappinsightsapplication-insightsspan"></a><span id="appinsights">Application Insights</span>

* **Application ID**: Application Insights API を使用している場合、このアプリケーションの識別に使用されます。 applicationId を取得するには、次の手順に従います。

   1. Application Insights リソースから、 **[API アクセス]** を選択します。
   
      ![アプリケーション ID を Application Insights リソースから取得する方法を示すスクリーンショット。](media/portal-app-insights-app-id.png)

   2. Metrics Advisor の **[アプリケーション ID]** フィールドに生成されたアプリケーション ID をコピーします。 

* **API キー**: API キーは、このリソースにアクセスするために、ブラウザーの外側のアプリケーションによって使用されます。 API キーを取得するには、次の手順に従います。

   1. Application Insights リソースから、 **[API アクセス]** を選択します。

   2. **[API キーの作成]** を選択します。

   3. 簡単な説明を入力し、 **[テレメトリを読み取る]** オプションを選択して、 **[キーの生成]** を選択します。

      ![Azure portal で API キーを取得する方法を示すスクリーンショット。](media/portal-app-insights-app-id-api-key.png)

       > [!IMPORTANT]
       > この API キーをコピーして保存します。 これは再表示されません。 このキーを紛失した場合は、新しいキーを作成する必要があります。

   4. API キーを Metrics Advisor の **[API キー]** フィールドにコピーします。

* **クエリ**: Azure Application Insights ログは、Azure Data Explorer 上に構築されており、Azure Monitor ログ クエリでは、同じ Kusto クエリ言語のバージョンが使用されます。 [Kusto クエリ言語のドキュメント](/azure/data-explorer/kusto/query)は、Application Insights に対するクエリを記述するためのプライマリ リソースとなります。 

    サンプル クエリ:

    ``` Kusto
    [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);
    ```
    具体的な例については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページ参照してください。
  
## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **接続文字列**: Azure Blob Storage (JSON) には、次の 2 つの認証の種類があります。

    * **基本**: この文字列の取得の詳細については、[Azure Storage の接続文字列の構成](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account)に関するページを参照してください。 また、Azure Blob Storage リソースの Azure portal にアクセスし、 **[設定]**  >  **[アクセス キー]** で接続文字列を直接検索できます。
    
    * **マネージド ID**: Azure リソースに対するマネージド ID では、BLOB およびキュー データへのアクセスを認可できます。 この機能では、Azure 仮想マシン (VM)、関数アプリ、仮想マシン スケール セット、その他のサービスで動作するアプリケーションから、Azure AD 資格情報を使用します。 
    
        マネージド ID は、Azure Blob Storage リソースの Azure portal で作成できます。 **[アクセス制御 (IAM)]** で、 **[ロールの割り当て]** を選択してから、 **[追加]** を選択します。 推奨されるロールの種類は、**Storage Blob データ閲覧者** です。 詳細については、[マネージド ID を使用した Azure Storage へのアクセス](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access-1)に関するページを参照してください。
    
        ![マネージド ID BLOB を示すスクリーンショット。](media/managed-identity-blob.png)
    

* **コンテナー**: Metrics Advisor では、1 つのコンテナーの下に BLOB ファイルとして格納する時系列データ (タイムスタンプごとに 1 つの BLOB) が想定されています。 これはコンテナー名フィールドです。

* **BLOB テンプレート**: Metrics Advisor では、パスを使用して、Blob Storage で JSON ファイルを検索します。 これは、Blob Storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json` で JSON ファイルを検索するために使用される BLOB ファイル テンプレートの例です。 `%Y/%m` はパスです。パスに `%d` がある場合は、`%m` の後に追加できます。 JSON ファイルの名前に日付が付けられている場合は、`%Y-%m-%d-%h-%M.json` を使用することもできます。

   サポートされているパラメーターは次のとおりです。
   
   * `%Y` は `yyyy` として書式設定された年です。
   * `%m` は `MM` として書式設定された月です。
   * `%d` は `dd` として書式設定された日です。
   * `%h` は `HH` として書式設定された時間です。
   * `%M` は `mm` として書式設定された分です。
  
   たとえば、次のデータセットでは、BLOB テンプレートは `%Y/%m/%d/00/JsonFormatV2.json` になります。
  
   ![BLOB テンプレートを示すスクリーンショット。](media/blob-template.png)
  

* **JSON 形式のバージョン**:JSON ファイルのデータ スキーマを定義します。 Metrics Advisor では、次のバージョンをサポートしています。 次のいずれかを選択して、フィールドに入力できます。
  
   * **v1** (既定値)

      メトリック *Name* と *Value* のみが受け入れられます。 次に例を示します。
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

   * **v2**

      メトリック *Dimensions* と *timestamp* も受け入れられます。 次に例を示します。
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

   JSON ファイルごとに 1 つのタイムスタンプのみが許可されます。 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **接続文字列**: Azure Cosmos DB にアクセスするための接続文字列。 これは、Azure portal の Azure Cosmos DB リソースの **[キー]** にあります。 詳細については、「[Azure Cosmos DB のデータへのアクセスをセキュリティで保護する](../../cosmos-db/secure-access-to-data.md)」を参照してください。
* **データベース**:クエリ対象のデータベース。 Azure portal の **[コンテナー]** で、 **[参照]** に移動し、データベースを検索します。
* **コレクション ID**:クエリ対象のコレクション ID。 Azure portal の **[コンテナー]** で、 **[参照]** に移動し、コレクション ID を検索します。
* **SQL クエリ**: データを取得して多次元時系列データに編成する SQL クエリ。 クエリでは、変数 `@IntervalStart` と `@IntervalEnd` を使用できます。 これらは `yyyy-MM-ddTHH:mm:ssZ` のように書式設定する必要があります。

    サンプル クエリ:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```

    詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **接続文字列**: Azure Data Explorer (Kusto) には、基本、サービス プリンシパル、キー コンテナーからのサービス プリンシパル、マネージド ID の 4 種類の認証があります。 接続文字列のデータ ソースは、URI 形式 ("https" で始まる) である必要があります。 URI は Azure portal で確認できます。
    
    * **基本**: Metrics Advisor では、Azure AD アプリケーション認証を使用して Azure Data Explorer (Kusto) へのアクセスをサポートします。 Azure AD アプリケーションを作成して登録し、Azure Data Explorer データベースへのアクセスを認可する必要があります。 詳細については、「[Azure Data Explorer で Azure Active Directory アプリケーションの登録を作成する](/azure/data-explorer/provision-azure-ad-app)」を参照してください。 接続文字列の例を次に示します。
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>;AAD Federated Security=True;Application Client ID=<Application Client ID>;Application Key=<Application Key>;Authority ID=<Tenant ID>
        ```

    * **サービス プリンシパル**: サービス プリンシパルは、アプリケーション オブジェクトから作成された具象インスタンスです。 サービス プリンシパルは、そのアプリケーションオブジェクトから特定のプロパティを継承します。 サービス プリンシパル オブジェクトには、特定のテナント内でアプリが実際に実行できること、アプリにアクセスできるユーザー、アプリからアクセスできるリソースを定義します。 Metrics Advisor でサービス プリンシパルを使用するには、次のようにします。
    
        1. Azure AD アプリケーションの登録を作成します。 詳細については、「[Azure Data Explorer で Azure Active Directory アプリケーションの登録を作成する](/azure/data-explorer/provision-azure-ad-app)」を参照してください。

        1. Azure Data Explorer のデータベース アクセス許可を管理します。 詳細については、「[Azure Data Explorer のデータベース アクセス許可を管理する](/azure/data-explorer/manage-database-permissions)」を参照してください。 

        1. Metrics Advisor で資格情報エンティティを作成します。 Metrics Advisor で[資格情報エンティティを作成する](how-tos/credential-entity.md)方法を参照します。これにより、サービス プリンシパルの認証の種類のデータ フィードを追加する場合に、そのエンティティを選択できます。 
        
        接続文字列の例を次に示します。
        
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **キー コンテナーからのサービス プリンシパル**: Azure Key Vault では、クラウド アプリとサービスで使用される暗号化キーとシークレット値を保護するのに役立ちます。 Key Vault を使用すると、キーとシークレットの値を暗号化できます。 まず、サービス プリンシパルを作成してから、Key Vault 内にサービス プリンシパルを格納する必要があります。 詳細については、[キー コンテナーからのサービス プリンシパルの資格情報エンティティの作成](how-tos/credential-entity.md#sp-from-kv)に関するページを参照し、詳細な手順に従って、キー コンテナーからのサービス プリンシパルを設定します。 接続文字列の例を次に示します。 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

    * **マネージド ID**: Azure リソースに対するマネージド ID では、BLOB およびキュー データへのアクセスを認可できます。 マネージド ID では、Azure 仮想マシン、関数アプリ、仮想マシン スケール セット、その他のサービスで動作するアプリケーションから、Azure AD 資格情報を使用します。 Azure リソースのマネージド ID と Azure AD 認証を使用すると、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。 [マネージド ID で認可する方法](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm)を参照してください。 
    
        マネージド ID は、Azure Data Explorer (Kusto) の Azure portal で作成できます。 **[アクセス許可]**  >  **[追加]** の順に選択します。 推奨されるロールの種類は、**admin/viewer** です。
        
        ![Kusto のマネージド ID を示すスクリーンショット。](media/managed-identity-kusto.png)

        接続文字列の例を次に示します。 
        ```
        Data Source=<URI Server>;Initial Catalog=<Database>
        ```

     
* **クエリ**: データを取得して多次元時系列データに編成するには、[Kusto クエリ言語](/azure/data-explorer/kusto/query)に関するページを参照してください。 クエリでは、変数 `@IntervalStart` と `@IntervalEnd` を使用できます。 これらは `yyyy-MM-ddTHH:mm:ssZ` のように書式設定する必要があります。

  サンプル クエリ:
    
  ```kusto
  [TableName] | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd);    
  ```

  詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **アカウント名**: Azure Data Lake Storage Gen2 の認証の種類は、基本、Azure Data Lake Storage Gen2 共有キー、サービス プリンシパル、キー コンテナーからのサービス プリンシパルです。
    
    * **基本**: Azure Data Lake Storage Gen2 の **アカウント名**。 これは、Azure Storage アカウント (Azure Data Lake Storage Gen2) リソースの **[アクセス キー]** にあります。 

    * **Azure Data Lake Storage Gen2 共有キー**: 最初に、Azure Data Lake Storage Gen2 にアクセスするためのアカウント キーを指定します (これは、基本認証の種類のアカウント キーと同じです)。 これは、Azure Storage アカウント (Azure Data Lake Storage Gen2) リソースの **[アクセス キー]** にあります。 次に、Azure Data Lake Storage Gen2 共有キーの種類の[資格情報エンティティを作成し](how-tos/credential-entity.md)、アカウント キーを入力します。 

        アカウント名は、基本認証の種類と同じです。
    
    * **サービス プリンシパル**: *サービス プリンシパル* は、アプリケーション オブジェクトから作成された具象インスタンスであり、そのアプリケーション オブジェクトから特定のプロパティが継承されます。 サービス プリンシパルは、アプリケーションが使用される各テナントで作成され、グローバルに一意なアプリ オブジェクトが参照されます。 サービス プリンシパル オブジェクトには、特定のテナント内でアプリが実際に実行できること、アプリにアクセスできるユーザー、アプリからアクセスできるリソースを定義します。

        アカウント名は、基本認証の種類と同じです。
    
        **手順 1:** Azure AD アプリケーションを作成して登録し、データベースへのアクセスを認可します。 詳細については、[Azure AD アプリの登録の作成](/azure/data-explorer/provision-azure-ad-app)に関するページを参照してください。

        **手順 2:** ロールを割り当てます。
        
        1. Azure portal で、 **[ストレージ アカウント]** サービスに移動します。
        
        2. このアプリケーションの登録に使用する Azure Data Lake Storage Gen2 アカウントを選択します。

        3. **[アクセス制御 (IAM)]** を選択します。

        4. **[+ 追加]** を選択し、メニューから **[ロールの割り当ての追加]** を選択します。

        5. **[選択]** フィールドを [Azure AD アプリケーション名] に設定し、ロールを **[Storage Blob データ共同作成者]** に設定します。 次に、**[保存]** を選択します。
        
        ![ロールを割り当てる手順を示すスクリーンショット。](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

        **手順 3:** Metrics Advisor で [資格情報エンティティを作成する](how-tos/credential-entity.md)方法を参照します。これにより、サービス プリンシパルの認証の種類のデータ フィードを追加する場合に、そのエンティティを選択できます。 
        
    * **キー コンテナーからのサービス プリンシパル**: Key Vault では、クラウド アプリとサービスで使用される暗号化キーとシークレット値を保護するのに役立ちます。 Key Vault を使用すると、キーとシークレットの値を暗号化できます。 まず、サービス プリンシパルを作成してから、キー コンテナー内にサービス プリンシパルを格納する必要があります。 詳細については、[キー コンテナーからのサービス プリンシパルの資格情報エンティティの作成](how-tos/credential-entity.md#sp-from-kv)に関するページを参照してください。 アカウント名は、基本認証の種類と同じです。

* **アカウント キー** (基本認証の種類にのみ必要): Azure Data Lake Storage Gen2 にアクセスするためのアカウント キーを指定します。 これは、Azure Storage アカウント (Azure Data Lake Storage Gen2) リソースの **[アクセス キー]** にあります。

* **ファイル システム名 (コンテナー)** :Metrics Advisor では、1 つのコンテナーの下に BLOB ファイルとして格納された時系列データ (タイムスタンプごとに 1 つの BLOB) を格納します。 これはコンテナー名フィールドです。 これは、Azure Storage アカウント (Azure Data Lake Storage Gen2) リソース インスタンスにあります。 **[Data Lake Storage]** で、 **[コンテナー]** を選択すると、コンテナー名が表示されます。

* **ディレクトリ テンプレート**: これは BLOB ファイルのディレクトリ テンプレートです。 サポートされているパラメーターは次のとおりです。

   * `%Y` は `yyyy` として書式設定された年です。
   * `%m` は `MM` として書式設定された月です。
   * `%d` は `dd` として書式設定された日です。
   * `%h` は `HH` として書式設定された時間です。
   * `%M` は `mm` として書式設定された分です。

   日次メトリックのクエリ サンプル: `%Y/%m/%d`。

   時間単位メトリックのクエリ サンプル: `%Y/%m/%d/%h`。

* **ファイル テンプレート**: Metrics Advisor では、パスを使用して、Blob Storage で JSON ファイルを検索します。 以下は、Blob Storage: `%Y/%m/FileName_%Y-%m-%d-%h-%M.json` で JSON ファイルを検索するために使用される BLOB ファイル テンプレートの例です。 `%Y/%m` はパスです。パスに `%d` がある場合は、`%m` の後に追加できます。 
   
   サポートされているパラメーターは次のとおりです。
   
   * `%Y` は `yyyy` として書式設定された年です。
   * `%m` は `MM` として書式設定された月です。
   * `%d` は `dd` として書式設定された日です。
   * `%h` は `HH` として書式設定された時間です。
   * `%M` は `mm` として書式設定された分です。

   Metrics Advisor では、次の例のように JSON ファイルのデータ スキーマがサポートされています。

   ``` JSON
   [
     {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
     {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
   ]
   ```

## <a name="span-ideventhubsazure-event-hubsspan"></a><span id="eventhubs">Azure Event Hubs</span>

* **制限事項**: 統合に関する次の制限事項に注意してください。

   * Metrics Advisor と Event Hubs の統合では、現在、パブリック プレビューの 1 つの Metrics Advisor インスタンスで 3 つを超えるアクティブなデータ フィードがサポートされていません。
   * Metrics Advisor では、一時停止したデータ フィードを再アクティブ化する場合など、常に最新のオフセットからのメッセージの使用を開始します。
   
      * データ フィードの一時停止中のメッセージは失われます。
      * データ フィードの取り込みの開始時刻は、データ フィードの作成時に、協定世界時の現在のタイムスタンプに自動的に設定されます。 この時刻は参照のみを目的としています。

   * コンシューマー グループごとに使用できるデータ フィードは 1 つだけです。 削除された別のデータ フィードからコンシューマー グループを再利用するには、削除してから 10 分以上待つ必要があります。
   * データ フィードの作成後に、接続文字列とコンシューマー グループを変更することはできません。
   * Event Hubs メッセージの場合は、JSON のみがサポートされており、JSON 値を入れ子になった JSON オブジェクトにすることはできません。 最上位レベルの要素には、JSON オブジェクトまたは JSON 配列を指定できます。
    
    有効なメッセージは、次のとおりです。

    ``` JSON
    Single JSON object 
    {
    "metric_1": 234, 
    "metric_2": 344, 
    "dimension_1": "name_1", 
    "dimension_2": "name_2"
    }
    ```
        
    ``` JSON
    JSON array 
    [
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 12.4,
            "location": "outdoor"
        },
        {
            "timestamp": "2020-12-12T12:00:00", "temperature": 24.8,
            "location": "indoor"
        }
    ]
    ```


* **接続文字列**: Event Hubs のインスタンスに移動します。 次に、新しいポリシーを追加するか、既存の共有アクセス ポリシーを選択します。 ポップアップ パネルで接続文字列をコピーします。
    ![Event Hubs のスクリーンショット。](media/datafeeds/entities-eventhubs.jpg)
    
    ![共有アクセス ポリシーのスクリーンショット。](media/datafeeds/shared-access-policies.jpg)

    接続文字列の例を次に示します。 
    ```
    Endpoint=<Server>;SharedAccessKeyName=<SharedAccessKeyName>;SharedAccessKey=<SharedAccess Key>;EntityPath=<EntityPath>
    ```

* **コンシューマー グループ**: [コンシューマー グループ](../../event-hubs/event-hubs-features.md#consumer-groups)は、イベント ハブ全体のビュー (状態、位置、またはオフセット) を表します。
これは、Azure Event Hubs のインスタンスの **[コンシューマー グループ]** メニューで確認できます。 コンシューマー グループでは、1 つのデータ フィードに対してのみサービスを提供できます。 データ フィードごとに新しいコンシューマー グループを作成します。
* **タイムスタンプ** (省略可能): Metrics Advisor では、ユーザー データ ソースにタイムスタンプ フィールドが含まれていない場合、イベントのタイムスタンプとして Event Hubs タイムスタンプを使用します。 タイムスタンプ フィールドは省略可能です。 タイムスタンプ列が選択されていない場合、サービスでは、タイムスタンプとしてエンキューされた時刻を使用します。

    タイムスタンプ フィールドは、次の 2 つの形式のいずれかと一致する必要があります。
    
    * `YYYY-MM-DDTHH:MM:SSZ`
    * `1970-01-01T00:00:00Z` のエポックからの秒またはミリ秒数。
    
    タイムスタンプは、細分性に合わせて左揃えになります。 たとえば、タイムスタンプが `2019-01-01T00:03:00Z` の場合、細分性は 5 分になり、Metrics Advisor によって、タイムスタンプが `2019-01-01T00:00:00Z` に揃えられます。 イベントのタイムスタンプが `2019-01-01T00:10:00Z` の場合、Metrics Advisor では、配置なしでタイムスタンプを直接使用します。 


## <a name="span-idlogazure-monitor-logsspan"></a><span id="log">Azure Monitor ログ</span>

Azure Monitor ログには、基本、サービス プリンシパル、キー コンテナーからのサービス プリンシパルの認証の種類があります。
* **基本**: **[テナント ID]** 、 **[クライアント ID]** 、 **[クライアント シークレット]** 、 **[ワークスペース ID]** に入力する必要があります。
   **テナント ID**、**クライアント ID**、**クライアント シークレット** を取得するには、[アプリまたは Web API の登録](../../active-directory/develop/quickstart-register-app.md)に関するページを参照してください。 **ワークスペース ID** は、Azure portal で確認できます。
   
    ![Azure portal でワークスペース ID を検索する場所を示すスクリーンショット。](media/workspace-id.png)
    
* **サービス プリンシパル**: サービス プリンシパルは、アプリケーション オブジェクトから作成された具象インスタンスであり、そのアプリケーション オブジェクトから特定のプロパティが継承されます。 サービス プリンシパルは、アプリケーションが使用される各テナントで作成され、グローバルに一意なアプリ オブジェクトが参照されます。 サービス プリンシパル オブジェクトには、特定のテナント内でアプリが実際に実行できること、アプリにアクセスできるユーザー、アプリからアクセスできるリソースを定義します。
    
    **手順 1:** Azure AD アプリケーションを作成して登録し、データベースへのアクセスを認可します。 詳細については、[Azure AD アプリの登録の作成](/azure/data-explorer/provision-azure-ad-app)に関するページを参照してください。

    **手順 2:** ロールを割り当てます。
    1. Azure portal で、 **[ストレージ アカウント]** サービスに移動します。
    2. **[アクセス制御 (IAM)]** を選択します。
    3. **[+ 追加]** を選択し、メニューから **[ロールの割り当ての追加]** を選択します。
    4. **[選択]** フィールドを [Azure AD アプリケーション名] に設定し、ロールを **[Storage Blob データ共同作成者]** に設定します。 次に、**[保存]** を選択します。
    
        ![ロールの割り当て方法を示すスクリーンショット。](media/datafeeds/adls-gen-2-app-reg-assign-roles.png)

    
    **手順 3:** Metrics Advisor で [資格情報エンティティを作成する](how-tos/credential-entity.md)方法を参照します。これにより、サービス プリンシパルの認証の種類のデータ フィードを追加する場合に、そのエンティティを選択できます。 
        
* **キー コンテナーからのサービス プリンシパル**: Key Vault では、クラウド アプリとサービスで使用される暗号化キーとシークレット値を保護するのに役立ちます。 Key Vault を使用すると、キーとシークレットの値を暗号化できます。 まず、サービス プリンシパルを作成してから、キー コンテナー内にサービス プリンシパルを格納する必要があります。 詳細については、[キー コンテナーからのサービス プリンシパルの資格情報エンティティの作成](how-tos/credential-entity.md#sp-from-kv)に関するページを参照してください。 

* **クエリ**: クエリを指定します。 詳細については、「[Azure Monitor でのログ クエリ](../../azure-monitor/logs/log-query-overview.md)」を参照してください。

    サンプル クエリ:

    ``` Kusto
    [TableName]
    | where [TimestampColumn] >= datetime(@IntervalStart) and [TimestampColumn] < datetime(@IntervalEnd)
    | summarize [count_per_dimension]=count() by [Dimension]
    ```

    詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。

## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **接続文字列**: Azure SQL Database および SQL Server の認証の種類は、基本、マネージ ID、Azure SQL 接続文字列、サービス プリンシパル、キー コンテナーからのサービス プリンシパルです。
    
    * **基本**: Metrics Advisor では、SQL Server データ ソース用の [ADO.NET スタイルの接続文字列](/dotnet/framework/data/adonet/connection-string-syntax)を受け取ります。
    接続文字列の例を次に示します。 
    
        ```
        Data Source=<Server>;Initial Catalog=<db-name>;User ID=<user-name>;Password=<password>
        ```
    
    * <span id='jump'>**マネージド ID**</span>: Azure リソースに対するマネージド ID では、BLOB およびキュー データへのアクセスを認可できます。 これは、Azure 仮想マシン、関数アプリ、仮想マシン スケール セット、その他のサービスで動作するアプリケーションから、Azure AD 資格情報を使用します。 Azure リソースのマネージド ID と Azure AD 認証を使用すると、クラウドで動作するアプリケーションに資格情報を保存することを避けることができます。 [マネージ エンティティを有効にする](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql.md)には、次の手順に従います。
    1. システム割り当てマネージド ID の有効化は、1 クリックで行うことができます。 Azure portal の Metrics Advisor ワークスペースで、 **[設定]**  >  **[アイデンティティ]**  >  **[システム割り当て]** の順に移動します。 次に、[状態] を **[オン]** に設定します。 
    
        ![状態をオンに設定する方法を示すスクリーンショット。](media/datafeeds/set-identity-status.png)

    1. Azure AD 認証を有効にします。 Azure portal の データ ソースで、 **[設定]**  >  **[Active Directory 管理者]** の順に移動します。 **[管理者の設定]** を選択してから、サーバーの管理者になる **Azure AD ユーザー アカウント** を選択します。 次に **[選択]** を選択します。
    
        ![管理者の設定方法を示すスクリーンショット。](media/datafeeds/set-admin.png)

    1. Metrics Advisor でマネージド ID を有効にします。 データベース管理ツールまたは Azure portal でクエリを編集できます。
    
        **管理ツール**: データベース管理ツールで、[認証] フィールドの **[Active Directory - MFA サポートで汎用]** を選択します。 **[ユーザー名]** フィールドに、手順 2 でサーバー管理者として設定した Azure AD アカウントの名前を入力します。 例: `test@contoso.com`。
    
        ![接続の詳細を設定する方法を示すスクリーンショット。](media/datafeeds/connection-details.png)
        
        **Azure portal**: SQL データベースで、 **[クエリ エディター]** を選択し、管理者アカウントにサインインします。
        ![Azure portal でクエリを編集する方法を示すスクリーンショット。](media/datafeeds/query-editor.png)

        次に、クエリ ウィンドウで次のコマンドを実行します (管理ツールの方法でも同じになることに注意してください)。
    
        ```
        CREATE USER [MI Name] FROM EXTERNAL PROVIDER
        ALTER ROLE db_datareader ADD MEMBER [MI Name]
        ```
    
        > [!NOTE]
        > `MI Name` は、Metrics Advisor のマネージド ID 名 (サービス プリンシパルの場合は、サービス プリンシパル名に置き換える必要があります)。 詳細については、[マネージド ID を使用した認可](../../storage/blobs/authorize-managed-identity.md#enable-managed-identities-on-a-vm)に関するページを参照してください。 
            
        接続文字列の例を次に示します。 
       
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
        
    * **Azure SQL 接続文字列**: 
      

        接続文字列の例を次に示します。 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>;User ID=<user-name>;Password=<password>
        ```
  

    * **サービス プリンシパル**: サービス プリンシパルは、アプリケーション オブジェクトから作成された具象インスタンスであり、そのアプリケーション オブジェクトから特定のプロパティが継承されます。 サービス プリンシパルは、アプリケーションが使用される各テナントで作成され、グローバルに一意なアプリ オブジェクトが参照されます。 サービス プリンシパル オブジェクトには、特定のテナント内でアプリが実際に実行できること、アプリにアクセスできるユーザー、アプリからアクセスできるリソースを定義します。
    
        **手順 1:** Azure AD アプリケーションを作成して登録し、データベースへのアクセスを認可します。 詳細については、[Azure AD アプリの登録の作成](/azure/data-explorer/provision-azure-ad-app)に関するページを参照してください。

        **手順 2:** 前述の手順 ([SQL Server のマネージド ID](#jump)) に従います。 

        **手順 3:** Metrics Advisor で [資格情報エンティティを作成する](how-tos/credential-entity.md)方法を参照します。これにより、サービス プリンシパルの認証の種類のデータ フィードを追加する場合に、そのエンティティを選択できます。 

        接続文字列の例を次に示します。 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```
  
    * **キー コンテナーからのサービス プリンシパル**: Key Vault では、クラウド アプリとサービスで使用される暗号化キーとシークレット値を保護するのに役立ちます。 Key Vault を使用すると、キーとシークレットの値を暗号化できます。 まず、サービス プリンシパルを作成してから、キー コンテナー内にサービス プリンシパルを格納する必要があります。 詳細については、[キー コンテナーからのサービス プリンシパルの資格情報エンティティの作成](how-tos/credential-entity.md#sp-from-kv)に関するページを参照してください。 接続文字列は、Azure SQL Server リソースの **[設定]**  >  **[接続文字列]** でも確認できます。
        
        接続文字列の例を次に示します。 
        
        ```
        Data Source=<Server>;Initial Catalog=<Database>
        ```

* **クエリ**: SQL クエリを使用してデータを取得し、多次元時系列データに編成します。 クエリで `@IntervalStart` および `@IntervalEnd` を使用すると、期待されるメトリック値を一定間隔で取得するのに役立ちます。 これらは `yyyy-MM-ddTHH:mm:ssZ` のように書式設定する必要があります。


    サンプル クエリ:
    
    ```SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd    
    ```
    
## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **接続文字列**: SAS (Shared Access Signature) URL を作成し、ここに入力します。 SAS URL を生成する最も簡単な方法は、Azure portal を使用することです。 最初に、 **[設定]** で、アクセスするストレージ アカウントに移動します。 次に、 **[Shared Access Signature ]** を選択します。 **[テーブル]** および **[オブジェクト]** のチェックボックスをオンにし、 **[SAS と接続文字列を生成する]** を選択します。 Metrics Advisor ワークスペースで、**テーブル サービスの SAS URL** をコピーして、テキスト ボックスに入力します。

    ![Azure Table Storage で Shared Access Signature を生成する方法を示すスクリーンショット。](media/azure-table-generate-sas.png)

* **[テーブル名]** : クエリ対象のテーブルを指定します。 これは、Azure Storage アカウント インスタンスで確認できます。 **[テーブル サービス]** セクションで **[テーブル]** を選択します。

* **クエリ**: クエリで `@IntervalStart` および `@IntervalEnd`を使用すると、期待されるメトリック値を一定間隔で取得するのに役立ちます。 これらは `yyyy-MM-ddTHH:mm:ssZ` のように書式設定する必要があります。

    サンプル クエリ:
    
    ``` mssql
    PartitionKey ge '@IntervalStart' and PartitionKey lt '@IntervalEnd'
    ```

    詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。


## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **接続文字列**: InfluxDB にアクセスするための接続文字列。
* **データベース**:クエリ対象のデータベース。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。

    サンプル クエリ:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    
詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。

* **ユーザー名**:これは、認証に対しては省略可能です。 
* **パスワード**:これは、認証に対しては省略可能です。 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **接続文字列**: MongoDB にアクセスするための接続文字列。
* **データベース**:クエリ対象のデータベース。
* **クエリ**: データを取得して、インジェスト用の多次元時系列データに編成するコマンド。 [db.runCommand()](https://docs.mongodb.com/manual/reference/method/db.runCommand/index.html) で コマンドを確認します。

    サンプル クエリ:

    ``` MongoDB
    {"find": "[TableName]","filter": { [Timestamp]: { $gte: ISODate(@IntervalStart) , $lt: ISODate(@IntervalEnd) }},"singleBatch": true}
    ```
    

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **接続文字列**: MySQL DB にアクセスするための接続文字列。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。

    サンプル クエリ:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn]< @IntervalEnd
    ```

    詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **接続文字列**: PostgreSQL DB にアクセスするための接続文字列。
* **Query**: データを取得して、インジェスト用の多次元時系列データに編成するクエリ。

    サンプル クエリ:

    ``` SQL
    SELECT [TimestampColumn], [DimensionColumn], [MetricColumn] FROM [TableName] WHERE [TimestampColumn] >= @IntervalStart and [TimestampColumn] < @IntervalEnd
    ```
    詳細については、[有効なクエリの作成に関するチュートリアル](tutorials/write-a-valid-query.md)に関するページを参照してください。
    
## <a name="span-idcsvlocal-files-csvspan"></a><span id="csv">ローカル ファイル (CSV)</span>

> [!NOTE]
> この機能は、異常検出に重点を置いた迅速なシステム評価のみに使用されます。 ローカル CSV からの静的データのみを受け入れ、単一の時系列データに対して異常検出を実行します。 リアルタイム データ インジェスト、異常通知、根本原因分析、クロスメトリック インシデント分析など、多次元メトリックを分析するには、サポートされている他のデータ ソースを使用します。

**CSV のデータに関する要件:**
- 分析する測定値を表す列を少なくとも 1 つ用意します。 ユーザー エクスペリエンスを高めて迅速化するには、タイムスタンプ列とメトリック列の 2 つの列を含む CSV ファイルを試します。 タイムスタンプの形式は `2021-03-30T00:00:00Z` のようにする必要があります。`seconds` の部分は `:00Z` にするのが最適です。 すべてのレコード間の時間の細分性は同じである必要があります。
- タイムスタンプ列は省略可能です。 タイムスタンプがない場合は、Metrics Advisor では、今日から開始されるタイムスタンプ (`00:00:00` 協定世界時) を使用します。 このサービスは、行の各測定値を 1 時間間隔でマップします。
- データ インジェスト中に、並べ替えやギャップ埋めは発生しません。 CSV ファイル内のデータが、タイムスタンプの順序 (**昇順 (ASC)** ) で並べ替えられていることを確認します。
 
## <a name="next-steps"></a>次のステップ

* メトリック データがシステムに取り込まれるのを待つ間、[データ フィード構成の管理方法](how-tos/manage-data-feeds.md)についてお読みください。
* メトリック データが取り込まれたら、[メトリックを構成し、検出構成を微調整](how-tos/configure-metrics.md)できます。