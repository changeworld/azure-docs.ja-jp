---
title: Azure Web アプリケーション ファイアウォール (WAF) のボット保護を構成する
description: Azure Application Gateway での Web アプリケーション ファイアウォール (WAF) のボット保護を構成する方法について説明します。
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 07/30/2021
ms.author: victorh
ms.openlocfilehash: 12c9466a4bee6adb77a0e46e63767c2b22fcc01d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729424"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway"></a>Azure Application Gateway での Web アプリケーション ファイアウォール用にボット保護を構成する

この記事では、Azure portal を使用して Application Gateway 用の Azure Web アプリケーション ファイアウォール (WAF) のボット保護規則を構成する方法について説明します。 

マネージド ボット保護規則セットを WAF に対して有効にし、既知の悪意のある IP アドレスからの要求をブロックしたりログに記録したりすることができます。 この IP アドレスのソースは、Microsoft の脅威インテリジェンス フィードです。 インテリジェント セキュリティ グラフは、Microsoft の脅威インテリジェンスを動作させる機能です。Azure Security Center を含む複数のサービスによって使用されます。

## <a name="prerequisites"></a>前提条件

「 [Application Gateway 用の Web アプリケーション ファイアウォール ポリシーの作成](create-waf-policy-ag.md)」に記載されている手順に従って、Application Gateway 用の基本的な WAF ポリシーを作成します。

## <a name="enable-bot-protection-rule-set"></a>ボット保護規則セットを有効にする

1. 以前に作成した **[Basic]\(基本\)** ポリシー ページの **[Settings]\(設定\)** で、**[Rules]\(規則\)** を選択します。  

2. 詳細ページで、 **[Manage rules]\(規則の管理\)**   セクションのドロップダウン メニューから、ボット保護規則のチェック ボックスをオンにし、 **[Save]\(保存\)** を選択します。

> [!div class="mx-imgBorder"]
> ![ボット保護](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>次のステップ

カスタム ルールの詳細については、[Azure Application Gateway での Web アプリケーション ファイアウォール v2 のカスタム規則](custom-waf-rules-overview.md)に関する記事を参照してください。