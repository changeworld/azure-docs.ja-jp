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
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/15/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 8ba4787852902f194deb3eee27ff9f0fc28fa5aa
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Azure Active Directory での管理者ロールの割り当て

Azure Active Directory (Azure AD) を使用すると、各種役割ごとに別々の管理者を指定することができます。 管理者は、Azure Portal 内のさまざまな機能にアクセスでき、ロールに応じて、ユーザーの作成や編集、他のユーザーへの管理者ロールの割り当て、ユーザー パスワードのリセット、ユーザー ライセンスの管理、ドメインの管理などを行うことができます。 Office 365 ポータルや Azure Portal 内で割り当てたのか、または Windows PowerShell 用 Azure AD モジュールを使用して割り当てたのかに関係なく、管理者ロールが割り当てられたユーザーは、組織がサブスクライブしているすべてのクラウド サービスで同じ権限を持つことになります。

## <a name="details-about-the-global-administrator-role"></a>全体管理者ロールの詳細
全体管理者は、すべての管理機能にアクセスできます。 既定では、Azure サブスクリプションにサインアップしたユーザーには、ディレクトリの全体管理者ロールが割り当てられます。 他の管理者ロールを割り当てることができるのは全体管理者だけです。

## <a name="assign-or-remove-administrator-roles"></a>管理者ロールの割り当てまたは削除
Azure Active Directory でユーザーに管理者ロールを割り当てる方法については、「[Azure Active Directory でユーザーを管理者ロールに割り当てる](active-directory-users-assign-role-azure-portal.md)」を参照してください。

## <a name="available-roles"></a>使用可能なロール
次の管理者ロールを使用できます。

* **課金管理者**: 購入、サブスクリプションの管理、サポート チケットの管理、サービス正常性の監視を行います。

* **コンプライアンス管理者**: このロールが割り当てられたユーザーは、Office 365 セキュリティ/コンプライアンス センターと Exchange 管理センター内で管理アクセス許可を持ちます。 詳しくは、「[Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **条件付きアクセス管理者**: このロールが割り当てられたユーザーは、Azure Active Directory の条件付きアクセスの設定を管理できます。
  > [!NOTE]
  > Azure で Exchange ActiveSync の条件付きアクセス ポリシーをデプロイするには、ユーザーは、グローバル管理者である必要もあります。
  
* **CRM サービス管理者**: このロールが割り当てられたユーザーは、Microsoft CRM Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **デバイス管理者**: このロールが割り当てられたユーザーは、Azure Active Directory に参加しているすべての Windows 10 デバイスのローカル コンピューター管理者になります。 Azure Active Directory 内のデバイス オブジェクトを管理することはできません。

* **ディレクトリ リーダー**: これは、[同意フレームワーク](active-directory-integrating-applications.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **ディレクトリ同期アカウント**: 使用しないでください。 このロールは、自動的に Azure AD Connect サービスに割り当てられます。他の用途に使用するためのものではなく、他の用途ではサポートされていません。

* **ディレクトリ ライター**: これは、[同意フレームワーク](active-directory-integrating-applications.md)をサポートしていないアプリケーションに割り当てられる従来のロールです。 このロールをユーザーに割り当てることはできません。

* **Exchange サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Exchange Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **グローバル管理者/会社の管理者**: このロールが割り当てられたユーザーは、Azure Active Directory のすべての管理機能と、Azure Active Directory とフェデレーションするサービス (Exchange Online、SharePoint Online、Skype for Business Online など) にアクセスできます。 Azure Active Directory テナントにサインアップしたユーザーがグローバル管理者になります。 他の管理者ロールを割り当てることができるのは全体管理者だけです。 会社に複数の全体管理者が存在してかまいません。 すべてのユーザーと他のすべての管理者のパスワードをリセットできます。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "会社の管理者" として識別されます。 [Azure Portal](https://portal.azure.com) では、"全体管理者" になります。
  >
  >

* **ゲスト招待元**: このロールが割り当てられたユーザーは、"メンバーは招待ができる" ユーザー設定が "いいえ" に設定されている場合に、Azure Active Directory B2B ゲスト ユーザーの招待を管理できます。 B2B コラボレーションの詳細については、「[Azure AD B2B コラボレーションとは](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)」をご覧ください。 その他の権限は含まれません。

* **Information Protection 管理者**: このロールが割り当てられたユーザーは、Azure Information Protection サービス上でのみユーザー権利があります。 Identity Protection Center、Privileged Identity Management、Office 365 サービス正常性の監視、および Office 365 のセキュリティ/コンプライアンス センター上のユーザー権利は付与されません。 また、Azure Information Protection ポリシーのラベルの構成、保護テンプレートの管理、および保護のアクティブ化を行うことができます。

* **Intune サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Intune Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。 さらに、このロールはポリシーを関連付けるためにユーザーとデバイスを管理することができ、グループを作成および管理することもできます。

* **メールボックス管理者**: このロールは、RIM Blackberry デバイスの Exchange Online 電子メール サポートの一部としてのみ使用されます。 組織が RIM Blackberry デバイスの Exchange Online 電子メールを使用していない場合、このロールは使用しません。

* **Partner Tier 1 サポート**: 使用しないでください。 このロールは廃止されており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **Partner Tier 2 サポート**: 使用しないでください。 このロールは廃止されており、将来的に Azure AD から削除されます。 このロールは少数の Microsoft 再販パートナーを対象としており、一般的な使用を目的としたものではありません。

* **パスワード管理者/ヘルプデスク管理者**: このロールが割り当てられたユーザーは、パスワードの変更、サービス要求の管理、サービス正常性の監視を行うことができます。 ヘルプデスク管理者が変更できるのは、ユーザーと他のヘルプデスク管理者のパスワードだけです。 

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、および Azure AD PowerShell では、このロールは "ヘルプデスクの管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"パスワード管理者" になります。
  >
  >
  
* **Power BI サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Power BI 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US)」をご覧ください。

* **特権ロール管理者**: このロールが割り当てられたユーザーは、Azure Active Directory と Azure AD Privileged Identity Management 内でロールの割り当てを管理できます。 また、このロールは Privileged Identity Management の全側面を管理できます。

* **レポート リーダー**: このロールが割り当てられたユーザーは、Office 365 管理センターで使用状況のレポート データとレポート ダッシュボードを表示できます。また、Power BI で導入コンテキスト パックを表示できます。 さらに、Azure AD のサインオン レポートとアクティビティ、および Microsoft Graph レポート API から返されるデータにもアクセスできます。 レポート リーダー ロールが割り当てられたユーザーは、関連する使用状況と導入メトリックにのみアクセスできます。 製品固有の管理センター (Exchange など) の設定を構成したり、アクセスしたりする管理者アクセス許可はありません。 

* **セキュリティ管理者**: このロールが割り当てられたユーザーは、セキュリティ閲覧者ロールのすべての読み取り専用アクセス許可を持ち、セキュリティ関連サービス (Azure Active Directory Identity Protection、Azure Information Protection、Privileged Identity Management、Office 365 セキュリティ/コンプライアンス センター) の構成を管理することもできます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

* **セキュリティ閲覧者**: このロールが割り当てられたユーザーは、Azure Active Directory、Identity Protection、Privileged Identity Management 内のすべての情報を含め、グローバルな読み取り専用アクセス権を持ち、Azure Active Directory のサインイン レポートと監査ログを閲覧できます。 また、このロールには、Office 365 セキュリティ/コンプライアンス センターでの読み取り専用アクセス許可が付与されます。 Office 365 のアクセス許可の詳細については、「[Office 365 セキュリティ/コンプライアンス センターでのアクセス許可](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1)」をご覧ください。

* **サービス サポート管理者**: このロールが割り当てられたユーザーは、Azure サービスと Office 365 サービスについて Microsoft へのサポート要求を開くことができます。また、Azure Portal と Office 365 管理ポータルで、サービス ダッシュボードとメッセージ センターを表示できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **SharePoint サービス管理者**: このロールが割り当てられたユーザーは、Microsoft SharePoint Online 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、サポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

* **Skype for Business/Lync サービス管理者**: このロールが割り当てられたユーザーは、Microsoft Skype for Business 内でグローバル アクセス許可を持ちます (このサービスが存在する場合)。また、Azure Active Directory で Skype 固有のユーザー属性を管理します。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 詳細については、「 [Office 365 の管理者ロールについて](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d)」をご覧ください。

  > [!NOTE]
  > Microsoft Graph API、Azure AD Graph API、Azure AD PowerShell では、このロールは "Lync サービス管理者" として識別されます。 [Azure Portal](https://portal.azure.com/) では、"Skype for Business サービス管理者" になります。
  >
  >

* **ユーザー アカウント管理者**: このロールが割り当てられたユーザーは、ユーザーとグループを作成し、全側面を管理できます。 さらに、このロールはサポート チケットを管理し、サービス正常性を監視できます。 ただし、いくつかの制限が適用されます。 たとえば、このロールはグローバル管理者を削除できません。 ユーザー アカウント管理者は、ユーザー、ヘルプデスク管理者、およびその他のユーザー アカウント管理者についてのみ、パスワードを変更できます。

## <a name="administrator-permissions"></a>管理者の権限

### <a name="billing-administrator"></a>課金管理者

| できること | できないこと |
| --- | --- |
|<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金および購入操作の実行</p> |<p>ユーザー パスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>監査ログの表示</p>|

### <a name="conditional-access-administrator"></a>条件付きアクセス管理者

| できること | できないこと |
| --- | --- |
|<p>会社情報とユーザー情報の表示</p><p>条件付きアクセスの設定の管理</p> |<p>ユーザー パスワードのリセット</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>監査ログの表示</p>|

### <a name="global-administrator"></a>全体管理者
| できること | できないこと |
| --- | --- |
|<p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>Office 製品の課金および購入操作の実行</p><p>ユーザー パスワードのリセット</p><p>他の管理者のパスワードのリセット</p> <p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p><p>監査ログの表示</p> |該当なし |

### <a name="password-administrator--helpdesk-administrator"></a>パスワード管理者/ヘルプデスク管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>パスワードの変更 (ユーザーおよび他のヘルプデスク管理者のみ)</p>|<p>Office 製品の課金および購入操作の実行</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>レポートを表示する</p>|

### <a name="information-protection-administrator"></a>Information Protection 管理者
イン | できること
-------- | ---------
Azure Information Protection | <li>グローバルとスコープ付きポリシーでのラベルと設定の構成<li>保護テンプレートの構成と管理<li>保護のアクティブ化または非アクティブ化
 
### <a name="reports-reader"></a>レポート リーダー 
できること | できないこと
------ | ----------
Azure AD サインイン レポートと監査ログの表示<br>会社情報とユーザー情報の表示<br>Office 365 使用状況ダッシュボードへのアクセス | ユーザー ビューの作成と管理<br>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理<br>他のユーザーへの管理者ロールの委任<br>会社情報の管理

### <a name="security-reader"></a>セキュリティ リーダー
| イン | できること |
| --- | --- |
| Identity Protection Center |各セキュリティ機能の全セキュリティ レポートと設定情報の閲覧<ul><li>スパム対策<li>暗号化<li>データ損失の防止<li>マルウェア対策<li>高度な脅威保護<li>フィッシング詐欺対策<li>メールフロー ルール |
| Privileged Identity Management |<p>Azure AD PIM に表示される、Azure AD ロール割り当てに関するポリシーとレポート、セキュリティ レビューのすべての情報に対する読み取り専用アクセス権を持ちます。また、将来的には、Azure AD ロール割り当て以外のシナリオのポリシー データとレポートに対する読み取りアクセス権も付与される予定です。<p>Azure AD PIM へのサインアップおよび Azure AD PIM の変更を行うことは**できません**。 このロールのユーザーは、追加のロール (グローバル管理者や特権ロール管理者など) の資格を持っている場合、PIM のポータルまたは PowerShell からそのロールを有効化することができます。 |
| <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター</p> |<ul><li>アラートの閲覧と管理<li>セキュリティ ポリシーの閲覧<li>検索調査における脅威インテリジェンス情報、Cloud App Discovery、および検査の閲覧<li>全レポートの閲覧 |

### <a name="security-administrator"></a>セキュリティ管理者
| イン | できること |
| --- | --- |
| Identity Protection Center |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>さらに、パスワードのリセットを除く IPC のすべての操作を行うことができます。 |
| Privileged Identity Management |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>Azure AD ロールのメンバーシップまたは設定を管理することは**できません**。 |
| <p>Office 365 サービス正常性の監視</p><p>Office 365 セキュリティ/コンプライアンス センター |<ul><li>セキュリティ リーダー ロールのすべての権限を持ちます。<li>Advanced Threat Protection 機能 (マルウェアおよびウイルス防止、悪意のある URL の構成、URL 追跡など) のすべての設定を構成することができます。 |

### <a name="service-administrator"></a>サービス管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p> |<p>ユーザー パスワードのリセット</p><p>Office 製品の課金操作と購入操作の実行</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>監査ログの表示</p> |

### <a name="user-account-administrator"></a>ユーザー アカウント管理者
| できること | できないこと |
| --- | --- |
| <p>会社情報とユーザー情報の表示</p><p>Office サポート チケットの管理</p><p>ユーザー、ヘルプデスク管理者、およびその他のユーザー アカウント管理者についてのみ、パスワードを変更できます</p><p>ユーザー ビューの作成と管理</p><p>ユーザーとグループの作成、編集、削除、およびユーザー ライセンスの管理 (制限付き)。 グローバル管理者を削除することも、他の管理者を作成することもできません。</p> |<p>Office 製品の課金および購入操作の実行</p><p>ドメインの管理</p><p>会社情報の管理</p><p>他のユーザーへの管理者ロールの委任</p><p>ディレクトリ同期の使用</p><p>Multi-Factor Authentication の有効化または無効化</p><p>監査ログの表示</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>全体管理者として同僚を追加するには

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com)に、テナント ディレクトリの全体管理者のアカウントでサインインします。

   ![Azure AD 管理センターを開く](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. **[ユーザーとグループ] &gt; [すべてのユーザー]** の順に選択します。

3. 全体管理者として指定するユーザーを見つけ、そのユーザーのブレードを開きます。

4. ユーザーのブレードで、**[ディレクトリ ロール]** を選択します。
 
5. [ディレクトリ ロール] ブレードで **[全体管理者]** ロールを選択し、保存します。

## <a name="deprecated-roles"></a>非推奨のロール

次のロールは使用しないでください。 これらは廃止されており、将来的に Azure AD から削除されます。

* アドホック ライセンス管理者
* メールで確認済みのユーザー作成者
* デバイスの参加
* デバイス マネージャー
* デバイス ユーザー
* デバイスの社内参加

## <a name="next-steps"></a>次の手順

* Azure サブスクリプションの管理者を変更する方法の詳細については、「 [Azure 管理者ロールを追加または変更する方法](../billing-add-change-azure-subscription-administrator.md)
* Microsoft Azure でリソース アクセスを制御する方法の詳細については、「 [Azure でのリソース アクセスについて](active-directory-understanding-resource-access.md)
* Azure Active Directory と Azure サブスクリプションの関係の詳細については、「 [Azure サブスクリプションを Azure Active Directory に関連付ける方法](active-directory-how-subscriptions-associated-directory.md)
* [ユーザーの管理](active-directory-create-users.md)
* [パスワードの管理](active-directory-manage-passwords.md)
* [グループの管理](active-directory-manage-groups.md)
