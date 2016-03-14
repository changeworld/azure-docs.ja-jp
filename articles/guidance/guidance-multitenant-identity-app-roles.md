<properties
   pageTitle="アプリケーション ロール | Microsoft Azure"
   description="アプリケーション ロールを使用して承認を実行する方法について説明します。"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

#  マルチテナント アプリケーションのアプリケーション ロール

この記事は[シリーズの一部]です。このシリーズに付属する完成した[サンプル アプリケーション]もあります。

アプリケーション ロールは、ユーザーにアクセス許可を割り当てるために使用されます。たとえば、[Tailspin Surveys][Tailspin] アプリケーションには、次のロールが定義されています。

- 管理者。そのテナントに属するすべてのアンケートに対してすべての CRUD 操作を実行できます。
- 作成者。新しいアンケートを作成できます。
- 閲覧者。そのテナントに属するすべてのアンケートを読み取ることができます。

[承認]処理で、最終的にロールはアクセス許可に変換されることがわかります。ただ、最初の問題は、どのようにロールを割り当てて管理するかです。ここでは、主に 3 つのオプションを指定しました。

-	[Azure AD アプリ ロール](#roles-using-azure-ad-app-roles)
-	[Azure AD セキュリティ グループ](#roles-using-azure-ad-security-groups)
-	[アプリケーション ロール マネージャー](#roles-using-an-application-role-manager)

## Azure AD アプリ ロールを使用したロール

これは Tailspin Surveys アプリで使用したアプローチです。

このアプローチでは、SaaS プロバイダーがアプリケーション マニフェストにアプリケーション ロールを追加して定義します。顧客がサインアップした後に、顧客の AD ディレクトリ管理者がユーザーをロールに割り当てます。ユーザーがサインインすると、ユーザーが割り当てたロールが要求として送信されます。

> [AZURE.NOTE] 顧客が Azure AD Premium を利用している場合、管理者はセキュリティ グループをロールに割り当て、グループのメンバーがそのアプリ ロールを継承します。グループ所有者は AD 管理者になる必要はないため、これはロール管理する上で便利な方法です。

このアプローチの長所:

-	単純なプログラミング モデル。
-	ロールはアプリケーションに固有です。1 つのアプリケーションに対するロール要求は、別のアプリケーションに送信されません。
-	顧客が AD テナントからアプリケーションを削除すると、ロールの割り当ては解除されます。
-	アプリケーションは、ユーザーのプロファイルの読み取り以外の追加の Active Directory アクセス許可を必要としません。

短所:

- Azure AD Premium を利用していない顧客は、セキュリティ グループをロールに割り当てることができません。このような顧客の場合、AD 管理者がすべてのユーザーの割り当てを実行する必要があります。
- Web アプリとは別にバックエンド Web API がある場合、Web アプリのロール割り当ては Web API に適用されません。この点の詳細については、[バックエンド Web API のセキュリティ保護]に関するページを参照してください。

### 実装

**ロールを定義します**。 SaaS プロバイダーは、アプリケーション マニフェストでアプリケーション ロールを宣言します。Surveys アプリのマニフェスト エントリの例を次に示します。

```
"appRoles": [
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Creators can create Surveys",
    "displayName": "SurveyCreator",
    "id": "1b4f816e-5eaf-48b9-8613-7923830595ad",
    "isEnabled": true,
    "value": "SurveyCreator"
  },
  {
    "allowedMemberTypes": [
      "User"
    ],
    "description": "Administrators can manage the Surveys in their tenant",
    "displayName": "SurveyAdmin",
    "id": "c20e145e-5459-4a6c-a074-b942bbd4cfe1",
    "isEnabled": true,
    "value": "SurveyAdmin"
  }
],
```

`value` プロパティはロール要求に含まれます。`id` プロパティは、定義されたロールの一意の識別子です。常に `id` の新しい GUID 値を生成します。

**ユーザーを割り当てます**。新しい顧客がサインアップすると、アプリケーションは顧客の AD テナントに登録されます。この時点で、そのテナントの AD 管理者はユーザーをロールに割り当てることができるようになります。

> [AZURE.NOTE] 前述のように、Azure AD Premium を利用している顧客は、セキュリティ グループをロールに割り当てることもできます。

次の Azure ポータルのスクリーンショットには、3 人のユーザーが表示されています。Alice は、ロールに直接割り当てられました。Bob は、ロールに割り当てられている "Surveys Admin" というセキュリティ グループのメンバーとしてロールを継承しました。Charles は、どのロールにも割り当てられていません。

![割り当てられたユーザー](media/guidance-multitenant-identity/role-assignments.png)

> [AZURE.NOTE] また、Azure AD Graph API を使用して、アプリケーションで自動的にロールを割り当てることもできます。ただし、この場合は、顧客の AD ディレクトリの書き込みアクセス許可をアプリケーションが取得している必要があります。書き込みアクセス許可があるアプリケーションは、多大な損害を引き起こす可能性があります。また、顧客は、アプリがディレクトリを台無しにすることはないと信頼しています。多くの顧客は、このレベルのアクセス権を付与することを嫌う場合があります。

**ロール要求を取得します**。ユーザーがサインインすると、アプリケーションは種類が `http://schemas.microsoft.com/ws/2008/06/identity/claims/role` の要求でユーザーに割り当てられたロールを受け取ります。

ユーザーは複数のロールを持つことができます。また、まったくロールを持たないこともできます。承認コードでは、ユーザーのロール要求が 1 つのみと想定しないでください。代わりに、特定の要求値が存在するかどうかを確認するコードを作成してください。

```csharp
if (context.User.HasClaim(ClaimTypes.Role, "Admin")) { ... }
```

## Azure AD セキュリティ グループを使用したロール

このアプローチでは、ロールは AD セキュリティ グループとして表されます。アプリケーションは、セキュリティ グループ メンバーシップに基づいてアクセス許可をユーザーに割り当てます。

長所:

-	Azure AD Premium を利用していない顧客の場合、このアプローチで、セキュリティ グループを使用してロールの割り当てを管理できます。

短所:

- 複雑。各テナントが異なるグループ要求を送信するため、アプリは、各テナントについて、どのセキュリティ グループがどのアプリケーション ロールに対応するかを追跡する必要があります。
- 顧客が AD テナントからアプリケーションを削除しても、セキュリティ グループは AD ディレクトリに残ります。

### 実装

アプリケーション マニフェストで、`groupMembershipClaims` プロパティを "SecurityGroup" に設定します。この設定は、AAD からグループ メンバーシップ要求を取得するために必要です。

```
{
   // ...
   "groupMembershipClaims": "SecurityGroup",
}
```

新しい顧客がサインアップすると、アプリケーションに必要なロールのセキュリティ グループを作成するように指示されます。顧客は、グループのオブジェクト ID をアプリケーションに入力する必要があります。この入力内容は、テナントごとにグループ ID をアプリケーション ロールに対応付けたテーブルに保存されます。

> [AZURE.NOTE] また、Azure AD Graph API を使用して、アプリケーションで自動的にグループを作成することもできます。自動的に作成する方がエラーが少なくなります。ただし、顧客の AD ディレクトリの "すべてのグループの読み取りおよび書き込み" アクセス許可をアプリケーションが取得する必要があります。多くの顧客は、このレベルのアクセス権を付与することを嫌う場合があります。

ユーザーがサインインした場合:

1.	アプリケーションは要求としてユーザーのグループを受け取ります。各要求の値はグループのオブジェクト ID です。
2.	Azure AD は、トークンで送信されるグループ数を制限しています。グループ数がその制限を超えると、Azure AD から特殊な "過剰" 要求が送信されます。この要求が存在する場合、アプリケーションから Azure AD Graph API にクエリして、そのユーザーが属するすべてのグループを取得する必要があります。詳細については、「[Authorization in Cloud Applications using AD Groups]」(AD グループを使用したクラウド アプリケーションでの承認) の「Groups claim overage」(グループ要求の過剰) を参照してください。
3.	アプリケーションはアプリケーションのデータベースからオブジェクト ID を検索し、対応するアプリケーション ロールを見つけて、ユーザーに割り当てます。
4.	アプリケーションは、アプリケーション ロールを表すユーザー プリンシパルにカスタム要求値を追加します。例: `survey_role` = "SurveyAdmin".

承認ポリシーには、グループ要求ではなく、カスタムロール要求を使用する必要があります。

## アプリケーション ロール マネージャーを使用したロール

このアプローチでは、アプリケーション ロールは Azure AD に保存されません。代わりに、アプリケーションはアプリケーション DB に各ユーザーのロール割り当てを保存します。たとえば、ASP.NET Identity の **RoleManager** クラスを使用します。

長所:

-	アプリはロールとユーザー割り当てを完全に制御できます。

短所:

- さらに複雑で、保守が困難になります。
- ロール割り当ての管理に AD セキュリティ グループを使用できません。
- アプリケーション データベースにユーザー情報が保存され、ユーザーが追加または削除されても、テナントの AD ディレクトリと同期されない可能性があります。   

このアプローチには、既存の例が多数あります。たとえば、「[認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ]」を参照してください。


## その他のリソース

-	[Azure AD アプリケーション ロールとロール要求を使用した Web アプリでの承認]
-	[Authorization in Cloud Applications using AD Groups] (AD グループを使用したクラウド アプリケーションでの承認)
-	[Roles based access control in cloud applications using Azure AD] (Azure AD を使用したクラウド アプリケーションのロール ベースのアクセス制御)
-	[サポートされているトークンと要求の種類]。Azure AD のロールとグループの要求について説明します。
-	[Azure Active Directory のアプリケーション マニフェストについて]

<!-- Links -->
[Tailspin]: guidance-multitenant-identity-tailspin.md
[シリーズの一部]: guidance-multitenant-identity.md
[承認]: guidance-multitenant-identity-authorize.md
[バックエンド Web API のセキュリティ保護]: guidance-multitenant-identity-web-api.md
[Authorization in Cloud Applications using AD Groups]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[認証および SQL DB を使用する ASP.NET MVC アプリの作成と、Azure App Service へのデプロイ]: ../app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md
[Azure Active Directory のアプリケーション マニフェストについて]: ../active-directory/active-directory-application-manifest.md
[サポートされているトークンと要求の種類]: ../active-directory/active-directory-token-and-claims.md
[Roles based access control in cloud applications using Azure AD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[Azure AD アプリケーション ロールとロール要求を使用した Web アプリでの承認]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims
[サンプル アプリケーション]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->