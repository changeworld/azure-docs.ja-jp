---
title: Application Gateway の Azure Web アプリケーション ファイアウォール - よく寄せられる質問
description: この記事では、Application Gateway の Web アプリケーション ファイアウォールに関してよく寄せられる質問の回答を示します
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 05/05/2020
ms.author: victorh
ms.openlocfilehash: 890688dba70a7fa654e97652b3e474b919f9a077
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585385"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall-on-application-gateway"></a>Application Gateway の Azure Web アプリケーション ファイアウォールに関してよく寄せられる質問

この記事では、Application Gateway の Azure Web アプリケーション ファイアウォール (WAF) 機能に関する一般的な質問に回答します。 

## <a name="what-is-azure-waf"></a>Azure WAF とは何ですか?

Azure WAF は Web アプリケーション ファイアウォールです。SQL インジェクション、クロスサイト スクリプティング、その他の Web エクスプロイトなどの一般的な脅威から Web アプリケーションを保護するのに役立ちます。 Web アプリケーションへのアクセスを制御するために、カスタム ルールとマネージド ルールの組み合わせからなる WAF ポリシーを定義できます。

Azure WAF ポリシーは、Application Gateway または Azure Front Door でホストされている Web アプリケーションに適用できます。

## <a name="what-features-does-the-waf-sku-support"></a>WAF SKU はどのような機能でサポートされていますか。

WAF SKU では、Standard SKU で利用可能なすべての機能がサポートされています。

## <a name="how-do-i-monitor-waf"></a>WAF を監視するにはどうすればよいですか?

WAF の監視には、診断ログを使用します。 詳細については、[Application Gateway の診断ログとメトリック](../../application-gateway/application-gateway-diagnostics.md)に関するページを参照してください。

## <a name="does-detection-mode-block-traffic"></a>検出モードではトラフィックがブロックされますか?

いいえ。 検出モードでは、WAF 規則をトリガーするトラフィックをログに記録するにとどまります。

## <a name="can-i-customize-waf-rules"></a>WAF ルールはカスタマイズできますか?

はい。 詳細については、[WAF 規則グループと規則のカスタマイズ](application-gateway-customize-waf-rules-portal.md)に関するページを参照してください。

## <a name="what-rules-are-currently-available-for-waf"></a>WAF で現在利用できるのはどのような規則ですか?

WAF で現在サポートされているのは、CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229)、[3.0](application-gateway-crs-rulegroups-rules.md#owasp30)、および [3.1](application-gateway-crs-rulegroups-rules.md#owasp31) です。 これらの規則は、Open Web Application Security Project (OWASP) が特定した 10 の脆弱性のほとんどに対するベースライン セキュリティを提供するものです。 

* SQL インジェクションからの保護
* クロスサイト スクリプティングに対する保護
* 一般的な Web 攻撃 (コマンド インジェクション、HTTP 要求スマグリング、HTTP 応答スプリッティング、リモート ファイル インクルード攻撃など) に対する保護
* HTTP プロトコル違反に対する保護
* HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)
* ボット、クローラー、スキャナーの防止
* アプリケーションのよくある構成ミスの検出 (Apache、IIS など)

詳細については、[OWASP の 10 の脆弱性](https://owasp.org/www-project-top-ten/)に関するページを参照してください。

## <a name="what-content-types-does-waf-support"></a>WAF はどのようなコンテンツ タイプをサポートしていますか。

Application Gateway WAF は、次のコンテンツ タイプをサポートしています (マネージド ルールの場合)。

* application/json
* application/xml
* application/x-www-form-urlencoded
* multipart/form-data

カスタム ルールの場合:

* application/x-www-form-urlencoded
* multipart/form-data

## <a name="does-waf-support-ddos-protection"></a>WAF は DDoS 保護をサポートしていますか?

はい。 アプリケーション ゲートウェイをデプロイしてある仮想ネットワーク上で、DDos 保護を有効にすることができます。 この設定によって、アプリケーション ゲートウェイの仮想 IP (VIP) も Azure DDoS Protection サービスによる保護の対象になります。

### <a name="does-waf-store-customer-data"></a>WAF は顧客データを格納しますか?

いいえ。WAF は顧客データを格納しません。

## <a name="next-steps"></a>次のステップ

- [Azure Web アプリケーション ファイアウォール](../overview.md)について学習します。
- [Azure Front Door](../../frontdoor/front-door-overview.md) の詳細を確認します。
