---
title: Azure AD のセキュリティで保護されたハイブリッド アクセス | Microsoft Docs
description: この記事では、オンプレミス、パブリッククラウド、またはプライベート クラウドにあるレガシ アプリケーションを Azure AD と統合するためのパートナー ソリューションについて説明します。
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2dcba2b9435cb20cc112da1355cc31cbcb8dff2
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029813"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全なハイブリッド アクセス:Azure Active Directory を使用してレガシ アプリをセキュリティで保護する

次を使用して Azure Active Directory (AD) に接続して、オンプレミスやクラウドでお使いのレガシ認証アプリケーションを保護できるようになりました。

- [Azure AD アプリケーション プロキシ](#secure-hybrid-access-through-azure-ad-application-proxy)

- [セキュリティで保護されたハイブリッド アクセス パートナー](#secure-hybrid-access-through-azure-ad-partner-integrations)

[Azure AD 条件付きアクセス](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)や [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) などの Azure AD 機能を使用し、すべてのアプリケーションでお使いのセキュリティに対する姿勢間の差をなくし、強化することができます。 Azure AD を ID プロバイダー (IDP) として使用することで、[シングル サインオン (SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) や[多要素認証 (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) などの最新の認証および認可方式を使用し、お使いのオンプレミスのレガシ アプリケーションをセキュリティで保護することができます。

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したセキュリティで保護されたハイブリッド アクセス
  
[アプリケーション プロキシ](https://docs.microsoft.com/azure/active-directory/app-proxy/what-is-application-proxy)を使用すると、お使いのオンプレミスの Web アプリケーションに[セキュリティで保護されたリモート アクセス](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application)を提供できます。 ユーザーは VPN を使用する必要はありません。 ユーザーは [SSO](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-config-sso-how-to#how-to-configure-single-sign-on) の後、簡単に任意のデバイスからアプリケーションに接続できるようになります。 サービスとしてのリモート アクセスを提供するアプリケーション プロキシを使用すると、企業ネットワーク外のユーザーに[お使いのオンプレミスのアプリケーションを簡単に発行](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application)できるようになります。 お使いのオンプレミス アプリケーションは変更せずに、自分のクラウド アクセス管理を拡張できます。 次の手順として、「[Azure AD アプリケーション プロキシのデプロイ計画](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-deployment-plan)」に進みます。

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Azure AD パートナー統合を使用したセキュリティで保護されたハイブリッド アクセス  

[Azure AD アプリケーション プロキシ](https://aka.ms/whyappproxy)に加えて、Microsoft では、オンプレミスのアプリケーションや、レガシ認証を使用するアプリケーションへのセキュリティで保護されたアクセスを実現するために、サードパーティのプロバイダーと提携しています。

![アプリ プロキシとパートナーを利用した、セキュリティで保護されたハイブリッド アクセスを示す図](./media/secure-hybrid-access/secure-hybrid-access.png)

以下のパートナーから、アプリケーションごとの条件付きアクセス ポリシーをサポートするための事前構築済みソリューションと、Azure AD との統合に関する詳細なガイダンスが提供されています。 

- [Akamai Enterprise Application Access](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix アプリケーション デリバリー コントローラー (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)  

- [Datawiza Access Broker](datawiza-with-azure-ad.md)

- [F5 Big-IP APM ADC](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [F5 Big-IP APM VPN](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial)

- [Silverfort Authentication Platform](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

以下のパートナーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。 

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
