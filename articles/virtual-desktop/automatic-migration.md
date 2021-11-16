---
title: Azure Virtual Desktop (クラシック) から自動的に移行する (プレビュー) - Azure
description: 移行モジュールを使用して Azure Virtual Desktop (クラシック) から Azure Virtual Desktop に自動的に移行する方法。
author: Heidilohr
ms.topic: how-to
ms.date: 09/15/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 6d5fed6547a32382413c1a128b1a9003bf099c88
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471765"
---
# <a name="migrate-automatically-from-azure-virtual-desktop-classic-preview"></a>Azure Virtual Desktop (クラシック) から自動的に移行する (プレビュー)

> [!IMPORTANT]
> Azure Virtual Desktop 用の移行モジュール ツールは現在、パブリック プレビュー段階にあります。
> ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用される法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

移行モジュール ツール (プレビュー) を使用すると、組織を Azure Virtual Desktop (クラシック) から Azure Virtual Desktop に自動的に移行できます。 この記事では、このツールを使用する方法を示します。 

## <a name="requirements"></a>必要条件

移行モジュールを使用する前に、次のものが準備できていることを確認してください。

- 新しい Azure サービス オブジェクトを作成する Azure サブスクリプション。

- サブスクリプションで Azure オブジェクトを作成するための共同作成者ロールと、アプリケーション グループにユーザーを割り当てるためのユーザー アクセス管理者ロールが割り当てられている必要があります。

- RDS テナント、または移行している特定のホスト プールに対する少なくともリモート デスクトップ サービス (RDS) 共同作成者のアクセス許可。

- 最新バージョンの Microsoft.RdInfra.RDPowershell PowerShell モジュール 

- 最新バージョンの Az.DesktopVirtualization PowerShell モジュール 

- 最新バージョンの Az.Resources PowerShell モジュール 

- コンピューターに移行モジュールをインストールする

- この記事で説明するスクリプトを実行するための PowerShell または PowerShell ISE。 PowerShell Core では Microsoft.RdInfra.RDPowershell モジュールが動作しません。

>[!IMPORTANT]
>移行では、米国地域内のサービス オブジェクトのみが作成されます。 サービス オブジェクトを別の地域に移行しようとしても、そのオブジェクトは動作しません。 また、Azure Virtual Desktop (クラシック) デプロイに 200 を超えるアプリ グループがある場合も移行できません。 移行できるのは、その環境を再構築して Azure Active Directory (Azure AD) テナント内のアプリ グループの数を削減した場合だけてす。

## <a name="prepare-your-powershell-environment"></a>PowerShell 環境を準備する

まず、移行プロセス用の PowerShell 環境を準備する必要があります。

PowerShell 環境を準備するには:

1. 開始する前に、次のコマンドレットを実行して、最新バージョンの Az.Desktop Virtualization および Az.Resources モジュールがあることを確認します。

    ```powershell
    Get-Module Az.Resources
    Get-Module Az.DesktopVirtualization
    https://www.powershellgallery.com/packages/Az.DesktopVirtualization/
    https://www.powershellgallery.com/packages/Az.Resources/
    ```

    ない場合は、次のコマンドレットを実行して、これらのモジュールをインストールしてインポートします。

    ```powershell
    Install-module Az.Resources
    Import-module Az.Resources
    Install-module Az.DesktopVirtualization
    Import-module Az.DesktopVirtualization
    ```

2. 次に、次のコマンドレットを実行して、現在の RDInfra PowerShell モジュールをアンインストールします。

    ```powershell
    Uninstall-Module -Name Microsoft.RDInfra.RDPowershell -AllVersions
    ```

3. その後、次のコマンドレットを使用して RDPowershell モジュールをインストールします。

    ```powershell
    Install-Module -Name Microsoft.RDInfra.RDPowershell -RequiredVersion 1.0.3414.0 -force
    Import-module Microsoft.RDInfra.RDPowershell
    ```

4. インストールをすべて完了したら、次のコマンドレットを実行して、適切なバージョンのモジュールがあることを確認します。

    ```powershell
    Get-Module Microsoft.RDInfra.RDPowershell
    ```

5. ここで、次のコマンドレットを実行して、移行モジュールをインストールしてインポートしましょう。

    ```powershell
    Install-Module -Name PackageManagement -Repository PSGallery -Force
    Install-Module -Name PowerShellGet -Repository PSGallery -Force
    # Then restart shell
    Install-Module -Name Microsoft.RdInfra.RDPowershell.Migration -RequiredVersion 1.0.3725-Prerelease -AllowPrerelease -AllowClobber
    Import-Module <Full path to the location of the migration module>\Microsoft.RdInfra.RDPowershell.Migration.psd1
    ```

6. 完了したら、PowerShell ウィンドウで Windows Virtual Desktop (クラシック) にサインインします。

    ```powershell
    Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
    ```

7. Azure Resource Manager にサインインします。

    ```powershell
    Login-AzAccount
    ```

8. 複数のサブスクリプションがある場合は、次のコマンドレットを使用して、リソースの移行先のサブスクリプションを選択します。

    ```powershell
    Select-AzSubscription -Subscriptionid <subID>
    ```

9. 選択したサブスクリプションの Azure portal で、リソース プロバイダーを登録します。

10. 最後に、プロバイダーを登録する必要があります。 これを実行するには、次の 2 つの方法があります。
    - PowerShell を使用する場合は、次のコマンドレットを実行します。
       
       ```powershell
       Register-AzResourceProvider -ProviderNamespace Microsoft.DesktopVirtualization
       ```
    
    - 代わりに Azure portal を使用する場合は、Azure portal を開いてサインインした後、 **[サブスクリプション]** に移動し、使用するサブスクリプションの名前を選択します。 その後、 **[リソース プロバイダー]**  >  **[Microsoft.DesktopVirtualization]** の順に移動し、 **[再登録]** を選択します。 UI にはまだ何も変更が表示されませんが、これで、PowerShell 環境でこのモジュールを実行する準備ができました。

## <a name="migrate-azure-virtual-desktop-classic-resources-to-azure-resource-manager"></a>Azure Virtual Desktop (クラシック) リソースを Azure Resource Manager に移行する

これで PowerShell 環境の準備ができたので、移行プロセスを開始できます。

Azure Virtual Desktop (クラシック) リソースを Azure Resource Manager に移行するには:

1. 移行する前に、既存のクラシック リソースが新しい Azure Resource Manager リソースにどのようにマップされるかを理解したい場合は、次のコマンドレットを実行します。
    
    ```powershell
    Get-RdsHostPoolMigrationMapping
    ```

    **Get-RdsHostPoolMigrationMapping** を使用すると、リソースの行先をマップする CSV ファイルを作成できます。 たとえば、テナントの名前が "Contoso" であり、マッピング ファイルを "contosouser" ファイルに格納する場合は、次のようなコマンドレットを実行します。

    ```powershell
    Get-RdsHostPoolMigrationMapping -Tenant Contoso -HostPool Office -Location EastUS -OutputFile 'C:\\Users\contosouser\OneDrive - Microsoft\Desktop\mapping.csv'
    ```

2. 次に、**Start-RdsHostPoolMigration** コマンドレットを実行して、1 つのホスト プールまたはテナント内のすべてのホスト プールのどちらを移行するかを選択します。

   次に例を示します。

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -Location WestUS
   ```

   特定のホスト プールのリソースを移行する場合は、ホスト プール名を含めます。 たとえば、"Office" という名前のホスト プールを移行する場合は、次のようなコマンドを実行します。

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments $false -Location EastUS
   ```

   ワークスペース名が指定されない場合、このモジュールでは、テナント名に基づいて自動的に作成されます。 ただし、特定のワークスペースを使用する場合は、そのリソース ID を次のように入力できます。

   ```powershell
   Start-RdsHostPoolMigration -Tenant Contoso -HostPool Office -CopyUserAssignments -Location EastUS -Workspace <Resource ID of workspacename>
   ```
    
   特定のワークスペースを使用したいが、そのリソース ID がわからない場合は、次のコマンドレットを実行します。

   ```powershell
   Get-AzWvdWorkspace -WorkspaceName <workspace> -ResourceGroupName <resource group> |fl
   ```

   また、既存のユーザー割り当てのユーザー割り当てモードも指定する必要があります。

      - 古いアプリ グループのすべてのユーザー割り当てを Azure Resource Manager アプリケーション グループにコピーするには、 **[コピー]** を使用します。 ユーザーは、自分のクライアントの両方のバージョンのフィードを表示できます。
      - ユーザー割り当てを変更しない場合は、 **[なし]** を使用します。 後で、Azure portal、PowerShell、または API を使用して、ユーザーまたはユーザー グループをアプリ グループに割り当てることができます。 ユーザーは、Azure Virtual Desktop (クラシック) クライアントを使用したフィードのみを表示できます。

   サブスクリプションあたり 2,000 のユーザー割り当てしかコピーできないため、実際の制限は、サブスクリプションに既に存在する割り当ての数によって異なります。 このモジュールでは、既に存在する割り当ての数に基づいて制限を計算します。 コピーするための十分な割り当てがない場合は、次のエラー メッセージが表示されます。"ユーザー割り当てをコピーするためのロールの割り当てクォータが不足しています。 -CopyUserAssignments スイッチなしでコマンドを再実行して移行してください。"

3. コマンド実行後、モジュールによってサービス オブジェクトを作成するまで、最大 15 分かかります。 ユーザー割り当てをコピーまたは移動した場合は、それにより、このモジュールがすべての設定を完了するためにかかる時間が追加されます。

   **Start-RdsHostPoolMigration** コマンドレットを実行すると、次の結果が得られます。

      - 指定したテナントまたはホスト プールの Azure サービス オブジェクト

      - 2 つの新しいリソース グループ:

         - ワークスペースを含む、"Tenantname" という名前のリソース グループ。

         - ホスト プールとデスクトップ アプリ グループを含む、"Tenantname_originalHostPoolName" という名前のリソース グループ。

      - 新しく作成されたアプリ グループに公開したすべてのユーザー。

      - 移行プロセス中のユーザーのダウンタイムを回避するために、仮想マシンは既存と新規の両方のホスト プールで使用できます。 これにより、ユーザーは同じユーザー セッションに接続できます。

   これらの新しい Azure サービス オブジェクトは Azure Resource Manager オブジェクトであるため、このモジュールは、それらに対してロールベースのアクセス制御 (RBAC) アクセス許可や診断設定を設定できません。 そのため、これらのオブジェクトの RBAC アクセス許可および設定を手動で更新する必要があります。

   このモジュールで初期のユーザー接続が検証されたら、必要に応じて、アプリ グループをさらに多くのユーザーまたはユーザー グループに公開することもできます。

   >[!NOTE]
   >移行の後、ユーザーにアクセス許可を割り当てた後にアプリ グループを別のリソース グループに移動すると、それによってすべての RBAC ロールが削除されます。 ユーザーの RBAC アクセス許可を始めから再割り当てする必要があります。

4. すべての Azure Virtual Desktop (クラシック) サービス オブジェクトを削除する場合は、**Complete-RdsHostPoolMigration** を実行して移行プロセスを完了します。 このコマンドレットでは、すべての Azure Virtual Desktop (クラシック) オブジェクトを削除し、新しい Azure オブジェクトのみを残します。 ユーザーは、新しく作成されたアプリ グループのフィードのみを自分のクライアントに表示できます。 このコマンドが完了したら、Azure Virtual Desktop (クラシック) テナントを安全に削除してプロセスを完了できます。

   次に例を示します。

   ```powershell
   Complete-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   特定のホスト プールを完了する場合は、このコマンドレットにホスト プール名を含めることができます。 たとえば、"Office" という名前のホスト プールを完了する場合は、次のようなコマンドを使用します。

    ```powershell
    Complete-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
    ```

    これにより、Azure Virtual Desktop (クラシック) によって作成されたすべてのサービス オブジェクトが削除されます。 新しい Azure オブジェクトのみが残され、ユーザーは、新しく作成されたアプリ グループのフィードのみを自分のクライアントに表示できます。 移行の最終処理を完了したら、Azure Virtual Desktop (クラシック) 内のテナントを明示的に削除する必要があります。

5. 移行について気が変わり、プロセスを元に戻したい場合は、**Revert-RdsHostPoolMigration** コマンドレットを実行します。
    
   次に例を示します。

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -Location EastUS
   ```

   特定のホスト プールを元に戻す場合は、このコマンドにホスト プール名を含めることができます。 たとえば、"Office" という名前のホスト プールを元に戻す場合は、次のように入力します。

   ```powershell
   Revert-RdsHostPoolMigration -Tenant Contoso -HostPool Office -Location EastUS
   ```

   このコマンドレットでは、新しく作成されたすべての Azure サービス オブジェクトを削除します。 ユーザーのクライアントには、Azure Virtual Desktop (クラシック) オブジェクトのフィードのみが表示されます。

   ただし、このコマンドレットでは、このモジュールが作成したワークスペースやそれに関連付けられているリソース グループは削除されません。 それを削除するには、これらの項目を手動で削除する必要があります。

6. Azure Virtual Desktop (クラシック) サービス オブジェクトをまだ削除しないが、移行をテストする場合は **、Set-RdsHostPoolHidden を実行できます**。

    次に例を示します。

    ```powershell
    Set-RdsHostPoolHidden -Tenant Contoso -Hostpool Office -Hidden $true -Location WestUS
    ```

    状態を "true" に設定すると、Azure Virtual Desktop (クラシック) リソースが非表示になります。 これを "false" に設定すると、リソースがユーザーに表示されます。

    *-Hostpool* パラメーターは省略可能です。 非表示にしたい特定の Azure Virtual Desktop (クラシック) ホスト プールがある場合は、このパラメーターを使用できます。

    このコマンドレットでは、Azure Virtual Desktop (クラシック) ユーザーのフィードやサービス オブジェクトを削除するのではなく、非表示にします。 ただし、これは通常、テストのためにのみ使用され、完了した移行としては見なされません。 移行を完了するには、**Complete-RdsHostPoolMigration** コマンドを実行する必要があります。 それ以外の場合は、**Revert-RdsHostPoolMigration** を実行してデプロイを元に戻します。

## <a name="troubleshoot-automatic-migration"></a>自動移行をトラブルシューティングする

このセクションでは、移行モジュールでよく発生する問題を解決する方法について説明します。

### <a name="i-cant-access-the-tenant"></a>テナントにアクセスできない

まず、次の 2 つのことを試してください。

- 管理者アカウントにテナントへのアクセスに必要なアクセス許可があることを確認してください。
- テナントで **Get-RdsTenant** を実行してみてください。

これらの 2 つのことが機能する場合は、移行のための RDS コンテキストと ADAL コンテキストを設定するために **Set-RdsMigrationContext** コマンドレットを実行してみてください。

1. **Add-RdsAccount** コマンドレットを実行して RDS コンテキストを作成します。       

2. グローバル変数 *$rdMgmtContext* で RDS コンテキストを見つけます。         

3. グローバル変数 *$AdalContext* で ADAL コンテキストを見つけます。

4. 見つかった変数を次の形式で使用して **Set-RdsMigrationContext** を実行します。

   ```powershell
   Set-RdsMigrationContext -RdsContext <rdscontext> -AdalContext <adalcontext>
   ```

## <a name="next-steps"></a>次のステップ

代わりに手動でデプロイを移行する方法を知りたい場合は、「[Azure Virtual Desktop (クラシック) から手動で移行する](manual-migration.md)」を参照してください。

移行が完了した後、[チュートリアル](create-host-pools-azure-marketplace.md)を確認して、Azure Virtual Desktop がどのように動作するかを理解します。 「[既存のホスト プールを展開する](expand-existing-host-pool.md)」と[RDP のプロパティのカスタマイズ](customize-rdp-properties.md)に関する記事で、高度な管理機能について学習します。

サービス オブジェクトの詳細については、「[Azure Virtual Desktop の環境](environment-setup.md)」を確認してください。
