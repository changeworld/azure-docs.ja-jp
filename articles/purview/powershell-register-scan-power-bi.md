---
title: PowerShell を使用した Power BI の登録とスキャン (プレビュー)
description: PowerShell を使用して Azure Purview で Power BI テナントを登録およびスキャンする方法について説明します。
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/03/2020
ms.locfileid: "96550612"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>PowerShell を使用した Azure Purview での Power BI の登録とスキャン (プレビュー) 

この記事では、PowerShell を使用して、Azure Purview カタログで Power BI テナントのスキャンを設定する方法について説明します。

## <a name="power-bi-authentication-background"></a>Power BI 認証の背景

Power BI テナント内の成果物をスキャンするには、Purview カタログで Power BI の管理 API に接続する必要があります。 Power BI の管理 API では現在、2 種類の認証がサポートされています。

- マネージド ID (MSI)
- 委任されたユーザー認証

> [!Note]
> 委任されたユーザー認証が必須である場合を除き、MSI をお勧めします。

## <a name="create-a-security-group"></a>セキュリティ グループの作成

すべての Purview カタログには独自のシステム割り当てマネージド ID があり、スキャンを有効にするためには、そのID に Power BI テナントへのアクセス権が付与されている必要があります。 カタログ名を使用すると、Azure portal 上でその ID を検索できます。

1. [Azure portal](https://portal.azure.com) で、Azure Active Directory を検索します。
1. 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)」に従って、Azure Active Directory で新しいセキュリティ グループを作成します。

    > [!Tip]
    > 使用するセキュリティ グループが既にある場合は、この手順を省略できます。

1. 必ず **[グループの種類]** として [セキュリティ] を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="セキュリティ グループの種類":::

1. [メンバー]、 **[+ メンバーの追加]** の順に選択して、カタログのマネージド ID をこのセキュリティ グループに追加します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="カタログのマネージド インスタンスをグループに追加する":::

1. カタログを検索して選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="カタログを検索して追加する":::

1. 追加されたことを示す成功通知が表示されます。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="カタログの MSI の追加の成功":::

## <a name="associate-the-security-group-with-power-bi"></a>セキュリティ グループの Power BI への関連付け

1. [Power BI 管理ポータル](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1)にログインします。 機能フラグ `allowServicePrincipalsUseReadAdminAPIsUI=1` を追加します。 このフラグにより、セキュリティ グループの関連付けを可能にする機能が有効になります。 たとえば、

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > テナント設定のページを表示するには、Power BI 管理者である必要があります。

1. **[開発者向け設定]**  >  **[読み取り専用 Power BI API の使用をサービス プリンシパルに許可 (プレビュー)]** の順に選択します。
1. **[特定のセキュリティ グループ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="サービス プリンシパルが読み取り専用 Power BI 管理 API のアクセス許可を取得できるようにする方法を示す画像":::

    > [!Caution]
    > 作成したセキュリティ グループ (データ カタログのマネージド ID をメンバーとして持つ) に読み取り専用 Power BI 管理 API の使用を許可するときは、このテナント内のすべての Power BI 成果物のメタデータ (ダッシュボードやレポートの名前、所有者、説明など) へのアクセスも許可します。 メタデータが Azure Purview に取り込まれると、Purview のアクセス許可 (Power BI のアクセス許可ではない) によって、そのメタデータを表示できるユーザーが決まります。

    > [!Note]
    > 開発者向け設定からセキュリティ グループを削除できますが、以前に抽出されたメタデータは Purview アカウントから削除されません。 これは個別に削除することができます。

## <a name="register-power-bi-and-set-up-a-scan"></a>Power BI の登録とスキャンの設定

Power BI テナントの管理 API に接続するためのカタログのアクセス許可が付与されたので、次はカタログでスキャンを設定する必要があります。 これを行うには、PowerShell スクリプトを構成して実行します。

1. ADC PowerShell コマンドレットをダウンロードして抽出します。
1. スクリプトの先頭に代入する値を指定して、スクリプトを構成します。

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > コマンドが実行されるサブスクリプションの共同作成者または所有者である必要があります。

1. 次のスクリプトを実行してスキャンを開始します。

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Power BI の登録とスキャン

推奨されている認証方法は MSI です。 ただし、カタログとは異なる Azure テナントにある Power BI テナントをスキャンするには、委任された認証を使用します。

委任された認証を行うには、管理者ユーザーの資格情報だけでなく、Power BI 管理者の資格情報も必要です。 また、Azure アプリを作成し、それに Power BI の Tenant.ReadAll アクセス許可を付与する必要もあります。

1. [Azure portal](https://portal.azure.com) に移動し、 **[アプリの登録]** を検索します。

1. **[アプリの登録]** で、 **[+ 新規登録]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="新しい Azure アプリの登録の作成方法を示す画像":::

1. アプリの名前を入力します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="新しいアプリを登録する":::

1. アプリが作成されたら、 **[API のアクセス許可]** 、 **[+ アクセス許可の追加]** の順に選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="アプリにアクセス許可を追加する方法を示す画像":::

1. **[API アクセス許可の要求]** で **[Power BI サービス]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="PBI サービスの選択方法を示す画像":::

1. **[委任されたアクセス許可]** と **[Tenant.Read.All]** を選択します。 **[アクセス許可の追加]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="API のアクセス許可を要求する方法を示す画像":::

1. **[管理者の同意の付与]** を選択します

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="管理者の同意を付与する方法を示す画像":::

1. **[アプリケーション (クライアント) ID]** と **[ディレクトリ (テナント) ID]** の値をコピーします。  これらの値は、スキャンを設定するときに使用します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="クライアントとテナントの ID のコピーを示す画像":::

1. PowerShell でスキャンを構成します。 このスクリプトでは、資格情報の入力が求められます。 使用する Azure サブスクリプションには、少なくとも共同作成者ロールと Purview データ ソース管理者ロールが必要です。

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. スキャンを実行します。

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>次のステップ

Azure Purview の使用を開始するには、[Azure Purview アカウントの作成](create-catalog-portal.md)に関するクイックスタートを参照してください。
