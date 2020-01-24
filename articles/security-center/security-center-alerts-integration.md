---
title: Azure Security Center の Azure セキュリティ製品からの脅威検出アラート
description: このトピックでは、Azure Security Center で脅威アラートを表示できる Azure セキュリティ製品について説明します
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913035"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Azure WAF と Azure DDoS Protection からの脅威検出アラート

Azure Security Center では、次の Azure セキュリティ製品によって生成される脅威検出アラートを表示して収集することができます (製品ごとに個別のライセンスが必要です)。

* [Azure WAF](#azure-waf)
* [Azure DDoS Protection](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 新たな脆弱性から保護するために、WAF は自動的に更新されます。このとき、追加の構成は不要です。 WAF アラートは、Security Center にストリーミングされます。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。

## Azure DDoS Protection <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 

DDoS 攻撃は、アプリケーションのリソースを使い果たして、そのアプリケーションを正当なユーザーから使用不能にしようとします。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

Azure DDoS Protection を、アプリケーション設計のベスト プラクティスと組み合わせることにより、DDoS 攻撃を防御できます。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)に関する記事を参照してください。

Azure DDoS Protection アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azureddos)に関するページを参照してください。