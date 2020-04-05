---
title: チュートリアル:Azure CDN の WAF ポリシーを作成する - Azure portal
description: このチュートリアルでは、Azure portal を使用して Azure CDN の Web アプリケーション ファイアウォール (WAF) ポリシーを作成する方法について説明します。
author: vhorne
ms.service: web-application-firewall
services: web-application-firewall
ms.topic: tutorial
ms.date: 03/18/2020
ms.author: victorh
ms.openlocfilehash: 738be1361bfbd944575abceb08781b241336f6e8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "79472544"
---
# <a name="tutorial-create-a-waf-policy-on-azure-cdn-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Azure CDN の WAF ポリシーを作成する

このチュートリアルでは、基本的な Azure Web アプリケーション ファイアウォール (WAF) ポリシーを作成し、Azure Content Delivery Network (CDN) のエンドポイントに適用する方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAF ポリシーを作成する
> * CDN エンドポイントに関連付ける
> * WAF 規則を構成する

## <a name="prerequisites"></a>前提条件

次の手順に従って Azure CDN プロファイルとエンドポイントを作成する: 「[クイックスタート: Azure CDN プロファイルとエンドポイントの作成](../../cdn/cdn-create-new-endpoint.md)」を参照してください。 

## <a name="create-a-web-application-firewall-policy"></a>Web アプリケーション ファイアウォールのポリシーを作成します

まず、ポータルを使用して、マネージド既定のルール セット (DRS) を持つ基本の WAF ポリシーを作成します。

1. 画面の左上で **[リソースの作成]** を選択し、**WAF** を検索して、 **[Web アプリケーション ファイアウォール]** を選択して、 **[作成]** を選択します。
2. **[Create a WAF policy]\(WAF ポリシーの作成\)** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | 設定                 | Value                                              |
    | ---                     | ---                                                |
    | 次に対するポリシー            |[Azure CDN (プレビュー)] を選択します。|
    | サブスクリプション            |Front Door のサブスクリプションの名前を選択します。|
    | Resource group          |Front Door のリソース グループの名前を選択します。|
    | ポリシー名             |WAF ポリシーの一意の名前を入力します。|

   ![WAF ポリシーを作成する](../media/waf-cdn-create-portal/basic.png)

3. **[WAF ポリシーを作成する]** ページの **[関連付け]** タブで、 **[CDN エンドポイントの追加]** を選択し、次の設定を入力して、 **[追加]** を選択します。

    | 設定                 | Value                                              |
    | ---                     | ---                                                |
    | CDN プロファイル              | 実際の CDN プロファイル名を選択します。|
    | エンドポイント           | 実際のエンドポイントの名前を選択し、 **[追加]** を選択します。|
    
    > [!NOTE]
    > エンドポイントは WAF ポリシーに関連付けられている場合に淡色表示されます。最初に、関連付けられているポリシーからエンドポイントを削除してから、そのエンドポイントを新しい WAF ポリシーに再度関連付ける必要があります。
1. **[Review + create]\(確認と作成\)** を選択し、次に **[作成]** を選択します。

## <a name="configure-web-application-firewall-policy-optional"></a>Web アプリケーション ファイアウォール ポリシーを構成する (省略可能)

### <a name="change-mode"></a>モードを変更する

既定では、WAF ポリシーを作成するとその WAF ポリシーは "*検出*" モードになります。 "*検出*" モードでは、WAF で要求がブロックされることはありません。 代わりに、WAF ルールに一致する要求が WAF ログに記録されます。

WAF の動作を確認するには、モードの設定を *[検出]* から *[防止]* に変更できます。 *防止*モードでは、既定の規則セット (DRS) で定義されている規則に一致する要求はブロックされ、WAF ログに記録されます。

 ![WAF ポリシー モードを変更する](../media/waf-cdn-create-portal/policy.png)

### <a name="custom-rules"></a>カスタム規則

カスタム ルールを作成するには、 **[カスタム ルール]** セクションの下の **[カスタム ルールの追加]** を選択します。 これで、カスタム ルールの構成ページが開きます。 カスタム ルールには、**一致ルール**と**レート制限ルール**の 2 種類があります。

次のスクリーンショットは、クエリ文字列に **blockme** という値が含まれている場合に要求をブロックするカスタム一致ルールを示しています。

![WAF ポリシー モードを変更する](../media/waf-cdn-create-portal/custommatch.png)

レート制限ルールには、次の例に示すように、 **[レート制限の期間]** と **[レート制限のしきい値 (要求数)]** という 2 つの追加フィールドが必要です。

![WAF ポリシー モードを変更する](../media/waf-cdn-create-portal/customrate.png)

### <a name="default-rule-set-drs"></a>既定の規則セット (DRS)

Azure で管理される既定の規則セットは既定で有効になります。 ルール グループ内の個々のルールを無効にするには、そのルール グループ内のルールを展開し、ルール番号の前のチェック ボックスをオンにして、上のタブの **[無効]** を選択します。 規則セット内の個々の規則のアクションの種類を変更するには、規則番号の前にあるチェック ボックスをオンにして、上のタブの **[Change action]\(アクションの変更\)** を選択します。

 ![WAF 規則セットを変更する](../media/waf-cdn-create-portal/managed2.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Web アプリケーション ファイアウォールについて学習する](../overview.md)
