---
title: Azure Active Directory でのマルチテナント ユーザー管理に関する一般的な考慮事項
description: ゲスト アカウントによる複数の Azure Active Directory テナントへのユーザー アクセスを設計するときの一般的な考慮事項について説明します
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77a93eb869cd3135f548331a82de9593497829c0
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130269747"
---
# <a name="common-considerations-for-multi-tenant-user-management"></a>マルチテナント ユーザー管理に関する一般的な考慮事項

複数のコラボレーション パターンに関連する多くの考慮事項があります。 

## <a name="directory-object-considerations"></a>ディレクトリ オブジェクトに関する考慮事項

コンソールを使用して、ゲスト ユーザー アカウントへの招待を手動で作成できます。 これを行うと、作成されるユーザー オブジェクトのユーザーの種類は "*ゲスト*" になります。 他の手法を使用して招待を作成すると、ユーザーの種類をゲスト アカウント以外に設定できます。 たとえば、API を使用すると、アカウントをメンバー アカウントまたはゲスト アカウントのどちらにするかを構成できます。  

* [ゲスト機能の制限の一部をなくすことができます](../external-identities/user-properties.md#remove-guest-user-limitations)。

* [ゲスト アカウントは、メンバーというユーザーの種類に変換できます](../external-identities/user-properties.md#can-azure-ad-b2b-users-be-added-as-members-instead-of-guests)。

> **重要** ゲスト アカウントからユーザー アカウントに変換する場合、Exchange Online による B2B アカウントの処理方法に関して問題が発生する可能性があります。 ゲスト メンバーとして招待されたアカウントでメールを有効にすることはできません。 ゲスト メンバー アカウントでメールを有効にするには、次の方法が最適です。
>* 組織間ユーザーをゲスト アカウントとして招待します。
>* GAL でアカウントを示します。
>* UserType を Member に設定します。

この方法を使用すると、アカウントは Exchange Online で MailUser として表示されます。

### <a name="issues-with-using-mail-contact-objects-instead-of-external-users-or-members"></a>外部ユーザーまたはメンバーの代わりにメール連絡先オブジェクトを使用する場合の問題

従来の GAL 同期を使用して、別のテナントのユーザーを表すことができます。 Azure AD B2B コラボレーションを使用するのではなく GAL 同期を行った場合、メール連絡先オブジェクトが作成されます。 

* メール連絡先オブジェクトとメールが有効なゲスト ユーザー (メンバーまたはゲスト) は、同じメール アドレスで同じテナントに同時に存在することはできません。 

* 招待されたゲスト ユーザーと同じメール アドレスを持つメール連絡先オブジェクトが存在する場合、ゲスト ユーザーは作成されますが、メールは有効になりません。 

* 同じメール アドレスを持つ、メールが有効なゲスト ユーザーが存在する場合、メール連絡先オブジェクトを作成しようとすると、作成時に例外がスローされます。

メール連絡先オブジェクトとゲスト ユーザーのさまざまな状態の結果を次に示します。

| 既存の状態| プロビジョニング シナリオ| 有効な結果 |
| - |-|-|
| None| B2B メンバーを招待する| メールが有効でないメンバー ユーザー。 上記の重要な注意を参照 |
| None| B2B ゲストを招待する| メールが有効なゲスト ユーザー |
| メール連絡先オブジェクトが存在する| B2B メンバーを招待する| エラー – プロキシ アドレスの競合 |
| メール連絡先オブジェクトが存在する| B2B ゲストを招待する| メール連絡先とメールが有効でないゲスト ユーザー。 上記の重要な注意を参照 |
| メールが有効な B2B ゲスト ユーザー| メール連絡先オブジェクトを作成する| エラー |
| メールが有効な B2B メンバー ユーザーが存在する| メール連絡先を作成する| エラー |


**従来の GAL 同期は推奨されません**。 代わりに、Azure AD B2B コラボレーションを使用して、次のものを作成してください。

* GAL に表示できる外部ゲスト ユーザー

* 外部メンバー ユーザーを作成します。既定で GAL に表示されますが、メールは有効になっていません。

一部の組織では、メール連絡先オブジェクトを使用して GAL にユーザーを表示します。 この方法では、メール連絡先はセキュリティ プリンシパルではないため、他のアクセス許可を付与せずに GAL を統合します。 

この目標を達成するためのより優れたアプローチは次のとおりです。
* ゲスト ユーザーを招待する
* GAL でそれらを再表示する
* [サインインからブロック](/powershell/module/azuread/set-azureaduser&preserve-view=true)して無効にする。

メール連絡先オブジェクトをユーザー オブジェクトに変換することはできません。 そのため、メール連絡先オブジェクトに関連付けられているプロパティは転送できません。 たとえば、グループ メンバーシップや他のリソース アクセスは転送されません。  

メール連絡先オブジェクトを使用してユーザーを表すと、次の課題があります。

* **Office 365 グループ** – Office 365 グループでは、メンバーになり、グループに関連付けられているコンテンツと対話することが許可されるユーザーの種類を管理するポリシーがサポートされています。 たとえば、グループでゲスト アカウントの参加が許可されない場合があります。 これらのポリシーでメール連絡先オブジェクトを管理することはできません。

* **Azure AD セルフサービス グループ管理 (SSGM)** – メール連絡先オブジェクトには、SSGM 機能を使用するグループのメンバーになる資格はありません。 ユーザー オブジェクトではなく連絡先として表される受信者を含むグループを管理するには、追加のツールが必要になる場合があります。

* **Azure AD Identity Governance - アクセス レビュー** – アクセス レビュー機能を使用して、Office 365 グループのメンバーシップを確認および構成証明できます。 アクセス レビューは、ユーザー オブジェクトに基づいています。 メール連絡先オブジェクトで表されるメンバーは、アクセス レビューの対象外です。

* **Azure AD Identity Governance - エンタイトルメント管理 (EM)** – EM を使用して、会社の EM ポータルで外部ユーザーに対するセルフサービス アクセス要求を有効にした場合、要求時にユーザー オブジェクトが作成されます。 メール連絡先オブジェクトはサポートされません。 

## <a name="azure-ad-conditional-access-considerations"></a>Azure AD の条件付きアクセスに関する考慮事項

ユーザーのホーム テナントでのユーザー、デバイス、またはネットワークの状態は、リソース テナントに伝達されません。 そのため、ゲスト ユーザー アカウントは、次の制御を使用する条件付きアクセス (CA) ポリシーを満たさない可能性があります。

* **多要素認証が必要である** – ゲスト ユーザーは、ホーム テナントで MFA が満たされた場合でも、リソース テナントでの MFA に登録して応答する必要があり、結果として複数の MFA チャレンジが発生します。 また、MFA 証明をリセットする必要がある場合、テナント間の複数の MFA 証明登録を認識していない可能性があります。 認識がないと、ユーザーはホーム テナントとリソース テナントの一方または両方の管理者に連絡することが必要な場合があります。

* **デバイスが準拠としてマークされている必要がある** – デバイス ID がリソース テナントに登録されていないので、ゲスト ユーザーはこの制御を必要とするリソースへのアクセスをブロックされます。

* **Hybrid Azure AD Join を使用したデバイスが必要である** - デバイス ID がリソース テナント (またはリソース テナントに接続されている オンプレミスの Active Directory) に登録されていないので、ゲスト ユーザーはこの制御を必要とするリソースへのアクセスをブロックされます。

* **承認済みクライアント アプリまたはアプリ保護ポリシーが必要である** – デバイスの登録も必要であるため、外部ゲスト ユーザーはリソース テナントの Intune モバイル アプリ管理 (MAM) ポリシーを適用できません。 この制御を使用するリソース テナントの条件付きアクセス (CA) ポリシーでは、ホーム テナントの MAM 保護でポリシーを満たすことができません。 外部ゲスト ユーザーを、MAM ベースのすべての CA ポリシーから除外する必要があります。 

さらに、次の CA の条件を使用することはできますが、可能性のある影響に注意してください。

* **サインイン リスクとユーザー リスク** – サインイン リスクとユーザー リスクは、ホーム テナントでのユーザーの動作によってある程度決定されます。 データとリスク スコアは、ホーム テナントに格納されます。  
‎リソース テナント ポリシーによってゲスト ユーザーがブロックされている場合、リソース テナント管理者はアクセスを有効にできない可能性があります。 詳しくは、「[Identity Protection と B2B ユーザー](../identity-protection/concept-identity-protection-b2b.md)」をご覧ください。

* **場所** – リソース テナントで定義されている名前付きの場所の定義が、ポリシーのスコープを決定するために使用されます。 ホーム テナントで管理されている信頼できる場所は、ポリシーのスコープで評価されません。 一部のシナリオでは、組織は信頼できる場所をテナント間で共有することが必要な場合があります。 信頼できる場所を共有するには、リソースと条件付きアクセス ポリシーが定義されている各テナントで、場所が定義されている必要があります。

## <a name="other-access-control-considerations"></a>アクセス制御に関するその他の考慮事項

アクセス制御を構成するときの追加の考慮事項。
* [アクセス制御ポリシー](../external-identities/conditional-access.md)を定義して、リソースへのアクセスを制御します。
* ゲスト ユーザーに留意して CA ポリシーを設計します。 
* 特にゲスト ユーザー用のポリシーを作成します。 
* 組織の既存の CA ポリシーで "すべてのユーザー" 条件が使われている場合、"ゲスト" ユーザーは "すべてのユーザー" のスコープ内にあるため、このポリシーはゲスト ユーザーに影響します。
* "ゲスト" アカウント用に専用の CA ポリシーを作成します。 

"すべてのユーザー" 式を利用する動的グループのセキュリティ強化について詳しくは、[動的グループと Azure AD B2B のコラボレーション](../external-identities/use-dynamic-groups.md)に関する記事をご覧ください。

### <a name="require-user-assignment"></a>ユーザー割り当てを要求する

アプリケーションで [ユーザーの割り当てが必要ですか] プロパティが [いいえ] に設定されている場合、ゲスト ユーザーはアプリケーションにアクセスできます。 アプリケーション管理者は、アクセス制御の影響を理解する必要があります (特に、アプリケーションに機密情報が含まれている場合)。 詳しくは、[ユーザーのセットに Azure AD アプリを制限する方法](../develop/howto-restrict-your-app-to-a-set-of-users.md)に関する記事をご覧ください。 

### <a name="terms-and-conditions"></a>使用条件

[Azure AD の使用条件](../conditional-access/terms-of-use.md)により、組織でエンド ユーザーに情報を提示するために使うことができる簡単な方法が提供されます。 使用条件を使って、リソースにアクセスする前に使用条件に同意するようゲスト ユーザーに要求できます。

### <a name="licensing-considerations-for-guest-users-with-azure-ad-premium-features"></a>Azure AD Premium 機能でのゲスト ユーザーのライセンスに関する考慮事項

Azure AD External Identities (ゲスト ユーザー) の価格は、月間アクティブ ユーザー (MAU) に基づきます。 アクティブ ユーザーは、1 か月間に認証アクティビティを使用した一意のユーザーの数です。 MAU の課金は、無料レベルと柔軟で予測可能な価格を提供することによってコストを削減するのに役立ちます。 さらに、Premium P1 機能と Premium P2 機能の両方で、1 か月あたり最初の 50,000 人の MAU については無料となります。 Premium 機能には、ゲスト ユーザーに対する条件付きアクセス ポリシーと Azure MFA が含まれます。

詳しくは、[Azure AD External Identities の MAU 課金モデル](../external-identities/external-identities-pricing.md)に関する記事をご覧ください。

## <a name="office-365-considerations"></a>Office 365 に関する考慮事項

次の情報は、この記事のシナリオのコンテキストでの Office 365 への対処についての説明です。 詳しくは、[Office 365 のテナント間のコラボレーション](/office365/enterprise/office-365-inter-tenant-collaboration)に関する記事をご覧ください。

### <a name="microsoft-exchange-online"></a>Microsoft Exchange Online

Exchange Online により、ゲスト ユーザーの特定の機能が制限されます。 外部ゲストではなく外部メンバーを作成することで、制限が減る可能性があります。 ただし、現時点では、次のいずれも外部ユーザーに対してサポートされていません。 

* ゲスト ユーザーには、Exchange Online のライセンスを割り当てることができます。 ただし、Exchange Online 用のトークンの発行を受けることはできません。 その結果、リソースにはアクセスできません。

   * ゲスト ユーザーは、リソース テナント内の共有された、またはデリゲートされた Exchange Online メールボックスを使用できません。

   * ゲスト ユーザーは、共有メールボックスに割り当てられることはできますが、アクセスすることはできません。

* ゲスト ユーザーは、GAL に含められるには、再表示される必要があります。 既定では、非表示になっています。

   * 非表示のゲスト ユーザーは、招待時に作成されます。 作成は、ユーザーが招待を引き換えたかどうかとは無関係です。 そのため、すべてのゲスト ユーザーが再表示された場合、一覧には招待を引き換えていないゲスト ユーザーのユーザー オブジェクトが含まれます。 シナリオに基づいて、オブジェクトを一覧に含めることも、含めないこともできます。

   * ゲスト ユーザーは、[Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) を使って再表示できます。 [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) PowerShell コマンドレットを実行して、HiddenFromAddressListsEnabled プロパティの値を $false に設定できます。  
‎  
`‎Set-MailUser [GuestUserUPN] -HiddenFromAddressListsEnabled:$false` ‎  
‎ここで、"GuestUserUPN" は計算された UserPrincipalName です。 例:  
‎  
`‎Set-MailUser guestuser1_contoso.com#EXT#@fabricam.onmicrosoft.com -HiddenFromAddressListsEnabled:$false`

* PrimarySmtpAddress、ExternalEmailAddress、EmailAddresses、MailTip など、Exchange 固有のプロパティの更新は、[Exchange Online PowerShell](/powershell/exchange/exchange-online-powershell-v2?view=exchange-ps&preserve-view=true) を使うことによってのみ設定できます。 Exchange Online 管理センターの GUI を使って属性を変更することはできません。 

上に示すように、メール固有のプロパティには [Set-MailUser](/powershell/module/exchange/set-mailuser?view=exchange-ps&preserve-view=true) PowerShell コマンドレットを使用できます。 [Set-User](/powershell/module/exchange/set-user?view=exchange-ps&preserve-view=true) PowerShell コマンドレットを使うと、その他にも多くのユーザー プロパティを変更できます。 プロパティの大部分は、Azure AD Graph API を使って変更することもできます。

### <a name="microsoft-sharepoint-online"></a>Microsoft SharePoint Online

SharePoint Online には、ユーザーが Azure Active Directory テナントのゲストのメンバーかどうかに応じた、独自のサービス固有のアクセス許可があります。 

詳しくは、[Office 365 の外部共有と Azure Active Directory B2B コラボレーション](../external-identities/o365-external-user.md)に関する記事をご覧ください。

SharePoint Online で外部共有を有効にした後、SharePoint Online のユーザー選択ウィンドウでゲスト ユーザーを検索する機能は、既定ではオフになっています。 この設定のため、Exchange Online の GAL から非表示にされているゲスト ユーザーを検出することはできません。 ゲスト ユーザーを表示するには 2 つの方法があります (相互に排他的ではありません)。

* いくつかの方法でゲスト ユーザーを検索する機能を有効にできます。
   * テナントとサイト コレクション レベルで "ShowPeoplePickerSuggestionsForGuestUsers" の設定を変更します。 
   * [Set-SPOTenant](/powershell/module/sharepoint-online/Set-SPOTenant?view=sharepoint-ps&preserve-view=true) および [Set-SPOSite](/powershell/module/sharepoint-online/set-sposite?view=sharepoint-ps&preserve-view=true) の [SharePoint Online PowerShell](/powershell/sharepoint/sharepoint-online/connect-sharepoint-online?view=sharepoint-ps&preserve-view=true) コマンドレットを使用して機能を設定します。  
‎

* Exchange Online の GAL に表示されるゲスト ユーザーは、SharePoint Online のユーザー ピッカーにも表示されます。 アカウントは、"ShowPeoplePickerSuggestionsForGuestUsers" の設定に関係なく表示されます。

### <a name="microsoft-teams"></a>Microsoft Teams

Microsoft Teams には、ユーザーの種類に基づいてアクセスを制限する機能があります。 ユーザーの種類を変更すると、コンテンツのアクセスと使用可能な機能に影響する可能性があります。 

* Microsoft Teams の "テナント切り替え" メカニズムでは、ユーザーは、ホーム テナントの外部で Teams を使用するときに、Teams クライアントのコンテキストを手動で切り替えることが必要な場合があります。

* Teams フェデレーションを使用すると、別のまったく外部のドメインの Teams ユーザーが、内部のユーザーとの検索、通話、チャット、会議の設定をできるようにすることが可能です。 詳しくは、「[Microsoft Teams での外部アクセスの管理](/microsoftteams/manage-external-access)」をご覧ください。 

 

### <a name="licensing-considerations-for-guest-users-in-teams"></a>Teams でのゲスト ユーザーのライセンスに関する考慮事項

Office 365 のワークロードで Azure B2B を使用する場合、いくつかの重要な考慮事項があります。 ゲスト アカウントのエクスペリエンスがメンバー アカウントのものと同じではない場合があります。 

**Microsoft グループ**。 Microsoft グループでのゲスト アカウントのエクスペリエンについて詳しくは、[Office 365 グループへのゲストの追加](https://support.office.com/article/adding-guests-to-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6)に関する記事をご覧ください。 

**Microsoft Teams**。 Microsoft Teams でのゲスト アカウントのエクスペリエンについて詳しくは、「[Teams のチーム所有者、メンバー、およびゲスト機能](https://support.office.com/article/team-owner-member-and-guest-capabilities-in-teams-d03fdf5b-1a6e-48e4-8e07-b13e1350ec7b?ui=en-US&rs=en-US&ad=US)」をご覧ください。 

B2B 外部メンバーを使用することで、Teams の完全に忠実なエクスペリエンスを有効にできます。 最近、Office 365 では、マルチテナント組織に関するライセンス ポリシーが明らかにされました。

* ホーム テナントでのライセンスを持つユーザーは、同じ法人内の別のテナントのリソースにアクセスできます。 アクセス権は、 **[外部メンバー]** の設定を使用して付与され、追加のライセンス料金はかかりません。 この設定は、SharePoint、OneDrive for Business、Teams、グループに適用されます。 

   * ホーム テナントでのユーザーのライセンス状態を自動的に確認し、追加のライセンス割り当てや構成なしでメンバーとして参加できるようにするためのエンジニアリング作業が進行中です。 ただし、今すぐ外部メンバーを使用したいお客様には、アカウント エグゼクティブが Microsoft ビジネス デスクと一緒に作業する必要があるライセンスの回避策があります。 

   * 現時点からエンジニアリングされたライセンス ソリューションが有効になるまで、お客様は *Teams の試用版ライセンス* を利用できます。 ライセンスは、外部テナント内の各ユーザーに割り当てることができます。 ライセンスの期間は 1 年で、上記のすべてのワークロードが有効になります。

   * お客様が B2B ゲストを B2B メンバーに変換することを望まれる場合、Microsoft Teams には、新しいチャネルを作成するできないことや、既存のチームにアプリケーションを追加する機能など、既知の問題がいくつかあります。 

* **Identity Governance** の機能 (エンタイトルメント管理、アクセス レビュー) については、ゲスト ユーザーまたは外部メンバーは他のライセンスが必要な場合があります。 アカウント チームまたはビジネス デスクと協力して、組織にとって適切な回答を得てください。

**他の製品** (Dynamics CRM など) では、ユーザーが存在するテナントごとにライセンスが必要な場合があります。 アカウント チームと協力して、組織にとって適切な回答を得てください。

## <a name="next-steps"></a>次のステップ
[マルチテナント ユーザー管理の概要](multi-tenant-user-management-introduction.md)

[マルチテナント エンド ユーザー管理シナリオ](multi-tenant-user-management-scenarios.md)

[マルチテナントの一般的なソリューション](multi-tenant-common-solutions.md)