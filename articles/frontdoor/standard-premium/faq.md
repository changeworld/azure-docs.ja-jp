---
title: 'Azure Front Door: よく寄せられる質問'
description: このページでは、Azure Front Door Standard/Premium に関してよく寄せられる質問の回答をご紹介します。
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 05/18/2021
ms.author: duau
ms.openlocfilehash: 2be4e0b08ca31cabbd0ffbadf37760d289da7eb9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331264"
---
# <a name="frequently-asked-questions-for-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) についてよく寄せられる質問

この記事では、Azure Front Door の機能に関する一般的な質問を回答します。

## <a name="general"></a>全般

### <a name="what-is-azure-front-door"></a>Azure Front Door とは

Azure Front Door は、インテリジェントな脅威保護が備わっている、セキュリティで保護された高速で信頼性が高い最新のクラウド CDN です。 インテリジェントな脅威保護により、グローバルなハイパースケール アプリケーション、API、Web サイト、クラウド サービスに対し、静的および動的なコンテンツ アクセラレーション、グローバル負荷分散、強化されたセキュリティを提供します。

### <a name="what-features-does-azure-front-door-support"></a>Azure Front Door ではどのような機能がサポートされますか?

Azure Front Door では以下がサポートされています。

* 静的コンテンツと動的アプリケーション アクセラレーションの両方
* TLS/SSL オフロードとエンド ツー エンド TLS
* Web アプリケーション ファイアウォール
* Cookie ベースのセッション アフィニティ
* URL パスベースのルーティング
* 無料の証明書と複数のドメイン管理

サポートされる機能の一覧については、[Azure Front Door の概要](overview.md)に関する記事をご覧ください。

### <a name="what-is-the-difference-between-azure-front-door-and-azure-application-gateway"></a>Azure Front Door と Azure Application Gateway の違いは何ですか?

Front Door と Application Gateway は両方ともレイヤー 7 (HTTP/HTTPS) のロード バランサーであり、主な違いは、Front Door がグローバル サービスである点です。 Application Gateway はリージョン サービスです。 Front Door はリージョン全体のさまざまなスケール ユニット、クラスター、スタンプのユニット間で負荷分散ができますが、Application Gateway は、スケール ユニット内にある、仮想マシンやコンテナーの間で負荷分散ができます。

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Front Door の背後に Application Gateway をデプロイする必要はありますか?

Front Door の背後で Application Gateway を使用するべき主要なシナリオは次のとおりです。

* Front Door は、パスベースの負荷分散をグローバル レベルでしか実行できません。仮想ネットワーク (VNET) 内でトラフィックをさらに負荷分散したい場合は、Application Gateway を使用する必要があります。
* Front Door は VM やコンテナー レベルで機能しないので、接続のドレインは実行できません。 ただし、Application Gateway を使用すると、接続のドレインを実行できます。 
* Front Door の内側で Application Gateway を使用すると、100% の TLS/SSL オフロードを実現し、その仮想ネットワーク (VNET) 内で HTTP 要求のみをルーティングできます。
* Front Door と Application Gateway は両方ともセッション アフィニティをサポートします。 Front Door は、後続のトラフィックをユーザー セッションから特定のリージョン内の同じクラスターまたはバックエンドに転送できます。 Application Gateway は、トラフィックをクラスター内の同じサーバーに関連付けることができます。  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Front Door の背後に Azure Load Balancer をデプロイできますか?

Azure Front Door では、トラフィックをルーティングする先のパブリック VIP または公開されている DNS 名が必要です。 Front Door の背後に Azure Load Balancer をデプロイすることは、一般的なユース ケースです。

### <a name="what-protocols-does-azure-front-door-support"></a>Azure Front Door ではどのようなプロトコルがサポートされますか?

Azure Front Door では、HTTP、HTTPS、HTTP/2 がサポートされます。

### <a name="how-does-azure-front-door-support-http2"></a>Azure Front Door では HTTP/2 がどのようにサポートされますか?

HTTP/2 プロトコルのサポートを利用できるのは、Azure Front Door に接続しているクライアントだけです。 バックエンド プール内のバックエンドへの通信は、HTTP/1.1 を介して実行されます。 HTTP/2 のサポートは既定で有効です。

### <a name="what-resources-are-supported-today-as-part-of-an-origin-group"></a>現在、配信元グループの一部としてどのようなリソースがサポートされていますか。

配信元グループは、次の 2 種類の配信元で構成することができます。

- パブリック配信元には、ストレージ アカウント、App Service アプリ、Kubernetes インスタンスのほか、パブリック接続を備えたあらゆるカスタム ホスト名が含まれます。 これらの配信元は、パブリック IP アドレスまたはパブリックに解決可能な DNS ホスト名を介して定義する必要があります。 配信元グループのメンバーは、パブリック接続がある限り、複数の可用性ゾーンやリージョンにまたがって、または Azure の外部にでもデプロイすることができます。 パブリック配信元は、Azure Front Door の Standard レベルと Premium レベルでサポートされます。
- [Private Link 配信元](concept-private-link.md)は、Azure Front Door (Premium) を使用している場合に利用できます。

### <a name="what-regions-is-the-service-available-in"></a>このサービスは、どのリージョンで利用できますか?

Azure Front Door はグローバル サービスであり、特定の Azure リージョンには関連付けられていません。 Front Door を作成するときに指定する必要がある唯一の場所はリソース グループです。 この場所は基本的にはリソース グループのメタデータを格納する場所を指定しています。 Front Door リソース自体はグローバル リソースとして作成され、構成はすべてのエッジ ロケーションにグローバルにデプロイされます。 

### <a name="where-are-the-edge-locations-for-azure-front-door"></a>Azure Front Door のエッジ ロケーションはどこにありますか。

Azure Front Door のすべてのエッジ ロケーションのリストは、[Azure Front Door のエッジ ロケーション](edge-locations.md)に関する記事でご覧いただけます。

### <a name="is-azure-front-door-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door はアプリケーション専用のデプロイですか、それとも複数の顧客と共有されますか?

Azure Front Door は、グローバルに分散されたマルチ テナント サービスです。 Front Door のインフラストラクチャは、すべての顧客間で共有されます。 Front Door プロファイルを作成することで、アプリケーションに必要な特定の構成が定義されます。 Front Door に加えられた変更は、他の Front Door の構成には影響しません。

### <a name="is-http-https-redirection-supported"></a>HTTP から HTTPS へのリダイレクトはサポートされていますか?

はい。 実際には、Azure Front Door では、ホスト、パス、クエリ文字列のリダイレクトに加えて、URL リダイレクトの一部がサポートされています。 [URL リダイレクト](concept-rule-set-url-redirect-and-rewrite.md)の詳細を確認してください。 

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>バックエンドへのアクセスを Azure Front Door のみにロックダウンするにはどうしたらよいですか?

特定の Front Door インスタンスからのトラフィックのみを受け入れるようにアプリケーションを制限する最善の方法は、プライベート エンドポイント経由でアプリケーションを公開することです。 Front Door とアプリケーション間のネットワーク トラフィックは、Vnet および Microsoft バックボーン ネットワーク上の Private Link を経由することで、パブリック インターネットに晒されないようになっています。

詳細については、[Private Link を使用して Front Door の配信元をセキュリティで保護する](concept-private-link.md)方法に関するページを参照してください。  

別の方法で特定の Front Door からのトラフィックのみを受け入れるようにアプリケーションを制限するには、バックエンド用の IP ACL を設定する必要があります。 その後、バックエンドのトラフィックを、Front Door によって送信されたヘッダー 'X-Azure-FDID' の特定の値に制限します。 以下に、これらの手順の詳細を示します。

* 使用するバックエンドが、Azure Front Door のバックエンド IP アドレス空間と Azure のインフラストラクチャ サービスからのトラフィックのみを受け入れるように IP ACL 処理を構成します。 バックエンドに対する ACL については、以下の IP の詳細を参照してください。
 
    * Front Door のバックエンド IP アドレスの範囲については、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)に関するページの「*AzureFrontDoor.Backend*」セクションを参照してください。 または、[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#security-rules)でサービス タグ *AzureFrontDoor.Backend* を使用することもできます。
    * 仮想化されたホスト IP アドレスを通した Azure の[基本的なインフラストラクチャ サービス](../../virtual-network/network-security-groups-overview.md#azure-platform-considerations): `168.63.129.16` および `169.254.169.254`。

    > [!WARNING]
    > Front Door のバックエンド IP 空間は後で変更される可能性があります。ただし Microsoft はその前に、[Azure IP 範囲およびサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)との統合が確実に行われるようにします。 変更または更新について知るために、[Azure IP 範囲とサービス タグ](https://www.microsoft.com/download/details.aspx?id=56519)をサブスクライブすることをお勧めします。

* API バージョン `2020-01-01` 以降を使用して、Front Door で GET 操作を実行します。 API 呼び出しで、`frontdoorID` フィールドを探します。 Front Door からバックエンドに送信された受信ヘッダー "**X-Azure-FDID**" を、フィールド `frontdoorID` の値でフィルター処理します。 `Front Door ID` の値は、Front Door ポータル ページの [概要] セクションでも確認できます。 

* バックエンド Web サーバーでルール フィルターを適用して、結果の "X-Azure-FDID" ヘッダー値に基づいてトラフィックを制限します。

  [Microsoft インターネット インフォメーション サービス (IIS)](https://www.iis.net/) の例を次に示します。

    ``` xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
        <system.webServer>
            <rewrite>
                <rules>
                    <rule name="Filter_X-Azure-FDID" patternSyntax="Wildcard" stopProcessing="true">
                        <match url="*" />
                        <conditions>
                            <add input="{HTTP_X_AZURE_FDID}" pattern="xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" negate="true" />
                        </conditions>
                        <action type="AbortRequest" />
                    </rule>
                </rules>
            </rewrite>
        </system.webServer>
    </configuration>
    ```

* Azure Front Door では、クライアントが Front Door に接続するときに使用する IP アドレスの一覧を提供する *AzureFrontDoor.Frontend* サービス タグもサポートされています。 Azure Front Door の後方にデプロイされたサービスへの接続が許可されている必要がある送信トラフィックを制御する場合は、*AzureFrontDoor.Frontend* サービス タグを使用できます。 Azure Front Door では、他の Azure サービスと内部的に統合するための追加のサービス タグ *AzureFrontDoor.FirstParty* もサポートされています。 Azure Front Door サービス タグのユース ケースの詳細については、「[利用可能なサービス タグ](../../virtual-network/service-tags-overview.md#available-service-tags)」を参照してください。

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>エニーキャスト IP は、Front Door の有効期間を通じて変更できますか?

Front Door のフロントエンド エニーキャスト IP は、通常は変更すべきでなく、Front Door の有効期間を通じて静的のままにしておくことができます。 ただし、同じであることの **保証はありません**。 IP アドレスへの直接的な依存関係は存在しないようにしてください。

### <a name="does-azure-front-door-support-static-or-dedicated-ips"></a>Azure Front Door では静的または専用 IP アドレスがサポートされていますか?

いいえ、Azure Front Door では静的または専用のフロントエンド エニーキャスト IP アドレスは現在サポートされていません。 

### <a name="does-azure-front-door-support-x-forwarded-for-headers"></a>Azure Front Door では x-forwarded-for ヘッダーはサポートされていますか?

はい、Azure Front Door では X-Forwarded-For、X-Forwarded-Host、および X-Forwarded-Proto ヘッダーがサポートされています。 X-Forwarded-For の場合、ヘッダーが既に存在していれば、Front Door はクライアント ソケット IP をそれに追加します。 そうでない場合、これは値としてクライアント ソケット IP アドレスを持つヘッダーを追加します。 X-Forwarded-Host と X-Forwarded-Proto の場合、値は上書きされます。  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-does-my-front-door-still-work-when-being-updated"></a>Azure Front Door のデプロイにはどのくらい時間がかかりますか? Front Door は更新中に動作しますか?

ほとんどのルール エンジン構成の更新は、20 分以内に完了します。 更新が完了するとすぐにルールが有効になります。 

 > [!Note]  
  > ほとんどのカスタム TLS/SSL 証明書の更新は、グローバルにデプロイされるまでに数分から 1 時間かかります。

ルートやバックエンド プールに対する更新はシームレスであり、ダウンタイムは発生しません (新しい構成が正しい場合)。 また、"Azure Front Door マネージド" から "独自の証明書の使用" に、またはその逆に切り替える場合を除き、証明書の停止が発生することはありません。


## <a name="configuration"></a>構成

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door は仮想ネットワーク内で負荷分散またはトラフィックのルーティングができますか?

Azure Front Door (Standard) には、トラフィックをルーティングするために、パブリック IP またはパブリックに解決可能な DNS 名が必要です。 Azure Front Door は、仮想ネットワーク内のリソースに直接ルーティングすることはできません。 この問題は、パブリック IP を持つ Application Gateway または Azure Load Balancer を使用することで解決できます。

Azure Front Door (Premium) は、[Private Link の配信元](concept-private-link.md)へのトラフィックのルーティングをサポートします。

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door"></a>Azure Front Door でのさまざまなタイムアウトおよび制限とは何ですか?

[Azure Front Door のさまざまなタイムアウトおよび制限](../../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)についてのすべてのドキュメントを参照してください。

### <a name="how-long-does-it-take-for-a-rule-to-take-effect-after-being-added-to-the-front-door-rules-engine"></a>Front Door ルール エンジンに追加された後、ルールが有効になるまでにどれくらいの時間がかかりますか?

ほとんどのルール エンジンは、20 分以内に構成の更新が完了します。 更新が完了するとすぐにルールが有効になります。 

### <a name="can-i-configure-azure-cdn-behind-my-front-door-profile-or-front-door-behind-my-azure-cdn"></a>Azure CDN を Front Door プロファイルの背後に構成したり、Front Door を Azure CDN の背後に構成したりすることはできますか?

Azure Front Door と Azure CDN を同時に構成することはできません。これは、どちらのサービスでも、要求に応答するときに同じ Azure エッジ サイトが使用されているためです。 

## <a name="performance"></a>パフォーマンス

### <a name="how-does-azure-front-door-support-high-availability-and-scalability"></a>Azure Front Door では高可用性とスケーラビリティはどのようにサポートされていますか?

Azure Front Door は、アプリケーションのスケーラビリティのニーズに対応できる膨大な容量を持つ、グローバルに分散されたマルチ テナント プラットフォームです。 Microsoft のグローバル ネットワークのエッジから提供される Front Door は、リージョン間またはさまざまなクラウド間でのアプリケーション全体または個別のマイクロサービスのフェールオーバーを可能にする、グローバル負荷分散機能を提供します。

## <a name="tls-configuration"></a>TLS の構成

### <a name="what-tls-versions-are-supported-by-azure-front-door"></a>Azure Front Door ではどの TLS バージョンがサポートされますか?

2019 年 9 月以降に作成されたすべての Front Door プロファイルでは、既定の最小値として TLS 1.2 が使用されます。

Front Door では、TLS バージョン 1.0、1.1、1.2 がサポートされます。 TLS 1.3 はまだサポートされていません。 詳細については、[Azure Front Door のエンドツーエンド TLS](../concept-end-to-end-tls.md) に関するページを参照してください。

### <a name="why-is-https-traffic-to-my-backend-failing"></a>バックエンドへの HTTPS トラフィックが失敗するのはなぜですか?

正常性プローブか転送要求かに関係なく、バックエンドに正常に HTTPS 接続した場合、HTTPS トラフィックが失敗する原因は 2 つあります。

* **証明書のサブジェクト名の不一致**: HTTPS 接続の場合、Front Door では、バックエンドからバックエンドのホスト名と一致するサブジェクト名を持つ有効な CA からの証明書が提示されることを前提としています。 例えば、バックエンドのホスト名が `myapp-centralus.contosonews.net` に設定されており、TLS ハンドシェイク中にバックエンドが提供する証明書のサブジェクト名に `myapp-centralus.contosonews.net` や `*myapp-centralus*.contosonews.net` が含まれていないなどです。 Front Door によって接続が拒否され、エラーが生成されます。 
    * **解決策**: コンプライアンスの観点から推奨はされませんが、Front Door で証明書のサブジェクト名のチェックを無効にすることで、このエラーを回避できます。 このオプションは、Azure portal の [設定]、および API の BackendPoolsSettings にあります。
* **無効な CA からのバックエンド ホスティング証明書**: Front Door のバックエンドでは、[有効な証明機関](https://ccadb-public.secure.force.com/microsoft/IncludedCACertificateReportForMSFT)からの証明書のみを使用できます。 内部 CA からの証明書または自己署名証明書は許可されません。

### <a name="can-i-use-clientmutual-authentication-with-azure-front-door"></a>Azyre Front Door でクライアント/相互認証を使用できますか?

いいえ。 TLS 1.2 をサポートしている Azure Front Door は [RFC 5246](https://tools.ietf.org/html/rfc5246) でクライアント/相互認証を導入していますが、現時点で Azure Front Door はクライアント/相互認証をサポートしていません。

## <a name="diagnostics-and-logging"></a>診断とログ記録

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door"></a>どのような種類のメトリックとログを Azure Front Door で使用できますか?

ログとその他の診断機能については、「Monitoring metrics and logs for Front Door」(Front Door のメトリックとログの監視) を参照してください。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診断ログにおける保持ポリシーとは何ですか?

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Azure Monitor ログに送信することもできます。 詳細については、[Azure Front Door のログ](how-to-logs.md)に関するページを参照してください。

### <a name="how-do-i-get-audit-logs-for-azure-front-door"></a>Azure Front Door の監査ログはどのように取得できますか?

Azure Front Door では監査ログを使用できます。 ポータルで、Front Door のメニュー ページの **[アクティビティ ログ]** を選択して監査ログにアクセスします。 

### <a name="can-i-set-alerts-with-azure-front-door"></a>Azure Front Door でアラートを設定できますか?

はい、Azure Front Door ではアラートがサポートされています。 メトリックに関するアラートが構成されます。 

## <a name="billing"></a>課金

### <a name="will-i-be-billed-for-the-azure-front-door-resources-that-are-disabled"></a>無効になっている Azure Front Door リソースは課金されますか?

Front Door プロファイルなどの Azure Front Door リソースは、無効になっている場合には課金されません。

## <a name="next-steps"></a>次のステップ

[Front Door Standard または Premium を作成する](create-front-door-portal.md)方法について学習します。
