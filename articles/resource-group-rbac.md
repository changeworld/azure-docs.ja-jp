<properties
   pageTitle="リソースへのアクセスの管理"
   description="ロールベースのアクセス制御 (RBAC) を使用して、Azure にデプロイされたリソースに対するアクセス許可を管理します。"
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="10/28/2015"
   ms.author="tomfitz"/>

# リソースへのアクセスの管理

Azure リソース マネージャーを使用すれば、組織内のユーザーが、リソースにアクセスまたはリソースを管理するための適切なアクセス許可を保有しているかどうかを確認することができます。リソース マネージャーでは、ロールベースのアクセス制御 (RBAC) を利用することで、個々のリソースまたはリソース グループへのセキュリティ ポリシーの適用を簡単にしています。たとえば、サブスクリプション内の特定の仮想マシンへのアクセス許可をユーザーに付与することができます。また、サブスクリプション内のすべての Web サイトの管理はユーザーに許可するがその他のリソースの管理は許可しないようにすることもできます。

このトピックでは、ロールとアクセス許可の割り当てに使用するコマンドについて説明します。ロールベースのアクセス制御の概要については、「[Microsoft Azure ポータルでのロールベースのアクセス制御](active-directory/role-based-access-control-configure.md)」をご覧ください。

## 概念

ロール ベースのアクセス制御を理解する上で重要となる概念は次のとおりです。

1. プリンシパル - ユーザー、セキュリティ グループ、アプリケーションなどアクセス許可が付与されるエンティティです。
2. ロール - 許可される一連の操作です。
3. スコープ - サブスクリプション、リソース グループ、リソースなど、ロールが適用されるレベルです。
3. ロール割り当て - ロールにプリンシパルを追加してスコープを設定するプロセスです。

## ロールの例
RBAC の概念を理解するために、一般的なロール定義の例をいくつかを見てみましょう。

| 役割 | 実行できる操作 |
| ------- | ----------------- |
| 閲覧者 | **/読み取り (任意の内容を読み取り可能) | | 所有者 | * (任意の内容を読み取り/書き込み可能) |

**ExampleGroup** という名前のリソースグループについて、**ユーザー A** に**閲覧者**ロールを割り当てて、サブスクリプション全体について、**ユーザー B** に**所有者**ロールを割り当てるには、次のようになります。

| 役割 | プリンシパル | Scope |
| --------|-------------|---------- |
| 閲覧者 | ユーザー A | /subscriptions/{subscriptionId}/resourceGroups/examplegroup |
| 所有者 | ユーザー B | /subscriptions/{subscriptionId} |

## シナリオ例

このトピックでは、Azure PowerShell と、Mac、Linux、および Windows 用の Azure CLI と、REST API を使用して以下の一般的なシナリオを実行する方法について説明します。

1. サブスクリプションで使用可能なロールと、それらのロールで許可される操作を表示する。
2. サブスクリプション全体を対象にした閲覧者アクセス許可をグループのメンバーに付与する。
3. 共同作業者アクセス許可をアプリケーションに付与して、アプリケーションでリソース グループ内のリソースを管理できるようにする。
4. 特定の Web サイトでの所有者アクセス許可を 1 つのユーザーに付与する。
5. リソース グループの監査ログを一覧表示する。


## PowerShell を使用してアクセスを管理する方法

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]


### 使用可能なロールを表示する
サブスクリプションで使用可能なすべてのロールを表示するには、**Get-AzureRmRoleDefinition** コマンドを実行します。

    PS C:\> Get-AzureRmRoleDefinition
    
    Name             : API Management Service Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage API Management services, but not access to them.
    Actions          : {Microsoft.ApiManagement/Services/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read,
                       Microsoft.Resources/subscriptions/resourceGroups/resources/read...}
    NotActions       : {}
    AssignableScopes : {/}

    Name             : Application Insights Component Contributor
    Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
    IsCustom         : False
    Description      : Lets you manage Application Insights components, but not access to them.
    Actions          : {Microsoft.Insights/components/*, Microsoft.Insights/webtests/*, Microsoft.Authorization/*/read,
                       Microsoft.Resources/subscriptions/resourceGroups/read...}
    NotActions       : {}
    AssignableScopes : {/}
    ...

### サブスクリプションを対象にした閲覧者アクセス許可をグループに付与する
1. **Get-AzureRmRoleDefinition** コマンドの実行時にロール名を指定して、**閲覧者**ロールの定義を確認します。実行できる操作が、割り当てようとしている操作であることを確認する。

        PS C:\> Get-AzureRmRoleDefinition Reader
   
        Name             : Reader
        Id               : /subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/{guid}
        IsCustom         : False
        Description      : Lets you view everything, but not make any changes.
        Actions          : {*/read}
        NotActions       : {}
        AssignableScopes : {/}

2. **Get-AzureRmADGroup** コマンドを実行して必要なセキュリティ グループを取得します。サブスクリプション内のグループの実際の名前を指定する。次の例では、ExampleAuditorGroup となっています。

        PS C:\> $group = Get-AzureRmAdGroup -SearchString ExampleAuditorGroup

3. 監査セキュリティ グループのロール割り当てを作成します。コマンドが完了すると、新しいロール割り当てが返されます。

        PS C:\> New-AzureRmRoleAssignment -ObjectId $group.Id -Scope /subscriptions/{subscriptionId}/ -RoleDefinitionName Reader


###リソース グループを対象にした共同作業者アクセス許可をアプリケーションに付与する
1. **Get-AzureRmRoleDefinition** コマンドの実行時にロール名を指定して、**共同作業者**ロールの定義を確認します。実行できる操作が、割り当てようとしている操作であることを確認する。

        PS C:\> Get-AzureRmRoleDefinition Contributor

2. **Get-AzureRmADServicePrincipal** コマンドを実行し、サブスクリプション内のアプリケーションの名前を指定して、サービス プリンシパル オブジェクト ID を取得します。次の例では、ExampleApplication となっています。

        PS C:\> $service = Get-AzureRmADServicePrincipal -SearchString ExampleApplicationName

3. **New-AzureRmRoleAssignment** コマンドを実行して、サービス プリンシパルのロールの割り当てを作成します。

        PS C:\> New-AzureRmRoleAssignment -ObjectId $service.Id -ResourceGroupName ExampleGroupName -RoleDefinitionName Contributor

Azure Active Directory アプリケーションおよびサービス プリンシパルのセットアップの詳細については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」を参照してください。

###リソースを対象にした所有者アクセス許可をユーザーに付与する
1. **Get-AzureRmRoleDefinition** コマンドの実行時にロール名を指定して、**所有者**ロールの定義を確認します。実行できる操作が、割り当てようとしている操作であることを確認する。

        PS C:\> Get-AzureRmRoleDefinition Owner

2. ユーザーのロール割り当てを作成する。

        PS C:\> New-AzureRmRoleAssignment -UserPrincipalName "someone@example.com" -ResourceGroupName {groupName} -ResourceType "Microsoft.Web/sites" -ResourceName "mysite" -RoleDefinitionName Owner


###リソース グループの監査ログを一覧表示する。
リソース グループの監査ログを取得するには、**Get-AzureRmLog** コマンド (1.0 プレビューより前のバージョンの Azure PowerShell では **Get-AzureResourceGroupLog**) を実行します。

      PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroupName

## Mac、Linux、Windows 用の Azure CLI の使用方法

Mac、Linux、Windows 用の Azure CLI がインストールされていない場合、または組織のアカウントが Azure CLI を使用する構成になっていない場合は、「[Azure CLI のインストールと構成](xplat-cli-install.md)」を参照してください。

1. 資格情報を使用して Azure アカウントにログインします。ログインの結果が返されます。

        azure login

        ...
        info:    login command OK

2. 複数のサブスクリプションがある場合、デプロイメントに使用するサブスクリプション ID を提供します。

        azure account set <YourSubscriptionNameOrId>

3. Azure リソース マネージャー モジュールに切り替えます。新しいモードの確認が表示されます。

        azure config mode arm
        
        info:     New mode is arm

### 使用可能なロールを表示する
サブスクリプションで使用可能なすべてのロールを表示します。ロールの定義の一覧が返されます。

    azure role list

### サブスクリプションを対象にした閲覧者アクセス許可をグループに付与する
1. **閲覧者**ロールの定義を取得します。実行できる操作が、割り当てようとしている操作であることを確認する。

        azure role show Reader
        
        info:    Executing command role show
        + Getting role definitions
        data:    Name    Actions  NotActions
        data:    ------  -------  ----------
        data:    Reader  */read
        info:    role show command OK

2. 名前でグループを検索し、必要なセキュリティ グループとその ObjectID を取得します。次の例では、ExampleAuditorGroup になっています。

        azure ad group show --search ExampleAuditorGroup
        
        info:    Executing command ad group show
        + Getting group list
        data:    Display Name:      ExampleAuditorGroup
        data:    ObjectId:          1c272299-9729-462a-8d52-7efe5ece0c5c
        data:    Security Enabled:  true
        data:    Mail Enabled:
        data:
        info:    ad group show command OK

3. セキュリティ グループのロール割り当てを作成します。

        azure role assignment create --objectId {group-object-id} -o Reader -c /subscriptions/{subscriptionId}/
        
        info:    Executing command role assignment create
        + Getting role definition id
        + Creating role assignment
        info:    role assignment create command OK


### リソース グループを対象にした共同作業者アクセス許可をアプリケーションに付与する
1. **共同作業者**ロールの定義を取得します。実行できる操作が、割り当てようとしている操作であることを確認する。

        azure role show Contributor

2. アプリケーションの ObjectID を取得します。取得するアプリケーションの名前を指定します。

        azure ad sp show --search ExampleApplicationName

2. アプリケーションのロール割り当てを作成します。

        azure role assignment create --ObjectId {service-principal-object-id} -o Contributor -c /subscriptions/{subscriptionId}/


###特定の Web サイトを対象にした所有者アクセス許可をユーザーに付与する
1. **所有者**ロールの定義を取得します。実行できる操作が、割り当てようとしている操作であることを確認する。

        azure role show Owner

2. ユーザーのロール割り当てを作成する。

        azure role assignment create --mail "someone@example.com" -o Owner -c /subscriptions/{subscriptionId}/resourceGroups/{groupName}/providers/Microsoft.Web/sites/{mySiteName}


###リソース グループの監査ログを一覧表示する。
リソース グループの監査ログを取得するには、**azure group log show** コマンドを実行し、目的のリソース グループの名前を指定します。

         azure group log show ExampleGroupName


## REST API を使用する方法
Azure リソース マネージャーの REST API を介してロールベースのアクセス制御を管理するには、要求を送信するときに一般的なヘッダーおよびパラメーター (認証トークンを含む) を設定する必要があります。詳細については、「[一般的なパラメーターとヘッダー](https://msdn.microsoft.com/library/azure/dn906885.aspx)」を参照してください。
   
サポートされている API のバージョンを確認するには、次のコードを実行します。

      GET https://management.azure.com/providers/Microsoft.Authorization?api-version=2015-01-01

このトピックでは、バージョン `2014-10-01-preview` を使用できます。

###役割の割り当ての作成
使用可能なロールの定義を取得します。

    GET https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions?api-version=2014-10-01-preview

ロールの割り当てを作成します。

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview
    {
      "properties": {
          "roleDefinitionId": "/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
          "principalId": "{principal-object-id}",
          "scope": "/subscriptions/{subscription-id}"
       }
    }


###ロールの割り当てを削除する

      Delete  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Authorization/roleAssignments/{role-assignment-id}?api-version=2014-10-01-preview


## 次のステップ

- ロールベースのアクセス制御の詳細については、「[Microsoft Azure ポータルでのロールベースのアクセス制御](role-based-access-control-configure.md)」をご覧ください。
- サービス プリンシパルを使用してサブスクリプション内のアプリケーションのアクセスを管理する方法の詳細については、「[Azure リソース マネージャーでのサービス プリンシパルの認証](resource-group-authenticate-service-principal.md)」および「[Azure ポータルを使用した Azure サービス プリンシパルの新規作成](../resource-group-create-service-principal-portal.md)」をご覧ください。
- 組織での監査操作の詳細については、「[リソース マネージャーの監査操作](resource-group-audit.md)」をご覧ください。
- カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。詳細については、「[ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。

 

<!---HONumber=Nov15_HO2-->