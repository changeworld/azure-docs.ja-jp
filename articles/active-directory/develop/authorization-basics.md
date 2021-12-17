---
title: 承認の基本 | Azure
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームの承認の基本について説明します。
services: active-directory
author: Chrispine-Chiedo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2021
ms.custom: template-concept
ms.author: cchiedo
ms.reviewer: johngarland, mamarxen, ianbe, marsma
ms.openlocfilehash: a3710d4af1b65b1220db8938b35cf9d6cb516579
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124734715"
---
# <a name="authorization-basics"></a>承認の基本

**承認** (*AuthZ* と省略されることもあります) は、リソースまたは機能へのアクセスを評価するために使用されるアクセス許可を設定するため使用されます。  これに対し、**認証** (*AuthN* と省略されることもあります) は、ユーザーまたはサービスのようなエンティティが、主張する本人であることを証明することに重点を置いています。

承認には、エンティティがアクセスできる機能 (またはリソース) や、エンティティがアクセスできるデータ、およびそのデータを使用して実行できる操作を含めることができます。 これらはしばしば "*アクセス制御*" と呼ばれています。

> [!NOTE]
> 認証と承認は、ユーザーのみに限定されない概念です。 サービスまたはデーモン アプリケーションは、多くの場合、特定のユーザーの代わりではなく、それら自身としてリソースに対する要求を行うように構築されます。 これらのトピックについて説明するとき、ユーザーまたはアプリケーションのいずれかについて言及するために "エンティティ" という用語が使用されます。


## <a name="authorization-approaches"></a>承認方法

承認を処理するいくつかの一般的な方法があります。 [ロールベースのアクセス制御](./custom-rbac-for-developers.md)は、Microsoft ID プラットフォームを使用する、現在最も一般的な方法です。


### <a name="authentication-as-authorization"></a>承認としての認証 

おそらく承認の最も単純な形式は、要求を行うエンティティが認証されているかどうかに基づいてアクセスを許可または拒否することです。 要求元は、自分が本人であることを証明できる場合、保護されたリソースまたは機能にアクセスできます。

### <a name="access-control-lists"></a>アクセス制御リスト

アクセス制御リスト (ACL) による承認では、リソースまたは機能へのアクセスが許可されている、または許可されていない特定のエンティティの明示的なリストを保持する必要があります。 ACL では、承認としての認証よりも細かい制御が可能になりますが、エンティティの数が増えるにつれて管理が困難になります。

### <a name="role-based-access-control"></a>ロールベースのアクセス制御 

ロールベースのアクセス制御 (RBAC) は、アプリケーションで承認を適用するためのおそらく最も一般的な方法です。 RBAC を使用すると、あるエンティティが実行できるアクティビティの種類を記述するためにロールが定義されます。 アプリケーション開発者は、個々のエンティティではなくロールに対してアクセスを許可します。 その後管理者は、さまざまなエンティティにロールを割り当てて、どのエンティティがどのリソースと機能にアクセスできるかを制御できます。

高度な RBAC 実装では、ロールがアクセス許可のコレクションにマップされることがあり、このときアクセス許可には実行できるアクションやアクティビティが細かく記述されます。 ロールはその後、アクセス許可の組み合わせとして構成されます。 アプリケーションについてのエンティティの全体的なアクセス許可のセットを計算するために、エンティティが割り当てられているさまざまなロールに付与されたアクセス許可の交差部分が取られます。 このアプローチの好例として、Azure サブスクリプション内のリソースへのアクセスを制御する RBAC 実装が挙げられます。

> [!NOTE]
> [アプリケーション RBAC](./custom-rbac-for-developers.md) は [Azure RBAC](../../role-based-access-control/overview.md) および [Azure AD RBAC](../roles/custom-overview.md#understand-azure-ad-role-based-access-control) とは異なります。 Azure のカスタム ロールと組み込みロールはどちらも Azure RBAC の一部であり、Azure リソースの管理に役立ちます。 Azure AD RBAC では Azure AD リソースを管理できます。

### <a name="attribute-based-access-control"></a>属性ベースのアクセス制御 

属性ベースのアクセス制御 (ABAC) は、よりきめ細かいアクセス制御のメカニズムです。 この方法では、エンティティの属性、アクセスされるリソース、および現在の環境にルールが適用されて、何らかのリソースまたは機能へのアクセスが許可されるかどうかが決定されます。 例として、マネージャーであるユーザーのみが、営業日の午前 9 時から午後 5 時までの時間帯に "営業時間中のマネージャーのみ" というメタデータ タグで識別されたファイルにアクセスできるようにすることがあります。 この場合、アクセスは、ユーザーの属性 (マネージャーとしてのステータス)、リソースの属性 (ファイルのメタデータ タグ)、および環境属性 (現在の時刻) を調べることによって決定されます。

ABAC の利点の 1 つは、ルールと条件の評価によってよりきめ細かい動的なアクセス制御を実現でき、非常に具体的なロールと RBAC 割り当てを大量に作成せずに済むことです。

Azure Active Directory で ABAC を実現する 1 つの方法は、[動的グループ](../enterprise-users/groups-create-rule.md)を使用することです。 動的グループを使用すると、管理者は必要な値を持つ特定のユーザー属性に基づいて、ユーザーをグループに動的に割り当てることができます。  たとえば、作成者グループを作成して、作成者という役職を持つすべてのユーザーを作成者グループに動的に割り当てることができます。  動的グループを承認のための RBAC と組み合わせて使用して、ロールをグループにマップし、ユーザーをグループに動的に割り当てることができます。

[Azure ABAC](../../role-based-access-control/conditions-overview.md) は、現在利用できる ABAC ソリューションの一例です。 Azure ABAC は、Azure RBAC を基盤としたものであり、特定のアクションのコンテキストにおける属性に基づいたロールの割り当て条件を追加することにより構築されます。 

## <a name="implementing-authorization"></a>承認の実装

承認ロジックは多くの場合、アクセス制御が必要なアプリケーションまたはソリューション内に実装されます。 多くの場合、アプリケーション開発プラットフォームには、承認の実装を簡略化するミドルウェアまたはその他の API ソリューションが提供されています。 例としては、ASP.NET の [AuthorizeAttribute](/aspnet/core/security/authorization/simple?view=aspnetcore-5.0&preserve-view=true) や Angular の [Route Guards](./scenario-spa-sign-in.md?tabs=angular2#sign-in-with-a-pop-up-window) の使用などがあります。

認証されたエンティティに関する情報に依存した承認方法の場合、アプリケーションでは認証時に交換される情報が評価されます。 たとえば、[セキュリティ トークン](./security-tokens.md)内に指定された情報を使用します。 セキュリティ トークンに含まれていない情報を得るために、アプリケーションでは外部リソースに追加の呼び出しを行う場合もあります。

開発者がアプリケーション内に承認ロジックを完全に埋め込むことが絶対に必要というわけではありません。 代わりに、専用の承認サービスを使用して、承認の実装と管理を一元化することができます。


## <a name="next-steps"></a>次のステップ

- アプリケーションでのロールベースのアクセス制御のカスタム実装の詳細については、「[アプリケーション開発者向けのロールベースのアクセス制御](./custom-rbac-for-developers.md)」を参照してください。
- Microsoft ID プラットフォームと統合できるようにアプリケーションを登録するプロセスについては、[アプリケーション モデル](./application-model.md)に関する記事を参照してください。
- 単純な認証ベースの承認を構成する例については、「[Azure AD ログインを使用するように App Service または Azure Functions アプリを構成する](../../app-service/configure-authentication-provider-aad.md)」を参照してください。