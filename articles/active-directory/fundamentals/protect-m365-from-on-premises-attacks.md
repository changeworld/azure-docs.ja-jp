---
title: オンプレミスの攻撃から Microsoft 365 を保護する
description: オンプレミスの攻撃から Microsoft 365 が確実に影響を受けないようにする方法に関するガイダンス
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/22/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d64552520db28b3fe38cc354a30ee5470ee091
ms.sourcegitcommit: 31cfd3782a448068c0ff1105abe06035ee7b672a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2021
ms.locfileid: "98060684"
---
# <a name="protecting-microsoft-365-from-on-premises-attacks"></a>オンプレミスの攻撃から Microsoft 365 を保護する

多くのお客様は、プライベートな企業ネットワークを Microsoft 365 に接続して、ユーザー、デバイス、アプリケーションがベネフィットを得られるようにしています。 しかし、このようなプライベート ネットワークのセキュリティを侵害できる、数多くの明確に文書化された方法があります。 Microsoft 365 は多くの組織にとって "神経系" として機能するため、侵害されたオンプレミス インフラストラクチャから保護することが不可欠です。

この記事では、オンプレミスの侵害から Microsoft 365 のクラウド環境を保護するためにシステムを構成する方法について説明します。 主に注目するのは、Azure AD テナントの構成設定、Azure AD テナントをオンプレミス システムに安全に接続する方法、およびオンプレミスの侵害からクラウド システムが保護される方法でシステムを運用するために必要なトレードオフです。

このガイダンスを実装して、Microsoft 365 のクラウド環境をセキュリティで保護することを強くお勧めします。
> [!NOTE]
> この記事は、もともとブログ記事として公開されたものです。 長期の公開と保守のため、こちらに移動されました。 <br>
この記事のオフライン バージョンを作成するには、お使いのブラウザーの機能を使用して PDF ファイルに出力してください。 更新については、ときどきこちらをご確認ください。

## <a name="primary-threat-vectors-from-compromised-on-premises-environments"></a>セキュリティが侵害されたオンプレミス環境からの主要な脅威ベクトル


Microsoft 365 クラウド環境は、広範な監視とセキュリティのインフラストラクチャからベネフィットを受けています。 世界中のトラフィックに対して機械学習と人知を使用することにより、攻撃を速やかに検出し、ほぼリアルタイムで再構成することができます。 オンプレミスのインフラストラクチャを Microsoft 365 に接続するハイブリッド デプロイでは、多くの組織で、重要な認証およびディレクトリ オブジェクトの状態管理の決定のため、オンプレミスのコンポーネントに信頼が委任されます。
残念ながら、オンプレミス環境が侵害された場合、これらの信頼関係により、攻撃者は Microsoft 365 環境を侵害できるようになります。

2 つの主要な脅威ベクトルは、**フェデレーションの信頼関係** と **アカウントの同期** です。 どちらのベクトルによっても、攻撃者にクラウドへの管理アクセスが許可されます。

* **フェデレーションの信頼関係** (SAML 認証など) は、オンプレミスの ID インフラストラクチャを介して Microsoft 365 に対する認証を行うために使用されます。 SAML トークン署名証明書が侵害された場合、フェデレーションにより、その証明書を持つすべてのユーザーは、クラウド内の任意のユーザーを偽装できるようになります。 **可能な場合は、Microsoft 365 に対する認証のためのフェデレーションの信頼関係を無効にすることをお勧めします。**

* **アカウントの同期** を使用すると、特権を持つユーザー (資格情報を含む) や、Microsoft 365 での管理者特権を付与されたグループを変更できます。 直接、または信頼されたロールやグループに含めることにより、**同期されたオブジェクトが Microsoft 365 のユーザーを超える権限を保持しないようにすることをお勧めします**。 これらのオブジェクトに、信頼されたクラウドのロールまたはグループにおいて、確実に直接または入れ子の割り当てがないようにします。

## <a name="protecting-microsoft-365-from-on-premises-compromise"></a>オンプレミスのセキュリティ侵害から Microsoft 365 を保護する


上で説明した脅威ベクトルに対処するには、以下に示す原則に従うことをお勧めします。

![Microsoft 365 の保護に関する参照アーキテクチャ ](media/protect-m365/protect-m365-principles.png)

*  **Microsoft 365 管理者アカウントを完全に分離します。** それらは次のようになっている必要があります

    * Azure AD で管理されている。

     * 多要素認証 (MFA) で認証される。

     *  Azure AD の条件付きアクセスによってセキュリティ保護されている。

     *  Azure マネージド ワークステーションを使用してのみアクセスされる。

これらは制限付きの使用アカウントです。 **これらは、Microsoft 365 で管理者特権を持つオンプレミス アカウントであってはなりません。** 詳細については、[Microsoft 365 の管理者ロールの概要](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide)に関するページを参照してください。
また、[Azure Active Directory での Microsoft 365 用のロール](../roles/m365-workload-docs.md)に関するページも参照してください。

*  **Microsoft 365 からデバイスを管理します。** Azure AD Join とクラウドベースのモバイル デバイス管理 (MDM) を使用して、オンプレミスのデバイス管理インフラストラクチャへの依存関係を排除します。これは、デバイスとセキュリティ制御を侵害するおそれがあるものです。

* **Microsoft 365 に対する昇格された特権を、オンプレミスのアカウントが持たないようにします。**
    NTLM、LDAP、または Kerberos 認証を必要とするオンプレミス アプリケーションにアクセスするアカウントには、組織のオンプレミス ID インフラストラクチャ内のアカウントが必要です。 サービス アカウントも含めて、これらのアカウントが特権のあるクラウドのロールまたはグループに含まれていないこと、およびこれらのアカウントへの変更により、クラウド環境の整合性が影響を受けないことを確実にします。 特権のあるオンプレミス ソフトウェアが、Microsoft 365 の特権のあるアカウントまたはロールに影響を与えることができないようにする必要があります。

*  **Azure AD クラウド認証** を使用して、オンプレミスの資格情報への依存関係を排除します。 常に、Windows Hello、FIDO、Microsoft Authenticator、Azure AD MFA などの強力な認証を使用します。

## <a name="specific-recommendations"></a>具体的な推奨事項


以下のセクションでは、上で説明した原則を実装する方法についての具体的なガイダンスを提供します。

### <a name="isolate-privileged-identities"></a>特権 ID を分離する


Azure AD では、管理者などの特権ロールを持つユーザーが、環境の他の部分の構築と管理に対する信頼の根幹になります。 セキュリティ侵害の影響を最小限に抑えるため、次のことを実装します。

* Azure AD と Microsoft 365 の特権ロールには、クラウド専用アカウントを使用します。

* Microsoft 365 と Azure AD を管理するための特権アクセス用に、[特権アクセス デバイス](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles)をデプロイします。

*  特権ロールを持つすべての人間アカウントへの Just-In-Time (JIT) アクセス用に [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) をデプロイし、ロールのアクティブ化に強力な認証が必要であるようにします。

* 管理者ロールには、[タスクを実行できる最小限の特権](../roles/delegate-by-task.md)を持つロールを提供します。

* 同時に委任と複数のロールが含まれる、より充実したロール割り当てエクスペリエンスを有効にするには、Azure AD セキュリティ グループまたは Microsoft 365 グループ (総称して "クラウド グループ") を使用し、[ロールベースのアクセス制御を有効にする](../roles/groups-assign-role.md)ことを検討します。 また、[管理単位](../roles/administrative-units.md)を使用して、ロールの範囲を組織の一部に限定することもできます。

* [緊急アクセス アカウント](../roles/security-emergency-access.md)をデプロイし、資格情報の保存にオンプレミスのパスワード コンテナーを使わないようにします。

詳細については、このトピックについての詳細なガイダンスが記載されている「[特権アクセスのセキュリティ保護](https://aka.ms/SPA)」を参照してください。 [Azure AD の管理者向けのセキュリティで保護されたアクセス プラクティス](../roles/security-planning.md)に関するページも参照してください。

### <a name="use-cloud-authentication"></a>クラウド認証を使用する 

資格情報は、主要な攻撃ベクトルです。 資格情報のセキュリティ保護を高めるには、次の方法を実装します。

* [パスワードなしの認証をデプロイする](../authentication/howto-authentication-passwordless-deployment.md): パスワードのない資格情報をデプロイすることで、パスワードの使用を可能な限り減らします。 これらの資格情報は、クラウド内でネイティブに管理および検証されます。 次の中から選択します。

   * [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/passwordless-strategy)

   * [Authenticator アプリ](../authentication/howto-authentication-passwordless-phone.md)

   * [FIDO2 セキュリティ キー](../authentication/howto-authentication-passwordless-security-key-windows.md)

* [Multi-Factor Authentication を展開する](https://aka.ms/deploymentplans/mfa): [Azure AD MFA を使用して複数の強力な資格情報](../fundamentals/resilience-in-credentials.md)をプロビジョニングします。 このようにすると、クラウド リソースへのアクセスには、操作可能なオンプレミスのパスワードに加えて、Azure AD で管理される資格情報が必要になります。

   * 詳細については、[Azure Active Directory での回復性があるアクセス制御管理戦略の作成](https://aka.ms/resilientaad)に関するページを参照してください。

**制限事項とトレードオフ**

* ハイブリッド アカウントのパスワードの管理には、パスワード保護エージェントやパスワード ライトバック エージェントなどのハイブリッド コンポーネントが必要です。 オンプレミスのインフラストラクチャが侵害された場合、攻撃者はこれらのエージェントが存在するコンピューターを制御できます。 これによってクラウド インフラストラクチャが侵害されることはありませんが、これらのコンポーネントがオンプレミスで侵害されるのをクラウド アカウントで保護することはできません。

*  オンプレミスの AD パスワード ポリシーによってこれが軽減されるという前提に基づき、Active Directory から同期されるオンプレミスのアカウントは Azure AD で期限切れにならないようにマークされます。 オンプレミスの AD が侵害され、AD Connect からの同期を無効にする必要がある場合は、[EnforceCloudPasswordPolicyForPasswordSyncedUsers](../hybrid/how-to-connect-password-hash-synchronization.md) オプションを設定する必要があります。

## <a name="provision-user-access-from-the-cloud"></a>クラウドからのユーザー アクセスをプロビジョニングする

プロビジョニングとは、アプリケーションまたは ID プロバイダーにユーザー アカウントとグループを作成することです。

![プロビジョニング アーキテクチャの図](media/protect-m365/protect-m365-provision.png)

* **クラウド HR アプリから Azure AD にプロビジョニングする:** これにより、クラウド HR アプリから Azure AD への Joiner-Mover-Leaver サイクルを中断することなく、オンプレミスの侵害を分離することができます。

* **クラウド アプリケーション:** 可能であれば、オンプレミスのプロビジョニング ソリューションではなく、[Azure AD アプリのプロビジョニング](../app-provisioning/user-provisioning.md)をデプロイします。 これにより、オンプレミスの侵害によって一部の SaaS アプリが悪意のあるユーザー プロファイルで侵害されるのを、防ぐことができます。 

* **Azure Active Directory External Identities:** [Azure AD B2B Collaboration](../external-identities/what-is-b2b.md) を使用します。
    これにより、パートナー、顧客、サプライヤーとの外部コラボレーションのための、オンプレミス アカウントでの依存関係が減ります。 他の ID プロバイダーとの直接フェデレーションを慎重に評価します。 次の方法で B2B ゲスト アカウントを制限することをお勧めします。

   *  ディレクトリ内の参照グループおよびその他のプロパティへのゲスト アクセスを制限します。 外部コラボレーション設定を使用して、ゲストが所属していないグループを読み取る機能を制限します。 

    *   Azure portal へのアクセスをブロックします。 まれに必要となる例外を作成できます。  すべてのゲストと外部ユーザーを含む条件付きアクセス ポリシーを作成してから、[アクセスをブロックするポリシーを実装します](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management.md)。 

* **切断されたフォレスト:** [Azure AD クラウド プロビジョニング](../cloud-provisioning/what-is-cloud-provisioning.md)を使用します。 これにより、切断されたフォレストに接続できるようになり、オンプレミスの侵害の影響を広げるおそれがあるフォレスト間の接続や信頼関係を確立する必要がなくなります。 * 
 
**制限事項とトレードオフ:**

* ハイブリッド アカウントのプロビジョニングに使用する場合、クラウド HR システムからの Azure AD は、オンプレミスの同期に依存して、AD から Azure AD へのデータ フローを完了します。 同期が中断された場合、新しい従業員レコードを Azure AD で利用できなくなります。

## <a name="use-cloud-groups-for-collaboration-and-access"></a>コラボレーションとアクセスにクラウド グループを使用する

クラウド グループを使用すると、オンプレミス インフラストラクチャからのコラボレーションとアクセスを切り離すことができます。

* **コラボレーション:** 最新のコラボレーションのために Microsoft 365 グループと Microsoft Teams を使用します。 オンプレミスの配布リストの使用を停止し、[Outlook の Microsoft 365 グループに配布リストをアップグレードします](https://docs.microsoft.com/office365/admin/manage/upgrade-distribution-lists?view=o365-worldwide)。

* **アクセス:** Azure AD セキュリティ グループまたは Microsoft 365 グループを使用して、Azure AD 内のアプリケーションへのアクセスを承認します。
* **Office 365 のライセンス:** グループベースのライセンスを使用し、クラウド専用グループを使用して Office 365 にプロビジョニングします。 これにより、グループ メンバーシップの制御がオンプレミスのインフラストラクチャから切り離されます。

オンプレミスの侵害からメンバーシップが引き継がれるのを避けるため、アクセスに使用されるグループの所有者は、特権 ID と見なされる必要があります。
引き継ぎには、オンプレミスでのグループ メンバーシップの直接操作、または Microsoft 365 での動的グループ メンバーシップに影響する可能性があるオンプレミスの属性の操作が含まれます。

## <a name="manage-devices-from-the-cloud"></a>クラウドからデバイスを管理する


Azure AD の機能を使用して、デバイスを安全に管理します。

-   **Windows 10 ワークステーションを使用する:** MDM ポリシーを使用して、[Azure AD Join を使用したデバイスをデプロイ](../devices/azureadjoin-plan.md)します。 完全に自動化されたプロビジョニング エクスペリエンスを実現するには、[Windows Autopilot](https://docs.microsoft.com/mem/autopilot/windows-autopilot) を有効にします。

    -   Windows 8.1 以前のコンピューターを廃止します。

    -   サーバー OS マシンをワークステーションとしてデプロイしないでください。

    -   すべてのデバイス管理ワークロードの権限のソースとして [Microsoft Intune](https://www.microsoft.com/en/microsoft-365/enterprise-mobility-security/microsoft-intune) を使用します。

-   Microsoft 365 と Azure AD を管理するための特権アクセス用に、[**特権アクセス デバイスをデプロイ**](https://docs.microsoft.com/security/compass/privileged-access-devices#device-roles-and-profiles)します。

 ## <a name="workloads-applications-and-resources"></a>ワークロード、アプリケーション、リソース 

-   **オンプレミスの SSO システム:** オンプレミスのフェデレーションと Web アクセス管理インフラストラクチャを廃止し、Azure AD を使用するようにアプリケーションを構成します。  

-   **最新の認証プロトコルをサポートする SaaS および LOB アプリケーション:** [シングル サインオン用に Azure AD を使用します](../manage-apps/what-is-single-sign-on.md)。 認証に Azure AD を使用するよう構成されているアプリが多いほど、オンプレミスが侵害された場合のリスクが小さくなります。


* **レガシ アプリケーション** 

   * 先進認証がサポートされていないレガシ アプリケーションに対する認証、承認、リモート アクセスは、[Azure AD アプリケーション プロキシ](../manage-apps/application-proxy.md)経由で有効にすることができます。また、[セキュリティで保護されたハイブリッド アクセス パートナー統合](../manage-apps/secure-hybrid-access.md)を使用して、ネットワークまたはアプリケーション配信コントローラー ソリューションによって有効にすることもできます。   

   * 先進認証をサポートしている VPN ベンダーを選択し、その認証を Azure AD と統合します。 オンプレミスが侵害された場合は、Azure AD を使用して VPN を無効にすることにより、アクセスを無効にしたりブロックしたりすることができます。

*  **アプリケーションとワークロード サーバー**

   * サーバーを必要としたアプリケーションまたはリソースは、Azure IaaS に移行して [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview) (Azure AD DS) を使用することにより、オンプレミスの AD での信頼と依存関係を切り離すことができます。 この分離を実現するには、Azure AD DS に使用される仮想ネットワークが、企業ネットワークに接続できないようにする必要があります。

   * [資格情報の階層化](https://aka.ms/TierModel)に関するガイダンスに従ってください。 通常、アプリケーション サーバーは階層 1 の資産と見なされます。

 ## <a name="conditional-access-policies"></a>条件付きアクセス ポリシー

Azure AD の条件付きアクセスを使用して、信号を解釈し、それに基づいて認証の決定を行います。 詳細については、[条件付きアクセスのデプロイ計画](https://aka.ms/deploymentplans/ca)に関するページを参照してください。

* [レガシ認証プロトコル](../fundamentals/auth-sync-overview.md): 可能な場合は常に、条件付きアクセスを使用して、[レガシ認証プロトコルをブロック](../conditional-access/howto-conditional-access-policy-block-legacy.md)します。 また、アプリケーション固有の構成を使用して、アプリケーション レベルでレガシ認証プロトコルを無効にします。

   * [Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/disable-basic-authentication-in-exchange-online#how-basic-authentication-works-in-exchange-online) と [SharePoint Online](https://docs.microsoft.com/powershell/module/sharepoint-online/set-spotenant?view=sharepoint-ps) に固有の詳細を参照してください。

* 推奨される [ID とデバイスのアクセス構成](https://docs.microsoft.com/microsoft-365/security/office-365-security/identity-access-policies?view=o365-worldwide)を実装します。

* 条件付きアクセスが含まれないバージョンの Azure AD を使用している場合は、[Azure AD のセキュリティの既定値群](../fundamentals/concept-fundamentals-security-defaults.md)を使用していることを確認します。

   * Azure AD の機能のライセンスに関する詳細については、[Azure AD 価格ガイド](https://azure.microsoft.com/pricing/details/active-directory/)に関するページを参照してください。

## <a name="monitoring"></a>監視 

オンプレミスの侵害から Microsoft 365 を保護するように環境を構成したら、環境を[予防的に監視](../reports-monitoring/overview-monitoring.md)します。
### <a name="scenarios-to-monitor"></a>監視するシナリオ

組織に固有のシナリオに加えて、次の主要なシナリオを監視します。 たとえば、ビジネス クリティカルなアプリケーションとリソースへのアクセスを、予防的に監視する必要があります。

* **疑わしいアクティビティ**: すべての [Azure AD リスク イベント](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection#risk-detection-and-remediation)で、疑わしいアクティビティを監視する必要があります。 [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) は、Azure Security Center とネイティブに統合されています。

   * 場所ベースのシグナルで多くのノイズが検出されないよう、ネットワークの[ネームド ロケーション](../reports-monitoring/quickstart-configure-named-locations.md)を定義します。 
*  **ユーザー/エンティティ行動分析 (UEBA) のアラート** UEBA を使用して、異常検出に関する分析情報を得ます。
   * Microsoft Cloud App Discovery (MCAS) により、[クラウドでの UEBA](https://docs.microsoft.com/cloud-app-security/tutorial-ueba) が提供されます。

   * [Azure ATP からオンプレミスの UEBA を統合する](https://docs.microsoft.com/defender-for-identity/install-step2)ことができます。 MCAS により、Azure AD Identity Protection からのシグナルが読み取られます。 

* **緊急アクセス アカウントのアクティビティ**: [緊急アクセス アカウント](../roles/security-emergency-access.md)を使用したすべてのアクセスを監視し、調査のためにアラートを作成する必要があります。 この監視には次のものを含める必要があります。 

   * サインイン。 

   * 資格情報の管理。 

   * グループ メンバーシップのすべての更新。 

   *    アプリケーションの割り当て。 
* **特権ロールのアクティビティ**: [Azure AD PIM によって生成されるセキュリティ アラート](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-configure-security-alerts?tabs=new#security-alerts)を構成して確認します。
    ユーザーが直接割り当てられたときに常にアラートを生成することで、PIM の外部での特権ロールの直接割り当てを監視します。
* **Azure AD のテナント全体の構成**: テナント全体の構成が変更されたら常に、システムでアラートが生成されるようにする必要があります。 これらには以下のものが含まれますが、限定されはしません
  *  カスタム ドメインの更新  

  * Azure AD B2B の許可およびブロック リストの変更。
  * Azure AD B2B で許可された ID プロバイダー (直接フェデレーションまたはソーシャル ログインによる SAML IDP)。  
  * 条件付きアクセスまたはリスク ポリシーの変更 

* **アプリケーション オブジェクトとサービス プリンシパル オブジェクト**:
   * 条件付きアクセスポリシーが必要になる可能性がある新しいアプリケーションまたはサービス プリンシパル。 

   * サービス プリンシパルに追加された新しい資格情報。
   * アプリケーションの同意アクティビティ。 

* **カスタム ロール**:
   * カスタム ロールの定義の更新。 

   * 新しく作成されたカスタム ロール。 

### <a name="log-management"></a>ログの管理

Azure Sentinel のような SIEM システムなどの一貫したツールセット、一般的なクエリ、調査やフォレンジックのプレイブックを容易にするため、ログのストレージと保持の戦略、設計、実装を定義します。

* **Azure AD ログ** 診断設定、ログの保持、SIEM インジェストなど、一貫したベスト プラクティスに従って生成されたログとシグナルを取り込みます。 ログ戦略には、次の Azure AD ログを含める必要があります。
   * サインイン アクティビティ 

   * 監査ログ 

   * リスク イベント 

Azure AD により、サインイン アクティビティ ログと監査ログのための [Azure Monitor の統合](../reports-monitoring/concept-activity-logs-azure-monitor.md)が提供されます。 リスク イベントは、[Microsoft Graph API](https://aka.ms/AzureADSecuredAzure/32b) を使用して取り込むことができます。 [Azure Monitor ログに Azure AD ログをストリーム配信する](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)ことができます。

* **ハイブリッド インフラストラクチャの OS セキュリティ ログ。** すべてのハイブリッド ID インフラストラクチャ OS ログをアーカイブし、 <br>階層 0 システムとして注意深く監視する必要があります (セキュリティ、外部からのアクセスの影響を考えて)。 これには次のものが含まれます 

   *  Azure AD Connect という単一のソリューションに代わりました。 [Azure AD Connect Health](https://aka.ms/AzureADSecuredAzure/32e) をデプロイし、ID の同期を監視する必要があります。

   *  アプリケーション プロキシ エージェント 


   * パスワード ライトバック エージェント 

   * パスワード保護ゲートウェイ マシン  

   * Azure MFA RADIUS 拡張機能のある NPS 

## <a name="next-steps"></a>次の手順
* [Azure AD を使用して ID およびアクセス管理の回復性を強化する](resilience-overview.md)

* [リソースへの外部アクセスをセキュリティで保護する](secure-external-access-resources.md) 
* [すべてのアプリを Azure AD と統合する](five-steps-to-full-application-integration-with-azure-ad.md)
