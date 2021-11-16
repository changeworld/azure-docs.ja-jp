---
title: Azure Front Door を使用したエンド ツー エンド TLS
description: Azure Front Door を使用するときのエンド ツー エンド TLS 暗号化について説明します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/02/2021
ms.author: duau
ms.openlocfilehash: 5e0cac6f9ba6a245ec201666adb2c5cfeed79c38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343099"
---
# <a name="end-to-end-tls-with-azure-front-door"></a>Azure Front Door を使用したエンド ツー エンド TLS

トランスポート層セキュリティ (TLS) (以前の Secure Sockets Layer (SSL)) は、Web サーバーとブラウザー間に暗号化されたリンクを確立するための標準的なセキュリティ テクノロジです。 このリンクにより、Web サーバーとブラウザー間で渡されるすべてのデータのプライバシーと暗号化が維持されます。

セキュリティまたはコンプライアンスの要件を満たすために、Azure Front Door (AFD) ではエンド ツー エンド TLS 暗号化がサポートされています。 Front Door の TLS/SSL オフロードでは、TLS 接続を終了し、Azure Front Door でトラフィックを復号化し、トラフィックを再暗号化してからバックエンドに転送します。 バックエンドへの接続はパブリック IP を介して行われます。 Azure Front Door で転送プロトコルとして HTTPS を構成して、クライアントからバックエンドまでのエンド ツー エンド TLS 暗号化を適用することを強くお勧めします。

## <a name="end-to-end-tls-encryption"></a>エンド ツー エンド TLS 暗号化

エンド ツー エンド TLS を使用すると、バックエンドへの転送中に機密データをセキュリティで保護しながら、グローバル負荷分散やキャッシュなどの Azure Front Door の機能を利用できます。 また、URL ベースのルーティング、TCP 分割、クライアントに最も近いエッジ ロケーションでのキャッシュ、エッジでの HTTP 要求のカスタマイズなどの機能もあります。

Azure Front Door では、エッジで TLS セッションをオフロードし、クライアント要求を復号化します。 次に、構成済みのルーティング規則を適用して、バックエンド プール内の適切なバックエンドに要求をルーティングします。 その後、バックエンドへの新しい TLS 接続が開始され、バックエンドに要求を送信する前に、バックエンドの証明書を使用してすべてのデータが再暗号化されます。 バックエンドからの応答は、同じプロセスで暗号化されてエンド ユーザーに返されます。 エンド ツー エンド TLS を有効にするには、転送プロトコルとして HTTPS を使用するように Azure Front Door を構成します。

## <a name="supported-tls-versions"></a>サポートされている TLS バージョン

Azure Front Door では、TLS プロトコルの 3 つのバージョン (TLS バージョン 1.0、1.1、1.2) がサポートされています。 2019 年 9 月以降に作成されたすべての Azure Front Door プロファイルでは、既定の最小バージョンとして TLS 1.2 が使用されますが、下位互換性を確保するために TLS 1.0 と TLS 1.1 も引き続きサポートされています。

Azure Front Door では、RFC 5246 でクライアント/相互認証が導入された TLS 1.2 がサポートされていますが、現時点では、クライアント/相互認証は Azure Front Door ではサポートされていません。

Azure portal または  [Azure REST API](/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion) を使用して、カスタム ドメインの HTTPS 設定で Azure Front Door に TLS の最小バージョンを構成できます。 現時点では、1.0 と 1.2 のどちらかを選択できます。 そのため、TLS 1.2 を最小バージョンとして指定すると、Azure Front Door がクライアントから受け入れる TLS の最小許容バージョンが制御されます。 Azure Front Door では、バックエンドへの TLS トラフィックを開始すると、バックエンドが確実かつ一貫して受け入れることができる最適な TLS バージョンのネゴシエーションを試行します。

## <a name="supported-certificates"></a>サポートされている証明書

TLS/SSL 証明書を作成する場合は、 [Microsoft の信頼された CA のリスト](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)に含まれている許可された証明機関 (CA) を使用した完全な証明書チェーンを作成する必要があります。 許可されていない CA を使用すると、要求が拒否されます。

内部 CA からの証明書または自己署名証明書は許可されません。

## <a name="online-certificate-status-protocol-ocsp-stapling"></a>オンライン証明書状態プロトコル (OCSP) ステープリング

Azure Front Door では OCSP ステープリングが既定でサポートされており、構成は不要です。

## <a name="backend-tls-connection-azure-front-door-to-backend"></a>バックエンド TLS 接続 (Azure Front Door からバックエンド)

HTTPS 接続の場合、Azure Front Door では、サブジェクト名がバックエンドの "*ホスト名*" と一致する、有効な証明機関 (CA) からの証明書を提示することをバックエンドに要求します。 たとえば、バックエンドのホスト名が  `myapp-centralus.contosonews.net`  に設定されており、TLS ハンドシェイク中にバックエンドが提示した証明書のサブジェクト名に  `myapp-centralus.contosonews.net`  も `*.contosonews.net`  も含まれていない場合、Azure Front Door はその接続を拒否し、エラーが発生します。

> [!NOTE]
> 証明書には、リーフ証明書と中間証明書が存在する完全な証明書チェーンが必要です。 ルート CA は、 [Microsoft の信頼された CA のリスト](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)に含まれている必要があります。 完全なチェーンを持たない証明書が提示された場合、その証明書が関係する要求は、期待通り動作することが保証されません。

セキュリティの観点から、Microsoft では証明書のサブジェクト名のチェックを無効にすることを推奨していません。 たとえば、テストなどの特定のユース ケースでは、配信元で自己署名証明書を使用する必要があります。 HTTPS 接続の失敗を解決するための回避策として、Azure Front Door で証明書のサブジェクト名のチェックを無効にすることができます。 無効にするオプションは、Azure portal の Azure Front Door 設定と、Azure Front Door API の BackendPoolsSettings にあります。 

## <a name="frontend-tls-connection-client-to-front-door"></a>フロントエンド TLS 接続 (クライアントから Front Door)

Azure Front Door カスタム ドメインでコンテンツを安全に配信するために HTTPS プロトコルを有効にするには、Azure Front Door によって管理されている証明書を使用するか、独自の証明書を使用するかを選択できます。  

* Azure Front Door マネージド証明書では、DigiCert を介して標準の TLS/SSL 証明書が提供され、Azure Front Door の Key Vault に格納されます。   

* 独自の証明書を使用することを選択した場合は、サポートされている CA からの証明書をオンボードできます。これは標準の TLS 証明書、Extended Validation 証明書、ワイルドカード証明書のいずれでもかまいません。  

* 自己署名証明書はサポートされていません。  [カスタム ドメインの HTTPS を有効にする方法](front-door-custom-domain-https.md)を参照してください。

### <a name="certificate-autorotation"></a>証明書の自動ローテーション

Azure Front Door マネージド証明書では、証明書は Azure Front Door によって管理され、90 日の有効期間内に自動ローテーションされます。 Azure Front Door の Standard/Premium マネージド証明書では、証明書は Azure Front Door によって管理され、45 日の有効期間内に自動ローテーションされます。 Azure Front Door マネージド証明書を使用していて、証明書の有効期限まで 60 日未満、また Standard/Premium SKU の場合 30 日未満の場合、サポート チケットを提出してください。 

独自のカスタム TLS/SSL 証明書の場合:

1. キー コンテナーで新しいバージョンの証明書が利用可能になったときに、証明書を最新バージョンに自動的にローテーションするには、シークレットのバージョンを "Latest" に設定します。 カスタム証明書の場合、証明書の有効期限に関係なく、1 から 2 日以内に新しいバージョンの証明書に自動ローテーションされます。

1. 特定のバージョンが選択されている場合、自動ローテーションはサポートされません。 証明書をローテーションするには、新しいバージョンを手動で再選択する必要があります。 新しいバージョンの証明書またはシークレットがデプロイされるまで、最大で 24 時間かかります。

    Front Door のサービス プリンシパルがキー コンテナーにアクセスできることを確認する必要があります。 キー コンテナーへのアクセスを許可する方法を参照してください。 証明書のサブジェクト名またはサブジェクト代替名 (SAN) が変更されていない限り、Azure Front Door による、更新された証明書ロールアウト処理によって運用環境のダウン タイムが発生することはありません。

## <a name="supported-cipher-suites"></a>サポートされている暗号スイート

TLS1.2 では、次の暗号スイートがサポートされています。

* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256

> [!NOTE]
> Windows 10 以降に関しては、より良いセキュリティのために 1 つまたは両方の ECDHE 暗号スイートを有効化することを推奨します。 Windows 8.1、8、7 は、これらの ECDHE 暗号スイートと互換性がありません。 これらのオペレーティング システムとの互換性のために、DHE 暗号スイートが提供されています。

TLS1.0 または TLS1.1 が有効になっているカスタム ドメインを使用している場合は、次の暗号スイートがサポートされます。

* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
* TLS_RSA_WITH_AES_256_GCM_SHA384
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA
* TLS_RSA_WITH_AES_128_CBC_SHA
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

Azure Front Door は、特定の暗号スイートの構成はサポートしていません。 ご自分の証明機関 (Verisign、Entrust、Digicert など) から独自のカスタム TLS/SSL 証明書を取得することができます。 その後、生成する証明書に対して特定の暗号スイートがマークされるようにすることができます。 

## <a name="next-steps"></a>次のステップ

* Azure Front Door の[カスタム ドメインを構成します](front-door-custom-domain.md)。
* [カスタム ドメインの HTTPS を有効します](front-door-custom-domain-https.md)。
