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
ms.openlocfilehash: 9540c207388dfc5b37e2dd03939b14753c3d8ec7
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791785"
---
# <a name="create-and-manage-a-catalog-of-resources-in-azure-ad-entitlement-management"></a>Azure AD エンタイトルメント管理でリソースのカタログを作成して管理する

この記事では、Azure Active Directory (Azure AD) エンタイトルメント管理でリソースのカタログを作成して管理したり、パッケージにアクセスしたりする方法について説明します。

## <a name="create-a-catalog"></a>カタログを作成する

カタログは、リソースとアクセス パッケージのコンテナーです。 関連するリソースとアクセス パッケージをグループ化するときは、カタログを作成します。 どのユーザーでも、カタログを作成すると、最初のカタログ所有者になります。 カタログ所有者は、さらに多くのカタログ所有者を追加できます。

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ作成者

> [!NOTE]
> ユーザー管理者ロールが割り当てられているユーザーは、カタログを作成したり、所有していないカタログ内のアクセス パッケージを管理したりできなくなります。 組織内のユーザーに、エンタイトルメント管理でカタログ、アクセス パッケージ、またはポリシーを構成するためにユーザー管理者ロールが割り当てられている場合は、代わりに、これらのユーザーに ID ガバナンス管理者ロールを割り当てる必要があります。

カタログを作成するには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで、 **[カタログ]** を選択します。

    ![Azure portal のエンタイトルメント管理カタログを示すスクリーンショット。](./media/entitlement-management-catalog-create/catalogs.png)

1. **[新しいカタログ]** を選択します。

1. カタログの一意の名前と説明を入力します。

    この情報は、アクセス パッケージの詳細に表示されます。

1. このカタログ内のアクセス パッケージを、作成されたらすぐにユーザーが要求できるようにする場合は、 **[有効]** を **[はい]** に設定します。

1. 選択された外部ディレクトリ内のユーザーがこのカタログ内のアクセス パッケージを要求できるようにする場合は、 **[Enabled for external users]\(外部ユーザーに対して有効にする\)** を **[はい]** に設定します。

    ![[新しいカタログ] ペインを示すスクリーンショット。](./media/entitlement-management-shared/new-catalog.png)

1. **[作成]** を選択してカタログを作成します。

## <a name="create-a-catalog-programmatically"></a>カタログをプログラミングで作成する

プログラムでカタログを作成するには 2 つの方法があります。

### <a name="create-a-catalog-with-microsoft-graph"></a>Microsoft Graph でカタログを作成する

Microsoft Graph を使用してカタログを作成できます。 委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーション、またはそのアプリケーション アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageCatalog を作成する](/graph/api/accesspackagecatalog-post?view=graph-rest-beta&preserve-view=true)ことができます。

### <a name="create-a-catalog-with-powershell"></a>PowerShell でカタログを作成する

カタログはまた、PowerShell で [Identity Governance 用の Microsoft Graph PowerShell コマンドレット](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) モジュール バージョン 1.6.0 以降の `New-MgEntitlementManagementAccessPackageCatalog` コマンドレットを使用して作成することもできます。

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$catalog = New-MgEntitlementManagementAccessPackageCatalog -DisplayName "Marketing"
```

## <a name="add-resources-to-a-catalog"></a>カタログにリソースを追加する

アクセス パッケージにリソースを含めるには、リソースがカタログ内に存在している必要があります。 追加できるリソースの種類は、グループ、アプリケーション、および SharePoint Online サイトです。 例:

* グループとしては、クラウドで作成された Microsoft 365 グループ、またはクラウドで作成された Azure AD セキュリティ グループを指定できます。 オンプレミスの Active Directory に由来するグループは、その所有者またはメンバー属性を Azure AD で変更できないため、リソースとして割り当てることができません。 配布グループとして Exchange Online に由来するグループも Azure AD で変更できません。
* アプリケーションとしては、Azure AD エンタープライズ アプリケーションを指定できます。これには、SaaS (サービスとしてのソフトウェア) アプリケーションと、Azure AD と統合された独自のアプリケーションの両方が含まれます。 複数のロールを持つアプリケーションの適切なリソースを選択する方法の詳細については、「[リソース ロールを追加する](entitlement-management-access-package-resources.md#add-resource-roles)」を参照してください。
* サイトとしては、SharePoint Online サイトまたは SharePoint Online サイト コレクションを指定できます。

**前提条件のロール:** 「[カタログにリソースを追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)」を参照してください。

カタログにリソースを追加するには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで **[カタログ]** を選択してから、リソースを追加するカタログを開きます。

1. 左側のメニューで、 **[リソース]** を選択します。

1. **[リソースの追加]** を選択します。

1. **[グループとチーム]** 、 **[アプリケーション]** 、または **[SharePoint サイト]** のリソースの種類を選択します。

    追加するリソースが表示されない、またはリソースを追加できない場合は、必要な Azure Active Directory ディレクトリ ロールおよびエンタイトルメント管理ロールを持っていることを確認してください。 必要なロールを持つ人物に、カタログへのリソース追加を依頼することが必要な場合があります。 詳細については、[リソースをカタログに追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)に関するページを参照してください。

1. カタログに追加する種類の 1 つ以上のリソースを選択します。

    ![[カタログにリソースを追加する] ペインを示すスクリーンショット。](./media/entitlement-management-catalog-create/catalog-add-resources.png)

1. 完了したら、 **[追加]** を選択します。

    これらのリソースをカタログ内のアクセス パッケージに含めることができるようになりました。

### <a name="add-resource-attributes-preview-in-the-catalog"></a>カタログでリソース属性 (プレビュー) を追加する

属性は、要求元がアクセス要求を送信する前に回答するよう求められる必須フィールドです。 これらの属性への回答は承認者に示されると共に、Azure AD のユーザー オブジェクトにもスタンプされます。 

> [!NOTE]
>リソースが含まれているアクセス パッケージへの要求を送信するには、その前に、そのリソースに設定されているすべての属性への回答が必要です。 要求元が回答を指定しない場合、その要求は処理されません。

アクセス要求のための属性を必要とするには:

1. 左側のメニューで **[リソース]** を選択すると、そのカタログ内のリソースの一覧が表示されます。 

1. 属性を追加するリソースの横にある省略記号を選択してから、 **[属性が必要 (プレビュー)]** を選択します。 

    ![[属性が必要 (プレビュー)] の選択を示すスクリーンショット。](./media/entitlement-management-catalog-create/resources-require-attributes.png)
 
1.  属性の種類を選択します。

    1. **[組み込み]** には、Azure AD のユーザー プロファイル属性が含まれます。
    1. **[ディレクトリ スキーマ拡張]** は、Azure AD にユーザー オブジェクトやその他のディレクトリ オブジェクトに関するより多くのデータを格納する方法を提供します。 これには、グループ、テナントの詳細、およびサービス プリンシパルが含まれます。 アプリケーションに対する要求の送信に使用できるのは、ユーザー オブジェクトの拡張機能属性だけです。
1. **[組み込み]** を選択した場合は、ドロップダウン リストから属性を選択します。 **[ディレクトリ スキーマ拡張]** を選択した場合は、テキスト ボックスに属性名を入力します。

    > [!NOTE]
    > User.mobilePhone 属性は、管理者以外のユーザーに対してのみ更新できます。 詳細については、[この Web サイト](/graph/permissions-reference#remarks-5)を参照してください。

1.  要求元がその回答のために使用する回答形式を選択します。 回答形式には、 **[短いテキスト]** 、 **[複数選択]** 、 **[長いテキスト]** が含まれます。

1.  複数選択を選択した場合は、回答のオプションを構成するために **[編集とローカライズ]** を選択します。 
    1. 表示される **[質問の表示/編集]** ペインで、要求元が質問に回答するときに提供する回答のオプションを **[回答値]** ボックスに入力します。
    1. 回答のオプションの言語を選択します。 追加の言語を選択した場合は、回答のオプションをローカライズできます。
    1. 必要な数の回答を入力し、 **[保存]** を選択します。

1. 直接割り当てとセルフサービス要求で属性値を編集可能にする場合は、 **[はい]** を選択します。

    > [!NOTE]
    > ![属性を編集可能にする方法を示すスクリーンショット。](./media/entitlement-management-catalog-create/attributes-are-editable.png)
    > - **[属性値が編集可能である]** ボックスで **[いいえ]** を選択し、属性値 "*が空である*" 場合、ユーザーはその属性の値を入力できます。 保存した後、この値を編集することはできません。 
    > - **[属性値が編集可能である]** ボックスで **[いいえ]** を選択し、属性値 "*が空でない*" 場合、ユーザーは、直接割り当てとセルフサービス要求のどちらのときも既存の値を編集できません。
 
    ![ローカリゼーションの追加を示すスクリーンショット。](./media/entitlement-management-catalog-create/add-attributes-questions.png)

1.  ローカリゼーションを追加する場合は、 **[ローカリゼーションの追加]** を選択します。

    1. **[質問のローカリゼーションの追加]** ペインで、選択された属性に関連する質問をローカライズする言語の言語コードを選択します。
    1. 構成した言語で、 **[ローカライズされたテキスト]** ボックスに質問を入力します。
    1. 必要なすべてのローカリゼーションを追加したら、 **[保存]** を選択します。

       ![ローカリゼーションの保存を示すスクリーンショット。](./media/entitlement-management-catalog-create/attributes-add-localization.png)

1.  **[属性が必要 (プレビュー)]** ページですべての属性情報が完了したら、 **[保存]** を選択します。

### <a name="add-a-multi-geo-sharepoint-site"></a>Multi-Geo SharePoint サイトを追加する

1. SharePoint で [Multi-Geo](/microsoft-365/enterprise/multi-geo-capabilities-in-onedrive-and-sharepoint-online-in-microsoft-365) が有効になっている場合は、サイトを選択する環境を選択します。
    
    :::image type="content" source="media/entitlement-management-catalog-create/sharepoint-multi-geo-select.png" alt-text="[SharePoint Online サイトの選択] ペインを示すスクリーンショット。":::

1. 次に、カタログに追加するサイトを選択します。

### <a name="add-a-resource-to-a-catalog-programmatically"></a>プログラムでカタログにリソースを追加する

Microsoft Graph を使用して、カタログにリソースを追加することもできます。 委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーまたはカタログおよびリソースの所有者は、API を呼び出して、[accessPackageResourceRequest を作成する](/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta&preserve-view=true)ことができます。 ただし、アプリケーション アクセス許可を持つアプリケーションは、要求の時点でユーザー コンテキストがないと、まだプログラムでリソースを追加できません。

## <a name="remove-resources-from-a-catalog"></a>カタログからリソースを削除する

カタログからリソースを削除できます。 カタログからリソースを削除できるのは、それが、そのカタログのどのアクセス パッケージでも使用されていない場合だけです。

**前提条件のロール:** 「[カタログにリソースを追加するために必要なロール](entitlement-management-delegate.md#required-roles-to-add-resources-to-a-catalog)」を参照してください。

カタログからリソースを削除するには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで **[カタログ]** を選択してから、リソースを削除するカタログを開きます。

1. 左側のメニューで、 **[リソース]** を選択します。

1. 削除するリソースを選択します。

1. **[削除]** を選択します。 オプションで、省略記号 ( **...** ) を選択してから、 **[リソースの削除]** を選択します。

## <a name="add-more-catalog-owners"></a>カタログ所有者を追加する

カタログを作成したユーザーが最初のカタログ所有者になります。 カタログの管理を委任するには、カタログ所有者ロールにユーザーを追加します。 カタログ所有者を追加すると、カタログ管理の責任を共有するのに役立ちます。

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

カタログ所有者ロールにユーザーを割り当てるには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで **[カタログ]** を選択してから、管理者を追加するカタログを開きます。

1. 左側のメニューで、 **[ロールと管理者]** を選択します。

    ![カタログのロールと管理者を示すスクリーンショット。](./media/entitlement-management-shared/catalog-roles-administrators.png)

1. **[所有者の追加]** を選択して、これらのロールのメンバーを選択します。

1. **[選択]** をクリックすると、これらのメンバーが追加されます。

## <a name="edit-a-catalog"></a>カタログを編集する

カタログの名前と説明を編集できます。 この情報は、アクセス パッケージの詳細に表示されます。

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

カタログを編集するには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで **[カタログ]** を選択してから、編集するカタログを開きます。

1. カタログの **[概要]** ページで、 **[編集]** を選択します。

1. カタログの名前、説明、または有効になっている設定を編集します。

    ![カタログ設定の編集を示すスクリーンショット。](./media/entitlement-management-shared/catalog-edit.png)

1. **[保存]** を選択します。

## <a name="delete-a-catalog"></a>カタログを削除する

カタログを削除できるのは、そのカタログにどのアクセス パッケージも含まれていない場合に限られます。

**前提条件のロール:** グローバル管理者、ID ガバナンス管理者、ユーザー管理者、またはカタログ所有者

カタログを削除するには:

1. Azure portal で、 **[Azure Active Directory]**  >  **[ID ガバナンス]** の準に選択します。

1. 左側のメニューで **[カタログ]** を選択してから、削除するカタログを開きます。

1. カタログの **[概要]** ページで、 **[削除]** を選択します。

1. 表示されるメッセージ ボックスで、 **[はい]** を選択します。

### <a name="delete-a-catalog-programmatically"></a>プログラムでカタログを削除する

Microsoft Graph を使用してカタログを削除することもできます。 委任された `EntitlementManagement.ReadWrite.All` アクセス許可を持つアプリケーションを有する適切なロールのユーザーは、API を呼び出して、[accessPackageCatalog を削除する](/graph/api/accesspackagecatalog-delete?view=graph-rest-beta&preserve-view=true)ことができます。

## <a name="next-steps"></a>次のステップ

[アクセス パッケージ管理者にアクセス ガバナンスを委任する](entitlement-management-delegate-managers.md)