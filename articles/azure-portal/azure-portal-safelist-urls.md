---
title: ファイアウォールまたはプロキシ サーバーで Azure portal の URL を許可する
description: ご利用のネットワークと Azure portal およびそのサービスとの間の接続性を最適化するために、それらの URL を許可リストに追加することをお勧めします。
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516363"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>ファイアウォールまたはプロキシ サーバーで Azure portal の URL を許可する

ご利用のネットワークと Azure portal およびそのサービスとの間の接続性を最適化するために、Azure portal の特定の URL を許可リストに追加することをお勧めします。 そうすることにより、ローカルまたはワイドエリアネットワークと Azure クラウドの間のパフォーマンスと接続性が向上します。

プロキシ サーバーやファイアウォールなどのデバイスがネットワーク管理者によってデプロイされていることは少なくありません。これらのデバイスによって、ユーザーのインターネット アクセスに対するセキュリティと制御を向上させることができます。 ユーザーを保護するように設計されたルールは、ビジネスに関連する正当なインターネットトラフィックをブロックまたは低速化する場合があります。 たとえば、以下に記載した URL で行われるユーザーと Azure との間の通信が、そのようなトラフィックに該当します。

> [!TIP]
> これらのドメインに対するネットワーク接続の問題を診断するうえで役立つ情報については、 https://portal.azure.com/selfhelp をご覧ください。

## <a name="azure-portal-urls-for-proxy-bypass"></a>プロキシのバイパス対象となる Azure portal の URL

Azure portal に対して許可する URL エンドポイントは、組織がデプロイされている Azure クラウドによって異なります。 それらのエンドポイントへのネットワーク トラフィックを許可し、制限をバイパスするには、ご利用のクラウドを選択し、記載されている一連の URL をプロキシ サーバーまたはファイアウォールに追加してください。 他の Microsoft 製品やサービスに関連する URL を追加することもできますが、ここに記載されている URL 以外にポータル関連の URL を追加することは推奨されません。

#### <a name="public-cloud"></a>[パブリック クラウド](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[米国政府のクラウド](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[中国政府のクラウド](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> これらのエンドポイントへのトラフィックには、標準的な TCP ポートが使用されます (HTTP では 80、HTTPS では 443)。
