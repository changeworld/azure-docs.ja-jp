---
title: Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、削除する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、削除する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618094"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でアクセス パッケージの割り当てを表示、追加、および削除する

Azure AD エンタイトルメント管理では、アクセス パッケージを割り当てたユーザー、そのポリシー、および状態を確認できます。 アクセス パッケージに適切なポリシーが設定されている場合は、ユーザーをアクセス パッケージに直接割り当てることもできます。 この記事では、アクセス パッケージの割り当てを表示、追加、および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

Azure AD エンタイトルメント管理を使用し、ユーザーをアクセス パッケージに割り当てるには、次のいずれかのライセンスが必要です。

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5 ライセンス

## <a name="view-who-has-an-assignment"></a>割り当てられているユーザーを表示する

**必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャー、またはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[割り当て]** をクリックして、アクティブな割り当ての一覧を表示します。

    ![アクセス パッケージへの割り当ての一覧](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 特定の割り当てをクリックすると、その他の詳細が表示されます。

1. 必ずしもすべてのリソース ロールが適切にプロビジョニングされなかった割り当ての一覧を表示するには、フィルターの状態をクリックし、 **[Delivering]\(配信中)** を選択します。

    **[要求]** ページでユーザーの対応する要求を見つけることで、配信エラーのその他の詳細を確認できます。

1. 有効期限切れの割り当てを確認するには、フィルターの状態をクリックし、 **[Expired]\(有効期限切れ)** を選択します。

1. フィルター処理された一覧の CSV ファイルをダウンロードするには、 **[ダウンロード]** をクリックします。

## <a name="view-assignments-programmatically"></a>プログラムによる割り当ての表示
### <a name="view-assignments-with-microsoft-graph"></a>Microsoft Graph を使用して割り当てを表示する
Microsoft Graph を使用して、アクセス パッケージの割り当てを取得することもできます。  委任された `EntitlementManagement.Read.All` または `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageAssignments をリストする](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)ことができます。 ID ガバナンス管理者は、複数のカタログからアクセス パッケージを取得できますが、ユーザーがカタログ固有の委任管理ロールにのみ割り当てられている場合、要求では、`$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'` などの特定のアクセス パッケージを示すようにフィルターを指定する必要があります。 アプリケーションのアクセス許可 `EntitlementManagement.Read.All` または `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションでも、この API を使用できます。

### <a name="view-assignments-with-powershell"></a>PowerShell を使用した割り当ての表示

このクエリは、PowerShell 内で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン 1.6.0 以降の `Get-MgEntitlementManagementAccessPackageAssignment` コマンドレットを使用して実行できます。 このコマンドレットは、`Get-MgEntitlementManagementAccessPackage` コマンドレットからの応答に含まれるアクセス パッケージ ID をパラメーターとして受け取ります。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>ユーザーを直接割り当てる 

場合によっては、ユーザーがアクセス パッケージを要求するプロセスをたどらなくて済むように、アクセス パッケージに特定のユーザーを直接割り当てることもできます。 ユーザーを直接割り当てるには、アクセス パッケージに、管理者に直接割り当てを許可するポリシーが必要です。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックします。

1. **[新しい割り当て]** をクリックして、[Add user to access package]\(アクセス パッケージにユーザーを追加する) を開きます。

    ![割り当て - アクセス パッケージにユーザーを追加する](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  ユーザーの今後の要求とライフサイクルを統制、追跡するためのポリシーを **[ポリシーの選択]** リストで選択します。 選択したユーザーに異なるポリシー設定を割り当てたい場合は、 **[新しいポリシーの作成]** をクリックして新しいポリシーを追加します。

1.  ポリシーを選択すると、ユーザーを追加して、選択したポリシーに基づいて、このアクセス パッケージを割り当てるユーザーを選択できるようになります。

    > [!NOTE]
    > 質問付きのポリシーを選択した場合は、一度に 1 人のユーザーのみを割り当てることができます。

1. 選択したユーザーの割り当てを開始および終了する日付と時刻を設定します。 終了日が指定されていない場合は、ポリシーのライフサイクルの設定が使用されます。

1.  必要に応じて、記録保存のために直接割り当ての妥当性を入力します。

1.  選択したポリシーに追加の要求元情報が含まれている場合は、 **[質問の表示]** をクリックしてユーザーに代わって回答し、 **[保存]** をクリックします。  

     ![割り当て - [質問の表示] をクリック](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![割り当て - [質問] ペイン](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. **[追加]** をクリックして、選択したユーザーをアクセス パッケージに直接割り当てます。

    しばらくしてから **[更新]** をクリックすると、割り当ての一覧にユーザーが表示されます。
    
> [!NOTE]
> ユーザーをアクセス パッケージに割り当てる際、管理者は、そのユーザーが既存のポリシー要件に基づいてそのアクセス パッケージの対象となっていることを確認する必要があります。 そうしない場合、ユーザーはアクセス パッケージに正常に割り当てられません。 ユーザー リクエストの承認を必要とするポリシーがアクセス パッケージに含まれている場合、指定された承認者による必要な承認を得ずに、ユーザーを直接アクセス パッケージに割り当てることはできません。

## <a name="directly-assign-any-user-preview"></a>任意のユーザーを直接割り当てる (プレビュー)
Azure AD エンタイトルメント管理により、外部ユーザーをアクセス パッケージに直接割り当てて、パートナーとの共同作業を簡単にすることもできます。 これを行うには、アクセス パッケージに、まだディレクトリに存在しないユーザーがアクセスを要求できるようにするポリシーがある必要があります。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1.  Azure portal で **[Azure Active Directory]** を選択し、 **[Identity Governance]** を選択します。

1.  左側のメニューで **[アクセス パッケージ]** をクリックしてから、ユーザーを追加するアクセス パッケージを開きます。

1.  左側のメニューで **[割り当て]** をクリックします。

1.  **[新しい割り当て]** を選択して、 **[ユーザーのアクセス パッケージへの追加]** を開きます。

1.  **[ポリシーの選択]** の一覧で、 **[自分のディレクトリ内以外のユーザーの場合]** に設定されることを許可するポリシーを選択します

1. **[任意のユーザー]** を選択します。 このアクセス パッケージに割り当てるユーザーを指定することができます。
    ![割り当て - アクセス パッケージにユーザーを追加する](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. ユーザーの **[名前]** (省略可能) とユーザーの **[メール アドレス]** (必須) を入力します。

    > [!NOTE]
    > - 追加するユーザーは、ポリシーのスコープ内にある必要があります。 たとえば、ポリシーが **[特定の接続済み組織]** に設定されている場合、ユーザーのメールアドレスは、選択した組織のドメインからのものである必要があります。 追加しようとしているユーザーのメール アドレスが jen@*foo.com* でも、選択した組織のドメインが *bar.com* である場合、そのユーザーをアクセス パッケージに追加することはできません。
    > - 同様に、 **[構成済みの接続されたすべての組織]** を含めるようにポリシーを設定した場合、ユーザーのメール アドレスは、構成されている接続済み組織のいずれかからのものである必要があります。 そうでない場合、ユーザーはアクセス パッケージに追加されません。
    > - アクセス パッケージに任意のユーザーを追加する場合は、ポリシーを構成するときに、 **[すべてのユーザー (すべての接続済み組織 + 外部ユーザー)]** を選択するようにする必要があります。

1.  選択したユーザーの割り当てを開始および終了する日付と時刻を設定します。 終了日が指定されていない場合は、ポリシーのライフサイクルの設定が使用されます。
1.  **[追加]** をクリックして、選択したユーザーをアクセス パッケージに直接割り当てます。
1.  しばらくしてから **[更新]** をクリックすると、割り当ての一覧にユーザーが表示されます。

## <a name="directly-assigning-users-programmatically"></a>プログラムによるユーザーの直接割り当て
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>Microsoft Graph を使用してアクセス パッケージにユーザーを割り当てる
Microsoft Graph を使用して、アクセス パッケージにユーザーを直接割り当てることもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーション、またはそのアプリケーションのアクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageAssignmentRequest を作成する](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)ことができます。 この要求では、`requestType` プロパティの値は `AdminAdd` であり、`accessPackageAssignment` プロパティは、割り当てられているユーザーの `targetId` を格納する構造体です。

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>PowerShell を使用してアクセス パッケージにユーザーを割り当てる

PowerShell 内で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン1.6.0 以降の `New-MgEntitlementManagementAccessPackageAssignmentRequest` コマンドレットを使用して、アクセス パッケージにユーザーを割り当てることができます。 このコマンドレットは、パラメーターを受け取ります
* `Get-MgEntitlementManagementAccessPackage` コマンドレットからの応答に含まれるアクセス パッケージ ID、
* `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` コマンドレットからの応答に含まれるアクセス パッケージ割り当てポリシー ID、
* 対象ユーザーのオブジェクト ID。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

PowerShell 内で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン1.6.1 以降の `New-MgEntitlementManagementAccessPackageAssignment` コマンドレットを使用して、アクセス パッケージに複数のユーザーを割り当てることもできます。 このコマンドレットは、パラメーターを受け取ります
* `Get-MgEntitlementManagementAccessPackage` コマンドレットからの応答に含まれるアクセス パッケージ ID、
* `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` コマンドレットからの応答に含まれるアクセス パッケージ割り当てポリシー ID、
* 文字列の配列として、または `Get-MgGroupMember` コマンドレットから返されるユーザー メンバーの一覧としての、対象ユーザーのオブジェクト ID。

たとえば、現在グループのメンバーであるすべてのユーザーに、アクセス パッケージへの割り当てもあることを確認する場合は、このコマンドレットを使用して、現在割り当てのないユーザーに対する要求を作成できます。  このコマンドレットでは、割り当てのみが作成されることに注意してください。割り当ては削除されません。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>割り当ての削除

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、アクセス パッケージ マネージャーまたはアクセス パッケージ割り当てマネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. 左側のメニューで **[割り当て]** をクリックします。
 
1. 割り当てをアクセス パッケージから削除するユーザーの横にあるチェック ボックスをオンにします。 

1. 左ウィンドウの上部付近にある **[削除]** ボタンをクリックします。 
 
    ![割り当て - アクセス パッケージからユーザーを削除する](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    割り当てが削除されたことを知らせる通知が表示されます。 

## <a name="remove-an-assignment-programmatically"></a>プログラムによる割り当ての削除
### <a name="remove-an-assignment-with-microsoft-graph"></a>Microsoft Graph を使用して割り当てを削除する
Microsoft Graph を使用して、アクセス パッケージへのユーザーの割り当てを削除することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーション、またはそのアプリケーションのアクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageAssignmentRequest を作成する](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)ことができます。  この要求では、`requestType` プロパティの値は `AdminRemove` であり、`accessPackageAssignment` プロパティは、削除される `accessPackageAssignment` を識別する `id` プロパティを格納する構造体です。

### <a name="remove-an-assignment-with-powershell"></a>PowerShell を使用した割り当ての削除

PowerShell 内で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン 1.6.0 以降の `New-MgEntitlementManagementAccessPackageAssignmentRequest` コマンドレットを使用して、ユーザーの割り当てを削除できます。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージの要求と設定を変更する](entitlement-management-access-package-request-policy.md)
- [レポートとログを表示する](entitlement-management-reports.md)
