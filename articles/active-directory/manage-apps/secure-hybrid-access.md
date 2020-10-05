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
ms.date: 9/10/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d64f7423d537958b6d3c388cb12f23bd2e30e36
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/15/2020
ms.locfileid: "90087090"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全なハイブリッド アクセス:Azure Active Directory を使用してレガシ アプリをセキュリティで保護する

次を使用して Azure Active Directory (AD) に接続して、オンプレミスやクラウドでお使いのレガシ認証アプリケーションを保護できるようになりました。

- [Azure AD アプリケーション プロキシ](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [既存のお使いのアプリケーション デリバリー コントローラーおよびネットワーク](#sha-through-networking-and-delivery-controllers)

- [仮想プライベートネットワーク (VPN) アプリケーションおよび Software-Defined Perimeter (SDP) アプリケーション](#sha-through-vpn-and-sdp-applications)

Azure AD [条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)や Azure AD [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) などの Azure AD 機能を使用し、すべてのアプリケーションでお使いのセキュリティに対する姿勢間の差をなくし、強化することができます。

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したセキュリティで保護されたハイブリッド アクセス (SHA)
  
[アプリケーション プロキシ](https://aka.ms/whyappproxy)を使用すると、お使いのオンプレミスの Web アプリケーションに[セキュリティで保護されたリモート アクセス](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)を提供できます。 ユーザーが VPN を使用する必要はありません。 ユーザーは [シングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-sso)の後、簡単に任意のデバイスからアプリケーションに接続できるようになります。 サービスとしてのリモート アクセスを提供するアプリケーション プロキシを使用すると、企業ネットワーク外のユーザーに[お使いのオンプレミスのアプリケーションを簡単に発行](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)できるようになります。 お使いのオンプレミス アプリケーションは変更せずに、自分のクラウド アクセス管理を拡張できます。 次の手順として、「[Azure AD アプリケーション プロキシのデプロイ計画](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan)」に進みます。

## <a name="azure-ad-partner-integrations"></a>Azure AD のパートナーとの統合

### <a name="sha-through-networking-and-delivery-controllers"></a>ネットワークおよびデリバリー コントローラーを使用した SHA

Microsoft では、[ゼロ トラスト フレームワーク](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)を使用するために、[Azure AD アプリケーション プロキシ](https://aka.ms/whyappproxy)以外に、サードパーティ プロバイダーと提携しています。 お使いの既存のネットワークとデリバリー コントローラーを使用し、これまでは Azure AD では保護できなかった自分のビジネス プロセスに非常に重要なレガシ アプリケーションを容易に保護できます。 これらのアプリケーションの保護を開始するのに必要なものはすべて、おそらく既に持っていることでしょう。

![ネットワーク パートナーとアプリケーション プロキシを使用した、セキュリティで保護されたハイブリッド アクセスを示す図](./media/secure-hybrid-access/secure-hybrid-access.png)

Azure AD と統合できる、構築済みのソリューションと詳細なガイダンスを提供しているベンダーは次のとおりです。

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix アプリケーション デリバリー コントローラー (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>VPN アプリケーションおよび SDP アプリケーションを使用した SHA

VPN および SDP ソリューションを使用すると、自分の組織のデータを保護しながら、任意の場所で任意のデバイスから自分のエンタープライズ ネットワークに、セキュリティで保護されたアクセスを提供できます。 Azure AD を ID プロバイダー (IDP) として使用することで、Azure AD [シングル サインオン](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)や[多要素認証](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)などの最新の認証および承認方法を使用し、お使いのオンプレミスのレガシ アプリケーションをセキュリティで保護することができます。  

![VPN パートナーとアプリ プロキシを使用した、セキュリティで保護されたハイブリッド アクセスを示す図 ](./media/secure-hybrid-access/app-proxy-vpn.png)

Azure AD と統合できる構築済みのソリューションと詳細なガイダンスを提供している VPN および SDP ベンダーは次のとおりです。

• [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

• [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

• [F5 Big-IP APM](https://aka.ms/f5-hybridaccessguide)

• [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

• [Zscaler Private Access (ZPA)](https://aka.ms/zscaler-hybridaccessguide)
