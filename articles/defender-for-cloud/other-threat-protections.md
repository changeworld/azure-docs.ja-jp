---
title: Microsoft Defender for Cloud による追加の脅威の防止
description: Microsoft Defender for Cloud から利用できる脅威の防止について説明します
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: c9c3b8da52e1d8725c6ddee92377d300b9c9cb3a
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373425"
---
# <a name="additional-threat-protections-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud の追加の脅威の防止

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

組み込みの[高度な保護計画](defender-for-cloud-introduction.md)に加え、Microsoft Defender for Cloud には、次の脅威の防止機能も用意されています。

> [!TIP]
> Defender for Cloud の脅威の防止機能を有効にするには、該当するワークロードを含むサブスクリプションで強化されたセキュリティ機能を有効にする必要があります。

## <a name="threat-protection-for-azure-network-layer"></a>Azure ネットワーク レイヤーの脅威の防止 <a name="network-layer"></a>
Defender for Cloud のネットワーク レイヤー分析は、サンプルの [IPFIX データ](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)に基づくものであり、Azure コア ルーターによって収集されるパケット ヘッダーです。 このデータ フィードに基づき、Defender for Cloud は機械学習モデルを使用して、悪意のあるトラフィック アクティビティを特定し、それらにフラグを設定します。 IP アドレスを強化するために、Defender for Cloud では、Microsoft の脅威インテリジェンス データベースも使用します。

一部のネットワーク構成では、Defender for Cloud による、不審なネットワーク アクティビティに対するアラートの生成が制限されます。 Defender for Cloud でネットワーク アラートを生成するには、次の条件を確認します。

- 仮想マシンにパブリック IP アドレスがある (または、仮想マシンがパブリック IP アドレスを持つロード バランサー上にある)。
- 仮想マシンのネットワーク エグレス トラフィックが、外部 ID ソリューションによってブロックされていない。

Azure ネットワーク レイヤー アラートの一覧については、[アラートのリファレンス表](alerts-reference.md#alerts-azurenetlayer)に関するページを参照してください。


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Azure Cosmos DB の脅威の防止 (プレビュー)<a name="cosmos-db"></a>

Azure Cosmos DB アラートは、通常とは異なる、害を及ぼす可能性がある、Azure Cosmos DB アカウントへのアクセスや悪用が試行された場合に生成されます。

詳細については、次を参照してください。

- [Azure Cosmos DB の Advanced Threat Protection (プレビュー)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
- [Azure Cosmos DB 向け脅威保護アラートの一覧 (プレビュー)](alerts-reference.md#alerts-azurecosmos)


## <a name="display-recommendations-in-microsoft-defender-for-cloud-apps"></a>Microsoft Defender for Cloud アプリに推奨事項を表示する<a name="azure-mcas"></a>

Microsoft Defender for Cloud Apps (旧称 Microsoft Cloud App Security) は、ログの収集、API コネクタ、リバース プロキシなど、さまざまなデプロイ モードをサポートするクラウド アクセス セキュリティ ブローカー (CASB) です。 お使いの Microsoft およびサード パーティ製クラウド サービス全体にわたるサイバー攻撃の脅威を特定し、対処するために、豊富な表示機能、データ送受信の制御、高度な分析を備えています。

Microsoft Defender for Cloud Apps を有効にし、Defender for Cloud の設定内から統合を選択した場合は、Defender for Cloud からのセキュリティ強化に関する推奨事項が Defender for Cloud Apps に表示されます。追加の構成は必要はありません。

> [!NOTE]
> Defender for Cloud では、そのリソースと同じ地域にセキュリティ関連の顧客データが格納されます。 Microsoft によってまだリソースの地域に Defender for Cloud がデプロイされていない場合、データは米国に格納されます。 Microsoft Defender for Cloud Apps が有効になっている場合、この情報は、Microsoft Defender for Cloud Apps の地域の場所ルールに従って格納されます。 詳細については、[非リージョン サービスのデータ ストレージ](https://azuredatacentermap.azurewebsites.net/)に関するページを参照してください。


## <a name="stream-security-alerts-from-other-microsoft-services"></a>他の Microsoft サービスからのセキュリティ アラートをストリーム配信する <a name="alerts-other"></a>

### <a name="display-azure-waf-alerts-in-defender-for-cloud"></a>Defender for Cloud で Azure WAF アラートを表示する <a name="azure-waf"></a>

Azure Application Gateway は、一般的な脆弱性やその悪用から Web アプリケーションを一元的に保護する Web アプリケーション ファイアウォール (WAF) を提供します。

Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 Application Gateway の WAF は、Open Web Application Security Project のコア ルール セット 3.0 または 2.2.9 に基づいています。 WAF は、新たな脆弱性から保護するために自動的に更新されます。 

Azure WAF のライセンスをお持ちの場合は、WAF アラートが Defender for Cloud にストリーミングされます。追加の構成は必要ありません。 WAF によって生成されるアラートの詳細については、[Web アプリケーション ファイアウォールの CRS 規則グループと規則](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31)に関する記事をご覧ください。


### <a name="display-azure-ddos-protection-alerts-in-defender-for-cloud"></a>Defender for Cloud で Azure DDoS Protection アラートを表示する <a name="azure-ddos"></a>

分散型サービス拒否 (DDoS) 攻撃は、簡単に実行できることが分かっています。 セキュリティの大きな不安材料になっており、アプリケーションをクラウドに移行する場合は特にそうです。 DDoS 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃では、インターネットを介して到達できるあらゆるエンドポイントが対象になる可能性があります。

DDoS 攻撃を防ぐには、Azure DDoS Protection のライセンスを購入し、アプリケーションの設計に関するベスト プラクティスに従っていることを確認します。 DDoS Protection では、各種のサービス レベルが提供されます。 詳細については、[Azure DDoS Protection の概要](../ddos-protection/ddos-protection-overview.md)に関する記事を参照してください。

Azure DDoS Protection を有効にしている場合は、DDoS アラートが Defender for Cloud にストリーム配信されます。追加の構成は必要ありません。 DDoS Protection によって生成されるアラートの詳細については、[アラートの参照テーブル](alerts-reference.md#alerts-azureddos)を参照してください。


## <a name="next-steps"></a>次のステップ
これらの脅威の防止機能からのセキュリティ アラートの詳細については、次の記事を参照してください。

- [すべての Defender for Cloud アラートの参照テーブル](alerts-reference.md)
- [Defender for Cloud のセキュリティ アラート](alerts-overview.md)
- [Defender for Cloud でセキュリティ アラートの管理と対応を行う](managing-and-responding-alerts.md)
- [Defender for Cloud のデータを継続的にエクスポートする](continuous-export.md)
