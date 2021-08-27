---
title: Cloudflare Web Application Firewall を使用して Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: アプリケーションを悪意のある攻撃から保護するために Cloudflare Web Application Firewall を使用して Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/24/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f2b4f2e54c046ca7abc2172b8df33f14def902fd
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122767898"
---
# <a name="tutorial-configure-cloudflare-with-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用して Cloudflare を構成する

このサンプル チュートリアルでは、カスタム ドメインを使用して、Azure Active Directory (AD) B2C テナントに対して [Cloudflare Web Application Firewall (WAF)](https://www.cloudflare.com/waf/) ソリューションを有効にする方法について説明します。 Cloudflare WAF は、SQLi や XSS など、脆弱性を悪用することを狙った悪意のある攻撃から組織を保護します。

>[!NOTE]
>この機能はパブリック プレビュー段階にあります。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

- [Cloudflare](https://dash.cloudflare.com/sign-up) アカウント。

## <a name="scenario-description"></a>シナリオの説明

Cloudflare WAF 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C** テナント – テナントで定義されているカスタム ポリシーを使用してユーザーの資格情報を検証する承認サーバー。  ID プロバイダーとも呼ばれます。

- [**Azure Front Door**](../frontdoor/front-door-overview.md) – AzureB2C テナントに対してカスタム ドメインを有効にする役割を担います。 Cloudflare WAF からのすべてのトラフィックは、Azure AD B2C テナントに到着する前に Azure Front Door にルーティングされます。

- **Cloudflare** – 承認サーバーに送信されるすべてのトラフィックを管理する Web アプリケーション ファイアウォール。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

Azure AD B2C でカスタム ドメインを使用するには、Azure Front Door によって提供されるカスタム ドメイン機能を使用する必要があります。 [Azure AD B2C カスタム ドメインを有効にする](./custom-domain.md?pivots=b2c-user-flow)方法を理解します。  

Azure Front Door を使用して Azure AD B2C のカスタム ドメインを正常に構成したら、次に進む前に[カスタム ドメインをテスト](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)します。  

## <a name="onboard-with-cloudflare"></a>Cloudflare を使用してオンボードする

[サインアップ](https://dash.cloudflare.com/sign-up)して Cloudflare アカウントを作成します。 WAF を有効にするには、[Pro SKU](https://www.cloudflare.com/plans/) 以上が必要です。

### <a name="configure-dns"></a>DNS を構成する

1. ドメインに対して WAF を有効にするには、ドメイン id.contosobank.co.uk の例で示しているように、CNAME エントリの DNS コンソールから **プロキシ設定を有効にする** 必要があります。

   ![プロキシ設定の選択方法を示す画像](./media/partner-cloudflare/select-proxy-settings.png)

1. [DNS] ペインの下にある **[プロキシの状態]** オプションを切り替えます。

1. **[プロキシ化]** に切り替えると、オレンジ色に変わります。 最終的な設定は次のようになります。

   ![プロキシ化を選択する方法を示す画像](./media/partner-cloudflare/select-proxied.png)

### <a name="configure-waf"></a>WAF の構成

Cloudflare 設定で [WAF を構成](https://www.cloudflare.com/waf/)します。

### <a name="configure-firewall-rule"></a>ファイアウォール規則の構成

コンソールの最上部ペインにあるファイアウォール オプションを使用して、ファイアウォール規則を追加、更新、または削除します。 たとえば、次のファイアウォール設定では、要求が Azure Front Door に送信される前に、*contosobank.co.uk* ドメインへのすべての受信要求に対して CAPTCHA を有効にします。 [ファイアウォール規則の設定](https://support.cloudflare.com/hc/articles/360016473712-Cloudflare-Firewall-Rules)について理解します。

![CAPTCHA の強制方法を示す画像](./media/partner-cloudflare/configure-firewall-rule.png)

### <a name="test-the-settings"></a>設定のテスト

1. この規則を保存した後は、カスタム ドメインへのアクセスが要求されるたびに CAPTCHA を完了する必要があります。

   ![Cloudflare WAF での CAPTCHA の強制を示す画像](./media/partner-cloudflare/enforce-captcha.png)

   > [!NOTE]
   > Cloudflare には、カスタマイズされたブロック ページを作成するための [**その他のオプション**](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-)も各種用意されています。  

2. CAPTCHA を正常に完了すると、ユーザーは Azure AD B2C ポリシーに誘導されます。

   ![Azure AD B2C ポリシーのログインを示す画像](./media/partner-cloudflare/azure-ad-b2c-policy.png)


## <a name="additional-resources"></a>その他のリソース

- [Cloudflare のカスタム ページの問題のトラブルシューティング](https://support.cloudflare.com/hc/en-us/articles/200172706-Configuring-Custom-Pages-Error-and-Challenge-#5QWV2KVjLnaAQ8L4tjiguw)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)

## <a name="next-steps"></a>次のステップ 
 
- [Azure AD B2C でカスタム ドメインを構成する](./custom-domain.md?pivots=b2c-user-flow)
