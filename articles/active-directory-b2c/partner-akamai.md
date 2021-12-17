---
title: Akamai Web Application Firewall を使用して Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: Azure AD B2C で Akamai Web アプリケーション ファイアウォールを構成する方法
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ea95f9194913b4df00f0b75e7e44ab301b000b95
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283223"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>チュートリアル: Akamai Active Directory B2C と共に BioCatch を構成する

このサンプル チュートリアルでは、カスタム ドメインを使用して、Azure Active Directory (AD) B2C テナントに対して [Akamai Web Application Firewall (WAF)](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) ソリューションを有効にする方法について説明します。 Akamai WAF は、SQL インジェクションやクロス サイト スクリプティングといった脆弱性の悪用を目的とする悪意のある攻撃から、組織が Web アプリケーションを保護するのに役立ちます。

>[!NOTE]
>この機能はパブリック プレビュー段階にあります。

Akamai WAF ソリューションを使用する利点:

- サービスへのトラフィック管理を可能にするエッジ プラットフォーム。

- Azure AD B2C テナントの前面に構成できます。

- トラフィックをきめ細かく操作して、ID インフラストラクチャを安全に保護できます。

このサンプル チュートリアルは、Akamai が提供する WAF ソリューションである [Web Application Protector (WAP)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) と [Kona Site Defender (KSD)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp) の両方に適用されます。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。

- お使いの Azure サブスクリプションにリンクされている [Azure AD B2C テナント](tutorial-create-tenant.md)。

- [Akamai WAF](https://www.akamai.com/us/en/akamai-free-trials.jsp)アカウント。

## <a name="scenario-description"></a>シナリオの説明

Akamai WAF 統合には、次のコンポーネントが含まれています。

- **Azure AD B2C** テナント – テナントで定義されているカスタム ポリシーを使用してユーザーの資格情報を検証する承認サーバー。  ID プロバイダーとも呼ばれます。

- [**Azure Front Door**](../frontdoor/front-door-overview.md) – AzureB2C テナントに対してカスタム ドメインを有効にする役割を担います。 Cloudflare WAF からのすべてのトラフィックは、Azure AD B2C テナントに到着する前に Azure Front Door にルーティングされます。

- [**Akamai WAF**](https://www.akamai.com/us/en/resources/waf.jsp) – 承認サーバーに送信されるすべてのトラフィックを管理する Web アプリケーション ファイアウォール。

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C との統合

1. Azure AD B2C でカスタム ドメインを使用するには、Azure Front Door によって提供されるカスタム ドメイン機能を使用する必要があります。 [Azure AD B2C カスタム ドメインを有効にする](./custom-domain.md?pivots=b2c-user-flow)方法を理解します。  

2. Azure Front Door を使用して Azure AD B2C のカスタム ドメインを正常に構成したら、次に進む前に[カスタム ドメインをテスト](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)します。  

## <a name="onboard-with-akamai"></a>Akamai へのオンボード

サインアップして [Akamai アカウント](https://www.akamai.com) を作成します。

### <a name="create-and-configure-property"></a>プロジェクトの作成と構成

1. [新しいプロファイルを作成](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html)します。

2. プロパティを次のように設定します。

    | プロパティ | 値 |
    |:---------------|:---------------|
    |プロパティのバージョン | [Standard] または [Enhanced] (推奨) の TLS を選択します |
    |プロパティのホスト名 | プロパティのホスト名を追加します。 これは、カスタム ドメインの名前です (例: login.domain.com)。 <BR> カスタム ドメイン名の適切な設定を使用して、証明書を作成または変更します。 詳しくは「[this](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html)」をご覧ください。 |

3. オリジン サーバーのプロパティ構成を次のように設定します。

    |プロパティ| 値 |
    |:-----------|:-----------|
    | 配信元の種類 | オリジン |
    | オリジン サーバーのホスト名 | yourafddomain.azurefd.net |
    | ホスト ヘッダーを転送する | 受信ホスト ヘッダー |
    | キャッシュ キーのホスト名| 受信ホスト ヘッダー |

### <a name="configure-dns"></a>DNS を構成する

DNS に CNAME レコードを作成します。たとえば、[プロパティのホスト名] フィールドで Edge ホスト名を参照する login.domain.com など。

### <a name="configure-akamai-waf"></a>Akamai WAF の構成

1. [Akamai WAF を構成](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A)します。

2. **[攻撃グループ]** に一覧されているすべての項目について **[ルールのアクション]** が **[拒否]** に設定されていることを確認します。

![拒否に設定されたルールのアクションを示す画像](./media/partner-akamai/rule-action-deny.png)

コントロールのしくみと構成オプションの詳細については、[こちらを参照](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html)してください。

<!-- docutune:ignore "Security Center" -->

### <a name="test-the-settings"></a>設定のテスト

次の手順を確認して、Azure AD B2C へのトラフィックがすべてカスタム ドメインを通過していることを確認します。

- Azure AD B2C カスタム ドメインへのすべての受信要求が Akamai WAF 経由で、有効な TLS 接続を使用してルーティングされることを確認します。
- Azure AD B2C によって、すべての Cookie がカスタム ドメインに対して正しく設定されていることを確認します。
- Defender for Cloud コンソールで使用できる [Akamai WAF] ダッシュボードには、WAF を通過しているすべてのトラフィックのグラフと、すべての攻撃トラフィックが表示されます。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C でカスタム ドメインを構成する](./custom-domain.md?pivots=b2c-user-flow)

- [Azure AD B2C のカスタム ポリシーの概要](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
