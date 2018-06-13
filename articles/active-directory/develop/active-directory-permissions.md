---
title: Azure AD におけるアクセス許可 | Microsoft Docs
description: Azure Active Directory におけるスコープとアクセス許可、およびそれらの使用方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156893"
---
# <a name="permissions-in-azure-ad"></a>Azure AD におけるアクセス許可
Azure Active Directory (Azure AD) では、OAuth フローと OpenID Connect (OIDC) フローの両方でアクセス許可を広く利用します。 アプリが Azure AD から受け取るアクセス トークンには、特定のリソースに関してアプリに付与されたアクセス許可 (スコープとも呼ばれます) を示す要求が含まれています。 呼び出す API に適したアクセス許可がトークンに含まれていることを確認するだけで済むため、リソースに対する承認が容易になります。 

## <a name="types-of-permissions"></a>アクセス許可の種類
Azure AD では、次の 2 種類のアクセス許可が定義されています。 
* **委任されたアクセス許可** - サインインしているユーザーが存在するアプリで使用されます。 これらのアプリでは、ユーザーまたは管理者が、アプリから要求されたアクセス許可に同意すると、API の呼び出し時にサインインしているユーザーとして動作するためのアクセス許可がアプリに委任されます。 API によっては、ユーザーが API に直接同意することができない場合があり、代わりに ["管理者の同意" が必要](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent)になります。
* **アプリケーションのアクセス許可** - サインインしているユーザーが存在しない状態で実行されるアプリ (バックグラウンド サービスまたはデーモンとして実行されるアプリなど) で使用されます。 通常、アプリケーションのアクセス許可は非常に強力であり、ユーザー境界を越えるデータや管理者に限定されたデータへのアクセスを許可するため、これらのアクセス許可には[管理者だけが同意](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant)できます。 

有効なアクセス許可は、アプリが API に要求を行うときに付与されるアクセス許可です。 

* 委任されたアクセス許可の場合、アプリの有効なアクセス許可は、(同意によって) アプリに付与されている委任されたアクセス許可と現在サインインしているユーザーの特権の共通部分の最小特権になります。 サインインしているユーザーよりも多くの特権をアプリに付与することはできません。 組織内では、サインインしているユーザーの特権は、ポリシーによって決定することも、1 つ以上の管理者ロールのメンバーシップによって決定することもできます。 管理者ロールの詳細については、[Azure AD での管理者ロールの割り当て](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md)に関する記事をご覧ください。
    たとえば、Microsoft Graph で委任されたアクセス許可として `User.ReadWrite.All` がアプリに付与されているとします。 通常、このアクセス許可は、組織内のすべてのユーザーのプロファイルを読み取り、更新する権限をアプリに付与します。 サインインしているユーザーが全体管理者の場合、アプリは組織内のすべてのユーザーのプロファイルを更新できます。 ただし、サインインしているユーザーが管理者ロールに属していない場合、アプリが更新できるのは、サインインしているユーザーのプロファイルだけになります。 アプリにはユーザーの代理で動作するためのアクセス許可が付与されていますが、そのユーザーに組織内の他のユーザーのプロファイルを更新する権限がないため、アプリがこれを実行することはできません。
* アプリケーションのアクセス許可の場合、アプリの有効なアクセス許可は、そのアクセス許可が暗示する完全なレベルの権限になります。 たとえば、アプリケーションのアクセス許可として `User.ReadWrite.All` が付与されているアプリは、組織内のすべてのユーザーのプロファイルを更新できます。 

## <a name="permission-attributes"></a>アクセス許可の属性
Azure AD のアクセス許可には、ユーザー、管理者、またはアプリ開発者が、そのアクセス許可によって付与されるアクセスの対象について、情報に基づいて判断するのに役立つ多数のプロパティがあります。 

> [!NOTE]
> Azure AD アプリケーションまたはサービス プリンシパルが公開するアクセス許可は、Azure portal または PowerShell を使用して表示できます。 次のスクリプトを実行して、Microsoft Graph で公開されているアクセス許可を表示してみてください。
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| プロパティ名 | [説明] | 例 | 
| --- | --- | --- |
| ID | このアクセス許可を一意に識別する GUID 値です。 | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | このスコープが使用可能かどうかを示します。 | true | 
| type | このアクセス許可にユーザーの同意と管理者の同意のどちらが必要かを示します。 | User | 
| AdminConsentDescription | 管理者の同意エクスペリエンスで管理者に表示される説明です。 | ユーザーのメールボックスのメールを読み取ることをアプリに許可します。 | 
| AdminConsentDisplayName | 管理者の同意エクスペリエンスで管理者に表示されるフレンドリ名です。 | ユーザーのメールの読み取り | 
| UserConsentDescription | ユーザーの同意エクスペリエンスでユーザーに表示される説明です。 |  ユーザーが自分のメールボックスのメールを読み取ることをアプリに許可します。 | 
| UserConsentDisplayName | ユーザーの同意エクスペリエンスでユーザーに表示されるフレンドリ名です。 | メールの読み取り | 
| 値 | OAuth 2.0 認可フローにおいて、アクセス許可の識別に使用される文字列です。 完全修飾アクセス許可名を形成するために、アプリ ID URI 文字列と組み合わせることもできます。 | `Mail.Read` | 

## <a name="types-of-consent"></a>同意の種類
Azure AD のアプリケーションでは、必要なリソースや API へのアクセス権を取得するために同意を利用します。 成功を収めるために、アプリが認識しておく必要があると考えられる多くの種類の同意があります。 アクセス許可を定義する場合は、ユーザーがアプリや API へのアクセス権を取得する方法も理解しておく必要があります。

* **静的なユーザーの同意** - アプリが対話する必要があるリソースを指定すると、[OAuth 2.0 認可フロー](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code)で自動的に発生します。 静的なユーザーの同意シナリオでは、Azure portal のアプリの構成で、アプリに必要なすべてのアクセス許可が既に指定されている必要があります。 ユーザー (または、必要に応じて管理者) がこのアプリに同意していなかった場合、Azure AD によって、現時点でユーザーに同意を求めるメッセージが表示されます。 

    API の静的セットへのアクセスを要求する Azure AD アプリの登録の詳細をご覧ください。
* **動的なユーザーの同意** - v2 Azure AD アプリ モデルの機能です。 このシナリオでは、アプリは、[v2 アプリの OAuth 2.0 認可フロー](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent)で必要な一連のスコープを要求します。 ユーザーがまだ同意していなかった場合、現時点で同意を求められます。 動的な同意の詳細については、[こちら](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent)をご覧ください。

    > [!NOTE]
    > 動的な同意は便利な場合もありますが、管理者の同意を必要とするアクセス許可の場合、管理者の同意エクスペリエンスで同意する時点でこれらのアクセス許可を把握できないため、大きな問題が生じます。 管理特権のスコープが必要な場合は、アプリでそれらのスコープを Azure portal に登録する必要があります。
  
* **管理者の同意** - 特定の高い権限のアクセス許可がアプリに必要な場合に必要となります。 これにより、管理者は、組織の高い権限が必要なデータにアプリやユーザーがアクセスすることを承認する前に制御を強化できます。 管理者の同意を付与する方法の詳細については、[こちら](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)をご覧ください。

## <a name="best-practices"></a>ベスト プラクティス

### <a name="resource-best-practices"></a>リソースのベスト プラクティス
API を公開するリソースでは、保護するデータまたはアクションに固有のアクセス許可を定義します。 これにより、クライアントには不要なデータへのアクセス許可が付与されなくなり、ユーザーには同意対象のデータに関する十分な情報が提供されるようになります。

リソースでは、`Read` アクセス許可と `ReadWrite` アクセス許可をそれぞれ明示的に定義します。 

リソースでは、ユーザー境界を越えるデータへのアクセスを許可するすべてのアクセス許可を `Admin` アクセス許可としてマークします。 

リソースは、`Subject.Permission[.Modifier]` という名前付けパターンに従います。`Subject` は使用可能なデータの種類に対応し、`Permission` はそのデータに対してユーザーが実行できるアクションに対応します。`Modifier` は、別のアクセス許可の特殊化を記述するために必要に応じて使用します。 例:  
* Mail.Read - ユーザーにメールの読み取りを許可します。 
* Mail.ReadWrite - ユーザーにメールの読み取りまたは書き込みを許可します。
* Mail.ReadWrite.All - 管理者またはユーザーに、組織内のすべてのメールへのアクセスを許可します。

### <a name="client-best-practices"></a>クライアントのベスト プラクティス
アプリに必要なスコープのアクセス許可だけを要求します。 アプリに付与されているアクセス許可が多すぎると、アプリが侵害された場合に、ユーザー データが公開される危険性があります。

クライアントは、同じアプリからアプリケーションのアクセス許可と委任されたアクセス許可を要求しないようにする必要があります。 これを行うと、特権が昇格され、ユーザーは自分のアクセス許可では許可されないデータにアクセスできるようになる可能性があります。 




