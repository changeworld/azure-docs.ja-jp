---
title: Azure Active Directory でマルチテナント ユーザー管理の設定をする
description: ゲスト アカウントによる Azure Active Directory テナント間のユーザー アクセスを設定する、複数の方法を説明します
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/25/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 379135bc446dbef6a2145a6339b5ddd318a5f579
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269974"
---
# <a name="multi-tenant-user-management"></a>マルチテナント ユーザー管理 

複数のユーザーを 1 つの Azure Active Directory (Azure AD) テナントにプロビジョニングすると、リソースをまとめて閲覧したり、ポリシーと管理をそれぞれ 1 つにまとめたりできます。 この方法により、一貫性のあるユーザー ライフサイクル管理ができます。 

**Microsoft では、できる限りシングル テナントを選ぶことをお勧めしています**。 ただし、今すぐには、必要なものを 1 つの Azure AD テナントにまとめられない場合もあります。 組織の中には、2 つ以上の Azure AD テナントを使用しているマルチテナントの組織もあります。 この場合、テナント間で独自のコラボレーションを行ったり、独自の管理要件を設定したりすることが必要な場合があります。

組織の ID およびアクセス管理 (IAM) の要件は、次の理由により複雑化する場合があります。

* 合併、買収、資産や子会社の売却。

* パブリック、ソブリンまたは地域クラウド間のコラボレーション。

* 1 つの Azure AD テナントに統合できない政治的な理由や組織の構造。

このガイダンスは、一貫性のあるユーザー ライフサイクル管理を行うのにも役立ちます。つまり、Azure で利用できるツール、特に Azure AD B2B コラボレーションを使用して、複数テナントに対するユーザーのプロビジョニング、管理、プロビジョニング解除を行うのに役立ちます。 That is, provisioning, managing, and deprovisioning users across tenants using the tools available with Azure. Specifically, by using <bpt id="p1">[</bpt>Azure AD B2B collaboration<ept id="p1">](../external-identities/what-is-b2b.md)</ept>.

## <a name="azure-ad-b2b-collaboration"></a>Azure AD B2B コラボレーション

Azure AD コラボレーションを使用すれば、会社のアプリケーションやサービスを、外部のゲスト ユーザーと安全に共有できます。 どんな組織のユーザーでも問題ありません。 Azure AD B2B コラボレーションを使用すれば、IT 環境やデータへのアクセスを継続的に管理できます。 Azure AD B2B コラボレーションでは、組織内のユーザーにゲスト アクセスを与えることもできます。 B2B ゲスト ユーザー アクセスは従来、組織の管理外にある外部ユーザーのアクセス認証に使用されます。 ですが、ゲスト ユーザー アクセスは、組織で管理している複数テナント間のアクセス管理にも使用できます。 厳密に言うと B2B ソリューションではありませんが、Azure AD B2B コラボレーションは、複数テナントのシナリオで、組織内のユーザーを管理するときにも使用できます。

次のリンクで、Azure AD B2B コラボレーションの詳しい情報を提供しています。

| [アーティクル]| 説明 |
| - |-|
| **概念に関する記事**|  |
| [B2B のベスト プラクティス](../external-identities/b2b-fundamentals.md)| ユーザーと管理者がスムーズに作業するための推奨事項。|  
| [B2B と Office 365 の外部共有](../external-identities/o365-external-user.md)| B2B、Office 365、SharePoint および OneDrive でリソースを共有する場合の共通点と異なる点を説明しています。|  
| [Azure AD B2B コラボレーション ユーザーのプロパティ](../external-identities/user-properties.md)| Azure Active Directory (Azure AD) の B2B ゲスト ユーザー オブジェクトのプロパティと状態を説明しています。 招待の引き換え前後の作業を詳しく説明しています。|  
| [B2B ユーザー トークン](../external-identities/user-token.md)| B2B ゲスト ユーザーのベアラー トークンの例を紹介しています。|  
| [B2B 向けの条件付きアクセス](../external-identities/conditional-access.md)| ゲスト ユーザーに対する条件付きアクセスと MFA の仕組みを説明しています。|  
| **ハウツー記事**|  |
| [PowerShell を使用して Azure AD B2B コラボレーション ユーザーを一括で招待する](../external-identities/bulk-invite-powershell.md)| PowerShell で外部ユーザーへの招待を一括送信する方法を説明しています。|
| [B2B ゲスト ユーザーに多要素認証を適用する](../external-identities/b2b-tutorial-require-mfa.md)|テナント、アプリ、個々のゲスト ユーザーの認証レベルに、条件付きアクセスと MFA のポリシーを適用します。 |
| [電子メール ワンタイム パスコード認証](../external-identities/one-time-passcode.md)| 電子メール ワンタイム パスコード機能では、B2B ゲスト ユーザーが Azure AD、Microsoft アカウント (MSA)、Google フェデレーションなどの他の手段を使用して認証できないときに、ユーザーの認証が行われます。|

## <a name="terminology"></a>用語

これらの用語は、このコンテンツを通じて使用します。

* **リソース テナント**: ユーザーが他者と共有するリソースが存在する Azure AD テナント。

* **ホーム テナント**: リソース テナントのリソースへのアクセスが必要なユーザーが存在する Azure AD テナント。

* **ユーザー ライフサイクル管理**: リソースに対するユーザー アクセスをプロビジョニング、管理、プロビジョニング解除するプロセス。

* **統一 GAL**: 各テナントのユーザーは、Global Address List (GAL) に存在する組織のユーザーを見ることができます。

## <a name="deciding-how-to-meet-your-requirements"></a>要件を満たす方法を決める

組織独自の要件に基づいて、複数テナント上でユーザーを管理する方法を決めます。 効果的な方法を設定するには、次のことを検討します。

* テナントの数

* 組織の種類

* 現在のトポロジ

* ユーザー同期に関する具体的要件 

### <a name="common-requirements"></a>一般的な要件

多くの組織では、すぐに実行するコラボレーションの要件を最初に定めます。 これらの要件は Day One (初日) 要件とも呼ばれ、エンド ユーザーが、有益な業務を行うことを妨げられずに、スムーズに参加できるようにするための要件です。 Day One 要件と管理要件を定めるときは、これらのゴールを設定することを検討してください。 

| 要件の分類| よくある要件|
| ------------ | - |
| **通信要件**|  |
| 統一グローバル アドレス リスト| 各ユーザーが、ホーム テナントの GAL で、他のすべてのユーザーを見ることができる。 |
| 現在対応可能かどうかの情報| ユーザーが現在対応可能であるかどうかを、他のユーザーに分かるようにする。 [Exchange Online の組織の関係](/exchange/sharing/organization-relationships/create-an-organization-relationship)で、これを実行できます。|
| チャットと在席確認| 他のユーザーが在席しているかどうかを確認して、インスタント メッセージを送れるようにする。 これは [Microsoft Teams の外部アクセス](/microsoftteams/manage-external-access)で設定できます。|
| 会議室などのリソースの予約| 組織に存在する会議室などのリソースを、ユーザーが予約できるようにする。 現在、複数テナントに対する会議室予約はできません。|
単一の電子メール ドメイン| *users@contoso.com* などの単一の電子メール ドメインで、すべてのユーザーが電子メールを送受信できるようにする。 現在、送信には、サード パーティ製のアドレス書き換えソリューションが必要です。|
| **アクセス要件**|  |
| ドキュメントへのアクセス| SharePoint、OneDrive、Teams でユーザーがドキュメントを共有できるようにする |
| 管理| 複数テナントに対してデプロイしたサブスクリプションとサービスの構成を、管理者が管理できるようにする |
| アプリケーション アクセス| 組織全体のアプリケーションにエンド ユーザーがアクセスできるようにする |
| シングル サインオン| ユーザーが、追加のサインイン情報を入力せずに、組織全体のリソースにアクセスできるようにする。|

### <a name="patterns-for-account-creation"></a>アカウント作成の方法 

ゲスト ユーザー アカウントのライフサイクルを作成、管理する方法はいくつかあります。 Microsoft は、汎用性のある 3 種類の方法を用意しました。 それらの方法で要件を設定、実行できます。 自分たちのシナリオに最適な方法を選び、それから、その方法の詳細を定めてください。

| メカニズム |  説明 | 最適な条件 |
| - | - | - |
| [エンド ユーザーによる実施](multi-tenant-user-management-scenarios.md#end-user-initiated-scenario) | テナント、アプリ、リソースにゲスト ユーザーを招待する権限を、リソース テナントの管理者から、リソース テナント内のユーザーに委任します。 ホーム テナントのユーザーは、個別に招待を受けるかサインアップします。 |  <li>ユーザーがリソースにアクセスするとき、一時的に用意した権限を使用する。 <li>ユーザーの属性の自動同期が必要ない。<li>統一 GAL が必要ない。 |
|[スクリプト化](multi-tenant-user-management-scenarios.md#scripted-scenario) | リソース テナントの管理者が、“プル” プロセスのスクリプトをデプロイすることで、ゲスト ユーザーの検出とプロビジョニングを自動化し、共有が必要なシナリオに対応します。 |  <li>テナントが 2 つ以下。<li>ユーザーの属性の自動同期が必要ない。<li>ユーザーがリソースにアクセスするとき、事前に設定した (一時的に用意したものでない) 権限を使用する。|
|[自動](multi-tenant-user-management-scenarios.md#automated-scenario)|リソース テナント管理者が、ID プロビジョニング システムを使用して、プロセスのプロビジョニングとプロビジョニング解除を自動化します。 |  <li>プロビジョニングとプロビジョニング解除を含む完全な ID ライフサイクル管理を、自動で行う必要がある。<li>GAL への書き込みを行い、動的エンタイトルメントが必要なシナリオに対応するために、属性同期が必要。<li>ユーザーが “Day One” (初日) にリソースにアクセスするとき、事前に設定した (一時的に用意したものでない) 権限を使用する。|

  
## <a name="next-steps"></a>次のステップ

[マルチテナント ユーザー管理シナリオ](multi-tenant-user-management-scenarios.md)

[マルチテナントで考慮するべきこと](multi-tenant-common-considerations.md)

[マルチテナントでよく利用するソリューション](multi-tenant-common-solutions.md)
