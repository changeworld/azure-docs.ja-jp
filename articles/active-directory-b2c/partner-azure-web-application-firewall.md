---
title: Azure Web Application Firewall を使用して Azure Active Directory B2C を構成するためのチュートリアル
titleSuffix: Azure AD B2C
description: アプリケーションを悪意のある攻撃から保護するために Azure Web Application Firewall を使用して Azure Active Directory B2C を構成するためのチュートリアル
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 54cb0cdbff2ac11334f168e41a18107d3d0dfe87
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763220"
---
# <a name="tutorial-configure-azure-web-application-firewall-with-azure-active-directory-b2c"></a>チュートリアル: Azure Active Directory B2C を使用して Azure Web Application Firewall を構成する

このサンプル チュートリアルでは、カスタム ドメインを使用して、Azure Active Directory (AD) B2C テナントに対して [Azure Web Application Firewall (WAF)](https://azure.microsoft.com/services/web-application-firewall/#overview) ソリューションを有効にする方法について説明します。 Azure WAF では、一般的な悪用や脆弱性からの Web アプリケーションの一元的な保護が提供されます。

>[!NOTE]
>この機能はパブリック プレビュー段階にあります。

## <a name="prerequisites"></a>前提条件

作業を開始するには、以下が必要です。

- Azure サブスクリプション – サブスクリプションがない場合は、[無料のアカウント](https://azure.microsoft.com/free/)を入手できます。

- [Azure AD B2C テナント](tutorial-create-tenant.md) – テナントで定義されているカスタム ポリシーを使用してユーザーの資格情報を検証する承認サーバー。  ID プロバイダーとも呼ばれます。

- [Azure Front Door (AFD)](../frontdoor/index.yml) – Azure AD B2C テナントに対してカスタム ドメインを有効にする役割を担います。  

- [Azure WAF](https://azure.microsoft.com/services/web-application-firewall/#overview) – 承認サーバーに送信されるすべてのトラフィックを管理します。

## <a name="azure-ad-b2c-setup"></a>Azure AD B2C のセットアップ

Azure AD B2C でカスタム ドメインを使用するには、AFD によって提供されるカスタム ドメイン機能を使用する必要があります。 [Azure AD B2C カスタム ドメインを有効にする](./custom-domain.md?pivots=b2c-user-flow)方法を理解します。  

AFD を使用して Azure AD B2C のカスタム ドメインを正常に構成したら、次に進む前に[カスタム ドメインをテスト](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain)します。  

## <a name="onboard-with-azure-waf"></a>Azure WAF を使用してオンボードする

Azure WAF を有効にするには、WAF ポリシーを構成し、そのポリシーを保護のために AFD に関連付けます。

### <a name="create-a-waf-policy"></a>WAF ポリシーを作成する

[Azure portal](https://portal.azure.com) で、マネージド既定ルール セット (DRS) を使用して基本的な WAF ポリシーを作成します。

1. [Azure portal](https://portal.azure.com) にアクセスします。 **[リソースの作成]** を選択し、Azure WAF を見つけます。 **[Azure Web Application Firewall (WAF)]**  >  **[作成]** を選択します。

2. **[WAF ポリシーを作成する]** ページに移動して、 **[基本]** タブを選択します。次の情報を入力し、それ以外の設定については既定値をそのまま使用します。

| 値 | 説明 |
|:--------|:-------|
| 次に対するポリシー | グローバル WAF (フロント ドア)|
| フロント ドア SKU | Basic、Standard、Premium の SKU の中から選択します |
|サブスクリプション | Front Door のサブスクリプションの名前を選択します |
| Resource group | Front Door のリソース グループの名前を選択します |
| ポリシー名 | WAF ポリシーの一意の名前を入力します |
| [ポリシーの状態] | [有効] に設定します |
| ポリシー モード | [検出] に設定します |

3. **[確認と作成]** を選択します

4. [WAF ポリシーを作成する] ページの **[関連付け]** タブに移動し、 **[+ フロント ドア プロファイルを関連付ける]** を選択して、次の設定を入力します

| 値 | 説明 |
|:----|:------|
| Front Door | Azure AD B2C カスタムドメインに関連付けられているお使いのフロント ドアの名前を選択します |
| ドメイン | WAF ポリシーを関連付ける Azure AD B2C カスタム ドメインを選択します|

5. **[追加]** を選択します。

6. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

### <a name="change-policy-mode-from-detection-to-prevention"></a>ポリシー モードを検出から防止に変更する

WAF ポリシーを作成すると、ポリシーは既定で検出モードになります。 検出モードでは、WAF によって要求はブロックされず、代わりに、WAF 規則に一致する要求は WAF ログに記録されます。 WAF のログの詳細については、[Azure WAF の監視とログ](../web-application-firewall/afds/waf-front-door-monitor.md)に関する記事を参照してください。

サンプル クエリでは、過去 24 時間に WAF ポリシーによってブロックされたすべての要求が示されます。 詳細には、ルール名、要求データ、ポリシーによって実行されたアクション、ポリシー モードが含まれます。

![画像には、ブロックされた要求が示されています](./media/partner-azure-web-application-firewall/blocked-requests-query.png)

![画像には、ブロックされた要求の詳細が示されています](./media/partner-azure-web-application-firewall/blocked-requests-details.png)

WAF によって検出モードで要求がキャプチャされるようにすることをお勧めします。 WAF ログを調べて、擬陽性の結果の原因になっているルールがポリシーにあるかどうかを確認します。 次に、[WAF ログに基づいて WAF ルールを除外します](../web-application-firewall/afds/waf-front-door-exclusion.md#define-exclusion-based-on-web-application-firewall-logs)。

WAF の動作を確認するには、[Switch to prevention mode]\(防止モードに切り替え\) を使用して、検出を防止モードに変更します。 既定ルール セット (DRS) で定義されているルールに一致するすべての要求がブロックされ、WAF ログに記録されます。

![画像には防止モードへの切り替えが示されています](./media/partner-azure-web-application-firewall/switch-to-prevention-mode.png)

検出モードに戻す場合は、[Switch to detection mode]\(検出モードに切り替え\) オプションを使用して行うことができます。

![画像には検出モードへの切り替えが示されています](./media/partner-azure-web-application-firewall/switch-to-detection-mode.png)

## <a name="next-steps"></a>次の手順

- [Azure WAF の監視とログ](../web-application-firewall/afds/waf-front-door-monitor.md)

- [Front Door Service の除外リストを使用する WAF](../web-application-firewall/afds/waf-front-door-exclusion.md)