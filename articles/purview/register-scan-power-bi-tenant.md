---
title: Power BI テナントに接続して管理する
description: このガイドでは、Azure Purview で Power BI テナントに接続し、Purview の機能を使用して Power BI テナント ソースをスキャンおよび管理する方法について説明します。
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 39b6dd297ad0fbb739272db41900e68c799e790d
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853803"
---
# <a name="connect-to-and-manage-a-power-bi-tenant-in-azure-purview"></a>Azure Purview で Power BI テナントに接続して管理する

この記事では、Power BI テナントを登録する方法と、Azure Purview でテナントを認証して操作する方法について概説します。 Azure Purview の詳細については、[概要の記事](overview.md)を参照してください。

## <a name="supported-capabilities"></a>サポートされる機能

|**メタデータの抽出**|  **フル スキャン**  |**増分スキャン**|**スコープ スキャン**|**分類**|**アクセス ポリシー**|**系列**|
|---|---|---|---|---|---|---|
| [あり](#register)| [あり](#scan)| いいえ | いいえ | いいえ | いいえ| [はい](how-to-lineage-powerbi.md)|

> [!Note]
> Purview インスタンスと Power BI テナントが同じ Azure テナントにある場合、Power BI テナントのスキャンの設定にはマネージド ID (MSI) 認証のみが使用できます。

### <a name="known-limitations"></a>既知の制限事項

-   テナント間のシナリオでは、現在、テナント間の Power BI テナントの登録とスキャンに使用できる UX エクスペリエンスはありません。
-   Purview Studio を使用して PowerShell により登録されている Power BI クロス テナントを編集すると、データ ソース登録が一貫性のないスキャン動作で改ざんされます。
-   [Power BI メタデータのスキャンの制限](/power-bi/admin/service-admin-metadata-scanning)に関するページを確認してください。


## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* アクティブな [Purview リソース](create-catalog-portal.md)。

* Purview Studio でソースを登録して管理するには、データ ソース管理者およびデータ閲覧者である必要があります。 詳細については、[Azure Purview のアクセス許可](catalog-permissions.md)に関するページを参照してください。

## <a name="register"></a>登録

このセクションでは、[同じテナント](#authentication-for-a-same-tenant-scenario)と[テナント間](#steps-to-register-cross-tenant)の両方のシナリオで Azure Purview に Power BI テナントを登録する方法について説明します。

### <a name="authentication-for-a-same-tenant-scenario"></a>同じテナント シナリオの認証

同じテナントとテナント間の両方のシナリオで、認証を設定するには、セキュリティ グループを作成し、Purview のマネージド ID を追加します。

1. [Azure portal](https://portal.azure.com) で、**Azure Active Directory** を検索します。
1. 「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)」に従って、Azure Active Directory で新しいセキュリティ グループを作成します。

    > [!Tip]
    > 使用するセキュリティ グループが既にある場合は、この手順を省略できます。

1. **[グループの種類]** として **[セキュリティ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="セキュリティ グループの種類のスクリーンショット。":::

1. Purview のマネージド ID をこのセキュリティ グループに追加します。 **[メンバー]** を選択してから **[+ メンバーの追加]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="カタログのマネージド インスタンスをグループに追加する方法のスクリーンショット。":::

1. Purview のマネージド ID を検索し、それを選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="名前を検索してカタログを追加する方法を示すスクリーンショット。":::

    追加されたことを示す成功通知が表示されます。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="カタログ MSI の正常な追加を示すスクリーンショット。":::

#### <a name="associate-the-security-group-with-the-tenant"></a>セキュリティ グループのテナントへの関連付け

1. [Power BI 管理ポータル](https://app.powerbi.com/admin-portal/tenantSettings)にログインします。
1. **[テナント設定]** ページを選択します。

    > [!Important]
    > テナント設定のページを表示するには、Power BI 管理者である必要があります。

1. **[管理者 API 設定]**  >  **[Allow service principals to use read-only Power BI admin APIs (Preview)]\(読み取り専用 Power BI Admin API の使用をサービス プリンシパルに許可 (プレビュー)\)** を選択します。
1. **[特定のセキュリティ グループ]** を選択します。

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="サービス プリンシパルが読み取り専用 Power BI Admin API のアクセス許可を取得できるようにする方法を示す画像。":::

1. **[管理 API の設定]**  >  **[詳細メタデータを使用して管理 API の応答を強化する]** の順に選択し、トグルを有効にして、Purview Data Map が、そのスキャンの一部として Power BI データセットの詳細メタデータを自動的に検出できるようにします。

    > [!IMPORTANT]
    > Power BI テナントで管理 API の設定を更新した後、スキャンの登録の前に約 15 分待ってから接続をテストします。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-sub-artifacts.png" alt-text="サブ成果物のスキャンを有効にする Power BI 管理ポータル構成を示す画像。":::

    > [!Caution]
    > 作成したセキュリティ グループ (Purview のマネージド ID をメンバーとして持つ) に読み取り専用 Power BI Admin API の使用を許可するときは、このテナント内のすべての Power BI 成果物のメタデータ (ダッシュボードやレポートの名前、所有者、説明など) へのアクセスも許可します。 メタデータが Azure Purview に取り込まれると、Purview のアクセス許可 (Power BI のアクセス許可ではない) によって、そのメタデータを表示できるユーザーが決まります。
  
    > [!Note]
    > 開発者向け設定からセキュリティ グループを削除することはできますが、以前に抽出されたメタデータは Purview アカウントから削除されません。 これは、必要に応じて個別に削除することができます。

### <a name="steps-to-register-in-the-same-tenant"></a>同じテナントに登録する手順

これで、Power BI テナントの Admin API に接続するための Purview のマネージド ID のアクセス許可を付与したので、Azure Purview Studio からスキャンを設定できます。

1. 左側のナビゲーションで **[Data Map]** を選択します。

1. 次に、 **[登録]** を選択します。

    データ ソースとして **[Power BI]** を選択します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/select-power-bi-data-source.png" alt-text="選択可能なデータ ソースの一覧を示す画像。":::

1. Power BI インスタンスにフレンドリ名を付けます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-friendly-name.png" alt-text="Power BI データ ソースのフレンドリ名を示す画像。":::

    名前の長さは 3 から 63 文字で、文字、数字、アンダースコア、およびハイフンのみを使用できます。  スペースは使用できません。

    既定では、同じ Azure サブスクリプションに存在する Power BI テナントがシステムによって検出されます。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-datasource-registered.png" alt-text="登録済みの Power BI データ ソースを示す画像。":::

    > [!Note]
    > Power BI の場合、データ ソースの登録とスキャンは 1 つのインスタンスに対してのみ許可されています。

### <a name="steps-to-register-cross-tenant"></a>テナント間を登録する手順

テナント間のシナリオでは、PowerShell を使用して Power BI テナントを登録およびスキャンできます。 UI エクスペリエンス経由で Azure Purview Studio を使用して、リモート テナントの資産を参照および検索できます。 

Power BI テナントがある Azure AD テナントが、Azure Purview アカウントがプロビジョニングされている Azure AD テナントとは異なる場合、このガイドの使用を検討してください。 次の手順を使用して、テナント間のシナリオで Azure Purview の 1 つ以上の Power BI テナントを登録してスキャンします。

1. [Managed Scanning PowerShell モジュール](https://github.com/Azure/Purview-Samples/blob/master/Cross-Tenant-Scan-PowerBI/ManagedScanningPowerShell.zip)をダウンロードし、その内容を任意の場所に抽出します。

1. ご使用のコンピューターで、Windows タスク バーの検索ボックスに「**PowerShell**」と入力します。 検索一覧で、 **[Windows PowerShell]** を選択したまま (または、右クリックして) **[管理者として実行]** を選択します。

1. まだインストールされていない場合は、マシンにモジュールをインストールしてインポートします。

   ```powershell
    Install-Module -name az
    Import-Module -name az
    Login-AzAccount
    ```

1. Azure AD 管理者の資格情報を使用して、Power BI テナントがある Azure 環境にサインインします。

   ```powershell
    Login-AzAccount
    ```

1. PowerShell ウィンドウで次のコマンドを入力します。`<path-to-managed-scanning-powershell-modules>` は、`C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell` などの抽出したモジュールがあるフォルダーのパスに置き換えてください。

   ```powershell
   dir -Path <path-to-managed-scanning-powershell-modules> -Recurse | Unblock-File
   ```

1. 次のコマンドを入力して、PowerShell モジュールをインストールします。

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\ManagedScanningPowerShell\Microsoft.DataCatalog.Management.Commands.dll'
   ```

1. 同じ PowerShell セッションを使用して、次のパラメーターを設定します。 `purview_tenant_id` は、Azure Purview がデプロイされている Azure AD テナント ID で更新し、`powerbi_tenant_id` は、Power BI テナントが配置されている Azure AD テナントで更新します。`purview_account_name` は、既存の Azure Purview アカウントです。

   ```powershell
   $azuretenantId = '<purview_tenant_id>'
   $powerBITenantIdToScan = '<powerbi_tenant_id>'
   $purviewaccount = '<purview_account_name>'
   ```

1. テナント間のサービス プリンシパルを作成します。

   1. Power BI が配置されている Azure Active Directory テナントに、アプリの登録を作成します。 `password` フィールドは、強力なパスワードで更新し、`app_display_name` は、Power BI テナントがホストされている Azure AD テナント内の存在しないアプリケーション名で更新してください。

      ```powershell   
      $SecureStringPassword = ConvertTo-SecureString -String <'password'> -AsPlainText -Force
      $AppName = '<app_display_name>'
      New-AzADApplication -DisplayName $AppName -Password $SecureStringPassword
      ```

   1. Azure Active Directory ダッシュボードで、新しく作成したアプリケーションを選択し、 **[アプリの登録]** を選択します。 アプリケーションに対して次の委任されたアクセス許可を割り当て、テナントに対する管理者の同意を付与します。

      - Power BI Service     Tenant.Read.All
      - Microsoft Graph      openid

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-delegated-permissions.png" alt-text="Power BI サービスと Microsoft Graph の委任されたアクセス許可のスクリーンショット。":::

   1. Azure Active Directory ダッシュボードで、新しく作成したアプリケーションを選択し、 **[認証]** を選択します。 **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリ内のアカウント (任意の Azure AD ディレクトリ - マルチテナント)]** を選択します。 

      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-multitenant.png" alt-text="サポートされているアカウントの種類のマルチテナントのスクリーンショット。":::

   1. **[暗黙的な許可およびハイブリッド フロー]** で、必ず **[ID トークン (暗黙的およびハイブリッド フローに使用)]** を選択してください
    
      :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-id-token-hybrid-flows.png" alt-text="ID トークン ハイブリッド フローのスクリーンショット。":::

   1. Web ブラウザーで次の URL を実行して、サービス プリンシパルのテナント固有のサインイン URL を作成します。

      ```
      https://login.microsoftonline.com/<purview_tenant_id>/oauth2/v2.0/authorize?client_id=<client_id_to_delegate_the_pbi_admin>&scope=openid&response_type=id_token&response_mode=fragment&state=1234&nonce=67890
      ```
    
      必ずパラメーターを正しい情報に置き換えてください。
      
      - `<purview_tenant_id>` は、Azure Purview アカウントがプロビジョニングされる Azure Active Directory テナント ID (GUID) です。
      - `<client_id_to_delegate_the_pbi_admin>` は、サービス プリンシパルに対応するアプリケーション ID です
   
   1. 管理者以外のアカウントを使用してサインインします。 これは外部テナントでサービス プリンシパルをプロビジョニングするために必要です。

   1. ダイアログが表示されたら、 _[基本プロファイルの表示]_ および _[アクセス権を付与したデータへのアクセスの管理]_ に対して要求されたアクセス許可に同意します。

1. `client_id_to_delegate_the_pbi_admin` を、新しく作成されたアプリケーションのアプリケーション (クライアント) ID で更新し、PowerShell セッションで次のコマンドを実行します。

   ```powershell
   $ServicePrincipalId = '<client_id_to_delegate_the_pbi_admin>'
   ```

1. Power BI テナントがある Azure Active Directory テナントにユーザー アカウントを作成し、Azure AD ロール **Power BI 管理者** を割り当てます。 以下の行の `pbi_admin_username` と `pbi_admin_password` を対応する情報で更新して、PowerShell ターミナルで実行します。

    ```powershell
    $UserName = '<pbi_admin_username>'
    $Password = '<pbi_admin_password>'
    ```

    > [!Note]
    > ポータルから Azure Active Directory でユーザー アカウントを作成する場合、パブリック クライアント フロー オプションは、既定では **[いいえ]** です。 これを **[はい]** に切り替える必要があります。
    > <br>
    > :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-public-client-flows.png" alt-text="パブリック クライアント フローのスクリーンショット。":::
    
1. Azure Purview Studio で、_データ ソース管理者_ を、サービス プリンシパルとルート コレクションの Power BI ユーザーに割り当てます。 

1. テナント間の Power BI テナントを Azure Purview アカウント内の新しいデータ ソースとして登録するには、次のコマンドレットの `service_principal_key` を更新して PowerShell セッションで実行します。

    ```powershell
    Set-AzDataCatalogSessionSettings -DataCatalogSession -TenantId $azuretenantId -ServicePrincipalAuthentication -ServicePrincipalApplicationId $ServicePrincipalId -ServicePrincipalKey '<service_principal_key>' -Environment Production -DataCatalogAccountName $purviewaccount

    Set-AzDataCatalogDataSource -Name 'pbidatasource' -AccountType PowerBI -Tenant $powerBITenantIdToScan -Verbose
    ```

## <a name="scan"></a>スキャン

以下の手順に従って Power BI テナントをスキャンし、自動的に資産を識別してデータを分類します。 スキャン全般の詳細については、[スキャンとインジェストの概要](concept-scans-and-ingestion.md)に関するページを参照してください

このガイドでは、[同じテナント](#create-and-run-scan-for-same-tenant-power-bi)と[テナント間](#create-and-run-scan-for-cross-tenant-power-bi)の両方のスキャン シナリオについて説明します。

### <a name="create-and-run-scan-for-same-tenant-power-bi"></a>同じテナントの Power BI のスキャンを作成して実行する

新しいスキャンを作成して実行するには、次の操作を行います。

1. Purview Studio で、左側のメニューの **[データ マップ]** に移動します。

1. **[ソース]** に移動します。

1. 登録されている Power BI ソースを選択します。

1. **[+ 新しいスキャン]** を選択します。

1. スキャンに名前を付けます。 次に、個人用ワークスペースを含める、または除外するオプションを選択します。 サポートされている認証方法は **[マネージド ID]** のみであることに留意してください。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-scan-setup.png" alt-text="Power BI スキャンの設定を示す画像。":::

    > [!Note]
    > 個人用ワークスペースを含める、または除外するようにスキャンの構成を切り替えると、PowerBI ソースのフル スキャンがトリガーされます。

1. 次のステップに進む前に、 **[接続のテスト]** を選択します。 **[接続のテスト]** に失敗した場合、 **[レポートの表示]** を選択して詳細な状態を表示し、問題をトラブルシューティングします。
    1. [Access]\(アクセス\) - 失敗状態は、ユーザー認証が失敗したことを意味します。 マネージド ID を使用したスキャンは、ユーザー認証が必要ないため常に合格します。
    1. [Assets (+ lineage)]\(資産 (+ 系列)\) - 失敗状態は、Purview - Power BI 承認に失敗したことを意味します。 Power BI 管理ポータルで関連付けられているセキュリティ グループに Purview のマネージド ID が追加されていることを確認します。
    1. [Detailed metadata (Enhanced)]\(詳細メタデータ (拡張)\) - 失敗状態は、Power BI 管理ポータルが次の設定で無効になっていことを意味します - **[Enhance admin APIs responses with detailed metadata]\(詳細メタデータを使用して管理 API の応答を強化する\)**

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/power-bi-test-connection-status-report.png" alt-text="テスト接続の状態レポート ページのスクリーンショット。":::

1. スキャン トリガーを設定します。 オプションとしては、 **[1 度]** 、 **[7 日ごと]** 、 **[30 日ごと]** があります。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/scan-trigger.png" alt-text="Purview スキャン スケジューラのスクリーンショット。":::

1. **[Review new scan]\(新しいスキャンを確認\)** で **[保存および実行]** を選択してスキャンを開始します。

    :::image type="content" source="media/setup-power-bi-scan-catalog-portal/save-run-power-bi-scan.png" alt-text="Power BI ソースの保存と実行のスクリーンショット。":::

### <a name="create-and-run-scan-for-cross-tenant-power-bi"></a>テナント間の Power BI のスキャンを作成して実行する

Azure Purview 内で新しいスキャンを作成して実行するには、PowerShell セッションで次のコマンドレットを実行します。

   ```powershell
   Set-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan' -AuthorizationType PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password -IncludePersonalWorkspaces $true -Verbose

   Start-AzDataCatalogScan -DataSourceName 'pbidatasource' -Name 'pbiscan'
   ```

[!INCLUDE [create and manage scans](includes/view-and-manage-scans.md)]

## <a name="next-steps"></a>次のステップ

ソースの登録が完了したので、以下のガイドに従って Azure Purview とご利用のデータの詳細について学習します。

- [Azure Purview のデータ分析情報](concept-insights.md)
- [Azure Purview のデータ系列](catalog-lineage-user-guide.md)
- [Data Catalog の検索](how-to-search-catalog.md)
