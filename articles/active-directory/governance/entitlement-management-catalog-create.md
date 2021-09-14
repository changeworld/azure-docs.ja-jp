---
title: エンタイトルメント管理でリソースのカタログを作成して管理する - Azure AD
description: Azure Active Directory エンタイトルメント管理でリソースとアクセス パッケージの新しいコンテナーを作成する方法を説明します。
services: active-directory
documentationCenter: ''
author: ajburnle
manager: ''
editor: HANKI
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 8/31/2021
ms.author: ajburnle
ms.reviewer: hanki
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9178daf615b0d3f02188e30e1bdd7c37c628b4c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427429"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でリソースのカタログを作成して管理する

## <a name="create-a-catalog"></a>カタログを作成する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。 カタログ所有者は、別のカタログ所有者を追加できます。

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ作成者

> [!NOTE]
> ユーザー管理者ロールが割り当てられているユーザーは、所有していないカタログでカタログを作成したり、アクセス パッケージを管理したりすることはできなくなりました。 組織内のユーザーに、エンタイトルメント管理でカタログ、アクセス パッケージ、またはポリシーを構成するユーザー管理者ロールが割り当てられている場合、代わりに、これらのユーザーに **Identity Governance 管理者** ロールを割り当てる必要があります。

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックします。

    ![Azure portal 内のエンタイトルメント管理カタログ](./media/entitlement-management-catalog-create/catalogs.png)

1. **[新しいカタログ]** をクリックします。

1. カタログの一意の名前と説明を入力します。

    この情報は、アクセス パッケージの詳細に表示されます。

1. このカタログ内のアクセス パッケージが作成されたらすぐにユーザーがそのパッケージを要求できるようにする場合は、 **[有効]** を **[はい]** に設定します。

1. 選択された外部ディレクトリ内のユーザーがこのカタログ内のアクセス パッケージを要求できるようにする場合は、 **[Enabled for external users]\(外部ユーザーに対して有効にする\)** を **[はい]** に設定します。

    ![新しいカタログ ペイン](./media/entitlement-management-shared/new-catalog.png)

1. **[作成]** をクリックすると、カタログが作成されます。

## <a name="create-a-catalog-programmatically"></a>カタログをプログラミングで作成する
### <a name="create-a-catalog-with-microsoft-graph"></a>Microsoft Graph でカタログを作成する

Microsoft Graph を使用して、カタログを作成することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーション、またはそのアプリケーション アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageCatalog を作成する](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)ことができます。

### <a name="create-a-catalog--with-powershell"></a>PowerShell でカタログを作成する

カタログは、PowerShell 内で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン 1.6.0 以降の `New-MgEntitlementManagementAccessPackageCatalog` コマンドレットを使用して作成できます。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>カタログにリソースを追加する

アクセス パッケージにリソースを含めるには、リソースがカタログ内に存在している必要があります。 追加できるリソースの種類は、グループ、アプリケーション、および SharePoint Online サイトです。

* 追加できるグループは、クラウドで作成された Microsoft 365 グループ、またはクラウドで作成された Azure AD セキュリティ グループです。  オンプレミスの Active Directory に由来するグループは、その所有者またはメンバー属性を Azure AD で変更できないため、リソースとして割り当てることができません。   Exchange Online で配布グループとして生成されるグループも、Azure AD で変更できません。
* 追加できるアプリケーションは Azure AD エンタープライズ アプリケーションで、これには SaaS アプリケーションと、Azure AD と統合された独自アプリケーションの両方が含まれます。 複数のロールを持つアプリケーションに適切なリソースを選択する方法の詳細については、「[リソース ロールを追加する](entitlement-management-access-package-resources.md#add-resource-roles)」を参照してください。
* 追加できるサイトは、SharePoint Online サイトまたは SharePoint Online サイト コレクションです。

**事前に必要なロール:** [リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを追加するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. **[リソースの追加]** をクリックします。

1. リソースの種類として、 **[Groups and Teams]\(Groups と Teams\)** 、 **[アプリケーション]** 、または **[SharePoint サイト]** のいずれかをクリックします。

    追加するリソースが表示されない、またはリソースを追加できない場合は、必要な Azure Active Directory ディレクトリ ロールおよびエンタイトルメント管理ロールを持っていることを確認してください。 必要なロールを持つ人物に、カタログへのリソース追加を依頼することが必要な場合があります。 詳細については、[リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照してください。

1. カタログに追加する種類のリソースを 1 つ以上選択します。

    ![カタログにリソースを追加する](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 作業が終了したら、 **[追加]** をクリックします。

    これらのリソースをカタログ内のアクセス パッケージに含めることができるようになりました。

### <a name="add-resource-attributes-preview-in-the-catalog"></a>カタログにリソース属性 (プレビュー) を追加する

属性とは、要求元がアクセス要求を送信する前に、要求元に応答を要求する必須フィールドです。 これらの属性への応答は、承認者に対して表示され、Azure Active Directory のユーザー オブジェクトにもスタンプされます。 

> [!NOTE]
>リソースに設定されているすべての属性は、そのリソースを含むアクセス パッケージの要求が送信できるようになる前に、応答を要求します。 要求元が応答を提供しない場合、要求は処理されません。

アクセス要求のための属性を要求するには、次の手順に従います。

1. 左側のメニューで **[リソース]** をクリックすると、カタログ内のリソースの一覧が表示されます。 

1. 属性を追加するリソースの横にある省略記号をクリックし、 **[属性が必要 (プレビュー)]** を選択します。 

    ![リソースの追加 - [属性が必要] を選択](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  属性の種類を選択します。

    1. **ビルトイン**: Azure Active Directory ユーザー プロファイル属性を含みます。
    1. **ディレクトリ スキーマ拡張機能**: ユーザー オブジェクトや他のディレクトリ オブジェクトの Azure Active Directory に追加データを格納する方法を提供します。 これには、グループ、テナントの詳細、およびサービス プリンシパルが含まれます。 アプリケーションに対する要求の送信に使用できるのは、ユーザー オブジェクトの拡張機能属性だけです。
    1. **[ビルトイン]** を選択した場合は、ドロップダウン リストから属性を選択できます。 **[ディレクトリ スキーマ拡張機能]** を選択した場合は、テキストボックスに属性名を入力できます。

    > [!NOTE]
    > User.mobilePhone 属性は、管理者以外のユーザーに対してのみ更新できます。 [こちら](/graph/permissions-reference#remarks-5)をご覧ください。 

1.  要求元が回答する回答形式を選択します。 回答形式には、"**短いテキスト**"、"**複数選択**"、および "**長いテキスト**" があります。

1.  複数選択を選択した場合は、 **[編集とローカライズ]** ボタンをクリックして回答のオプションを構成します。
    1. [編集とローカライズ] を選択すると、 **[質問の表示/編集]** ペインが開きます。
    1. 質問の回答時に要求元に指定する回答のオプションを、 **[Answer values]\(回答の値\)** ボックスに入力します。
    1. 応答オプションのための言語を選択します。 追加の言語を選択した場合は、応答オプションをローカライズできます。
    1. 必要な数の回答を入力し、 **[保存]** をクリックします。

1. 直接割り当てとセルフサービス要求で属性値を編集可能にする場合は、 **[はい]** を選択します。

    > [!NOTE]
    > ![リソースの追加 - 属性の追加 - 属性を編集可能にする](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - [属性値が編集可能である] フィールドで **[いいえ]** を選択し、属性値 **[が空]** を選択した場合、ユーザーはその属性の値を入力できます。 保存後は、値を編集できなくなります。 
    > - [属性値が編集可能である] フィールドで **[いいえ]** を選択し、属性値 **[が空ではない]** を選択した場合、ユーザーは、直接割り当て時とセルフサービス要求時のどちらも既存の値を編集できません。
 
    ![リソースの追加 - 属性の追加 - 質問](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  ローカライズを追加する場合は、 **[Add localization]\(ローカライズの追加\)** をクリックします。

    1. **[質問のローカリゼーションの追加]** ペインで、選択した属性に関連する質問をローカライズする言語の言語コードを選択します。
    1. 構成した言語で、**Localized Text\(ローカライズされたテキスト)** ボックスに質問を入力します。
    1. 必要なすべてのローカライズを追加したら、 **[保存]** をクリックします。

    ![リソースの追加 - 属性の追加 - ローカライズ](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  **[属性が必要 (プレビュー)]** ページですべての属性情報が完了したら、 **[保存]** をクリックします。

### <a name="add-a-multi-geo-sharepoint-site"></a>複数の地理的な SharePoint サイトを追加する

1. SharePoint に対して [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) が有効になっている場合、サイトの選択元の環境を選択します。
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="アクセス パッケージ - リソース ロールの追加 - SharePoint Multi-Geo サイトの選択":::

1. 次に、カタログに追加するサイトを選択します。 

### <a name="adding-a-resource-to-a-catalog-programmatically"></a>プログラムによるカタログへのリソースの追加

Microsoft Graph を使用して、カタログにリソースを追加することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーまたはカタログおよびリソースの所有者は、API を呼び出して、[accessPackageResourceRequest を作成する](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)ことができます。  ただし、要求の時点でユーザー コンテキストがないと、アプリケーション アクセス許可のあるアプリケーションではまだ、リソースをプログラミングで追加できません。

## <a name="remove-resources-from-a-catalog"></a>カタログからリソースを削除する

カタログからリソースを削除できます。 カタログからリソースを削除できるのは、そのリソースがカタログのどのアクセス パッケージでも使用されていない場合に限られます。

**事前に必要なロール:** [リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、リソースを削除するカタログを開きます。

1. 左側のメニューで **[リソース]** をクリックします。

1. 削除するリソースを選択します。

1. **[削除]** をクリックします (または、省略記号 ( **...** ) をクリックして **[リソースの削除]** をクリックします)。


## <a name="add-additional-catalog-owners"></a>カタログ所有者をさらに追加する

カタログを作成したユーザーは最初のカタログ所有者になります。 カタログの管理を委任するには、カタログ所有者ロールにユーザーを追加します。 これは、カタログの管理責任を共有するのに役立ちます。 

次の手順でカタログ作成者ロールにユーザーを割り当てます。

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、管理者を追加するカタログを開きます。

1. 左側のメニューで **[ロールと管理者]** をクリックします。

    ![カタログのロールと管理者](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **[所有者の追加]** をクリックし、それらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="edit-a-catalog"></a>カタログを編集する

カタログの名前と説明を編集できます。 この情報は、アクセス パッケージの詳細に表示されます。

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、編集するカタログを開きます。

1. カタログの **[概要]** ページで **[編集]** をクリックします。

1. カタログの名前、説明、または有効になっている設定を編集します。

    ![カタログの設定を編集する](./media/entitlement-management-shared/catalog-edit.png)

1. **[保存]** をクリックします。

## <a name="delete-a-catalog"></a>カタログを削除する

カタログを削除できるのは、そのカタログにどのアクセス パッケージも含まれていない場合に限られます。

**事前に必要なロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[カタログ]** をクリックし、削除するカタログを開きます。

1. カタログの **[概要]** で **[削除]** をクリックします。

1. 表示されるメッセージ ボックスで、 **[はい]** をクリックします。

### <a name="deleting-a-catalog-programmatically"></a>プログラムによるカタログの削除

Microsoft Graph を使用して、カタログを削除することもできます。  委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageCatalog を削除する](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)ことができます。

## <a name="next-steps"></a>次のステップ

- [アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)