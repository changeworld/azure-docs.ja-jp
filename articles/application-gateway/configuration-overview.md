---
title: Azure Application Gateway 構成の概要
description: この記事では、Azure Application Gateway のさまざまなコンポーネントの構成方法に関する情報を提供します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 61b3a9e066a3ee20effa97f1c6c7a0bd1ae90ac0
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58285840"
---
# <a name="application-gateway-configuration-overview"></a>アプリケーション ゲートウェイ構成の概要

アプリケーション ゲートウェイは、さまざまなシナリオを実行するために多様な方法で構成できる複数のコンポーネントで構成されています。 この記事では各コンポーネントの構成方法を手順を追って説明します。

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

上のサンプル図は、3 つのリスナーがあるアプリケーションの構成を示しています。 最初の 2 つのリスナーは、それぞれ `http://acme.com/*` と `http://fabrikam.com/*` のマルチサイト リスナーです。 両方ともポート 80 でリッスンしています。 3 番目のリスナーは、エンド ツー エンド SSL 終了の基本リスナーです。 

## <a name="prerequisites"></a>前提条件

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Azure 仮想ネットワークと専用サブネット

アプリケーション ゲートウェイは、お客様の仮想ネットワーク内の専用デプロイメントです。 仮想ネットワーク内では、ご使用のアプリケーション ゲートウェイのために専用サブネットが必要です。 このサブネットに特定のアプリケーション ゲートウェイ デプロイメントの複数インスタンスを配置できます。 また、このサブネットに他のアプリケーション ゲートウェイをデプロイできますが、アプリケーション ゲートウェイ サブネットに他のリソースをデプロイすることはできません。  

> [!NOTE]   
> 同じサブネット上の Standard_v2 と Standard Application Gateway の混在はサポートされていません。

#### <a name="size-of-the-subnet"></a>サブネットのサイズ

Application Gateway は、インスタンスごとに 1 つのプライベート IP アドレスを使用します。さらに、プライベート フロントエンド IP 構成を使用している場合は、もう 1 つのプライベート IP アドレスを使用します。 また、Azure は、内部使用のために、各サブネットの最初の 4 個の IP アドレスと最後の IP アドレスを予約しています。 たとえば、インスタンス 3 つとプライベート フロントエンド IP なしとしてアプリケーション ゲートウェイを設定する場合、サブネットには少なくとも 8 個の IP アドレス (内部使用のための 5 個の IP アドレスとアプリケーション ゲートウェイの 3 つのインスタンスのための 3 個の IP アドレス) が必要になります。 したがって、このケースではサブネット サイズ /29 以上が必要になります。 3 つのインスタンスとプライベート フロントエンド IP 構成用の 1 つの IP アドレスがある場合は、9 個の IP アドレス (アプリケーション ゲートウェイの 3 つのインスタンスのための 3 個の IP アドレス、プライベート フロントエンド IP のための 1 個の IP アドレス、内部使用のための 5 個の IP アドレス) が必要になります。 したがって、このケースではサブネット サイズ /28 以上が必要になります。

ベスト プラクティスとして、サブネット サイズは /28 以上を使用してください。 こうすると 11 個の使用可能なアドレスが得られます。 アプリケーションの負荷によって 10 個を超えるインスタンスが必要な場合には、サブネット サイズ /27 または /26 を検討する必要があります。

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでサポートされるネットワーク セキュリティ グループ

ネットワーク セキュリティ グループ (NSG) はアプリケーション ゲートウェイ サブネットでサポートされますが、次の制約があります。 

- Application Gateway v1 SKU ではポート 65503 から 65534 の、v2 SKU ではポート 65200 から 65535 の受信トラフィックを例外にする必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに対する変更を開始できません。

- 送信インターネット接続はブロックできません。 NSG の既定のアウトバウンド規則ではインターネット接続が既に許可されています。 既定のアウトバウンド規則は削除しないこと、および送信インターネット接続を拒否する他のアウトバウンド規則を作成しないことをお勧めします。

- AzureLoadBalancer タグからのトラフィックを許可する必要があります。

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>アプリケーション ゲートウェイ アクセスを少数のソース IP に限定する

このシナリオは、アプリケーション ゲートウェイ サブネットの NSG を使用して行うことができます。 次の制約を次の優先順位でサブネットに適用する必要があります。

1. ソース IP と IP 範囲からの着信トラフィックを許可します。
2. [バックエンド ヘルス通信](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)用にポート 65503 ~ 65534 のすべてのソースからの着信要求を許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに対する変更を開始することはできません。
3. [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview)で着信 Azure Load Balancer プローブ (AzureLoadBalancer タグ) と受信仮想ネットワーク トラフィック (VirtualNetwork タグ) を許可します。
4. すべて拒否ルールを使用して、その他すべての着信トラフィックをブロックします。
5. インターネットのすべての宛先への送信トラフィックを許可します。

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネットでサポートされるユーザー定義ルート

v1 SKU の場合、ユーザー定義ルート (UDR) は、エンドツーエンドの要求/応答の通信を変えない限り、アプリケーション ゲートウェイ サブネットでサポートされます。 たとえば、パケットの検査のためにファイアウォール アプリケーションを指す Application Gateway サブネットに UDR をセットアップできますが、パケットが意図した宛先の後検査にリーチできることを確認する必要があります。 これに失敗すると、不適切な正常性プローブやトラフィック ルーティング動作が発生する場合があります。 これには仮想ネットワークの ExpressRoute や VPN Gateway によってプロパゲートされる学習済みのルートまたは既定の 0.0.0.0/0 ルートが含まれます。

v2 SKU の場合、アプリケーション ゲートウェイ サブネット上の UDR はサポートされていません。 詳細については、「[自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations)」を参照してください。

> [!NOTE]
> アプリケーション ゲートウェイ サブネット上で UDR を使用すると、[バックエンド正常性の表示](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health)でヘルス状態が **[不明]** と表示され、アプリケーション ゲートウェイのログとメトリックの生成が失敗します。 バックエンド正常性、ログおよびメトリックを表示できるように、アプリケーション ゲートウェイ サブネット上で UDR を使用しないことをお勧めします。

## <a name="frontend-ip"></a>フロントエンド IP

アプリケーション ゲートウェイは、パブリック IP アドレスまたはプライベート IP アドレス (あるいは両方) を持つように構成できます。 パブリック IP が必要となるのは、インターネットに接続している VIP を介してインターネット上でクライアントがアクセスする必要があるバックエンドをホストするときです。 パブリック IP は、インターネットに公開されない内部エンドポイント、すなわち内部ロード バランサー (ILB) エンドポイントでは必要ありません。 ILB を使用したゲートウェイの構成は、インターネットに接続されていない社内用ビジネス アプリケーションで便利です。 また、セキュリティの境界でインターネットに接続されていない多階層アプリケーション内のサービスや階層でも便利ですが、ラウンド ロビンの負荷分散、セッションの持続性、または Secure Sockets Layer (SSL) ターミネーションが必要です。

1 つのパブリック IP アドレスまたは 1 つのプライベート IP アドレスしかサポートされません。 アプリケーション ゲートウェイを作成するときにフロントエンド IP を選択します。 

- パブリック IP の場合は、新しいパブリック IP の作成またはアプリケーション ゲートウェイと同じ場所にある既存のパブリック IP の使用を選択できます。 新しいパブリック IP アドアレスを作成する場合、選択した IP アドレスの種類 (静的または動的) を後で変更することはできません。 詳しくは、[静的なパブリック IP と動的なパブリック IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) に関するページをご覧ください。 

- プライベート IP の場合は、アプリケーション ゲートウェイが作成されるサブネットのプライベート IP アドレスを指定することを選択できます。 明示的に指定しないと、サブネットから任意の IP アドレスが自動的に選択されます。 詳しくは、[内部ロード バランサー (ILB) エンドポイントを使用したアプリケーション ゲートウェイの作成](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)に関するページをご覧ください。

フロントエンド IP が関連付けられた "*リスナー*" が、フロントエンド IP での着信要求をチェックします。

## <a name="listeners"></a>リスナー

リスナーは、ポート、プロトコル、ホストおよび IP アドレスを使用して着信接続要求をチェックする論理エンティティです。 そのため、リスナーを構成するときは、ポート、プロトコル、ホストおよび IP アドレスの値として、ゲートウェイ上の着信要求の対応する値と同じものを入力する必要があります。 Azure portal を使用してアプリケーション ゲートウェイを作成するときにも、リスナーのプロトコルとポートを選択して既定のリスナーを作成します。 さらに、リスナー上で HTTP2 サポートを有効化するかどうかも選択できます。 アプリケーション ゲートウェイが作成された後で、この既定リスナー (*appGatewayHttpListener*/*appGatewayHttpsListener*) の設定の編集や新しいリスナーの作成を行うことができます。

### <a name="listener-type"></a>リスナーの種類

新しいリスナーを作成するとき、[基本リスナーまたはマルチサイト リスナー](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners)を選択できます。 

- アプリケーション ゲートウェイの背後で 1 つのサイトをホストする場合は、基本リスナーを選択します。 [基本リスナーのアプリケーション ゲートウェイを作成する方法](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)をご覧ください。

- 同じアプリケーション ゲートウェイ上で、複数の Web アプリケーションを構成している場合、または同じ親ドメインの複数のサブドメインを構成している場合は、マルチサイト リスナーを選択します。 マルチサイト リスナーの場合は、さらにホスト名も入力する必要があります。 これは、アプリケーション ゲートウェイが複数の Web サイトを同じパブリック IP アドレスとポートでホストするために、HTTP 1.1 ホスト ヘッダーを利用しているためです。

#### <a name="order-of-processing-listeners"></a>リスナーを処理する順序

v1 SKU の場合、リスナーは表示される順序で処理されます。 そのため、基本リスナーが着信要求と一致する場合は、基本リスナーが要求を最初に処理します。 したがって、トラフィックが正しいバックエンドにルーティングされるようにするには、基本リスナーの前にマルチサイト リスナーを構成する必要があります。

v2 SKU の場合、マルチサイト リスナーは基本リスナーの前に処理されます。

### <a name="frontend-ip"></a>フロントエンド IP

このリスナーに関連付けるフロントエンド IP を選択します。 リスナーは、この IP 上の着信要求をリッスンします。

### <a name="frontend-port"></a>フロントエンド ポート

フロント エンド ポートを選択します。 既存のポートから選択するか、新しいポートを作成できます。 [許可されているポート範囲](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports)から任意の値を選択できます。 これにより、80 や 443 のような一般的なポートだけではなく、許可されているカスタム ポートを用途に合わせて使用できます。 1 つのポートを、パブリック向けリスナーまたはプライベート向けリスナーのいずれかに使用できます。

### <a name="protocol"></a>Protocol

HTTP プロトコルか HTTPS プロトコルを選択する必要があります。 

- HTTP を選択すると、クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化されずに送られます。

- [Secure Sockets Layer (SSL) 終了](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl)または[エンド ツー エンド SSL 暗号化](https://docs.microsoft.com/azure/application-gateway/ssl-overview)に関心がある場合は、HTTPS を選択します。 HTTPS を選択すると、クライアントとアプリケーション ゲートウェイ間のトラフィックは暗号化され、SSL 接続はアプリケーション ゲートウェイで終了されます。  エンド ツー エンド SSL 暗号化が必要な場合は、*バックエンド HTTP 設定*を構成するときに、さらに HTTPS プロトコルを選択する必要があります。 これにより、トラフィックがアプリケーション ゲートウェイからバックエンドに送られるときに再暗号化されます。

  Secure Sockets Layer (SSL) 終了とエンド ツー エンド SSL 暗号化を構成するには、SSL プロトコル仕様に従ってアプリケーション ゲートウェイが対称キーを導出できるように証明書をリスナーに追加する必要があります。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (PFX) 形式である必要があります。 このファイル形式により、アプリケーション ゲートウェイがトラフィックの暗号化および暗号化解除を実行する際に必要な秘密キーをエクスポートできます。 

#### <a name="supported-certificates"></a>サポートされている証明書

[SSL 終了でサポートされる証明書](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination)に関するページをご覧ください。

### <a name="additional-protocol-support"></a>その他のプロトコルのサポート

#### <a name="http2-support"></a>HTTP2 のサポート

HTTP/2 プロトコルのサポートを利用できるのは、アプリケーション ゲートウェイ リスナーに接続しているクライアントだけです。 バックエンド サーバー プールへの通信は、HTTP/1.1 で行われます。 既定では、HTTP/2 のサポートは無効になっています。 これを有効にする Azure PowerShell コード スニペットの例を次に示します。

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>WebSocket のサポート

WebSocket のサポートは既定で有効です。 ユーザーが構成可能な、WebSocket のサポートを選択的に有効または無効にするための設定はありません。 WebSocket は HTTP リスナーと HTTPS リスナーの両方で使用できます。 

### <a name="custom-error-page"></a>カスタム エラー ページ

カスタム エラー ページはグローバル レベルとリスナー レベルで定義できますが、Azure portal でのグローバル レベルのカスタム エラー ページの作成は現在サポートされていません。 リスナー レベルで 403 WAF エラー用のカスタム エラー ページまたは 502 メンテナンス ページを構成できます。 また、特定のエラー状態コード用の公的にアクセス可能な BLOB URL を指定する必要もあります。 詳細については、[カスタム エラー ページの作成](https://docs.microsoft.com/azure/application-gateway/custom-error)に関するページを参照してください。

![Application Gateway のエラー コード](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

グローバル カスタム エラー ページを構成するには、[構成用の Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) を使用します。 

### <a name="ssl-policy"></a>SSL ポリシー

SSL 証明書の管理を一元化し、バックエンド サーバー ファームからの暗号化と復号化のオーバーヘッドを低減することができます。 この一元化された SSL の処理によって、お客様の組織のセキュリティ要件に適したサーバーで中心的な役割を担う SSL ポリシーを指定することもできます。  既定、定義済み、およびカスタムの SSL ポリシーを選択できます。 

SSL プロトコルのバージョンを管理する SSL ポリシーを構成できます。 TLS1.0、TLS1.1、および TLS1.2 を拒否するようにアプリケーション ゲートウェイを構成できます。 SSL 2.0 および 3.0 は既定で無効になっているため、構成できません。 詳細については、「[Application Gateway の SSL ポリシーの概要](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview)」を参照してください。

リスナーを作成した後で、要求のルーティング規則に関連付けます。これによりリスナーで受け取った要求をバックエンドにルーティングする方法が決まります。

## <a name="request-routing-rule"></a>要求のルーティング規則

Azure portal を使用してアプリケーション ゲートウェイを作成するとき、既定の規則 (*rule1*) を作成します。これによって、既定のリスナー (*appGatewayHttpListener*) が既定のバックエンド プール (*appGatewayBackendPool*) と既定のバックエンド HTTP 設定 (*appGatewayBackendHttpSettings*) にバインドされます。 アプリケーション ゲートウェイが作成された後で、この既定の規則の設定の編集や新しい規則の作成を行うことができます。

### <a name="rule-type"></a>規則の種類

新しい規則を作成するときに、[基本の規則またはパスベースの規則](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule)を選択できます。 

- 関連付けられたリスナー (例: blog.contoso.com/*) に対するすべての要求を 1 つのバックエンド プールに転送する場合は、基本のリスナーを選択します。 
- 特定の URL パスを含む要求を特定のバックエンド プールに転送する場合は、パスベースのリスナーを選択します。 パスのパターンは URL のパスのみに適用され、クエリ パラメーターには適用されません。


#### <a name="order-of-processing-rules"></a>規則を処理する順序

v1 SKU の場合、着信要求のパターンとの照合は、パスベースの規則の URL パス マップにパスが指定された順序で行われます。 このため、URL パス マップの複数のパスと要求が一致する場合、最初に指定されているパスが照合されると、そのパスに関連付けられたバックエンドに要求が転送されます。

v2 SKU の場合、URL パス マップに指定されたパスの順序よりも完全一致が優先されます。 このため、複数のパスと要求が一致する場合、要求と正確に一致するパスに関連付けられたバックエンドに要求が転送されます。 着信要求のパスが URL パス マップのどのパスとも正確に一致しない場合、着信要求のパターンとの照合は、パスベースの規則の URL パス マップにパスが指定された順序で行われます。

### <a name="associated-listener"></a>関連付けられたリスナー

"*リスナー*" に関連付けられた "*要求のルーティング規則*" が評価されて、要求のルーティング先の "*バックエンド プール*" を決定できるように、リスナーを規則に関連付ける必要があります。

### <a name="associated-backend-pool"></a>関連付けられたバックエンド プール

リスナーが受け取った要求を処理するバックエンド ターゲットを含むバックエンド プールを関連付けます。 基本の規則の場合、バックエンド プールは 1 つしか許可されません。関連付けられたリスナーに対するすべての要求はそのバックエンド プールに転送されます。 パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド プールを追加します。 ここに入力した URL パスと一致する要求が、対応するバックエンド プールに転送されます。 また、既定のバックエンド プールを追加してください。この規則に入力したどの URL パス要求はそこに転送されます。

### <a name="associated-backend-http-setting"></a>関連付けられたバックエンド HTTP 設定

各規則のバックエンド HTTP 設定を追加します。 要求は、この設定に指定されるポート番号、プロトコル、および他の設定を使用して、アプリケーション ゲートウェイからバックエンド ターゲットにルーティングされます。 基本の規則の場合、バックエンド HTTP 設定は 1 つしか許可されません。関連付けられたリスナーに対するすべての要求は、この HTTP 設定を使用して対応するバックエンド ターゲットに転送されます。 パスベースの規則の場合は、各 URL パスに対応する複数のバックエンド HTTP 設定を追加します。 ここに入力した URL パスと一致する要求は、各 URL パスと対応する HTTP 設定を使用して、対応するバックエンド ターゲットに転送されます。 また、既定の HTTP 設定を追加してください。この規則に入力したどの URL パスとも一致しない要求は、既定の HTTP 設定を使用して既定のバックエンド プールに転送されます。

### <a name="redirection-setting"></a>リダイレクト設定

基本の規則でリダイレクトを構成すると、関連付けられたリスナーに対するすべての要求は、リダイレクト ターゲットにリダイレクトされ、グローバル リダイレクトが実現します。 パスベースの規則でリダイレクトを構成すると、特定のサイト領域 (/cart/* で示されるショッピング カート領域など) に対する要求のみが、リダイレクト ターゲットにリダイレクトされ、パスベースのリダイレクトが実現します。 

リダイレクト機能について詳しくは、[リダイレクトの概要](https://docs.microsoft.com/azure/application-gateway/redirect-overview)に関するページをご覧ください。

- #### <a name="redirection-type"></a>リダイレクトの種類

  必要なリダイレクトの種類を選択します。Permanent(301)、Temporary(307)、Found(302)、または See other(303) があります。

- #### <a name="redirection-target"></a>リダイレクト ターゲット

  リダイレクトのターゲットとして別のリスナーまたは外部サイトを選択できます。 

  - ##### <a name="listener"></a>リスナー

    ゲートウェイ上のあるリスナーから別のリスナーにリダイレクトするには、リダイレクト ターゲットとしてリスナーを選択することが役立ちます。 この設定が必要となるのは、HTTP から HTTPS へのリダイレクトを有効化、つまり着信 HTTP 要求をチェックするソース リスナーから、着信 HTTPS 要求をチェックする宛先リスナーにトラフィックをリダイレクトする場合です。 元の要求のクエリ文字列とパスを選択して、リダイレクト ターゲットに転送する要求に含めることもできます。![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    HTTP から HTTPS へのリダイレクトについて詳しくは、[ポータルを使用した HTTP から HTTP へのリダイレクト](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)、[PowerShell を使用した HTTP から HTTP へのリダイレクト](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)、[CLI を使用した HTTP から HTTP へのリダイレクト](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)に関するページをご覧ください。

  - ##### <a name="external-site"></a>外部サイト

    規則に関連付けられたリスナーに対するトラフィックを外部サイトにリダイレクトするときに、外部サイトを選択します。 元の要求のクエリ文字列を選択して、リダイレクト ターゲットに転送する要求に含めることができます。 元の要求内のパスを外部サイトに転送することはできません。

    外部サイトへのリダイレクトについて詳しくは、[PowerShell を使用した外部サイトへのトラフィックのリダイレクト](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)に関するページおよび [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli) をご覧ください。

#### <a name="rewrite-http-header-setting"></a>HTTP ヘッダー設定を書き換える

この機能では、要求/応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求および応答ヘッダーを追加、削除、または更新することができます。    この機能は PowerShell を使用した場合のみ構成できます。 ポータルと CLI はまだサポートされていません。 詳しくは、[HTTP ヘッダーの書き換え](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)の概要および[HTTP ヘッダーの書き換えの構成](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)に関するページをご覧ください。

## <a name="http-settings"></a>HTTP 設定

アプリケーション ゲートウェイは、このコンポーネントに指定された構成を使用してトラフィックをバックエンド サーバーにルーティングします。 HTTP 設定を作成したら、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。

### <a name="cookie-based-affinity"></a>Cookie ベースのアフィニティ

この機能は、同じサーバー上にユーザー セッションを保持する場合に便利です。 ゲートウェイで管理される Cookie を使用すると、アプリケーション ゲートウェイは、ユーザー セッションの後続のトラフィックを、処理のために同じサーバーに送ることができます。 この機能は、ユーザー セッションのためにセッションの状態をサーバー上でローカルに保存する場合に重要です。 アプリケーションが Cookie ベースのアフィニティを処理できない場合、この機能は使用できません。 Cookie ベースのセッション アフィニティを使用するには、クライアントによって Cookie がサポートされることを確認する必要があります。 

### <a name="connection-draining"></a>接続のドレイン

接続のドレインを使用すると、計画的なサービスの更新中にバックエンド プール メンバーを正常に削除することができます。 この設定は、規則の作成中にバックエンド プールのすべてのメンバーに適用することができます。 有効にすると、アプリケーション ゲートウェイは、バックエンド プールのすべての登録解除インスタンスが新しい要求を受け取らないようにし、既存の要求は構成された制限時間内に完了するようにします。 これは、API 呼び出しによってバックエンド プールから明示的に削除されたバックエンド インスタンスと、正常性プローブの判断によって異常であるとレポートされたバックエンド インスタンスの両方に適用されます。

### <a name="protocol"></a>Protocol

アプリケーション ゲートウェイでは、要求のバックエンド サーバーへのルーティングに対して HTTP プロトコルと HTTPS プロトコルの両方がサポートされます。 HTTP プロトコルを選択すると、トラフィックは暗号化されずにバックエンド サーバーに送られます。 バックエンド サーバーに対する暗号化されない通信が許容されない場合には、HTTPS プロトコルを選択する必要があります。 この設定と、リスナーでの HTTPS プロトコルの選択を組み合わせると、[エンド ツー エンド SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview) が実現します。 これによって、機密データを暗号化して安全にバックエンドに送信することができます。 エンド ツー エンド SSL が有効になったバックエンド プール内の各バックエンド サーバーは、セキュリティで保護された通信を許可するように証明書で構成する必要があります。

### <a name="port"></a>ポート

これは、アプリケーション ゲートウェイから届くトラフィックをバックエンド サーバーがリッスンするポートです。 1 から 65535 までの範囲のポートを構成できます。

### <a name="request-timeout"></a>要求タイムアウト

[接続がタイムアウトしました] エラーを返すまで、アプリケーション ゲートウェイがバックエンド プールからの応答の受信を待機する秒数です。

### <a name="override-backend-path"></a>バックエンド パスのオーバーライド

この設定を使用して、要求がバックエンドに転送されるときに使用できる、オプションのカスタム転送パスを構成できます。 これにより、**[バックエンド パスのオーバーライド]** フィールドに指定したカスタム パスと一致する受信パスの部分が、転送先パスにコピーされます。 この機能のしくみを理解するには以下の表をご覧ください。

- HTTP 設定が基本の要求ルーティング規則に関連付けられている場合:

  | 元の要求  | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- HTTP 設定がパスベースの要求ルーティング規則に関連付けられている場合:

  | 元の要求           | パスの規則       | バックエンド パスのオーバーライド | バックエンドに転送される要求 |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>[App Service 用に使用します]

これは、App Service バックエンドの必要な 2 つの設定を選択する UI ショートカットです。バックエンド アドレスからのホスト名の選択を有効にし、新しいカスタム プローブを作成します。 前者を行う理由は、**[バックエンド アドレスからホスト名を選択します]** 設定のセクションで説明しています。 新しいプローブが作成されるのは、プローブのヘッダーもバックエンド メンバーのアドレスから選択される場合です。

### <a name="use-custom-probe"></a>[カスタム プローブの使用]

この設定を使用して、[カスタム プローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) をこの HTTP 設定と関連付けます。 カスタム プローブを 1 つだけ HTTP 設定と関連付けることができます。 カスタム プローブを明示的に関連付けないと、バックエンドの正常性を監視するために[既定のプローブ](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings)が使用されます。 バックエンドの正常性監視をきめ細かく制御するために、カスタム プローブを作成することをお勧めします。

> [!NOTE]   
> 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性の監視を開始しません。

### <a name="pick-host-name-from-backend-address"></a>[バックエンド アドレスからホスト名を選択します]

この機能によって、要求の *host* ヘッダーが、IP アドレスまたは完全修飾ドメイン名 (FQDN) を使用するバックエンド プールのホスト名に動的に設定されます。 これが役に立つのは、バックエンドのドメイン名がアプリケーション ゲートウェイの DNS 名と異なっており、バックエンドとしてのマルチテナント サービスのケースなど、バックエンドが特定のホスト ヘッダーまたは SNI 拡張機能を利用して正しいエンドポイントに解決されるシナリオです。 App Service は 1 つの IP アドレスの共有領域を使用するマルチ テナント サービスであるため、カスタム ドメイン設定に構成されたホスト名を使用しないと App Service にはアクセスできません。 既定ではカスタム ドメイン名は *example.azurewebsites.net* です。 したがって、アプリケーション ゲートウェイを使用して、App Service に明示的に登録されていないホスト名またはアプリケーション ゲートウェイの FQDN によって App Service にアクセスしたい場合は、**[バックエンド アドレスからホスト名を選択します]** 設定を有効にすることで、元の要求のホスト名をオーバーライドして、App Service のホスト名にする必要があります。

カスタム ドメインを所有しており、既存のカスタム DNS 名を App Service にマップしている場合は、この設定を有効にする必要はありません。

> [!NOTE]   
> この設定は App Service Environment (ASE) では必要ありません。ASE は専用デプロイメントであるためです。 

### <a name="host-name-override"></a>[ホスト名の上書き]

この機能によって、アプリケーション ゲートウェイの着信要求の *host* ヘッダーが、ここに指定するホスト名に置き換えられます。 たとえば、www\.contoso.com が **[ホスト名]** 設定に指定された場合は、元の要求 https://appgw.eastus.cloudapp.net/path1 は https://www.contoso.com/path1 に変更されてから、要求がバックエンド サーバーに転送されます。 

## <a name="backend-pool"></a>バックエンド プール

バックエンド プールには、4 種類のバックエンド メンバー (特定の仮想マシン、仮想マシン スケール セット、IP アドレス/FQDN、または App Service) を指定できます。 各バックエンド プールには、同じ種類の複数のメンバーを指定できます。 同じバックエンド プール内で異なる種類のメンバーの指定はサポートされていません。 

バックエンド プールを作成した後で、それを 1 つ以上の要求ルーティング規則に関連付ける必要があります。 アプリケーション ゲートウェイ上の各バックエンド プールに対して正常性プローブを構成する必要もあります。 要求のルーティング規則の条件が満たされると、アプリケーション ゲートウェイは、トラフィックを対応するバックエンド プール内の正常なサーバー (正常性プローブによって判別) に転送します。

## <a name="health-probes"></a>正常性プローブ

アプリケーション ゲートウェイがバックエンド内のすべてのリソースの正常性を既定で監視する場合でも、正常性監視をきめ細かく制御できるように、各バックエンド HTTP 設定用のカスタム プローブを作成することを強くお勧めします。 カスタムの正常性プローブを構成する方法については、「[カスタムの正常性プローブの設定](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings)」をご覧ください。

> [!NOTE]   
> カスタムの正常性プローブを作成したら、バックエンド HTTP 設定に関連付ける必要があります。 対応する HTTP 設定が明示的にリスナーに関連付けられていない限り、カスタム プローブはバックエンド プールの正常性の監視を開始しません。

## <a name="next-steps"></a>次の手順

アプリケーション ゲートウェイのコンポーネントについて学習した後は、次を行うことができます。

- [Azure portal を使用してアプリケーション ゲートウェイを作成する](quick-create-portal.md)
- [PowerShell を使用してアプリケーション ゲートウェイを作成する](quick-create-powershell.md)
- [Azure CLI を使用してアプリケーション ゲートウェイを作成する](quick-create-cli.md)
