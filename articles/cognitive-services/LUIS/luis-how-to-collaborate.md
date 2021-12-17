---
title: 他の人と共同作業を行う - LUIS
titleSuffix: Azure Cognitive Services
description: アプリの所有者は、オーサリング リソースに共同作成者を追加できます。 これらの共同作業者は、アプリのモデル変更、トレーニング、および発行が可能です。
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2021
ms.openlocfilehash: 5a320d6368d4fdecaf4001fc9255c4c0ff3b276c
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2021
ms.locfileid: "114285586"
---
# <a name="add-contributors-to-your-app"></a>アプリに共同作成者を追加する

アプリの所有者は、アプリに共同作成者を追加できます。 これらの共同作業者は、アプリのモデル変更、トレーニング、および発行が可能です。 アカウントを [移行](luis-migration-authoring.md)すると、オーサリング リソースの "_共同作成者_" は、 **[アクセス制御 (IAM)]** ページを使用して、Azure portal で管理されます。 コラボレーターのメール アドレスと "_共同作成者_" ロールを使用して、ユーザーを追加します。

## <a name="add-contributor-to-azure-authoring-resource"></a>Azure オーサリング リソースに共同作成者を追加する

LUIS オーサリング エクスペリエンスが LUIS ポータルの **[管理] -> [Azure リソース]** ページのオーサリング リソースに関連付けられている場合は、移行済みです。

Azure portal で、自分の Language Understanding (LUIS) 作成リソースを見つけます。 種類は `LUIS.Authoring` です。 リソースの **[Access Control (IAM)]** ページで、投稿するユーザーの "**共同作成者**" ロールを追加します。 詳細な手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="view-the-app-as-a-contributor"></a>アプリに共同作成者として表示する

共同作成者として追加されたら、[LUIS ポータルにサインイン](sign-in-luis-portal.md)します。

[!INCLUDE [switch azure directories](includes/switch-azure-directories.md)]

### <a name="users-with-multiple-emails"></a>複数のメール アドレスを持つユーザー

LUIS アプリに共同作成者を追加する場合は、正確なメール アドレスを指定します。 Azure Active Directory (Azure AD) では、1 人のユーザーが複数のメール アカウントのいずれでも使用できますが、LUIS では、共同作成者を追加するときに指定したメール アドレスを使用してサインインする必要があります。

<a name="owner-and-collaborators"></a>

### <a name="azure-active-directory-resources"></a>Azure Active Directory リソース

組織で [Azure Active Directory](../../active-directory/index.yml) (Azure AD) を使用する場合、Language Understanding (LUIS) には、ユーザーが LUIS を使用するときのユーザー アクセスに関する情報に対するアクセス許可が必要です。 LUIS に必要なリソースはごくわずかです。

以下に関する管理者の同意など、管理者の同意があるか、管理者の同意が不要のアカウントでサインアップしようとしたときに、詳細な説明が表示されます。

* 組織のアカウントでアプリにサインインし、アプリにユーザーのプロファイルを読み取らせることを許可します。 基本的な会社情報の読み取りもアプリに許可します。 これにより、ユーザー ID、電子メール、名前などの基本的なプロファイル データを読み取るアクセス許可を LUIS に与えます
* ユーザーが現在アプリを使用していないときでも、ユーザーのデータの参照と更新をアプリに許可します。 このアクセス許可は、ユーザーのアクセス トークンを更新するために必要です。


### <a name="azure-active-directory-tenant-user"></a>Azure Active Directory テナント ユーザー

LUIS では、Azure Active Directory (Azure AD) の標準的な同意フローが使用されます。

テナント管理者は、Azure AD で LUIS を使用するためのアクセス許可を必要とするユーザーと直接的に協力する必要があります。

* まず、ユーザーが LUIS にサインインすると、管理者の承認を必要とするポップアップ ダイアログ ボックスが表示されます。 ユーザーは、続行する前にテナント管理者に問い合わせます。
* 次に、テナント管理者が LUIS にサインインすると、同意フロー ポップアップ ダイアログが表示されます。 これは、管理者がユーザーにアクセス許可を与えるために必要なダイアログです。 管理者がアクセス許可を承認すると、ユーザーは LUIS の使用を続行できます。 テナント管理者は、LUIS にサインインしなくても、LUIS のための[同意](https://account.activedirectory.windowsazure.com/r#/applications)にアクセスできます。 このページでは、一覧をフィルター処理して、名前 `LUIS` を含む項目だけを対象とすることができます。

テナント管理者が、特定のユーザーのみに LUIS を使わせたい場合は、いくつかの可能性のあるソリューションがあります。
* "管理者の同意" を付与します (Azure AD のすべてのユーザーに同意する) が、エンタープライズ アプリケーションのプロパティの下の [ユーザーの割り当てが必要ですか] を [はい] に設定し、最後に目的のユーザーのみをアプリケーションに割り当て/追加します。 この方法では、管理者はアプリに "管理者の同意" を与えますが、それにアクセスできるユーザーを制御できます。
* 2 つ目の解決策は、[Azure AD の ID を使用して Microsoft Graph で管理 API にアクセス](/graph/azuread-identity-access-management-concept-overview)し、特定のユーザーそれぞれに同意することです。

Azure Active Directory ユーザーと同意の詳細を確認します。
* [ご利用のアプリを特定のユーザー セットに制限する](../../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)

## <a name="next-steps"></a>次のステップ

* [バージョンを使用](luis-how-to-manage-versions.md)してアプリのライフ サイクルを制御する方法を確認します。
* [作成リソース](luis-how-to-azure-subscription.md)やそのリソースへの[共同作成者の追加](luis-how-to-collaborate.md)について理解します。
* オーサリング リソースとランタイム リソースの[作成方法](luis-how-to-azure-subscription.md)を確認します
* 新しい[オーサリング リソース](luis-migration-authoring.md)に移行します