---
title: Azure AD のセキュリティで保護されたハイブリッド アクセス | Microsoft Docs
description: この記事では、オンプレミス、パブリッククラウド、またはプライベート クラウドにあるレガシ アプリケーションを Azure AD と統合するためのパートナー ソリューションについて説明します。 アプリ デリバリー コントローラーまたはネットワークを Azure AD に接続することで、レガシ アプリをセキュリティで保護します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174429"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全なハイブリッド アクセス:Azure Active Directory を使用してレガシ アプリをセキュリティで保護する

次を使用して Azure Active Directory (AD) に接続して、オンプレミスやクラウドでお使いのレガシ認証アプリケーションを保護できるようになりました。

- [Azure AD アプリケーション プロキシ](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [既存のお使いのアプリケーション デリバリー コントローラーおよびネットワーク](#sha-through-networking-and-delivery-controllers)

- [仮想プライベートネットワーク (VPN) アプリケーションおよび Software-Defined Perimeter (SDP) アプリケーション](#sha-through-vpn-and-sdp-applications)

Azure AD [条件付きアクセス](../conditional-access/overview.md)や Azure AD [Identity Protection](../identity-protection/overview-identity-protection.md) などの Azure AD 機能を使用し、すべてのアプリケーションでお使いのセキュリティに対する姿勢間の差をなくし、強化することができます。

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したセキュリティで保護されたハイブリッド アクセス (SHA)
  
[アプリケーション プロキシ](./what-is-application-proxy.md)を使用すると、お使いのオンプレミスの Web アプリケーションに[セキュリティで保護されたリモート アクセス](./application-proxy.md)を提供できます。 ユーザーが VPN を使用する必要はありません。 ユーザーは [シングル サインオン](./add-application-portal-setup-sso.md)の後、簡単に任意のデバイスからアプリケーションに接続できるようになります。 サービスとしてのリモート アクセスを提供するアプリケーション プロキシを使用すると、企業ネットワーク外のユーザーに[お使いのオンプレミスのアプリケーションを簡単に発行](./application-proxy-add-on-premises-application.md)できるようになります。 お使いのオンプレミス アプリケーションは変更せずに、自分のクラウド アクセス管理を拡張できます。 次の手順として、「[Azure AD アプリケーション プロキシのデプロイ計画](./application-proxy-deployment-plan.md)」に進みます。

## <a name="azure-ad-partner-integrations"></a>Azure AD のパートナーとの統合

### <a name="sha-through-networking-and-delivery-controllers"></a>ネットワークおよびデリバリー コントローラーを使用した SHA

Microsoft では、[ゼロ トラスト フレームワーク](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)を使用するために、[Azure AD アプリケーション プロキシ](./what-is-application-proxy.md)以外に、サードパーティ プロバイダーと提携しています。 お使いの既存のネットワークとデリバリー コントローラーを使用し、これまでは Azure AD では保護できなかった自分のビジネス プロセスに非常に重要なレガシ アプリケーションを容易に保護できます。 これらのアプリケーションの保護を開始するのに必要なものはすべて、おそらく既に持っていることでしょう。

![ネットワーク パートナーとアプリケーション プロキシを使用した、セキュリティで保護されたハイブリッド アクセスを示す図](./media/secure-hybrid-access/secure-hybrid-access.png)

Azure AD と統合できる、構築済みのソリューションと詳細なガイダンスを提供しているベンダーは次のとおりです。

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix アプリケーション デリバリー コントローラー (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>VPN アプリケーションおよび SDP アプリケーションを使用した SHA

VPN および SDP ソリューションを使用すると、自分の組織のデータを保護しながら、任意の場所で任意のデバイスから自分のエンタープライズ ネットワークに、セキュリティで保護されたアクセスを提供できます。 Azure AD を ID プロバイダー (IDP) として使用することで、Azure AD [シングル サインオン](./what-is-single-sign-on.md)や[多要素認証](../authentication/concept-mfa-howitworks.md)などの最新の認証および承認方法を使用し、お使いのオンプレミスのレガシ アプリケーションをセキュリティで保護することができます。  

![VPN パートナーとアプリ プロキシを使用した、セキュリティで保護されたハイブリッド アクセスを示す図 ](./media/secure-hybrid-access/app-proxy-vpn.png)

以下の VPN ベンダーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

以下の SDP ベンダーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。

- [Datawiza Access Broker](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Silverfort Authentication Platform](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
