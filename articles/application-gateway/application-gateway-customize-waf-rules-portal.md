---
title: "Azure Application Gateway での Web アプリケーション ファイアウォール ルールのカスタマイズ - ポータル | Microsoft Docs"
description: "このページでは、ポータルを使用して Application Gateway で Web アプリケーション ファイアウォール ルールをカスタマイズする方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: c3e93614f012eecff0e88f5f2ad13db199406f4a
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---

# <a name="customize-web-application-firewall-rules-through-the-portal"></a>ポータルを使用した Web アプリケーション ファイアウォール ルールのカスタマイズ

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-customize-waf-rules-portal.md)
> * [PowerShell](application-gateway-customize-waf-rules-powershell.md)
> * [Azure CLI 2.0](application-gateway-customize-waf-rules-cli.md)

Application Gateway Web アプリケーション ファイアウォールは、Web アプリケーションを保護します。 こうした保護は、OWASP CRS ルールセットによって提供されます。 ルールによっては誤検出を発生させて、実際のトラフィックを妨げることがあります。  このため、アプリケーション ゲートウェイには、Web アプリケーション ファイアウォール対応アプリケーション ゲートウェイに対するルール グループとルールをカスタマイズする機能が用意されています。 特定のルール グループおよびルールの詳細については、[Web アプリケーション ファイアウォール CRS のルール グループおよびルール](application-gateway-crs-rulegroups-rules.md)に関するページをご覧ください

>[!NOTE]
> アプリケーション ゲートウェイが WAF レベルを使用していない場合、次の図に示すように、アプリケーション ゲートウェイを WAF レベルにアップグレードするオプションが表示されます。

![WAF を有効にする][fig1]

## <a name="view-rule-groups-and-rules"></a>ルール グループとルールの表示

アプリケーション ゲートウェイに移動し、**[Web アプリケーション ファイアウォール]** を選択します。  **[Advanced rule configuration (詳細なルール構成)]** をクリックします。  このビューでは、選択されたルール セットによって提供されるすべてのルール グループのテーブルがページに表示されます。

![無効化されたルールを構成する][1]

## <a name="search-for-rules-to-disable"></a>無効にするルールの検索

[Web アプリケーション ファイアウォール] 設定ブレードでは、テキスト検索によってルールをフィルター処理する機能を利用できます。 検索結果には、検索対象のテキストを含むルール グループとルールのみが表示されます。

![ルールを検索する][2]

## <a name="disable-rule-groups-and-rules"></a>ルール グループとルールの無効化

ルールの無効化は、ルール グループ全体を対象にすることも、1 つまたは複数のルール グループに含まれる特定のルールを対象にすることもできます。  無効にするルールのチェックボックスをオフにして、**[保存]** をクリックします。  この手順により、変更がアプリケーション ゲートウェイに保存されます。

![変更を保存する][3]

## <a name="next-steps"></a>次のステップ

無効になっているルールを構成したら、[Application Gateway 診断](application-gateway-diagnostics.md#diagnostic-logging)に関するページで WAF ログの表示方法を確認します

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png

