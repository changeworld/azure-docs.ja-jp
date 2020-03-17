---
title: Azure Web アプリケーション ファイアウォールの概要
description: この記事では、Azure Web アプリケーション ファイアウォール (WAF) の概要を説明します
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 03/06/2020
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 44bc8db5d8ada0378f8f9d0911ed398ba491d289
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851192"
---
# <a name="what-is-azure-web-application-firewall"></a>Azure Web アプリケーション ファイアウォールとは

Web アプリケーション ファイアウォール (WAF) では、一般的な悪用や脆弱性から Web アプリケーションを一元的に保護します。 Web アプリケーションが、一般的な既知の脆弱性を悪用した悪意のある攻撃の標的になるケースが増えています。 よくある攻撃の例として、SQL インジェクションやクロスサイト スクリプティングが挙げられます。

![WAF の概要](media/overview/wafoverview.png)

アプリケーション コードでのこのような攻撃の阻止は困難です。 アプリケーション トポロジの複数の層で厳格な保守、パッチ適用、監視が必要になる場合があります。 Web アプリケーション ファイアウォールを一元化することで、セキュリティの管理がはるかに簡単になります。 また、WAF を使用すると、アプリケーション管理者にとっては脅威や侵入に対する保護がより確実になります。

WAF のソリューションでは、個々の Web アプリケーションをセキュリティで保護するのではなく、一元的に既知の脆弱性の修正プログラムを適用することで、さらに迅速にセキュリティの脅威に対応できます。

## <a name="supported-services"></a>サポートされているサービス

WAF は [Azure Application Gateway](../application-gateway/overview.md) と [Azure Front Door Service](../frontdoor/front-door-overview.md) を使用してデプロイできます。 どちらのサービスもレイヤー 7 (HTTP/S) のロード バランサーですが、Application Gateway はリージョン サービスであり、Front Door はグローバル サービスです。 WAF には、特定のサービスごとにカスタマイズされた機能があります。

詳細については、各サービスの WAF の概要を参照してください。

## <a name="next-steps"></a>次のステップ

- Application Gateway 上の Web アプリケーション ファイアウォールの詳細については、「[Application Gateway 上の Web アプリケーション ファイアウォール](./ag/ag-overview.md)」を参照してください。
- Azure Front Door Service 上の Web アプリケーション ファイアウォールの詳細については、[Azure Front Door Service 上の Web アプリケーション ファイアウォール](./afds/afds-overview.md)に関するページを参照してください。
