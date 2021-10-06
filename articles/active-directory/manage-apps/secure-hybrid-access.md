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
ms.openlocfilehash: bc8d5d0b62f75d73bbe8879739069f1d28ce15bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642607"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>安全なハイブリッド アクセス:Azure Active Directory を使用してレガシ アプリをセキュリティで保護する

次を使用して Azure Active Directory (AD) に接続して、オンプレミスやクラウドでお使いのレガシ認証アプリケーションを保護できるようになりました。

- [Azure AD アプリケーション プロキシ](#secure-hybrid-access-through-azure-ad-application-proxy)

- [セキュリティで保護されたハイブリッド アクセス パートナー](#secure-hybrid-access-through-azure-ad-partner-integrations)

[Azure AD 条件付きアクセス](../conditional-access/overview.md)や [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md) などの Azure AD 機能を使用し、すべてのアプリケーションでお使いのセキュリティに対する姿勢間の差をなくし、強化することができます。 Azure AD を ID プロバイダー (IDP) として使用することで、[シングル サインオン (SSO)](what-is-single-sign-on.md) や[多要素認証 (MFA)](../authentication/concept-mfa-howitworks.md) などの最新の認証および認可方式を使用し、お使いのオンプレミスのレガシ アプリケーションをセキュリティで保護することができます。

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシを使用したセキュリティで保護されたハイブリッド アクセス
  
[アプリケーション プロキシ](../app-proxy/what-is-application-proxy.md)を使用すると、お使いのオンプレミスの Web アプリケーションに[セキュリティで保護されたリモート アクセス](../app-proxy/application-proxy-add-on-premises-application.md)を提供できます。 ユーザーは VPN を使用する必要はありません。 ユーザーは [SSO](../app-proxy/application-proxy-config-sso-how-to.md#how-to-configure-single-sign-on) の後、簡単に任意のデバイスからアプリケーションに接続できるようになります。 サービスとしてのリモート アクセスを提供するアプリケーション プロキシを使用すると、企業ネットワーク外のユーザーに[お使いのオンプレミスのアプリケーションを簡単に発行](../app-proxy/application-proxy-add-on-premises-application.md)できるようになります。 お使いのオンプレミス アプリケーションは変更せずに、自分のクラウド アクセス管理を拡張できます。 次の手順として、「[Azure AD アプリケーション プロキシのデプロイ計画](../app-proxy/application-proxy-deployment-plan.md)」に進みます。

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Azure AD パートナー統合を使用したセキュリティで保護されたハイブリッド アクセス  

[Azure AD アプリケーション プロキシ](../app-proxy/what-is-application-proxy.md)に加えて、Microsoft では、オンプレミスのアプリケーションや、レガシ認証を使用するアプリケーションへのセキュリティで保護されたアクセスを実現するために、サードパーティのプロバイダーと提携しています。

![アプリ プロキシとパートナーを利用した、セキュリティで保護されたハイブリッド アクセスを示す図](./media/secure-hybrid-access/secure-hybrid-access.png)

以下のパートナーから、**アプリケーションごとの条件付きアクセス ポリシー** をサポートするための事前構築済みソリューションと、Azure AD との統合に関する詳細なガイダンスが提供されています。 

- [Akamai Enterprise Application Access](../saas-apps/akamai-tutorial.md)

- [Citrix アプリケーション デリバリー コントローラー (ADC)](../saas-apps/citrix-netscaler-tutorial.md)  

- [Datawiza Access Broker](../manage-apps/datawiza-with-azure-ad.md)

- [F5 Big-IP APM ADC](../manage-apps/f5-aad-integration.md)

- [F5 Big-IP APM VPN](../manage-apps/f5-aad-password-less-vpn.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)

- [Silverfort Authentication Platform](../manage-apps/silverfort-azure-ad-integration.md)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

以下のパートナーが、Azure AD との統合のために事前に構築されたソリューションと詳細なガイダンスを提供しています。

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Connect Secure (PCS)](../saas-apps/pulse-secure-pcs-tutorial.md)

- [Pulse Secure Virtual Traffic Manager (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
