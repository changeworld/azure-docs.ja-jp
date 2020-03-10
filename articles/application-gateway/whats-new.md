---
title: Azure Application Gateway の新着情報
description: 最新のリリース ノート、既知の問題、バグの修正、非推奨の機能、予定されている変更点など、Azure Application Gateway の新着情報について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: c6d4d290493bbd234ab048e613b88f8857513cc8
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299557"
---
# <a name="whats-new-in-azure-application-gateway"></a>Azure Application Gateway の新着情報

Azure Application Gateway は、継続的に更新されます。 常に最新の開発情報を把握していただけるよう、この記事では以下に関する情報を提供します。

- 最新のリリース
- 既知の問題
- バグの修正
- 非推奨の機能

## <a name="new-features"></a>新機能

|機能  |説明  |追加日  |
|---------|---------|---------|
|アフィニティ Cookie の変更 |Cookie ベースのアフィニティが有効になっている場合、Application Gateway は、既存の ApplicationGatewayAffinity Cookie に加えて、*ApplicationGatewayAffinityCORS* という別の同一の Cookie を挿入します。 *ApplicationGatewayAffinityCORS* には、クロスオリジン要求に対してもスティッキー セッションが維持されるように、2 つの属性が追加されています (*SameSite = None; Secure*)。 詳細については、[Application Gateway の Cookie ベースのアフィニティ](configuration-overview.md#cookie-based-affinity)に関するトピックを参照してください。 |2020 年 2 月 |
|プローブの機能強化 |Application Gateway v2 SKU でのカスタム プローブの機能強化により、[プローブの構成](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)が簡素化されて[オンデマンド バックエンドの正常性テスト](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe)が容易になりました。さらに、バックエンドの正常性の問題のトラブルシューティングに役立てるため、[多くの診断情報](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages)が追加されています。  |2019 年 10 月 |
|その他のメトリック |ご利用の Application Gateway v2 SKU を監視するのに役立つ次の新しいメトリックを追加しました。[タイミング関連のメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)、バックエンド応答の状態、受信バイト数、送信バイト数、クライアント TLS プロトコル、現在のコンピューティング ユニット。 「[Application Gateway V2 SKU でサポートされるメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku)」を参照してください。 |2019 年 8 月 |
|WAF カスタム規則 |Application Gateway WAF_v2 では、カスタム ルールの作成がサポートされるようになりました。 [Application Gateway のカスタム ルール](custom-waf-rules-overview.md)に関する記事を参照してください。 |2019 年 6 月 |
|自動スケーリング、ゾーン冗長性、静的 VIP のサポートの一般公開 |自動スケーリング、ゾーン冗長性、パフォーマンスの向上、静的 VIP、Key Vault、ヘッダーの書き換えをサポートする v2 SKU の一般公開。 [Application Gateway の自動スケーリングのドキュメント](application-gateway-autoscaling-zone-redundant.md)を参照してください。 |2019 年 4 月 |
|Key Vault の統合 |Application Gateway では、HTTPS が有効なリスナーにアタッチされているサーバー証明書用の Key Vault との統合 (パブリック プレビュー段階) がサポートされるようになりました。 「[Key Vault 証明書での SSL 終了](key-vault-certs.md)」を参照してください。 |2019 年 4 月 |
|ヘッダーの CRUD/書き換え     |HTTP ヘッダーを書き換えることができるようになりました。 「[チュートリアル:アプリケーション ゲートウェイを作成して HTTP ヘッダーを書き換える](tutorial-http-header-rewrite-powershell.md)」を参照してください。|2018 年 12 月|
|WAF 構成と除外リスト     |WAF の構成と擬陽性の削減に役立つオプションが追加されました。 詳細については、「[Web アプリケーション ファイアウォールの要求サイズの制限と除外リスト](application-gateway-waf-configuration.md)」を参照してください。|2018 年 12 月|
|自動スケーリング、ゾーン冗長性、静的 VIP のサポート      |v2 の SKU には、自動スケーリング、パフォーマンスの向上など、多くの機能強化があります。 詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。|2018 年 9 月|
|接続のドレイン     |接続のドレインにより、バックエンド プールからメンバーを適切に削除できます。 詳細については、「[接続のドレイン](features.md#connection-draining)」を参照してください。|2018 年 9 月|
|カスタム エラー ページ     |カスタム エラー ページを使用すると、Web サイトの他のページと同じ形式でエラー ページを作成できます。 この機能を有効にするには、「[Application Gateway のカスタム エラー ページを作成する](custom-error.md)」を参照してください。|2018 年 9 月|
|メトリックの機能強化     |強化されたメトリックを使用して、Application Gateway の状態をよりよく把握することができます。 Application Gateway でメトリックを有効にするには、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」を参照してください。|2018 年 6 月|

## <a name="next-steps"></a>次のステップ

Azure Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。
