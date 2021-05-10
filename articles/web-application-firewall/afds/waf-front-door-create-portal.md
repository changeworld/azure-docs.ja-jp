---
title: チュートリアル:Azure Front Door 用の WAF ポリシーを作成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して Web アプリケーション ファイアウォール (WAF) ポリシーを作成する方法について説明します。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: victorh
ms.openlocfilehash: e7b4544530dc9c0c894ae7a0f2b1d2830f895928
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106122258"
---
# <a name="tutorial-create-a-web-application-firewall-policy-on-azure-front-door-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure Front Door で Web アプリケーション ファイアウォール ポリシーを作成する

このチュートリアルでは、基本的な Azure Web Application Firewall (WAF) ポリシーを作成し、Azure Front Door でフロントエンド ホストに適用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAF ポリシーを作成する
> * フロントエンド ホストに関連付ける
> * WAF 規則を構成する

## <a name="prerequisites"></a>前提条件

[フロント ドア](../../frontdoor/quickstart-create-front-door.md)か、[Front Door Standard または Premium](../../frontdoor/standard-premium/create-front-door-portal.md) のプロファイルを作成します。 

## <a name="create-a-web-application-firewall-policy"></a>Web アプリケーション ファイアウォールのポリシーを作成します

最初に、ポータルを使用して管理された既定の規則セット (DRS) で基本的な WAF ポリシーを作成します。 

1. 画面の左上で **[リソースの作成]** を選択し、**WAF** を検索して、 **[Web アプリケーション ファイアウォール (WAF)]** を選択し、 **[作成]** を選択します。

1. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | 次に対するポリシー              | **[グローバル WAF (フロント ドア)]** を選択します。 |
    | フロント ドア SKU          | Basic、Standard、Premium の SKU の中から選択します。 |
    | サブスクリプション            | Front Door のサブスクリプションの名前を選択します。|
    | Resource group          | Front Door のリソース グループの名前を選択します。|
    | ポリシー名             | WAF ポリシーの一意の名前を入力します。|
    | [ポリシーの状態]            | **[有効]** に設定します。 | 

   :::image type="content" source="../media/waf-front-door-create-portal/basic.png" alt-text="[Create a W A F policy]\(W A F ポリシーの作成\) ページのスクリーンショット。[確認と作成] ボタンのほか、サブスクリプション、リソース グループ、ポリシー名の各リスト ボックスが表示されています。":::

1. **[WAF ポリシーを作成する]** ページの **[関連付け]** タブで、 **[+ フロント ドア プロファイルを関連付ける]** を選択し、次の設定を入力して、 **[追加]** を選択します。

    | 設定                 | 値                                              |
    | ---                     | ---                                                |
    | フロント ドア プロファイル              | Front Door プロファイルの名前を選択します。 |
    | ドメイン          | WAF ポリシーを関連付けるドメインを選択し、 **[追加]** を選択します。 |

    :::image type="content" source="../media/waf-front-door-create-portal/associate-profile.png" alt-text="[フロント ドア プロファイルを関連付ける] ページのスクリーンショット。":::
    
    > [!NOTE]
    > ドメインが WAF ポリシーに関連付けられている場合は、淡色表示になります。まず関連付けられているポリシーからドメインを削除してから、新しい WAF ポリシーにドメインを再度関連付ける必要があります。

1. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

## <a name="configure-web-application-firewall-rules-optional"></a>Web アプリケーション ファイアウォール規則を構成する (省略可能)

### <a name="change-mode"></a>モードを変更する

WAF ポリシーを作成するとき、既定では WAF ポリシーは **検出** モードになります。 **検出** モードでは、WAF はすべての要求をブロックせず、代わりに、WAF 規則に一致する要求は WAF ログに記録されます。
WAF の動作を確認するには、モードの設定を **[検出]** から **[防止]** に変更できます。 **防止** モードでは、既定の規則セット (DRS) で定義されている規則に一致する要求はブロックされ、WAF ログに記録されます。

 :::image type="content" source="../media/waf-front-door-create-portal/policy.png" alt-text="[ポリシー設定] セクションのスクリーンショット。[モード] トグルが [防止] に設定されています。":::

### <a name="custom-rules"></a>カスタム規則

**[カスタム ルール]** セクションの下の **[Add custom rule]\(カスタム ルールの追加\)** を選択することで、カスタム ルールを作成できます。 これで、カスタム ルールの構成ページが起動されます。 

:::image type="content" source="../media/waf-front-door-create-portal/custom-rules.png" alt-text="[カスタム ルール] ページのスクリーンショット。":::

次に示すのは、クエリ文字列に **blockme** が含まれているときに要求をブロックするというカスタム ルールの構成方法の例です。

:::image type="content" source="../media/waf-front-door-create-portal/customquerystring2.png" alt-text="カスタム ルールの構成ページのスクリーンショット。QueryString 変数に blockme という値が含まれているかどうかを調べるルールの設定が表示されています。":::

### <a name="default-rule-set-drs"></a>既定の規則セット (DRS)

Azure で管理される既定の規則セットは既定で有効になります。 現在の既定のバージョンは DefaultRuleSet_1.0 です。 WAF の **[管理されているルール]** 、 **[割り当て]** のドロップダウン リストで、最近提供されたルール セット Microsoft_DefaultRuleSet_1.1 を使用できます。

各ルールを無効にするには、ルール番号の前にある **チェック ボックス** をオンにし、ページ上部の **[無効]** を選択します。 ルール セット内にある個々のルールのアクションの種類を変更するには、ルール番号の前にあるチェック ボックスをオンにして、ページ上部の **[アクションを変更する]** を選択します。

:::image type="content" source="../media/waf-front-door-create-portal/managed-rules.png" alt-text="[管理されているルール] ページのスクリーンショット。ルール セット、ルール グループ、ルールのほか、[有効]、[無効]、[アクションを変更する] の各ボタンが表示されています。" lightbox="../media/waf-front-door-create-portal/managed-rules-expanded.png":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Front Door の詳細を確認する](../../frontdoor/front-door-overview.md)
> [Azure Front Door Standard または Premium の詳細を確認する](../../frontdoor/standard-premium/overview.md)
