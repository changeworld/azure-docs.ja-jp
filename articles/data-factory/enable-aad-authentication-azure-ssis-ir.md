---
title: Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする | Microsoft Docs
description: この記事では、Azure-SSIS 統合ランタイムを構成して Azure Active Directory 認証を使用する接続を有効にする方法について説明します。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: douglasl
ms.openlocfilehash: 93d3e25957fb1f04400fa78423a5658d32f7d5fd
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36749720"
---
# <a name="enable-azure-active-directory-authentication-for-the-azure-ssis-integration-runtime"></a>Azure-SSIS 統合ランタイムに対して Azure Active Directory 認証を有効にする

この記事では、Azure Data Factory サービス ID で Azure-SSIS IR を作成する方法を示します。 Azure-SSIS 統合ランタイムに対するマネージド サービス ID (MSI) を含む Azure Active Directory (Azure AD) 認証を使用すると、SQL 認証の代わりに Data Factory MSI を使用して Azure-SSIS 統合ランタイムを作成できます。

Data Factory MSI について詳しくは、「[Azure Data Factory サービス ID](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity)」をご覧ください。

> [!NOTE]
> SQL 認証で Azure-SSIS 統合ランタイムを既に作成してある場合、現時点では、PowerShell で Azure AD 認証を使うように IR を再構成することはできません。

## <a name="create-a-group-in-azure-ad-and-make-the-data-factory-msi-a-member-of-the-group"></a>Azure AD にグループを作成し、Data Factory MSI をそのグループのメンバーにする

既存の Azure AD グループを使用するか、Azure AD PowerShell を使用して新しいグループを作成できます。

1.  [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2) モジュールをインストールします。

2.  `Connect-AzureAD` を使用してサインインし、次のコマンドを実行してグループを作成し、それを変数に保存します。

    ```powershell
    $Group = New-AzureADGroup -DisplayName "SSISIrGroup" `
                              -MailEnabled $false `
                              -SecurityEnabled $true `
                              -MailNickName "NotSet"
    ```

    出力は次の例のようになります。これは変数の値も調べます。

    ```powershell
    $Group

    ObjectId DisplayName Description
    -------- ----------- -----------
    6de75f3c-8b2f-4bf4-b9f8-78cc60a18050 SSISIrGroup
    ```

3.  Data Factory MSI をグループに追加します。 「[Azure Data Factory サービス ID](https://docs.microsoft.com/en-us/azure/data-factory/data-factory-service-identity)」に従って、プリンシパル サービス ID (例: 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc。ただしこの目的ではサービス ID アプリケーション ID を使用しないでください) を取得できます。

    ```powershell
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
    ```

    後でグループ メンバーシップを調べることもできます。

    ```powershell
    Get-AzureAdGroupMember -ObjectId $Group.ObjectId
    ```

## <a name="enable-azure-ad-on-azure-sql-database"></a>Azure SQL Database で Azure AD を有効にする

Azure SQL Database は、Azure AD ユーザーを使用したデータベースの作成をサポートしています。 そのため、Azure AD ユーザーを Active Directory 管理者として設定し、Azure AD ユーザーを使用して SSMS にログインできます。 その後、Azure AD グループに含まれるユーザーを作成し、IR がサーバー上に SQL Server Integration Services (SSIS) カタログを作成できるようにすることができます。

### <a name="enable-azure-ad-authentication-for-the-azure-sql-database"></a>Azure SQL Database に対する Azure AD 認証を有効にする

次の手順を使用して、[SQL Database に対する Azure AD 認証を構成する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)ことができます。

1.  Azure portal で、左側のナビゲーションから **[すべてのサービス]** -> **[SQL Server]** を選択します。

2.  Azure AD 認証を有効にする SQL Database を選択します。

3.  ブレードの **[設定]** セクションで **[Active Directory 管理者]** を選択します。

4.  コマンド バーで、**[管理者の設定]** を選択します。

5.  サーバーの管理者になる Azure AD ユーザー アカウントを選択して、**[選択]** を選びます。

6.  コマンド バーで、**[保存]** を選択します。

### <a name="create-a-contained-user-in-the-database-that-represents-the-azure-ad-group"></a>Azure AD グループを表すデータベース内に包含ユーザーを作成する

この次のステップでは、[Microsoft SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) が必要になります。

1.  SQL Server Management Studio を起動します。

2.  **[サーバーに接続]** ダイアログで、**[サーバー名]** フィールドに SQL サーバーの名前を入力します。

3.  **[認証]** フィールドで、**[Active Directory - MFA サポートで汎用]** を選択します。 (他の 2 つの Active Directory 認証の種類も使用できます。 「[SQL Database、Managed Instance、または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)」をご覧ください。)

4.  **[ユーザー名]** フィールドに、サーバー管理者として設定した Azure AD アカウントの名前を入力します (例: testuser@xxxonline.com)。

5.  **[接続]** を選択します。 サインイン プロセスを完了します。

6.  **オブジェクト エクスプローラー**で、**[データベース]** > [システム データベース] フォルダーを展開します。

7.  **master** データベースを右クリックして、**[新しいクエリ]** を選択します。

8.  クエリ ウィンドウで、次の行を入力し、ツールバーの **[実行]** を選択します。

    ```sql
    CREATE USER [SSISIrGroup] FROM EXTERNAL PROVIDER
    ```

    コマンドは正常に完了し、グループの包含ユーザーが作成されます。

9.  クエリ ウィンドウをクリアし、次の行を入力して、ツールバーの **[実行]** を選択します。

    ```sql
    ALTER ROLE dbmanager ADD MEMBER [SSISIrGroup]
    ```

    コマンドは正常に完了し、包含ユーザーにデータベースを作成する権限が与えられます。

## <a name="enable-azure-ad-on-azure-sql-database-managed-instance"></a>Azure SQL Database Managed Instance で Azure AD を有効にする

Azure SQL Database Managed Instance は、AD 管理者以外の Azure AD ユーザーによるデータベースの作成をサポートしていません。そのため、Active Directory 管理者として Azure AD グループを設定する必要があります。包含ユーザーを作成する必要はありません。

次の手順を使用して、[SQL Database マネージド インスタンス サーバーに対する Azure AD 認証を構成する](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure)ことができます。

7.  Azure portal で、左側のナビゲーションから **[すべてのサービス]** -> **[SQL Server]** を選択します。

8.  Azure AD 認証で有効にする SQL サーバーを選択します。

9.  ブレードの **[設定]** セクションで **[Active Directory 管理者]** を選択します。

10. コマンド バーで、**[管理者の設定]** を選択します。

11. Azure AD グループ (SSISIrGroup など) を検索して選択し、**[選択]** を選びます。

12. コマンド バーで、**[保存]** を選択します。

## <a name="provision-the-azure-ssis-ir-in-the-portal"></a>ポータルで Azure-SSIS IR をプロビジョニングする

Azure portal で Azure-SSIS IR をプロビジョニングするときは、**[SQL Settings]\(SQL の設定\)** ページで、[Use AAD authentication with your ADF MSI]\(ADF MSI で AAD 認証を使用する\) オプションをオンにします。 (次のスクリーンショットは、Azure SQL Database での IR の設定を示します。 マネージド インスタンスでの IR の場合、[Catalog Database Service Tier]\(カタログ データベース サービス階層\) プロパティは使用できません。その他の設定は同じです。)

Azure-SSIS 統合ランタイムの作成方法について詳しくは、「[Azure Data Factory で Azure-SSIS 統合ランタイムを作成する](https://docs.microsoft.com/en-us/azure/data-factory/create-azure-ssis-integration-runtime)」をご覧ください。

![Azure-SSIS 統合ランタイムの設定](media/enable-aad-authentication-azure-ssis-ir/enable-aad-authentication.png)

## <a name="provision-the-azure-ssis-ir-with-powershell"></a>PowerShell で Azure-SSIS IR をプロビジョニングする

PowerShell を使用して Azure-SSIS IR をプロビジョニングするには、次のようにします。

1.  [Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v5.5.0-March2018) モジュールをインストールします。

2.  スクリプトでは、*CatalogAdminCredential* パラメーターを設定しないでください。 例: 

    ```powershell
    Set-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                               -DataFactoryName $DataFactoryName `
                                               -Name $AzureSSISName `
                                               -Type Managed `
                                               -CatalogServerEndpoint $SSISDBServerEndpoint `
                                               -CatalogPricingTier $SSISDBPricingTier `
                                               -Description $AzureSSISDescription `
                                               -Edition $AzureSSISEdition `
                                               -Location $AzureSSISLocation `
                                               -NodeSize $AzureSSISNodeSize `
                                               -NodeCount $AzureSSISNodeNumber `
                                               -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                               -SetupScriptContainerSasUri $SetupScriptContainerSasUri

    Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                                 -DataFactoryName $DataFactoryName `
                                                 -Name $AzureSSISName
   ```
