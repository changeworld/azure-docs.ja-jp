---
title: Microsoft Azure Application Gateway の Azure Security Center への接続 | Microsoft Docs
description: Application Gateway と Azure Security Center を統合して、リソースの全体的なセキュリティを強化する方法について説明します。
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/30/2018
ms.author: rkarlin
ms.openlocfilehash: 5638b71147592ae71c741ca86da68ddfec668af5
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44299068"
---
# <a name="connecting-microsoft-azure-application-gateway-to-azure-security-center"></a>Microsoft Azure Application Gateway の Azure Security Center への接続
このドキュメントは、Application Gateway Web アプリケーション ファイアウォール (WAF) と Security Center の統合を構成するために役立ちます。

## <a name="why-connect-application-gateway"></a>Application Gateway を接続する理由
Application Gateway の WAF は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。 Security Center は Application Gateway と統合して、環境内の保護されていない Web アプリケーションへの脅威を防止および検出します。

## <a name="how-do-i-configure-this-integration"></a>この統合を構成する方法
Security Center は、サブスクリプション内の以前にデプロイされた WAF インスタンスを検出します。 アラートの統合を許可するには、これらのソリューションを Security Center に接続します。

> [!NOTE]
> Application Gateway WAF はまた、[[Add a Web Application Firewall] (Web アプリケーション ファイアウォールの追加)](security-center-add-web-application-firewall.md) で説明されているように、Security Center の **[推奨事項]** からプロビジョニングすることもできます。
>
>

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/) にサインインします。

2. **[Microsoft Azure] メニュー**の **[セキュリティ センター]** を選択します。

3. Security Center の **[リソース セキュリティの検疫]** で、**[セキュリティ ソリューション]** を選択します。

  ![Security Center の概要](./media/security-center-connect-application-gateway/overview.png)

4. **[検出されたソリューション]** で Microsoft SaaS ベースの Web アプリケーション ファイアウォールを見つけ、**[接続]** を選択します。

  ![検出されたソリューション](./media/security-center-connect-application-gateway/connect.png)

5. **[Connect WAF solution] (WAF ソリューションの接続)** が開きます。  **[接続]** を選択して、WAF と Security Center を統合します。

  ![[Connect WAF solution] (WAF ソリューションの接続)](./media/security-center-connect-application-gateway/waf-solution.png)

## <a name="next-steps"></a>次の手順

この記事では、Application Gateway WAF を Security Center に統合する方法について説明しまた。 Security Center の詳細については、次の記事を参照してください。

* [Security Center でセキュリティ ソリューションを統合する](security-center-partner-integration.md)
* [Microsoft Advanced Threat Analytics の Security Center への接続](security-center-ata-integration.md)
* [Azure Active Directory Identity Protection の Security Center への接続](security-center-aadip-integration.md)
* [Security Center でのセキュリティ正常性の監視](security-center-monitoring.md)。
* [Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)。
* [Azure Security Center の FAQ](security-center-faq.md)。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)。
