---
title: Azure Security Center からの追加の脅威の防止
description: Azure Defender を越えて Azure Security Center から利用できる脅威の防止について学習します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 10/12/2021
ms.author: memildin
ms.openlocfilehash: c2bed032510e278663a1d1d9f10043eaa6e9b0db
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858198"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Azure Security Center での追加の脅威の防止
組み込みの [Azure Defender の保護](azure-defender.md)に加えて、Azure Security Center によって次の脅威の防止機能も提供されています。

> [!TIP]
> Security Center の脅威の防止機能を有効にするには、Azure Defender を、適用可能なワークロードを含むサブスクリプションで有効にする必要があります。

## <a name="threat-protection-for-azure-network-layer"></a>Azure ネットワーク レイヤーの脅威の防止 <a name="network-layer"></a>
Security Center のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Security Center は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Security Center では、Microsoft の脅威インテリジェンス データベースも使用します。

Security Center が疑わしいネットワーク アクティビティに対してアラートを生成することを制限するネットワーク構成もあります。 Security Center でネットワーク アラートを生成するには、次のことを確認してください。
- 仮想マシンにパブリック IP アドレスがある (または、仮想マシンがパブリック IP アドレスを持つロード バランサー上にある)。
- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の脅威の防止 (プレビュー)<a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

* [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)

## <a name="display-azure-security-center-recommendations-in-microsoft-cloud-app-security"></a>Microsoft Cloud App Security に Azure Security Center の推奨事項を表示する <a name="azure-mcas"></a>

Microsoft Cloud App Security は、ログの収集、API コネクタ、リバース プロキシなど、さまざまな展開モードをサポートするクラウド アクセス セキュリティ ブローカー (CASB) です。 お使いの Microsoft およびサード パーティ製クラウド サービス全体にわたるサイバー攻撃の脅威を特定し、対処するために、豊富な表示機能、データ送受信の制御、高度な分析を備えています。

Microsoft Cloud App Security を有効にし、Security Center の設定内から統合を選択した場合、Security Center からのセキュリティ強化に関する推奨事項が Cloud App Security に表示されます。追加の構成は必要ありません。

> [!NOTE]
> Security Center では、そのリソースと同じ地域でセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Security Center がデプロイされていない場合、米国でデータが格納されます。 Microsoft Cloud App Security が有効になっている場合、この情報は、Microsoft Cloud App Security の位置情報ルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。


## <a name="stream-security-alerts-from-other-microsoft-services"></a>他の Microsoft サービスからのセキュリティ アラートをストリーム配信する <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-security-center"></a>Security Center に Azure WAF アラートを表示する <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 WAF は、新たな脆弱性から保護するために自動的に更新されます。 

Azure WAF のライセンスをお持ちの場合は、WAF アラートが Security Center にストリーミングされます。その際に追加の構成は必要ありません。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。


### <a name="display-azure-ddos-protection-alerts-in-security-center"></a>Security Center に Azure DDoS Protection アラートを表示する <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

DDoS 攻撃を防ぐには、Azure DDoS Protection のライセンスを購入し、アプリケーションの設計に関するベスト プラクティスに従っていることを確認します。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](../ddos-protection/ddos-protection-overview.md)に関する記事を参照してください。

Azure DDoS Protection を有効にしている場合は、DDoS アラートが Security Center にストリーム配信されます。追加の構成は必要ありません。 DDoS Protection によって生成されるアラートの詳細については、[アラートの参照テーブル](alerts-reference.md#alerts-azureddos)を参照してください。


## <a name="next-steps"></a>次のステップ
これらの脅威の防止機能からのセキュリティ アラートの詳細については、次の記事を参照してください。

* [Azure Security Center のすべてのアラートの参照テーブル](alerts-reference.md)
* [Security alerts in Azure Security Center](security-center-alerts-overview.md)
* [Azure Security Center でのセキュリティ アラートの管理と対応](security-center-managing-and-responding-alerts.md)
* [継続的に Security Center データをエクスポートする](continuous-export.md)
