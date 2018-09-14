---
title: Azure Active Directory の管理者ロールの割り当て | Microsoft Docs
description: 管理者ロールは、ユーザーの追加、管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。 管理者ロールが割り当てられているユーザーは、組織がサブスクライブしているすべてのクラウド サービスで同じアクセス許可を持ちます。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 08/31/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 384afb36f2a63fcbf290fa96ed15db2a1f469f55
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337853"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory での管理者ロールの割り当て

Azure Active Directory (Azure AD) を使用すると、各種役割ごとに別々の管理者を指定することができます。 Azure AD ポータルで、ユーザーの追加または変更、管理ロールの割り当て、ユーザーのパスワードのリセット、ユーザーのライセンスの管理、ドメイン名の管理などのタスクを実行する管理者を指定できます。

## <a name="details-about-the-global-administrator-role"></a>全体管理者ロールの詳細

全体管理者は、すべての管理機能にアクセスできます。 既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリの全体管理者ロールが割り当てられます。 他の管理者ロールを割り当てることができるのは全体管理者だけです。

## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除

Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、「[Azure Active Directory でユーザーを管理者ロールに割り当てる](../fundamentals/active-directory-users-assign-role-azure-portal.md)」を参照してください。

## <a name="available-roles"></a>使用可能なロール

次の管理者ロールを使用できます。

* **[アプリケーション管理者](#application-administrator)**: このロールのユーザーは、エンタープライズ アプリケーション、アプリケーション登録、アプリケーション プロキシの設定の全側面を作成して管理できます。 さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意する権限を付与します。 このロールのメンバーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

* **[アプリケーション開発者](#application-developer)**: このロールのユーザーは、[ユーザーはアプリケーションを登録できる] 設定が [いいえ] に設定されている場合に、アプリケーション登録を作成できます。 さらにこのロールでは、[ユーザーはアプリが自身の代わりに会社のデータにアクセスすることを許可できます] 設定が [いいえ] に設定されている場合に、メンバーが自分のために同意できます。 このロールのメンバーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されます。

* **[課金管理者](#billing-administrator)**: 購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。

* **[クラウド アプリケーション管理者](#cloud-application-administrator)**: このロールのユーザーは、(アプリケーション プロキシを管理する権限を除き) アプリケーション管理者ロールと同じアクセス許可を持ちます。 このロールは、エンタープライズ アプリケーションとアプリケーション登録の全側面を作成して管理する権限を付与します。 さらに、このロールは、委任されたアクセス許可とアプリケーション アクセス許可 (Microsoft Graph と Azure AD Graph を除く) に同意する権限を付与します。 このロールのメンバーは、新しいアプリケーション登録またはエンタープライズ アプリケーションを作成する際に、所有者として追加されません。

* **[コンプライアンス管理者](#compliance-administrator)**: このロールが割り当てられたユーザーは、Office 365 セキュリティ/コンプライアンス センターと Exchange 管理センター内で管理アクセス許可を持ちます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **[条件付きアクセス管理者](#conditional-access-administrator)**: このロールが割り当てられたユーザーは、Azure Active Directory の条件付きアクセスの設定を管理できます。
  > [!NOTE]
  > Azure で Exchange ActiveSync の条件付きアクセス ポリシーをデプロイするには、ユーザーは、グローバル管理者である必要もあります。
  
* **[デバイス管理者](#device-administrators)**: この役割は、[デバイス設定](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/)の追加のローカル管理者としてのみ割り当て可能です。 このロールのユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル マシン管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。 

* **[ディレクトリ閲覧者](#directory-readers)**: これは、[同意フレームワーク](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **[ディレクトリ同期アカウント](#directory-synchronization-accounts)**: 使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

* **[ディレクトリ ライター](#directory-writers)**: これは、[同意フレームワーク](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **[Dynamics 365 サービス管理者/CRM サービス管理者](#dynamics-365-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft Dynamics 365 Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Use the service admin role to manage your tenant (サービス管理者ロールを使用したテナントの管理)](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant)」を参照してください。

* **[Exchange サービス管理者](#exchange-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **[全体管理者/会社の管理者](#company-administrator)**: このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory の ID を使用するサービス (Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 Azure Active Directory テナントにサインアップしたユーザーが全体管理者になります。 他の管理者ロールを割り当てることができるのは全体管理者だけです。 会社に複数の全体管理者が存在してかまいません。 すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "会社の管理者" として識別されます。 [Azure Portal](https://portal.azure.com) では、"全体管理者" になります。
  >
  >

* **[ゲスト招待元](#guest-inviter)**: このロールが割り当てられたユーザーは、**[メンバーは招待ができる]** ユーザー設定が [いいえ] に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」をご覧ください。 その他の権限は含まれません。

* **[Information Protection 管理者](#information-protection-administrator)**: このロールが割り当てられたユーザーは、Azure Information Protection サービスのすべてのアクセス許可を持ちます。 このロールでは、Azure Information Protection ポリシーのラベルの構成、保護テンプレートの管理、保護のアクティブ化を行うことができます。 このロールでは、Identity Protection Center、Privileged Identity Management、Office 365 Service Health の監視、および Office 365 のセキュリティ/コンプライアンス センターのアクセス許可は付与されません。

* **[Intune サービス管理者](#intune-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。 詳細については、「[Microsoft Intune でのロール ベースの管理制御 (RBAC)](https://docs.microsoft.com/intune/role-based-access-control)」を参照してください。

* **[ライセンス管理者](#license-administrator)**: このロールのユーザーは、ユーザーに対するライセンス割り当ての追加、削除、更新、グループに対する (グループベースのライセンスを使用した) ライセンス割り当ての追加、削除、更新に加え、ユーザーに対する利用場所の管理を行うことができます。 このロールでは、サブスクリプションの購入と管理、グループの作成と管理を行う権限は与えられません。また、利用場所を超える範囲でのユーザーの作成と管理を行う権限も与えられません。

* **[メッセージ センター閲覧者](#message-center-reader)**: このロールのユーザーは、自分の組織の Exchange、Intune、Microsoft Teams などのサービス構成に対する [Office 365 メッセージ センター](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093)の通知や、正常性に関して注意を促す更新情報を監視できます。 メッセージ センター閲覧者は、投稿の毎週のメール ダイジェストを受け取り、Office 365 でメッセージ センターの投稿を共有できます。 Azure AD では、このロールに割り当てられているユーザーはユーザーやグループなどの読み取り専用アクセスのみを持ちます。 

* **[パートナー レベル 1 のサポート](#partner-tier1-support)**: 使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **[パートナー レベル 2 のサポート](#partner-tier2-support)**: 使用しないでください。 このロールは非推奨となっており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **[パスワード管理者/ヘルプデスク管理者](#helpdesk-administrator)**: このロールが割り当てられたユーザーは、パスワードの変更、サービス要求の管理、サービス正常性の監視を行うことができます。 ヘルプデスク管理者が変更できるのは、ユーザーと他のヘルプデスク管理者のパスワードだけです。 

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、および Azure AD PowerShell では、このロールは "ヘルプデスク管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"パスワード管理者" になります。
  >
  >
  
* **[Power BI サービス管理者](#power-bi-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Power BI 管理者の役割について](https://docs.microsoft.com/power-bi/service-admin-role)」を参照してください。

* **[特権ロール管理者](#privileged-role-administrator)**: このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 また、このロールは Privileged Identity Management の全側面を管理できます。

* **[レポート閲覧者](#reports-reader)**: このロールが割り当てられたユーザーは、Office 365 管理センターで使用状況のレポート データとレポート ダッシュボードを表示できます。また、Power BI で導入コンテキスト パックを表示できます。 さらに、Azure AD のサインオン レポートとアクティビティ、および Microsoft Graph レポート API から返されるデータにもアクセスできます。 レポート閲覧者ロールが割り当てられたユーザーは、関連する使用状況と導入メトリックにのみアクセスできます。 製品固有の管理センター (Exchange など) の設定を構成したり、アクセスしたりする管理者アクセス許可はありません。 

* **[セキュリティ管理者](#security-administrator)**: このロールが割り当てられたユーザーは、セキュリティ閲覧者ロールのすべての読み取り専用アクセス許可を持ち、セキュリティ関連サービス (Azure Active Directory Identity Protection、Azure Information Protection、Privileged Identity Management、Office 365 セキュリティ/コンプライアンス センター) の構成を管理することもできます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。
  
  | どこで | できること |
  | --- | --- |
  | Identity Protection Center |<ul><li>セキュリティ閲覧者ロールのすべての権限を持ちます。<li>さらに、パスワードのリセットを除く IPC のすべての操作を行うことができます。 |
  | Privileged Identity Management |<ul><li>セキュリティ閲覧者ロールのすべての権限を持ちます。<li>Azure AD ロールのメンバーシップまたは設定を管理することは**できません**。 |
  | <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター |<ul><li>セキュリティ閲覧者ロールのすべての権限を持ちます。<li>Advanced Threat Protection 機能 (マルウェアおよびウイルス防止、悪意のある URL の構成、URL 追跡など) のすべての設定を構成することができます。 |
  
* **[セキュリティ閲覧者](#security-reader)**: このロールが割り当てられたユーザーは、Azure Active Directory、Identity Protection、Privileged Identity Management 内のすべての情報を含め、グローバルな読み取り専用アクセス権を持ち、Azure Active Directory のサインイン レポートと監査ログを閲覧できます。 また、このロールには、Office 365 セキュリティ/コンプライアンス センターでの読み取り専用アクセス許可が付与されます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

  | どこで | できること |
  | --- | --- |
  | Identity Protection Center |各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メールフロー ルール |
  | Privileged Identity Management |<p>Azure AD PIM に表示される、Azure AD ロール割り当てに関するポリシーとレポート、セキュリティ レビューのすべての情報に対する読み取り専用アクセス権を持ちます。また、将来的には、Azure AD ロール割り当て以外のシナリオのポリシー データとレポートに対する読み取りアクセス権も付与される予定です。<p>Azure AD PIM へのサインアップおよび Azure AD PIM の変更を行うことは**できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格を持っている場合、PIM のポータルまたは PowerShell からそのロールを有効化することができます。 |
  | <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター</p> |<ul><li>アラートの閲覧と管理<li>セキュリティ ポリシーの閲覧<li>検索調査における脅威インテリジェンス情報、Cloud App Discovery、および検査の閲覧<li>全レポートの閲覧 |

* **[サービス サポート管理者](#service-support-administrator)**: このロールが割り当てられたユーザーは、Azure サービスと Office 365 サービスについて Microsoft へのサポート要求を開くことができます。また、Azure portal と Office 365 管理ポータルで、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **[SharePoint サービス管理者](#sharepoint-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **[Skype for Business/Lync サービス管理者](#lync-service-administrator)**: このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 詳細については、「[Skype for Business 管理者の役割について](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5)」を参照してください。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"Skype for Business サービス管理者" になります。
  >
  >

* **[ユーザー アカウント管理者](#user-account-administrator)**: このロールが割り当てられたユーザーは、ユーザーとグループを作成し、その全側面を管理できます。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 ただし、いくつかの制限が適用されます。 たとえば、このロールはグローバル管理者を削除できません。 ユーザー アカウント管理者は、ユーザー、ヘルプデスク管理者、およびその他のユーザー アカウント管理者についてのみ、パスワードを変更できます。

| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー、ヘルプデスク管理者、およびその他のユーザー アカウント管理者についてのみ、パスワードを変更できます</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理 (制限付き)。 グローバル管理者を削除することも、他の管理者を作成することもできません。</p> |<p>Office 製品の課金および購入操作の実行</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p><p>監査ログの表示</p> |

## <a name="deprecated-roles"></a>非推奨のロール

次のロールは使用しないでください。 これらは非推奨となっており、将来的に Azure AD から削除されます。

* アドホック ライセンス管理者
* デバイスの参加
* デバイス マネージャー
* デバイス ユーザー
* メールで確認済みのユーザー作成者
* メールボックス管理者
* デバイスの社内参加

## <a name="detailed-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可の詳細
以降の表は、各ロールに割り当てられている Azure Active Directory の具体的なアクセス許可の説明です。 全体管理者などの一部のロールは、Azure Active Directory 以外の Microsoft サービスに対する追加のアクセス許可を持つことができます。

### <a name="adhoc-license-administrator"></a>アドホック ライセンス管理者
アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/domains/default/read | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/groups/default/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/memberOf/read | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/read | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.aad.directory/groups/owners/read | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.aad.directory/groups/settings/read | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの読み取り。 |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Azure Active Directory での oAuth2PermissionGrants の更新。 |
| microsoft.aad.directory/organization/default/read | Azure Active Directory での organization の基本プロパティの読み取り。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/default/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory での users.invitedBy プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory での users.invitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="application-administrator"></a>アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.aad.directory/applications/default/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.aad.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/read | Azure Active Directory での appRoleAssignments の読み取り。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/default/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.reports/allEntities/read | Azure AD レポートの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="application-developer"></a>アプリケーション開発者
[ユーザーはアプリケーションを登録できる] の設定とは無関係にアプリケーション登録を作成できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Azure Active Directory での applications の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Azure Active Directory での appRoleAssignments の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Azure Active Directory での oAuth2PermissionGrants の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Azure Active Directory での servicePrincipals の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |

### <a name="billing-administrator"></a>課金管理者
支払情報の更新など、よく利用する課金関連タスクを実行できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/organization/default/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 課金の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="cloud-application-administrator"></a>クラウド アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面 (アプリ プロキシを除く) を作成して管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/create | Azure Active Directory での applications の作成。 |
| microsoft.aad.directory/applications/default/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.aad.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/applicationConfiguration/default/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/default/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Azure Active Directory での policies.applicationConfiguration プロパティの更新。 |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Azure Active Directory での policies.applicationConfiguration プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/default/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.reports/allEntities/read | Azure AD レポートの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="company-administrator"></a>会社の管理者
Azure AD のすべての側面と、Azure AD の ID が使用される Microsoft サービスを管理できます。

  > [!NOTE]
  > このロールは、ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Azure Active Directory での administrativeUnits の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/applications/allProperties/allTasks | Azure Active Directory での applications の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Azure Active Directory での appRoleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/contacts/allProperties/allTasks | Azure Active Directory での contacts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/contracts/allProperties/allTasks | Azure Active Directory での contracts の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/devices/allProperties/allTasks | Azure Active Directory での devices の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Azure Active Directory での directoryRoles の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Azure Active Directory での directoryRoleTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/domains/allProperties/allTasks | Azure Active Directory での domains の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groups/allProperties/allTasks | Azure Active Directory での groups の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Azure Active Directory での groupSettings の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Azure Active Directory での groupSettingTemplates の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Azure Active Directory での loginTenantBranding の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Azure Active Directory での oAuth2PermissionGrants の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/organization/allProperties/allTasks | Azure Active Directory での organization の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/policies/allProperties/allTasks | Azure Active Directory での policies の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Azure Active Directory での roleAssignments の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Azure Active Directory での roleDefinitions の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Azure Active Directory での scopedRoleMemberships の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/serviceAction/activateService | Azure Active Directory で Activateservice サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Azure Active Directory で Disabledirectoryfeature サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Azure Active Directory で Enabledirectoryfeature サービス アクションを実行可能 |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Azure Active Directory で Getavailableextentionproperties サービス アクションを実行可能 |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Azure Active Directory での servicePrincipals の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Azure Active Directory での subscribedSkus の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/users/allProperties/allTasks | Azure Active Directory での users の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |
| microsoft.aad.identityProtection/allEntities/allTasks | microsoft.aad.identityProtection でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.aad.reports/allEntities/allTasks | Azure AD レポートの読み取りと構成。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.commerce.billing/allEntities/allTasks | Office 365 課金の全側面の管理。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.office365.protectionCenter/allEntities/allTasks | Office 365 プロテクション センターの全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |

### <a name="compliance-administrator"></a>コンプライアンス管理者
Azure AD および Office 365 のコンプライアンスの構成とレポートを読み取り、管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.complianceManager/allEntities/allTasks | Office 365 コンプライアンス マネージャーの全側面の管理 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="conditional-access-administrator"></a>条件付きアクセス管理者
条件付きアクセスの機能を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/conditionalAccess/default/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.aad.directory/policies/conditionalAccess/default/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.aad.directory/policies/conditionalAccess/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Azure Active Directory での policies.conditionalAccess プロパティの更新。 |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Azure Active Directory での policies.conditionalAccess プロパティの読み取り。 |

### <a name="crm-service-administrator"></a>CRM サービス管理者
Dynamics 365 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Dynamics 365 の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="customer-lockbox-access-approver"></a>カスタマー ロックボックスのアクセス承認者
Microsoft サポートがお客様の組織データにアクセスする要求を承認することができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.office365.lockbox/allEntities/allTasks | Office 365 カスタマ― ロックボックスの全側面の管理 |

### <a name="device-administrators"></a>デバイス管理者
このロールのメンバーは、Azure AD 参加済みデバイスのローカル管理者グループに追加されます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groupSettings/default/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.aad.directory/groupSettingTemplates/default/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |

### <a name="device-managers"></a>デバイス マネージャー
Microsoft サポートがお客様の組織データにアクセスする要求を承認することができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/devices/default/read | Azure Active Directory での devices の基本プロパティの読み取り。 |
| microsoft.aad.directory/devices/default/update | Azure Active Directory での devices の基本プロパティの更新。 |
| microsoft.aad.directory/devices/memberOf/read | Azure Active Directory での devices.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredOwners/read | Azure Active Directory での devices.registeredOwners プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredOwners/update | Azure Active Directory での devices.registeredOwners プロパティの更新。 |
| microsoft.aad.directory/devices/registeredUsers/read | Azure Active Directory での devices.registeredUsers プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredUsers/update | Azure Active Directory での devices.registeredUsers プロパティの更新。 |

### <a name="directory-readers"></a>ディレクトリ リーダー
基本的なディレクトリ情報を読み取ることができます  (アプリケーションへのアクセス権を付与するため)。

  > [!NOTE]
  > このロールは、ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/default/read | Azure Active Directory での administrativeUnits の基本プロパティの読み取り。 |
| microsoft.aad.directory/administrativeUnits/members/read | Azure Active Directory での administrativeUnits.members プロパティの読み取り。 |
| microsoft.aad.directory/applications/default/read | Azure Active Directory での applications の基本プロパティの読み取り。 |
| microsoft.aad.directory/applications/owners/read | Azure Active Directory での applications.owners プロパティの読み取り。 |
| microsoft.aad.directory/contacts/default/read | Azure Active Directory での contacts の基本プロパティの読み取り。 |
| microsoft.aad.directory/contacts/memberOf/read | Azure Active Directory での contacts.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/contracts/default/read | Azure Active Directory での contracts の基本プロパティの読み取り。 |
| microsoft.aad.directory/devices/default/read | Azure Active Directory での devices の基本プロパティの読み取り。 |
| microsoft.aad.directory/devices/memberOf/read | Azure Active Directory での devices.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredOwners/read | Azure Active Directory での devices.registeredOwners プロパティの読み取り。 |
| microsoft.aad.directory/devices/registeredUsers/read | Azure Active Directory での devices.registeredUsers プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/default/read | Azure Active Directory での directoryRoles の基本プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Azure Active Directory での directoryRoles.eligibleMembers プロパティの読み取り。 |
| microsoft.aad.directory/directoryRoles/members/read | Azure Active Directory での directoryRoles.members プロパティの読み取り。 |
| microsoft.aad.directory/domains/default/read | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/groups/default/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/memberOf/read | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/read | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.aad.directory/groups/owners/read | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.aad.directory/groups/settings/read | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.aad.directory/groupSettings/default/read | Azure Active Directory での groupSettings の基本プロパティの読み取り。 |
| microsoft.aad.directory/groupSettingTemplates/default/read | Azure Active Directory での groupSettingTemplates の基本プロパティの読み取り。 |
| microsoft.aad.directory/oAuth2PermissionGrants/default/read | Azure Active Directory での oAuth2PermissionGrants の基本プロパティの読み取り。 |
| microsoft.aad.directory/organization/default/read | Azure Active Directory での organization の基本プロパティの読み取り。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/default/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.aad.directory/subscribedSkus/default/read | Azure Active Directory での subscribedSkus の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/default/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory での users.invitedBy プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory での users.invitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="directory-synchronization-accounts"></a>ディレクトリ同期アカウント
Azure AD Connect サービスでのみ使用されます。

  > [!NOTE]
  > このロールは、ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Azure Active Directory での organization.dirSync プロパティの更新。 |
| microsoft.aad.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/default/read | Azure Active Directory での policies の基本プロパティの読み取り。 |
| microsoft.aad.directory/policies/default/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.aad.directory/policies/owners/read | Azure Active Directory での policies.owners プロパティの読み取り。 |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Azure Active Directory での policies.policiesAppliedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/create | Azure Active Directory での servicePrincipals の作成。 |
| microsoft.aad.directory/servicePrincipals/default/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/default/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directorySync/allEntities/allTasks | Azure AD Connect でのすべてのアクションの実行。 |

### <a name="directory-writers"></a>ディレクトリ ライター
基本的なディレクトリ情報の読み取りと書き込みを実行できます  (アプリケーションへのアクセス権を付与するため)。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/default/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/groupSettings/create | Azure Active Directory での groupSettings の作成。 |
| microsoft.aad.directory/groupSettings/default/update | Azure Active Directory での groupSettings の基本プロパティの更新。 |
| microsoft.aad.directory/groupSettings/delete | Azure Active Directory での groupSettings の削除。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/default/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |

### <a name="exchange-service-administrator"></a>Exchange サービス管理者
Exchange 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.exchange/allEntities/allTasks | Exchange Online の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="guest"></a>ゲスト
ゲスト ユーザーの既定のロール。 制限された一部のディレクトリ情報を読み取ることができます。

  > [!NOTE]
  > このロールは、ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/default/read | Azure Active Directory での applications の基本プロパティの読み取り。 |
| microsoft.aad.directory/applications/owners/read | Azure Active Directory での applications.owners プロパティの読み取り。 |
| microsoft.aad.directory/domains/default/read | Azure Active Directory での domains の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/appRoleAssignments/read | Azure Active Directory での groups.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/groups/default/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.aad.directory/groups/memberOf/read | Azure Active Directory での groups.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/read | Azure Active Directory での groups.members プロパティの読み取り。 |
| microsoft.aad.directory/groups/owners/read | Azure Active Directory での groups.owners プロパティの読み取り。 |
| microsoft.aad.directory/groups/settings/read | Azure Active Directory での groups.settings プロパティの読み取り。 |
| microsoft.aad.directory/organization/basicProfile/read | Azure Active Directory での基本的な組織プロファイル情報の読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/default/read | Azure Active Directory での servicePrincipals の基本プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Azure Active Directory での servicePrincipals.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/members/read | Azure Active Directory での servicePrincipals.members プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/default/read | Azure Active Directory での servicePrincipals.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/owners/read | Azure Active Directory での servicePrincipals.owners プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Azure Active Directory での servicePrincipals.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/servicePrincipals/policies/read | Azure Active Directory での servicePrincipals.policies プロパティの読み取り。 |
| microsoft.aad.directory/users/basicProfile/read | Azure Active Directory での users.basicProfile プロパティの読み取り。 |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/default/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/eligibleMemberOf/read | Azure Active Directory での users.eligibleMemberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory での users.invitedBy プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory での users.invitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/pendingMemberOf/read | Azure Active Directory での users.pendingMemberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/scopedAdministratorOf/read | Azure Active Directory での users.scopedAdministratorOf プロパティの読み取り。 |

### <a name="guest-inviter"></a>ゲスト招待元
[メンバーがゲストを招待できる] の設定とは無関係にゲスト ユーザーを招待できます。

  > [!NOTE]
  > このロールは、ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Azure Active Directory での users.appRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/users/default/read | Azure Active Directory での users の基本プロパティの読み取り。 |
| microsoft.aad.directory/users/directReports/read | Azure Active Directory での users.directReports プロパティの読み取り。 |
| microsoft.aad.directory/users/invitedBy/read | Azure Active Directory での users.invitedBy プロパティの読み取り。 |
| microsoft.aad.directory/users/inviteGuest | Azure Active Directory でのゲスト ユーザーの招待。 |
| microsoft.aad.directory/users/invitedUsers/read | Azure Active Directory での users.invitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/users/manager/read | Azure Active Directory での users.manager プロパティの読み取り。 |
| microsoft.aad.directory/users/memberOf/read | Azure Active Directory での users.memberOf プロパティの読み取り。 |
| microsoft.aad.directory/users/oAuth2PermissionGrants/default/read | Azure Active Directory での users.oAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedDevices/read | Azure Active Directory での users.ownedDevices プロパティの読み取り。 |
| microsoft.aad.directory/users/ownedObjects/read | Azure Active Directory での users.ownedObjects プロパティの読み取り。 |
| microsoft.aad.directory/users/registeredDevices/read | Azure Active Directory での users.registeredDevices プロパティの読み取り。 |

### <a name="helpdesk-administrator"></a>ヘルプデスク管理者
管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="information-protection-administrator"></a>Information Protection 管理者
Azure Information Protection 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Azure Information Protection の全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="intune-service-administrator"></a>Intune サービス管理者
Intune 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/default/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/devices/create | Azure Active Directory での devices の作成。 |
| microsoft.aad.directory/devices/default/update | Azure Active Directory での devices の基本プロパティの更新。 |
| microsoft.aad.directory/devices/delete | Azure Active Directory での devices の削除。 |
| microsoft.aad.directory/devices/registeredOwners/update | Azure Active Directory での devices.registeredOwners プロパティの更新。 |
| microsoft.aad.directory/devices/registeredUsers/update | Azure Active Directory での devices.registeredUsers プロパティの更新。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/default/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/default/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.intune/allEntities/allTasks | Intune の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="license-administrator"></a>ライセンス管理者
ユーザーおよびグループの製品ライセンスを管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/usageLocation/update | Azure Active Directory での users.usageLocation プロパティの更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="lync-service-administrator"></a>Lync サービス管理者
Skype for Business 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Skype for Business Online の全側面の管理。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="message-center-reader"></a>メッセージ センター閲覧者
Office 365 メッセージ センター内でのみ自分の組織のメッセージと更新情報を閲覧することができます。 

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessmessagecenter/allEntities/allTasks | メッセージ センターでのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |

### <a name="partner-tier1-support"></a>パートナー レベル 1 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/default/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/default/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="partner-tier2-support"></a>パートナー レベル 2 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/default/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/domains/allTasks | Azure Active Directory での domains の作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/organization/default/update | Azure Active Directory での organization の基本プロパティの更新。 |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Azure Active Directory での organization.trustedCAsForPasswordlessAuth プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/default/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="power-bi-service-administrator"></a>Power BI サービス管理者
Power BI 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.powerApps.powerBI/allEntities/allTasks | Power BI の全側面の管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="privileged-role-administrator"></a>特権ロール管理者
Azure AD でロールの割り当てを管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Azure Active Directory での directoryRoles の更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |

### <a name="reports-reader"></a>レポート閲覧者
サインインと監査のレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.reports/allEntities/read | Azure AD レポートの読み取り。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.usageReports/allEntities/read | Office 365 の使用状況レポートの読み取り。 |

### <a name="security-administrator"></a>セキュリティ管理者
セキュリティ情報とレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/policies/create | Azure Active Directory での policies の作成。 |
| microsoft.aad.directory/policies/default/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.aad.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.identityProtection/allEntities/update | microsoft.aad.identityProtection でのすべてのリソースの更新。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.protectionCenter/allEntities/update | microsoft.office365.protectionCenter でのすべてのリソースの更新。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="security-reader"></a>セキュリティ閲覧者
Azure AD と Office 365 のセキュリティ情報とレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.identityProtection/allEntities/read | microsoft.aad.identityProtection でのすべてのリソースの読み取り。 |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | microsoft.aad.privilegedIdentityManagement でのすべてのリソースの読み取り。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.office365.protectionCenter/allEntities/read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |

### <a name="service-support-administrator"></a>サービス サポート管理者
サービス正常性に関する情報を読み取り、サポート チケットを管理することができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="sharepoint-service-administrator"></a>SharePoint サービス管理者
SharePoint サービスのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を備えています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.sharepoint/allEntities/allTasks | microsoft.office365.sharepoint でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="user-account-administrator"></a>ユーザー アカウント管理者
ユーザーとグループのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Azure Active Directory での appRoleAssignments の作成。 |
| microsoft.aad.directory/appRoleAssignments/delete | Azure Active Directory での appRoleAssignments の削除。 |
| microsoft.aad.directory/appRoleAssignments/update | Azure Active Directory での appRoleAssignments の更新。 |
| microsoft.aad.directory/contacts/create | Azure Active Directory での contacts の作成。 |
| microsoft.aad.directory/contacts/default/update | Azure Active Directory での contacts の基本プロパティの更新。 |
| microsoft.aad.directory/contacts/delete | Azure Active Directory での contacts の削除。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/create | Azure Active Directory での groups の作成。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/default/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/hiddenMembers/read | Azure Active Directory での groups.hiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/users/appRoleAssignments/update | Azure Active Directory での users.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/users/assignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/users/create | Azure Active Directory での users の作成。 |
| microsoft.aad.directory/users/default/update | Azure Active Directory での users の基本プロパティの更新。 |
| microsoft.aad.directory/users/delete | Azure Active Directory での users の削除。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/manager/update | Azure Active Directory での users.manager プロパティの更新。 |
| microsoft.aad.directory/users/password/update | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/users/userPrincipalName/update | Azure Active Directory での users.userPrincipalName プロパティの更新。 |
| microsoft.azure.accessService/allEntities/allTasks | Azure Access サービスの全側面の管理。 |
| microsoft.azure.serviceHealth/allEntities/allTasks | Azure Service Health の読み取りと構成。 |
| microsoft.azure.supportTickets/allEntities/allTasks | Azure サポート チケットの作成と管理。 |
| microsoft.office365.serviceHealth/allEntities/allTasks | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.supportTickets/allEntities/allTasks | Office 365 サポート チケットの作成と管理。 |

### <a name="user"></a>User
メンバー ユーザーの既定のロール。 すべてのディレクトリ情報の読み取りと、制限された一部のディレクトリ情報の書き込みが行えます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Azure Active Directory での applications の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/createAsOwner | Azure Active Directory での groups の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/groups/default/read | Azure Active Directory での groups の基本プロパティの読み取り。 |
| microsoft.aad.directory/oAuth2PermissionGrants/create | Azure Active Directory での oAuth2PermissionGrants の作成。 |
| microsoft.aad.directory/oAuth2PermissionGrants/delete | Azure Active Directory での oAuth2PermissionGrants の削除。 |
| microsoft.aad.directory/oAuth2PermissionGrants/update | Azure Active Directory での oAuth2PermissionGrants の更新。 |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Azure Active Directory での servicePrincipals の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/users/activateServicePlan | Azure Active Directory の Activateserviceplan ユーザー。 |
| microsoft.aad.directory/users/inviteGuest | Azure Active Directory でのゲスト ユーザーの招待。 |
| microsoft.aad.directory/applications/delete | Azure Active Directory での applications の削除。 |
| microsoft.aad.directory/applications/restore | Azure Active Directory での applications の復元。 |
| microsoft.aad.directory/applications/default/update | Azure Active Directory での applications の基本プロパティの更新。 |
| microsoft.aad.directory/applications/owners/update | Azure Active Directory での applications.owners プロパティの更新。 |
| microsoft.aad.directory/applications/policies/update | Azure Active Directory での applications.policies プロパティの更新。 |
| microsoft.aad.directory/devices/disable | Azure Active Directory での devices の無効化。 |
| microsoft.aad.directory/groups/appRoleAssignments/update | Azure Active Directory での groups.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/groups/delete | Azure Active Directory での groups の削除。 |
| microsoft.aad.directory/groups/default/update | Azure Active Directory での groups の基本プロパティの更新。 |
| microsoft.aad.directory/groups/dynamicMembershipRule/update | Azure Active Directory での groups.dynamicMembershipRule プロパティの更新。 |
| microsoft.aad.directory/groups/members/update | Azure Active Directory での groups.members プロパティの更新。 |
| microsoft.aad.directory/groups/owners/update | Azure Active Directory での groups.owners プロパティの更新。 |
| microsoft.aad.directory/groups/restore | Azure Active Directory での groups の復元。 |
| microsoft.aad.directory/groups/settings/update | Azure Active Directory での groups.settings プロパティの更新。 |
| microsoft.aad.directory/policies/delete | Azure Active Directory での policies の削除。 |
| microsoft.aad.directory/policies/default/update | Azure Active Directory での policies の基本プロパティの更新。 |
| microsoft.aad.directory/policies/owners/update | Azure Active Directory での policies.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Azure Active Directory での servicePrincipals.appRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Azure Active Directory での servicePrincipals.appRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/default/update | Azure Active Directory での servicePrincipals の基本プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/delete | Azure Active Directory での servicePrincipals の削除。 |
| microsoft.aad.directory/servicePrincipals/owners/update | Azure Active Directory での servicePrincipals.owners プロパティの更新。 |
| microsoft.aad.directory/servicePrincipals/policies/update | Azure Active Directory での servicePrincipals.policies プロパティの更新。 |
| microsoft.aad.directory/users/changePassword | Azure Active Directory でのすべてのユーザーのパスワードの変更。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/users/basicProfile/update | Azure Active Directory での users.basicProfile プロパティの更新。 |
| microsoft.aad.directory/users/mobile/update | Azure Active Directory での users.mobile プロパティの更新。 |
| microsoft.aad.directory/users/searchableDeviceKey/update | Azure Active Directory での users.searchableDeviceKey プロパティの更新。 |

## <a name="next-steps"></a>次の手順

* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../../billing/billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
