---
title: Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする
description: この記事では、Azure-SSIS 統合ランタイムを作成するために、Azure Data Factory の指定されたシステム/ユーザー割り当てマネージド ID を使用して Azure Active Directory 認証を有効にする方法について説明します。
ms.service: data-factory
ms.subservice: integration-services
ms.devlang: powershell
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.custom: seo-lt-2019
ms.date: 10/22/2021
ms.openlocfilehash: c800193e027821df00167930d06d9c13ad8cb67d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843651"
---
# <a name="enable-azure-active-directory-authentication-for-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

この記事では、Azure Data Factory (ADF) の指定されたシステム/ユーザー割り当てマネージド ID を使用した Azure Active Directory (Azure AD) 認証を有効にし、従来の認証方法 (SQL 認証など) の代わりにそれを使用して以下を行う方法について説明します。

- お客様に代わって Azure SQL Database サーバー/Managed Instance で SSIS カタログ データベース (SSISDB) をプロビジョニングする Azure SSIS 統合ランタイム (IR) を作成します。

- Azure SSIS IR で SSIS パッケージを実行するときに、さまざまな Azure リソースに接続します。

ADF のマネージド ID について詳しくは、「[Data Factory のマネージド ID](./data-factory-service-identity.md)」をご覧ください。

> [!NOTE]
> - このシナリオで、ADF の指定されたシステム/ユーザー割り当てマネージド ID を使用した Azure AD 認証は、Azure-SSIS IR のプロビジョニングとその後の開始操作でのみ使用されます。それにより SSISDB のプロビジョニングと接続が行われます。 SSIS パッケージの実行については、Azure-SSIS IR は SSISDB のプロビジョニング中に作成されるフル マネージド アカウント (*AzureIntegrationServiceDbo* および *AzureIntegrationServiceWorker*) での SQL 認証を使用して SSISDB に引き続き接続し、パッケージをフェッチします。
>
> - SQL 認証を使用して Azure-SSIS IR を既に作成している場合、現時点では、PowerShell を介して Azure AD 認証を使用するように再構成することはできませんが、Azure portal/ADF アプリを介して行うことは可能です。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="enable-azure-ad-authentication-on-azure-sql-database"></a>Azure SQL Database に対して Azure AD 認証を有効にする

Azure SQL Database は、Azure AD ユーザーを使用したデータベースの作成をサポートしています。 最初に、ADF の指定されたシステム/ユーザー割り当てマネージド ID をメンバーとして使用して Azure AD グループを作成する必要があります。 次に、Azure SQL Database サーバーの Active Directory 管理者として Azure AD ユーザーを設定してから、そのユーザーを使用して SQL Server Management Studio (SSMS) でそこに接続する必要があります。 最後に、Azure-SSIS IR が ADF の指定されたシステム/ユーザー割り当てマネージド ID を使用してユーザーに代わって SSISDB を作成できるように、Azure AD グループを表す包含ユーザーを作成する必要があります。

### <a name="create-an-azure-ad-group-with-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-member"></a>ADF の指定されたシステム/ユーザー割り当てマネージド ID をメンバーとして使用して Azure AD グループを作成する

既存の Azure AD グループを使用するか、または Azure AD PowerShell を使用して新しいグループを作成できます。

1. [Azure AD PowerShell](/powershell/azure/active-directory/install-adv2) モジュールをインストールします。

2. `Connect-AzureAD` を使用してサインインし、次のコマンドレットを実行してグループを作成し、それを変数に保存します。

   ```powershell
   $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                             -MailEnabled $false `
                             -SecurityEnabled $true `
                             -MailNickName "NotSet"
   ```

   結果は次の例のようになります。ここには、変数値も表示されます。

   ```powershell
   $Group

   ObjectId DisplayName Description
   -------- ----------- -----------
   6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
   ```

3. ADF の指定されたシステム/ユーザー割り当てマネージド ID をグループに追加します。 「[Data Factory のマネージド ID](./data-factory-service-identity.md)」の記事に従って、ADF の指定されたシステム/ユーザー割り当てマネージド ID のオブジェクト ID (たとえば 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc。ただし、この目的にアプリケーション ID は使用しないでください) を取得できます。

   ```powershell
   Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
   ```

   後でグループ メンバーシップを確認することもできます。

   ```powershell
   Get-AzureAdGroupMember -ObjectId $Group.ObjectId
   ```

### <a name="configure-azure-ad-authentication-for-azure-sql-database"></a>Azure SQL Database に対する Azure AD 認証を構成する

次の手順を使用して、[Azure SQL Database に対する Azure AD 認証を構成して管理する](../azure-sql/database/authentication-aad-configure.md)ことができます。

1. Azure Portal で、左側のナビゲーションから **[すべてのサービス]**  ->  **[SQL Server]** を選択します。

2. Azure AD 認証を構成する Azure SQL Database サーバーを選択します。

3. ブレードの **[設定]** セクションで **[Active Directory 管理者]** を選択します。

4. コマンド バーで、 **[管理者の設定]** を選択します。

5. サーバーの管理者にする Azure AD ユーザー アカウントを選択してから、 **[選択]** を選択します。

6. コマンド バーで、 **[保存]** を選択します。

### <a name="create-a-contained-user-in-azure-sql-database-representing-the-azure-ad-group"></a>Azure AD グループを表す包含ユーザーを Azure SQL Database に作成する

次の手順では、[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) が必要になります。

1. SSMS を起動します。

2. **[サーバーに接続]** ダイアログで、 **[サーバー名]** フィールドにサーバー名を入力します。

3. **[認証]** フィールドで、 **[Active Directory - MFA サポートで汎用]** を選択します (他の 2 つの Active Directory 認証の種類を使用することもできます。[Azure SQL Database の Azure AD 認証の構成と管理](../azure-sql/database/authentication-aad-configure.md)に関する記事を参照)。

4. **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前 (testuser@xxxonline.com など) を入力します。

5. **[接続]** を選択し、サインイン プロセスを完了します。

6. **オブジェクト エクスプローラー** で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

7. **マスター** データベースを右クリックし、 **[新しいクエリ]** を選択します。

8. クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

   ```sql
   CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
   ```

   このコマンドは正常に完了し、グループを表す包含ユーザーが作成されます。

9. クエリ ウィンドウをクリアして、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

   ```sql
   ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
   ```

   このコマンドは正常に完了し、包含ユーザーにデータベース (SSISDB) を作成する権限が与えられます。

10. SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、まず、**マスター** データベースにアクセス許可を付与する上記の手順が正常に完了していることを確認してください。 次に、**SSISDB** データベースを右クリックし、 **[新しいクエリ]** を選択します。

    1. クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

       ```sql
       CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
       ```

       このコマンドは正常に完了し、グループを表す包含ユーザーが作成されます。

    2. クエリ ウィンドウをクリアして、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

       ```sql
       ALTER ROLE db_owner ADD MEMBER [SSISIrGroup]
       ```

       コマンドは正常に完了し、包含ユーザーに SSISDB にアクセスする権限が与えられます。

## <a name="enable-azure-ad-authentication-on-azure-sql-managed-instance"></a>Azure SQL Managed Instance に対する Azure AD 認証を有効にする

Azure SQL Managed Instance は、ADF の指定されたシステム/ユーザー割り当てマネージド ID によるデータベースの直接の作成をサポートしています。 ADF の指定されたシステム/ユーザー割り当てマネージド ID を Azure AD グループに参加させたり、そのグループを表す包含ユーザーを Azure SQL Managed Instance 内に作成したりする必要はありません。

### <a name="configure-azure-ad-authentication-for-azure-sql-managed-instance"></a>Azure SQL Managed Instance に対する Azure AD 認証を構成する

[Azure SQL Managed Instance の Azure AD 管理者のプロビジョニング](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)の手順に従います。

### <a name="add-the-specified-systemuser-assigned-managed-identity-for-your-adf-as-a-user-in-azure-sql-managed-instance"></a>ADF の指定されたシステム/ユーザー割り当てマネージド ID を Azure SQL Managed Instance にユーザーとして追加する

次の手順では、[SSMS](/sql/ssms/download-sql-server-management-studio-ssms) が必要になります。

1. SSMS を起動します。

2. **sysadmin** である SQL Server アカウントを使用して、Azure SQL Managed Instance に接続します。 これは、Azure SQL Managed Instance で Azure AD サーバー プリンシパル (ログイン) のサポートが一般提供されるようになると削除される一時的な制限事項です。 Azure AD 管理者アカウントを使用してログインを作成しようとすると、次のエラーが表示されます。"*Msg 15247、Level 16、State 1、Line 1 このアクションを実行するアクセス許可がユーザーにありません*"

3. **オブジェクト エクスプローラー** で、 **[データベース]**  ->  **[システム データベース]** フォルダーを展開します。

4. **マスター** データベースを右クリックし、 **[新しいクエリ]** を選択します。

5. クエリ ウィンドウで、次の T-SQL スクリプトを実行して、ADF の指定されたシステム/ユーザー割り当てマネージド ID をユーザーとして追加します。

   ```sql
   CREATE LOGIN [{your managed identity name}] FROM EXTERNAL PROVIDER
   ALTER SERVER ROLE [dbcreator] ADD MEMBER [{your managed identity name}]
   ALTER SERVER ROLE [securityadmin] ADD MEMBER [{your managed identity name}]
   ```

   ADF にシステム マネージド ID を使用する場合、*your managed identity name* はご利用の ADF 名である必要があります。 ADF にユーザー割り当てマネージド ID を使用する場合、*your managed identity name* は、指定されたユーザー割り当てマネージド ID 名である必要があります。
    
   このコマンドは正常に完了し、ADF のシステム/ユーザー割り当てマネージド ID にデータベース (SSISDB) を作成する権限が与えられます。

6. SQL 認証を使用して作成された SSISDB を、Azure-SSIS IR でアクセスするために Azure AD 認証を使用するように切り替えたい場合は、まず、**マスター** データベースにアクセス許可を付与する上記の手順が正常に完了していることを確認してください。 次に、**SSISDB** データベースを右クリックし、 **[新しいクエリ]** を選択します。

   1. クエリ ウィンドウで、次の T-SQL コマンドを入力し、ツールバーの **[実行]** を選択します。

      ```sql
      CREATE USER [{your managed identity name}] FOR LOGIN [{your managed identity name}] WITH DEFAULT_SCHEMA = dbo
      ALTER ROLE db_owner ADD MEMBER [{your managed identity name}]
      ```

      このコマンドは正常に完了し、ADF のシステム/ユーザー割り当てマネージド ID に SSISDB にアクセスする権限が与えられます。

## <a name="provision-azure-ssis-ir-in-azure-portaladf-app"></a>Azure Portal/ADF アプリで Azure-SSIS IR をプロビジョニングする

Azure portal/ADF アプリで Azure-SSIS IR をプロビジョニングする場合は、 **[展開設定]** ページで、 **[Create SSIS catalog (SSISDB) hosted by Azure SQL Database server/Managed Instance to store your projects/packages/environments/execution logs]\(Azure SQL Database サーバー/マネージド インスタンスによってホストされる SSIS カタログ (SSISDB) を作成して、プロジェクト、パッケージ、環境、実行ログを格納する\)** チェック ボックスをオンにして、 **[Use AAD authentication with the system managed identity for Data Factory]\(Data Factory のシステム管理 ID による AAD 認証を使用する\)** または **[Use AAD authentication with a user-assigned managed identity for Data Factory]\(Data Factory のユーザー割り当てマネージド ID による AAD 認証を使用する\)** チェック ボックスをオンにして、SSISDB をホストするデータベース サーバーにアクセスするための Azure-SSIS IR の Azure AD 認証方法を選択します。

詳細については、[ADF での Azure-SSIS IR の作成](./create-azure-ssis-integration-runtime.md)に関する記事を参照してください。

## <a name="provision-azure-ssis-ir-with-powershell"></a>PowerShell で Azure-SSIS IR をプロビジョニングする

PowerShell を使用して Azure-SSIS IR をプロビジョニングするには、次のようにします。

1. [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) モジュールをインストールします。

2. スクリプトで `CatalogAdminCredential` パラメーターを設定しないでください。 次に例を示します。

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                         -DataFactoryName $DataFactoryName `
                                         -Name $AzureSSISName `
                                         -Description $AzureSSISDescription `
                                         -Type Managed `
                                         -Location $AzureSSISLocation `
                                         -NodeSize $AzureSSISNodeSize `
                                         -NodeCount $AzureSSISNodeNumber `
                                         -Edition $AzureSSISEdition `
                                         -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                         -CatalogServerEndpoint $SSISDBServerEndpoint `
                                         -CatalogPricingTier $SSISDBPricingTier

   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                           -DataFactoryName $DataFactoryName `
                                           -Name $AzureSSISName
   ```

## <a name="run-ssis-packages-using-azure-ad-authentication-with-the-specified-systemuser-assigned-managed-identity-for-your-adf"></a>ADF の指定されたシステム/ユーザー割り当てマネージド ID による Azure AD 認証を使用して SSIS パッケージを実行する

Azure-SSIS IR で SSIS パッケージを実行する場合、ADF の指定されたシステム/ユーザー割り当てマネージド ID による Azure AD 認証を使用して、さまざまな Azure リソースに接続できます。 現在、以下の接続マネージャーで、ADF の指定されたシステム/ユーザー割り当てマネージド ID を使用した Azure AD 認証がサポートされています。

- [OLEDB 接続マネージャー](/sql/integration-services/connection-manager/ole-db-connection-manager#managed-identities-for-azure-resources-authentication)

- [ADO.NET 接続マネージャー](/sql/integration-services/connection-manager/ado-net-connection-manager#managed-identities-for-azure-resources-authentication)

- [Azure Storage 接続マネージャー](/sql/integration-services/connection-manager/azure-storage-connection-manager#managed-identities-for-azure-resources-authentication)
