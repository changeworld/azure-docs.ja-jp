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
ms.date: 08/21/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 6c97d7c2f901110421f9fc5d0a1d4468d832c472
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2018
ms.locfileid: "42146911"
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


### <a name="to-add-a-colleague-as-a-global-administrator"></a>全体管理者として同僚を追加するには

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、テナント ディレクトリの全体管理者または特権ロール管理者のアカウントでサインインします。

   ![Azure AD 管理センターを開く](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. **[ユーザー]** を選択します。

3. 全体管理者として指定するユーザーを見つけ、そのユーザーのブレードを開きます。

4. ユーザーのブレードで、**[ディレクトリ ロール]** を選択します。
 
5. [ディレクトリ ロール] ブレードで **[全体管理者]** ロールを選択し、保存します。

## <a name="detailed-azure-active-directory-permissions"></a>Azure Active Directory のアクセス許可の詳細
以降の表は、各ロールに割り当てられている Azure Active Directory の具体的なアクセス許可の説明です。 全体管理者などの一部のロールは、Azure Active Directory 以外の Microsoft サービスに対する追加のアクセス許可を持つことができます。


### <a name="application-administrator"></a>アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面を作成して管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Azure Active Directory での Application の作成。 |
| microsoft.aad.directory/Application/Delete | Azure Active Directory での Application の削除。 |
| microsoft.aad.directory/Application/Update | Azure Active Directory での Application の標準プロパティの更新。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory での Applications.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/Application/Update/Owners | Azure Active Directory での Applications.Owners プロパティの更新。 |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory での AppRoleAssignment の作成。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory での AppRoleAssignment の削除。 |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory での AppRoleAssignment の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Create | Azure Active Directory での Policy の作成。 |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory での Policy の削除。 |
| microsoft.aad.directory/Policy/Update | Azure Active Directory での Policy の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory での Policies.Owners プロパティの更新。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Azure Active Directory (Azure AD Graph と Microsoft Graph) 以外のすべてのリソースに対してすべてのユーザーの代理として同意できる。 |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory での ServicePrincipal の作成。 |
| microsoft.aad.directory/ServicePrincipal/Delete | Azure Active Directory での ServicePrincipal の削除。 |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory での ServicePrincipal の標準プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory での ServicePrincipals.AppRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory での ServicePrincipals.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory での ServicePrincipals.Owners プロパティの更新。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.reports/AllEntities/Read | Azure AD レポートの読み取り。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="application-developer"></a>アプリケーション開発者
**[ユーザーはアプリケーションを登録できる]** の設定とは無関係にアプリケーション登録を作成できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Azure Active Directory での Application の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Azure Active Directory での AppRoleAssignment の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Azure Active Directory での OAuth2PermissionGrant の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Azure Active Directory での ServicePrincipal の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |

### <a name="billing-administrator"></a>課金管理者
支払情報の更新など、よく利用する課金関連タスクを実行できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/Organization/Update | Azure Active Directory での Organization の標準プロパティの更新。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの更新。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.billing/AllEntities/AllActions | Office 365 課金の全側面の管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="cloud-application-administrator"></a>クラウド アプリケーション管理者
アプリ登録とエンタープライズ アプリのすべての側面 (アプリ プロキシを除く) を作成して管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Azure Active Directory での Application の作成。 |
| microsoft.aad.directory/Application/Delete | Azure Active Directory での Application の削除。 |
| microsoft.aad.directory/Application/Update | Azure Active Directory での Application の標準プロパティの更新。 |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory での Applications.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/Application/Update/Owners | Azure Active Directory での Applications.Owners プロパティの更新。 |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory での AppRoleAssignment の作成。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory での AppRoleAssignment の削除。 |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory での AppRoleAssignment の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Create | Azure Active Directory での Policy の作成。 |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory での Policy の削除。 |
| microsoft.aad.directory/Policy/Update | Azure Active Directory での Policy の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory での Policies.Owners プロパティの更新。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Azure Active Directory (Azure AD Graph と Microsoft Graph) 以外のすべてのリソースに対してすべてのユーザーの代理として同意できる。 |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory での ServicePrincipal の作成。 |
| microsoft.aad.directory/ServicePrincipal/Delete | Azure Active Directory での ServicePrincipal の削除。 |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory での ServicePrincipal の標準プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory での ServicePrincipals.AppRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory での ServicePrincipals.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory での ServicePrincipals.Owners プロパティの更新。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.reports/AllEntities/Read | Azure AD レポートの読み取り。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="company-administrator"></a>会社の管理者
Azure AD のすべての側面と、Azure AD の ID が使用される Microsoft サービスを管理できます。 Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "会社の管理者" として識別されます。 [Azure Portal](https://portal.azure.com) では、"全体管理者" になります。

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Azure Active Directory での AdministrativeUnit の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Application/AllActions/AllProperties | Azure Active Directory での Application の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Azure Active Directory での AppRoleAssignment の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Azure Active Directory での CollaborationSpace の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Azure Active Directory での Contact の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Device/AllActions/AllProperties | Azure Active Directory での Device の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Azure Active Directory での DirectoryRole の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Azure Active Directory での DirectoryRoleTemplate の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Azure Active Directory での DirectorySetting の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Azure Active Directory での DirectorySettingTemplate の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Azure Active Directory での Domain の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Group/AllActions/AllProperties | Azure Active Directory での Group の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Azure Active Directory での LoginTenantBranding の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Azure Active Directory での OAuth2PermissionGrant の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Azure Active Directory での Policy の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Azure Active Directory (Azure AD Graph と Microsoft Graph) を含むすべてのリソースに対してすべてのユーザーの代理として同意できる。 |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Azure Active Directory での ServicePrincipal の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Azure Active Directory での Organization の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.directory/User/AllActions/AllProperties | Azure Active Directory での User の作成と削除、およびすべてのプロパティの読み取りと更新。 |
| microsoft.aad.aadconnect/AllEntities/AllActions | microsoft.aad.aadconnect でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.billing/AllEntities/AllActions | Office 365 課金の全側面の管理。 |
| microsoft.aad.compliance/AllEntities/AllActions | コンプライアンス センターでのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directorysync/AllEntities/AllActions | Azure AD Connect でのすべてのアクションの実行。 |
| microsoft.aad.lockbox/AllEntities/AllActions | Lockbox サービスの全側面の管理。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Privileged Role Management サービスの全側面の管理。 |
| microsoft.aad.reports/AllEntities/AllActions | Azure AD レポートの読み取りと構成。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.crm/AllEntities/AllActions | Dynamics 365 の全側面の管理。 |
| microsoft.exchange/AllEntities/AllActions | Exchange Online の全側面の管理。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | Information Protection の全側面の管理。 |
| microsoft.intune/AllEntities/AllActions | Intune の全側面の管理。 |
| microsoft.powerbi/AllEntities/AllActions | Power BI の全側面の管理。 |
| microsoft.protectioncenter/AllEntities/AllActions | Office 365 プロテクション センターの管理。 |
| microsoft.sharepoint/AllEntities/AllActions | SharePoint Online の管理。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | Skype for Business Online の管理。 |

### <a name="compliance-administrator"></a>コンプライアンス管理者
Azure AD および Office 365 のコンプライアンスの構成とレポートを読み取り、管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.compliance/AllEntities/AllActions | コンプライアンス センターでのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.exchange/Compliance/AllActions | Exchange Online でのコンプライアンスの管理。 |
| microsoft.sharepoint/Compliance/AllActions | SharePoint Online でのコンプライアンスの管理。 |
| microsoft.skypeforbusiness/Compliance/AllActions | Skype for Business Online でのコンプライアンスの管理。 |

### <a name="conditional-access-administrator"></a>条件付きアクセス管理者
条件付きアクセスの機能を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Azure Active Directory での ConditionalAccessPolicy の作成。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Azure Active Directory での ConditionalAccessPolicy の削除。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Azure Active Directory での ConditionalAccessPolicy の標準プロパティの読み取り。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Azure Active Directory での ConditionalAccessPolicys.Owners プロパティの読み取り。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Azure Active Directory での ConditionalAccessPolicys.PolicyAppliedTo プロパティの読み取り。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Azure Active Directory での ConditionalAccessPolicy の標準プロパティの更新。 |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Azure Active Directory での ConditionalAccessPolicys.Owners プロパティの更新。 |

### <a name="device-administrators"></a>デバイス管理者

このロールのユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル マシン管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

### <a name="directory-readers"></a>ディレクトリ リーダー
基本的なディレクトリ情報を読み取ることができます  (アプリケーションへのアクセス権を付与するため)。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Azure Active Directory での AdministrativeUnit の標準プロパティの読み取り。 |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Azure Active Directory での AdministrativeUnits.Members プロパティの読み取り。 |
| microsoft.aad.directory/Application/Read | Azure Active Directory での Application の標準プロパティの読み取り。 |
| microsoft.aad.directory/Application/Read/Owners | Azure Active Directory での Applications.Owners プロパティの読み取り。 |
| microsoft.aad.directory/CollaborationSpace/Read | Azure Active Directory での CollaborationSpace の標準プロパティの読み取り。 |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Azure Active Directory での CollaborationSpaces.Owners プロパティの読み取り。 |
| microsoft.aad.directory/Contact/Read | Azure Active Directory での Contact の標準プロパティの読み取り。 |
| microsoft.aad.directory/Contact/Read/MemberOf | Azure Active Directory での Contacts.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/Device/Read | Azure Active Directory での Device の標準プロパティの読み取り。 |
| microsoft.aad.directory/Device/Read/MemberOf | Azure Active Directory での Devices.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Azure Active Directory での Devices.RegisteredOwners プロパティの読み取り。 |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Azure Active Directory での Devices.RegisteredUsers プロパティの読み取り。 |
| microsoft.aad.directory/DirectoryRole/Read | Azure Active Directory での DirectoryRole の標準プロパティの読み取り。 |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Azure Active Directory での DirectoryRoles.EligibleMembers プロパティの読み取り。 |
| microsoft.aad.directory/DirectoryRole/Read/Members | Azure Active Directory での DirectoryRoles.Members プロパティの読み取り。 |
| microsoft.aad.directory/DirectorySetting/Read | Azure Active Directory での DirectorySetting の標準プロパティの読み取り。 |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Azure Active Directory での DirectorySettingTemplate の標準プロパティの読み取り。 |
| microsoft.aad.directory/Domain/Read | Azure Active Directory での Domain の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Azure Active Directory での Groups.AppRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/MemberOf | Azure Active Directory での Groups.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/Members | Azure Active Directory での Groups.Members プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/Owners | Azure Active Directory での Groups.Owners プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/Settings | Azure Active Directory での Groups.Settings プロパティの読み取り。 |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Azure Active Directory での OAuth2PermissionGrant の標準プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read | Azure Active Directory での ServicePrincipal の標準プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Azure Active Directory での ServicePrincipals.AppRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Azure Active Directory での ServicePrincipals.AppRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Azure Active Directory での ServicePrincipals.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Azure Active Directory での ServicePrincipals.OAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Azure Active Directory での ServicePrincipals.Owners プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Azure Active Directory での ServicePrincipals.OwnedObjects プロパティの読み取り。 |
| microsoft.aad.directory/Organization/Read | Azure Active Directory での Organization の標準プロパティの読み取り。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/Read | Azure Active Directory での User の標準プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/DirectReports | Azure Active Directory での Users.DirectReports プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/InvitedBy | Azure Active Directory での Users.InvitedBy プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/InvitedUsers | Azure Active Directory での Users.InvitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/Manager | Azure Active Directory での Users.Manager プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/MemberOf | Azure Active Directory での Users.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Azure Active Directory での Users.OAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OwnedDevices | Azure Active Directory での Users.OwnedDevices プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OwnedObjects | Azure Active Directory での Users.OwnedObjects プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | Azure Active Directory での Users.RegisteredDevices プロパティの読み取り。 |

### <a name="directory-synchronization-accounts"></a>ディレクトリ同期アカウント
Azure AD Connect サービスでのみ使用されます。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Azure Active Directory での Policy の作成。 |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory での Policy の削除。 |
| microsoft.aad.directory/Policy/Read | Azure Active Directory での Policy の標準プロパティの読み取り。 |
| microsoft.aad.directory/Policy/Read/Owners | Azure Active Directory での Policies.Owners プロパティの読み取り。 |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Azure Active Directory での Policies.PolicyAppliedTo プロパティの読み取り。 |
| microsoft.aad.directory/Policy/Update | Azure Active Directory での Policy の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory での Policies.Owners プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Create | Azure Active Directory での ServicePrincipal の作成。 |
| microsoft.aad.directory/ServicePrincipal/Read | Azure Active Directory での ServicePrincipal の標準プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Azure Active Directory での ServicePrincipals.AppRoleAssignedTo プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Azure Active Directory での ServicePrincipals.AppRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Azure Active Directory での ServicePrincipals.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Azure Active Directory での ServicePrincipals.OAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Azure Active Directory での ServicePrincipals.Owners プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Azure Active Directory での ServicePrincipals.OwnedObjects プロパティの読み取り。 |
| microsoft.aad.directory/ServicePrincipal/Update | Azure Active Directory での ServicePrincipal の標準プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Azure Active Directory での ServicePrincipals.AppRoleAssignedTo プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Azure Active Directory での ServicePrincipals.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Azure Active Directory での ServicePrincipals.Owners プロパティの更新。 |
| microsoft.aad.directory/Organization/Update/DirSync | Azure Active Directory での Organizations.DirSync プロパティの更新。 |
| microsoft.aad.directorysync/AllEntities/AllActions | Azure AD Connect でのすべてのアクションの実行。 |

### <a name="directory-writer"></a>ディレクトリ ライター
基本的なディレクトリ情報の読み取りと書き込みを実行できます  (アプリケーションへのアクセス権を付与するため)。

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Azure Active Directory での DirectorySetting の作成。 |
| microsoft.aad.directory/DirectorySetting/Delete | Azure Active Directory での DirectorySetting の削除。 |
| microsoft.aad.directory/DirectorySetting/Update | Azure Active Directory での DirectorySetting の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Create | Azure Active Directory での Group の作成。 |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory での Group の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Update | Azure Active Directory での Group の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory での Groups.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory での Groups.Members プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory での Groups.Owners プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory での Groups.Settings プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory でのユーザーのライセンスの管理。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/User/Update | Azure Active Directory での User の標準プロパティの更新。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory での Users.Manager プロパティの更新。 |

### <a name="dynamics-365-service-administrator"></a>Dynamics 365 サービス管理者
Dynamics 365 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.crm/AllEntities/AllActions | Dynamics 365 の全側面の管理。 |

### <a name="exchange-service-administrator"></a>Exchange サービス管理者
Exchange 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.exchange/AllEntities/AllActions | Exchange Online の全側面の管理。 |

### <a name="guest-inviter"></a>ゲスト招待元
**[メンバーがゲストを招待できる]** の設定とは無関係にゲスト ユーザーを招待できます。

  > [!NOTE]
  > このロールは、ゲスト ロールから追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Azure Active Directory でのゲスト ユーザーの招待。 |
| microsoft.aad.directory/User/Read | Azure Active Directory での User の標準プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/DirectReports | Azure Active Directory での Users.DirectReports プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/InvitedBy | Azure Active Directory での Users.InvitedBy プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/InvitedUsers | Azure Active Directory での Users.InvitedUsers プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/Manager | Azure Active Directory での Users.Manager プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/MemberOf | Azure Active Directory での Users.MemberOf プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Azure Active Directory での Users.OAuth2PermissionGrants プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OwnedDevices | Azure Active Directory での Users.OwnedDevices プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/OwnedObjects | Azure Active Directory での Users.OwnedObjects プロパティの読み取り。 |
| microsoft.aad.directory/User/Read/RegisteredDevices | Azure Active Directory での Users.RegisteredDevices プロパティの読み取り。 |

### <a name="helpdesk-administrator"></a>ヘルプデスク管理者
管理者以外のユーザーとヘルプデスク管理者のパスワードをリセットできます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Azure Active Directory での制限付き管理者と他のヘルプデスク管理者のパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |

### <a name="information-protection-administrator"></a>Information Protection 管理者
Azure Information Protection 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.informationprotection/AllEntities/AllActions | Information Protection の全側面の管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="intune-service-administrator"></a>Intune サービス管理者
Intune 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Azure Active Directory での Contact の作成。 |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory での Contact の削除。 |
| microsoft.aad.directory/Contact/Update | Azure Active Directory での Contact の標準プロパティの更新。 |
| microsoft.aad.directory/Device/Create | Azure Active Directory での Device の作成。 |
| microsoft.aad.directory/Device/Delete | Azure Active Directory での Device の削除。 |
| microsoft.aad.directory/Device/Update | Azure Active Directory での Device の標準プロパティの更新。 |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Azure Active Directory での Devices.RegisteredOwners プロパティの更新。 |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Azure Active Directory での Devices.RegisteredUsers プロパティの更新。 |
| microsoft.aad.directory/Group/Create | Azure Active Directory での Group の作成。 |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory での Group の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/Group/Delete | Azure Active Directory での Group の削除。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | Azure Active Directory での Groups.HiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/Group/Restore | Azure Active Directory での Group の復元。 |
| microsoft.aad.directory/Group/Update | Azure Active Directory での Group の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory での Groups.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory での Groups.Members プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory での Groups.Owners プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory での Groups.Settings プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/Update | Azure Active Directory での User の標準プロパティの更新。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory での Users.Manager プロパティの更新。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.intune/AllEntities/AllActions | Intune の全側面の管理。 |

### <a name="lync-service-administrator"></a>Lync サービス管理者
Skype for Business 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.skypeforbusiness/AllEntities/AllActions | Skype for Business Online の管理。 |

### <a name="message-center-reader"></a>メッセージ センター閲覧者
Office 365 メッセージ センター内でのみ自分の組織のメッセージと更新情報を閲覧することができます。 

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | メッセージ センターでのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |

### <a name="partner-tier1-support"></a>パートナー レベル 1 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/Contact/Create | Azure Active Directory での Contact の作成。 |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory での Contact の削除。 |
| microsoft.aad.directory/Contact/Update | Azure Active Directory での Contact の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Create | Azure Active Directory での Group の作成。 |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory での Group の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory での Groups.Members プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory での Groups.Owners プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory での User のライセンスの管理。 |
| microsoft.aad.directory/User/Delete | Azure Active Directory での User の削除。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/User/Restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/User/Update | Azure Active Directory での User の標準プロパティの更新。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory での Users.Manager プロパティの更新。 |
| microsoft.aad.directory/User/Update/PasswordUserScope | Azure Active Directory での管理者以外のパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="partner-tier2-support"></a>パートナー レベル 2 のサポート
使用しないでください。一般的な使用は想定されていません。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/Contact/Create | Azure Active Directory での Contact の作成。 |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory での Contact の削除。 |
| microsoft.aad.directory/Contact/Update | Azure Active Directory での Contact の標準プロパティの更新。 |
| microsoft.aad.directory/Domain/AllActions | Azure Active Directory での Domain の作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.directory/Group/Create | Azure Active Directory での Group の作成。 |
| microsoft.aad.directory/Group/Delete | Azure Active Directory での Group の削除。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Restore | Azure Active Directory での Group の復元。 |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory での Groups.Members プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/Organization/Update | Azure Active Directory での Organization の標準プロパティの更新。 |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの更新。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory での User のライセンスの管理。 |
| microsoft.aad.directory/User/Delete | Azure Active Directory での User の削除。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/User/Restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/User/Update | Azure Active Directory での User の標準プロパティの更新。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory での Users.Manager プロパティの更新。 |
| microsoft.aad.directory/User/Update/Password | Azure Active Directory でのすべてのユーザーのパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |

### <a name="power-bi-service-administrator"></a>Power BI サービス管理者
Power BI 製品のすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.powerbi/AllEntities/AllActions | Power BI の全側面の管理。 |

### <a name="privileged-role-administrator"></a>特権ロール管理者
Azure AD でロールの割り当てを管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Azure Active Directory での DirectoryRole の標準プロパティの更新。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Privileged Role Management サービスの全側面の管理。 |

### <a name="reports-reader"></a>レポート閲覧者
サインインと監査のレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Azure AD レポートの読み取り。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.office365.usagereports/AllEntities/Read | Office 365 の使用状況レポートの読み取り。 |

### <a name="security-administrator"></a>セキュリティ管理者
セキュリティ情報とレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Azure Active Directory での Applications.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/Policy/Create | Azure Active Directory での Policy の作成。 |
| microsoft.aad.directory/Policy/Delete | Azure Active Directory での Policy の削除。 |
| microsoft.aad.directory/Policy/Update | Azure Active Directory での Policy の標準プロパティの更新。 |
| microsoft.aad.directory/Policy/Update/Owners | Azure Active Directory での Policies.Owners プロパティの更新。 |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Azure Active Directory での ServicePrincipals.DefaultPolicy プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Privileged Identity Management の全側面の読み取り。 |
| microsoft.protectioncenter/AllEntities/Read | Office 365 プロテクション センターの全側面の読み取り。 |
| microsoft.protectioncenter/AllEntities/Update | Office 365 プロテクション センターの管理。 |

### <a name="security-reader"></a>セキュリティ閲覧者
Azure AD と Office 365 のセキュリティ情報とレポートを読み取ることができます。

  > [!NOTE]
  > このロールは、ディレクトリ閲覧者ロールから追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Privileged Identity Management の全側面の読み取り。 |
| microsoft.protectioncenter/AllEntities/Read | Office 365 プロテクション センターの全側面の読み取り。 |

### <a name="service-support-administrator"></a>サービス サポート管理者
サービス正常性に関する情報を読み取り、サポート チケットを管理することができます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |

### <a name="sharepoint-service-administrator"></a>SharePoint サービス管理者
SharePoint サービスのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

  > [!NOTE]
  > このロールは、Azure Active Directory 以外の追加のアクセス許可を持っています。 詳細については、上記のロールの説明を参照してください。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.sharepoint/AllEntities/AllActions | SharePoint Online の管理。 |

### <a name="user-account-administrator"></a>ユーザー アカウント管理者
ユーザーとグループのすべての側面を管理できます。

  > [!NOTE]
  > このロールは、[ユーザー ロール](https://docs.microsoft.com/azure/active-directory/users-default-permissions)から追加のアクセス許可を継承します。
  >
  >

| **アクション** | **説明** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Azure Active Directory での AppRoleAssignment の作成。 |
| microsoft.aad.directory/AppRoleAssignment/Delete | Azure Active Directory での AppRoleAssignment の削除。 |
| microsoft.aad.directory/AppRoleAssignment/Update | Azure Active Directory での AppRoleAssignment の標準プロパティの更新。 |
| microsoft.aad.directory/Contact/Create | Azure Active Directory での Contact の作成。 |
| microsoft.aad.directory/Contact/Delete | Azure Active Directory での Contact の削除。 |
| microsoft.aad.directory/Contact/Update | Azure Active Directory での Contact の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Create | Azure Active Directory での Group の作成。 |
| microsoft.aad.directory/Group/CreateAsOwner | Azure Active Directory での Group の作成。 作成者が最初の所有者として追加され、作成されたオブジェクトは作成者の 250 の作成オブジェクト クォータにカウントされます。 |
| microsoft.aad.directory/Group/Delete | Azure Active Directory での Group の削除。 |
| microsoft.aad.directory/Group/Read | Azure Active Directory での Group の標準プロパティの読み取り。 |
| microsoft.aad.directory/Group/Read/HiddenMembers | Azure Active Directory での Groups.HiddenMembers プロパティの読み取り。 |
| microsoft.aad.directory/Group/Restore | Azure Active Directory での Group の復元。 |
| microsoft.aad.directory/Group/Update | Azure Active Directory での Group の標準プロパティの更新。 |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Azure Active Directory での Groups.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Members | Azure Active Directory での Groups.Members プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Owners | Azure Active Directory での Groups.Owners プロパティの更新。 |
| microsoft.aad.directory/Group/Update/Settings | Azure Active Directory での Groups.Settings プロパティの更新。 |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Azure Active Directory での Organizations.TrustedCAsForPasswordlessAuth プロパティの読み取り。 |
| microsoft.aad.directory/User/AssignLicense | Azure Active Directory での User のライセンスの管理。 |
| microsoft.aad.directory/User/Create | Azure Active Directory での User の作成。 |
| microsoft.aad.directory/User/Delete | Azure Active Directory での User の削除。 |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Azure Active Directory 内のすべてのユーザー更新トークンの無効化。 |
| microsoft.aad.directory/User/Restore | Azure Active Directory での削除済みユーザーの復元。 |
| microsoft.aad.directory/User/Update | Azure Active Directory での User の標準プロパティの更新。 |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Azure Active Directory での Users.AppRoleAssignments プロパティの更新。 |
| microsoft.aad.directory/User/Update/Manager | Azure Active Directory での Users.Manager プロパティの更新。 |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Azure Active Directory での制限付き管理者、ヘルプデスク管理者、他のユーザー アカウント管理者のパスワードの更新。 詳細については、オンライン ドキュメントを参照してください。 |
| microsoft.aad.accessservice/AllEntities/AllActions | Azure Access Control でのすべてのリソースの作成と削除、および標準プロパティの読み取りと更新。 |
| microsoft.aad.supporttickets/AllEntities/AllActions | Office 365 サポート チケットの作成と管理。 |
| microsoft.aad.servicehealth/AllEntities/AllActions | Office 365 Service Health の読み取りと構成。 |

## <a name="next-steps"></a>次の手順

* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../../billing/billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
