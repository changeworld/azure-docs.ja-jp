---
title: Azure AD Multi-Factor Authentication のデプロイに関する考慮事項
description: Azure AD Multi-Factor Authentication の実装がうまくいくデプロイに関する考慮事項と戦略について説明します
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42ce13335c3bab4a853de5001760eca0d85fdf7b
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129352625"
---
# <a name="plan-an-azure-active-directory-multi-factor-authentication-deployment"></a>Azure AD の多要素認証のデプロイを計画する 

Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) は、データとアプリケーションへのアクセスを保護し、2 つ目の形式の認証を使用して別のセキュリティ層を提供するために役立ちます。 組織は、[条件付きアクセス](../conditional-access/overview.md)を使用して多要素認証を有効化し、ソリューションを組織の特定のニーズに適合させることができます。

このデプロイ ガイドでは、[Azure AD MFA](concept-mfa-howitworks.md) のロールアウトを計画して実装する方法について説明します。

## <a name="prerequisites-for-deploying-azure-ad-mfa"></a>Azure AD MFA をデプロイするための前提条件

デプロイを開始する前に、関連するシナリオについて次の前提条件が満たされていることを確認してください。

| シナリオ | 前提条件 |
|----------|--------------|
|先進認証を使用する **クラウド専用** の ID 環境 | **前提となるタスクなし** |
|**ハイブリッド ID** のシナリオ | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) をデプロイし、オンプレミスの Active Directory Domain Services (AD DS) と Azure AD の間でユーザー ID を同期する。 |
| クラウド アクセス用に公開された **オンプレミスのレガシ アプリケーション**| [Azure AD アプリケーション プロキシ](../app-proxy/application-proxy-deployment-plan.md)をデプロイする |

## <a name="choose-authentication-methods-for-mfa"></a>MFA の認証方法を選択する

2 つ目の要素の認証に使用できる方法は多数あります。 使用可能な認証方法の一覧から選択して、セキュリティ、使いやすさ、可用性の観点からそれぞれを評価できます。

>[!IMPORTANT]
>複数の MFA の方法を有効にして、最初の方法を使用できないときにユーザーがバックアップの方法を使用できるようにします。 次のメソッドがあります。

- [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-overview)
- [Microsoft Authenticator アプリ](concept-authentication-authenticator-app.md)
- [FIDO2 セキュリティ キー (プレビュー)](concept-authentication-passwordless.md#fido2-security-keys)
- [OATH ハードウェア トークン (プレビュー)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
- [OATH ソフトウェア トークン](concept-authentication-oath-tokens.md#oath-software-tokens)
- [SMS による認証](concept-authentication-phone-options.md#mobile-phone-verification)
- [音声通話の確認](concept-authentication-phone-options.md)

テナントで使用される認証方法を選択するときには、これらの方法のセキュリティと使いやすさを考慮してください。

![適切な認証方法の選択](media/concept-authentication-methods/authentication-methods.png)

これらの方法の強度とセキュリティ、およびそれらの動作の詳細については、次のリソースを参照してください。

- [Azure Active Directory で使用できる認証方法と検証方法](concept-authentication-methods.md)
- [ビデオ: 組織の安全を維持するために適切な認証方法を選択する](https://youtu.be/LB2yj4HSptc)

この [PowerShell スクリプト](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/)を使用すると、ユーザーの MFA 構成を分析し、適切な MFA 認証方法を提案できます。 

最適な柔軟性と使いやすさを実現するには、Microsoft Authenticator アプリを使用してください。 この認証方法は、パスワードレス、MFA のプッシュ通知、OATH コードなど、最適なユーザー エクスペリエンスと複数のモードを提供します。 Microsoft Authenticator アプリは、米国国立標準技術研究所 (NIST) の [Authenticator Assurance Level 2 の要件](../standards/nist-authenticator-assurance-level-2.md)にも対応しています。

テナントで使用できる認証方法を制御できます。 たとえば、SMS などの安全性の最も低い方法をブロックすることができます。

| 認証方法    | 管理方法 | Scoping |
|-----------------------|-------------|---------|
| Microsoft Authenticator (プッシュ通知とパスワードレスの電話によるサインイン)    | MFA の設定または
認証方法ポリシー | Authenticator のパスワードレスの電話によるサインインのスコープをユーザーとグループに設定できます |
| FIDO2 セキュリティ キー | 認証方法ポリシー | ユーザーとグループにスコープを設定できます |
| ソフトウェアまたはハードウェアの OATH トークン | MFA の設定 |     |
| SMS による認証 | MFA の設定 | プライマリ認証の SMS サインインを認証ポリシーで管理します。    SMS サインインのスコープをユーザーとグループに設定できます。 |
| 音声通話 | 認証方法ポリシー |       |


## <a name="plan-conditional-access-policies"></a>条件付きアクセス ポリシーを計画する

Azure AD MFA は、条件付きアクセス ポリシーを使用して適用されます。 これらのポリシーを使用すると、セキュリティのために必要な場合はユーザーに多要素認証を要求し、不要な場合はユーザーに対して何も行わないようにすることができます。

![概念的な条件付きアクセスのプロセス フロー](media/howto-mfa-getstarted/conditional-access-overview-how-it-works.png)

Azure portal では、 **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** で条件付きアクセス ポリシーを構成します。

条件付きアクセス ポリシーの作成の詳細については、[ユーザーが Azure portal にサインインするときに Azure AD MFA を要求する条件付きアクセス ポリシー](tutorial-enable-azure-mfa.md)に関するページを参照してください。 これにより、以下の知識が得られます。

- ユーザー インターフェイスに慣れる。
- 条件付きアクセスのしくみについて第一印象を得る。

Azure AD の条件付きアクセスのデプロイに関するエンドツーエンドのガイドについては、[条件付きアクセスのデプロイ計画](../conditional-access/plan-conditional-access.md)に関するページを参照してください。

### <a name="common-policies-for-azure-ad-mfa"></a>Azure AD MFA の一般的なポリシー

Azure AD MFA が必要となる一般的なユース ケースを次に示します。

- [管理者](../conditional-access/howto-conditional-access-policy-admin-mfa.md)のため
- [特定のアプリケーション](tutorial-enable-azure-mfa.md)用
- [すべてのユーザー](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)に対して
- [Azure の管理](../conditional-access/howto-conditional-access-policy-azure-management.md)のため
- [信頼されていないネットワークの場所](../conditional-access/untrusted-networks.md)から

### <a name="named-locations"></a>ネームド ロケーション

条件付きアクセス ポリシーを管理するには、条件付きアクセス ポリシーの場所の条件によって、アクセス制御設定をユーザーのネットワークの場所に関連付けることができます。 IP アドレスの範囲または国や地域の論理グループを作成できるように、[ネームド ロケーション](../conditional-access/location-condition.md)を使用することをお勧めします。 これにより、そのネームド ロケーションからのサインインをブロックする、すべてのアプリ用のポリシーが作成されます。 管理者はこのポリシーから必ず除外してください。

### <a name="risk-based-policies"></a>リスクベースのポリシー

組織で [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) を使用してリスクの徴候を検出している場合は、ネームド ロケーションではなく、[リスクベースのポリシー](../identity-protection/howto-identity-protection-configure-risk-policies.md)を使用することを検討してください。 ID 侵害の脅威があるときにパスワードの変更を強制するポリシー、または資格情報の漏洩、匿名 IP アドレスからのサインインなどのイベントによってサインインが[危険である](../identity-protection/overview-identity-protection.md#risk-detection-and-remediation)と見なされた場合に多要素認証を要求するポリシーを作成できます。 

リスク ポリシーには次のものが含まれます。

- [すべてのユーザーが Azure AD MFA に登録することを必須にする](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
- [リスクの高いユーザーのパスワードの変更を必須にする](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)
- [サインインのリスクが中以上のユーザーに対して MFA を必須にする](../identity-protection/howto-identity-protection-configure-risk-policies.md#enable-policies)

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>ユーザーをユーザーごとの MFA から条件付きアクセス ベースの MFA に変換する

ユーザーごとの MFA の使用が有効になっていたユーザーが Azure AD Multi-Factor Authentication を強制された場合は、次の PowerShell が、条件付きアクセス ベースの Azure AD Multi-Factor Authentication への変換に役立ちます。

この PowerShell を ISE ウィンドウで実行するか、`.PS1` ファイルとして保存し、ローカルで実行します。 この操作は、[MSOnline モジュール](/powershell/module/msonline#msonline)を使用することによってのみ実行できます。 

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {
    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )
    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }
        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}
# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

## <a name="plan-user-session-lifetime"></a>ユーザー セッションの有効期間を計画する

MFA のデプロイを計画するときには、ユーザーに入力を求める頻度を考慮することが重要です。 ユーザーに資格情報を求めることはしばしば目的にかなっているように思われますが、不足の結果に終わる可能性があります。 考えることなしに資格情報を入力するようにユーザーが訓練されている場合、悪意のある資格情報プロンプトに情報を意図せず渡してしまうことがあります。
Azure AD には、再認証を必要とする頻度を決定する複数の設定あります。 ビジネスおよびユーザーのニーズを理解し、環境が最適なバランスとなる設定を構成してください。

特定のビジネスのユース ケースでのみサインイン頻度ポリシーを使用して、エンド ユーザーのエクスペリエンスを向上させ、セッションの有効期間を短縮するために、プライマリ更新トークン (PRT) を持つデバイスを使用することをお勧めします。

詳細については、「[再認証プロンプトを最適化し、Azure AD MFA のセッションの有効期間について理解する](concepts-azure-multi-factor-authentication-prompts-session-lifetime.md)」を参照してください。

## <a name="plan-user-registration"></a>ユーザーの登録を計画する

MFA のすべてのデプロイでの主要な手順は、MFA を使用するようにユーザーに登録してもらうことです。 音声や SMS などの認証方法では事前登録が可能ですが、Authenticator アプリのような他の認証方法ではユーザーによる操作が必要となります。 管理者は、ユーザーが方法を登録する方法を決定する必要があります。 

### <a name="combined-registration-for-sspr-and-azure-ad-mfa"></a>SSPR と Azure AD MFA に対する統合された登録
Azure AD MFA と [Azure AD セルフサービス パスワード リセット (SSPR)](concept-sspr-howitworks.md) を[組み合わせた登録エクスペリエンス](howto-registration-mfa-sspr-combined.md)を使用することをお勧めします。 SSPR では、ユーザーは、Azure AD MFA に使用するのと同じ方法を使用して、セキュリティで保護された方法でパスワードをリセットすることができます。 組み合わされた登録は、エンド ユーザーにとっては 1 ステップです。

### <a name="registration-with-identity-protection"></a>Identity Protection を使用する登録
Azure AD Identity Protection は、Azure AD MFA のシナリオに対して、登録ポリシーと、自動化されたリスクの検出と修復のポリシーの両方の点で寄与します。 ID 侵害の脅威があるときにパスワードの変更を強制するポリシー、またはサインインにリスクがあると認められるときに MFA を要求するポリシーを作成できます。
Azure AD Identity Protection を使用する場合は、ユーザーが対話形式で次にサインインするときに登録を求めるように、[Azure AD MFA 登録ポリシーを構成](../identity-protection/howto-identity-protection-configure-mfa-policy.md)します。

### <a name="registration-without-identity-protection"></a>Identity Protection を使用しない登録
Azure AD Identity Protection を有効にするライセンスがない場合、ユーザーは、次にサインインで MFA が必要となったときに登録を求められます。 ユーザーに MFA を使用するように求めるには、条件付きアクセス ポリシーを使用して、HR システムなどの頻繁に使用されるアプリケーションを対象にすることができます。 ユーザーのパスワードが侵害された場合は、それを MFA の登録のために使用して、ユーザーのアカウントを制御することができます。 そのため、信頼されたデバイスと場所を必要とする[条件付きアクセス ポリシーでセキュリティ登録プロセスを保護する](../conditional-access/howto-conditional-access-policy-registration.md)ことをお勧めします。 また、[一時アクセス パス](howto-authentication-temporary-access-pass.md)も要求することにより、このプロセスをさらにセキュリティで保護することができます。 管理者によって発行される期間限定のパスコードは、強力な認証の要件を満たし、パスワードレスの方法を含む他の認証方法をオンボードするために使用できます。

### <a name="increase-the-security-of-registered-users"></a>登録されたユーザーのセキュリティを強化する
SMS または音声通話を使用した MFA に登録されているユーザーがいる場合は、Microsoft Authenticator アプリなどのより安全な方法に移行できます。 Microsoft では、ユーザーにサインイン時に Microsoft Authenticator アプリのセットアップを求めることができるようにする機能のパブリック プレビューを提供しています。 これらのセットアップの要請はグループ別に設定して、セットアップを求めるユーザーを制御できます。これにより、対象を限定したキャンペーンでユーザーをより安全な方法に移行できます。 

### <a name="plan-recovery-scenarios"></a>復旧シナリオを計画する 
既に説明したように、ユーザーが複数の MFA の方法に登録するようにして、1 つの方法が使用できない場合にバックアップがあるようにします。 ユーザーに使用可能なバックアップの方法がない場合は、次のようにすることができます。 

- 自分の認証方法を管理できるように、それらのユーザーに一時アクセス パスを提供します。 また、リソースに一時的にアクセスできる一時アクセス パスを提供することもできます。 
- 管理者としてそれらのユーザーの方法を更新します。 これを行うには、Azure portal でユーザーを選択し、[認証方法] を選択して、方法を更新します。
ユーザーへの伝達

予定されている変更、Azure AD MFA の登録要件、必要なユーザー アクションについて、ユーザーに通知することが重要です。 通知の草案として役立つ[通信テンプレート](https://aka.ms/mfatemplates)と[エンドユーザー文書](https://support.microsoft.com/account-billing/set-up-your-security-info-from-a-sign-in-prompt-28180870-c256-4ebf-8bd7-5335571bf9a8)が用意されています。 ユーザーを [https://myprofile.microsoft.com](https://myprofile.microsoft.com/) に誘導し、そのページの **[セキュリティ情報]** リンクを選択して登録してもらいます。

## <a name="plan-integration-with-on-premises-systems"></a>オンプレミスのシステムとの統合を計画する

Azure AD に対して直接認証し、最新の認証 (WS-Fed、SAML、OAuth、OpenID Connect) が使用されるアプリケーションでは、条件付きアクセス ポリシーを使用できます。
一部の従来のアプリケーションとオンプレミスのアプリケーションでは、Azure AD に対して直接認証が行われず、Azure AD MFA を使用するには追加の手順が必要となります。 Azure AD アプリケーション プロキシまたは[ネットワーク ポリシー サービス](/windows-server/networking/core-network-guide/core-network-guide#BKMK_optionalfeatures)を使用してそれらを統合できます。

### <a name="integrate-with-ad-fs-resources"></a>AD FS リソースとの統合

Active Directory フェデレーション サービス (AD FS) でセキュリティ保護されているアプリケーションを Azure AD に移行することをお勧めします。 ただし、これらを Azure AD に移行する準備ができていない場合は、Azure MFA アダプターを AD FS 2016 以降で使用できます。
組織が Azure AD とフェデレーションされている場合は、オンプレミスとクラウドの両方で、[AD FS リソースを使用した認証プロバイダーとして Azure AD MFA を構成する](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa)ことができます。  

### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS クライアントと Azure AD MFA

RADIUS 認証を使用しているアプリケーションの場合は、クライアント アプリケーションを最新のプロトコル (SAML、Open ID Connect、Azure AD 上の OAuth など) に移行することをお勧めします。 アプリケーションを更新できない場合は、[Azure MFA 拡張機能を持つネットワーク ポリシー サーバー (NPS)](howto-mfa-nps-extension.md) をデプロイできます。 ネットワーク ポリシー サーバー (NPS) 拡張機能は、RADIUS ベースのアプリケーションと Azure AD MFA の間のアダプターとして機能し、認証の 2 番目の要素が提供されます。

#### <a name="common-integrations"></a>一般的な統合

多くのベンダーでアプリケーションの SAML 認証がサポートされるようになりました。 可能な場合は、これらのアプリケーションを Azure AD とフェデレーションし、条件付きアクセスを使用して MFA を適用することをお勧めします。 ベンダーが先進認証をサポートしていない場合は、NPS 拡張機能を使用できます。
一般的な RADIUS クライアントの統合には、[リモート デスクトップ ゲートウェイ](howto-mfa-nps-extension-rdg.md)や [VPN サーバー](howto-mfa-nps-extension-vpn.md)などのアプリケーションが含まれます。 

その他の統合を次に示します。

- Citrix Gateway

  [Citrix Gateway](https://docs.citrix.com/en-us/advanced-concepts/implementation-guides/citrix-gateway-microsoft-azure.html#microsoft-azure-mfa-deployment-methods) では、RADIUS と NPS の両方の拡張機能の統合と、SAML 統合がサポートされています。

- Cisco VPN
  - Cisco VPN では、[SSO の RADIUS 認証と SAML 認証](../saas-apps/cisco-anyconnect.md)の両方がサポートされています。
  - RADIUS 認証から SAML に移行すると、NPS 拡張機能をデプロイすることなく、Cisco VPN を統合できます。

- すべての VPN

## <a name="deploy-azure-ad-mfa"></a>Azure AD MFA をデプロイする

MFA のロールアウト計画には、パイロット デプロイと、それに続くサポート キャパシティ内でのデプロイ ウェーブが含まれる必要があります。 条件付きアクセス ポリシーをパイロット ユーザーの小さなグループに適用することによってロールアウトを開始します。 パイロット ユーザー、使用されたプロセス、および登録動作への影響を評価した後、ポリシーにグループを追加するか、既存のグループにユーザーを追加することができます。

次の手順に従います。

1. 必要な前提条件を満たします
1. 選択した認証方法を構成します
1. 条件付きアクセス ポリシーを構成する
1. セッションの有効期間設定を構成します
1. Azure AD MFA 登録ポリシーを構成します 

## <a name="manage-azure-ad-mfa"></a>Azure AD MFA を管理する
このセクションでは、Azure AD MFA のレポートとトラブルシューティングに関する情報を提供します。

### <a name="reporting-and-monitoring"></a>レポートと監視

Azure AD には、技術的な分析情報とビジネス上の分析情報の提供、デプロイの進行状況の追跡、ユーザーが MFA でのサインインに成功したかどうかの確認を行うためのレポートがあります。 ビジネスおよび技術アプリケーションの所有者に、組織の要件に基づいてこれらのレポートの所有権を引き受けさせ、レポートを使用させます。

[認証方法アクティビティのダッシュボード](howto-authentication-methods-activity.md)を使用すると、組織全体での認証方法の登録と使用を監視できます。 これにより、登録中の方法や各方法の使用状況を把握できます。

#### <a name="sign-in-report-to-review-mfa-events"></a>MFA イベントを確認するためのサインイン レポート

Azure AD サインイン レポートには、ユーザーが多要素認証を求められたときのイベントの認証詳細と、条件付きアクセス ポリシーが使用されているかどうかが含まれています。 また、MFA に登録されているユーザーのレポートを作成するために PowerShell を使用することもできます。 

NPS 拡張機能と AD FS のログは、 **[セキュリティ]**  >  **[MFA]**  >  **[アクティビティ レポート]** から表示できます。

詳細およびその他の MFA レポートについては、[Azure AD Multi-Factor Authentication イベントの確認](howto-mfa-reporting.md#view-the-azure-ad-sign-ins-report)に関する記事を参照してください。

### <a name="troubleshoot-azure-ad-mfa"></a>Azure AD MFA のトラブルシューティング
一般的な問題に関する [Azure AD MFA のトラブルシューティング](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

[その他の ID 機能をデプロイする](../fundamentals/active-directory-deployment-plans.md)