---
title: App Service を使用した Azure Application Gateway のトラブルシューティング - App Service の URL へのリダイレクト
description: この記事では、Azure Application Gateway を Azure App Service と共に使用した場合のリダイレクトの問題のトラブルシューティング方法について説明します。
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 07/19/2019
ms.author: absha
ms.openlocfilehash: 7baadfb549b19bb12757c82d723578202b5cf8ad
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68347886"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway での App Service に関する問題のトラブルシューティング

Application Gateway でバックエンド サーバーとして App Service を使用したときに発生した問題を診断して解決する方法について説明します。

## <a name="overview"></a>概要

この記事では、次の問題のトラブルシューティング方法について説明します。

> [!div class="checklist"]
> * リダイレクトが生じたときに App Service の URL がブラウザーに公開される
> * App Service の ARRAffinity Cookie ドメインが元のホストではなく App Service のホスト名 (example.azurewebsites.net) に設定される

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 たとえば、アプリ サービスがアプリケーション ゲートウェイの背後でホストされており、クライアントに相対パスへのリダイレクトを行わせる必要があるときに、これを行えます。 (たとえば、contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトです)。アプリ サービスでは、リダイレクト応答を送信するとき、その応答の場所ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 そのため、クライアントでは、アプリケーション ゲートウェイ (contoso.com/path2) を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 アプリケーション ゲートウェイをバイパスすることは望ましくありません。

この問題が発生する主な理由としては、次のことが考えられます。

- App Service 上でリダイレクトが構成されています。 要求の末尾にスラッシュを追加するだけでリダイレクトが実行される場合があります。
- Azure AD 認証が構成されており、これが原因でリダイレクトが実行されています。

また、App Services を Application Gateway の背後で使用する場合、Application Gateway に関連付けられているドメイン名 (example.com) は、App Service のドメイン名 (たとえば example.azurewebsites.net) とは違うものになります。App Service によって設定される ARRAffinity Cookie のドメイン値では "example.azurewebsites.net" ドメイン名が使用されることなりますが、これは望ましいものではありません。 Cookie のドメイン名の値は、元のホスト名 (example.com) である必要があります。

## <a name="sample-configuration"></a>サンプル構成

- HTTP リスナー:基本またはマルチサイト
- バックエンド アドレス プール:App Service
- HTTP 設定:[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) を有効にします
- プローブ:[Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\) を有効にします

## <a name="cause"></a>原因

App Service はマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントに要求をルーティングします。 ただし、App Services の既定のドメイン名である *.azurewebsites.net (たとえば contoso.azurewebsites.net) は、アプリケーション ゲートウェイのドメイン名 (たとえば contoso.com) とは異なります。 クライアントからの元の要求には、ホスト名としてアプリケーション ゲートウェイのドメイン名 (contoso.com) が含まれているため、要求をアプリ サービスのバックエンドにルーティングするときに、元の要求内のホスト名をアプリ サービスのホスト名に変更するようにアプリケーション ゲートウェイを構成する必要があります。  これを実現するには、Application Gateway の HTTP 設定の [Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\) スイッチと、正常性プローブ構成の [Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\) スイッチを使用します。



![appservice-1](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

これにより、App Service でリダイレクトが実行されるときは、特に構成されていない限り、場所ヘッダーには元のホスト名 "contoso.com" ではなくオーバーライドされたホスト名 "example.azurewebsites.net" が使用されます。 以下の要求と応答のヘッダー例を参照してください。
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
上の例で、応答ヘッダーにはリダイレクト用の状態コード 301 があり、location ヘッダーには元のホスト名 "www.contoso.com" ではなく App Service のホスト名があります。

## <a name="solution-rewrite-the-location-header"></a>解決方法:場所ヘッダーを書き換える

場所ヘッダーのホスト名をアプリケーション ゲートウェイのドメイン名に設定する必要があります。 これを行うには、応答の場所ヘッダーに azurewebsites.net が含まれるかどうかを評価し、場所ヘッダーにアプリケーション ゲートウェイのホスト名を設定するアクションを実行する条件を持つ[書き換えルール](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)を作成します。  [場所ヘッダーの書き換え方法](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#modify-a-redirection-url)に関する記事の手順を参照してください。

> [!NOTE]
> HTTP ヘッダーの書き換えのサポートは、Application Gateway の [Standard_V2 と WAF_v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) でのみ利用できます。 V1 SKU を使用している場合は、V2 SKU で利用できる書き換えとその他の[高度な機能](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#feature-comparison-between-v1-sku-and-v2-sku)を使用できるように、[V1 から V2 に移行する](https://docs.microsoft.com/azure/application-gateway/migrate-v1-v2)ことを強くお勧めします。

## <a name="alternate-solution-use-app-services-custom-domain-instead-of-default-domain-name"></a>代替ソリューション:既定のドメイン名ではなく App Service のカスタム ドメインを使用する

V1 SKU を使用している場合、場所ヘッダーを書き換える機能は V2 SKU でのみ使用できるため、それを実行することはできません。 このため、リダイレクトの問題を解決するには、ホストのオーバーライドの代わりに、Application Gateway で受信されるのと同じホスト名を App Service に渡す必要があります。

これを実行すると、App Service では、それ自身のホスト ヘッダーではなく Application Gateway を指している元のホスト ヘッダーにリダイレクトされます (ある場合)。

これを実現するには、カスタム ドメインを所有し、以下の手順を実行する必要があります。

- ドメインを App Service のカスタム ドメイン リストに登録します。 そのためには、カスタム ドメインに App Service の FQDN を指す CNAME が必要です。 詳細については、「[既存のカスタム DNS 名を Azure App Service にマップする](https://docs.microsoft.com//azure/app-service/app-service-web-tutorial-custom-domain)」を参照してください。

![appservice-2](./media/troubleshoot-app-service-redirection-app-service-url/appservice-2.png)

- それが完了すると、App Service でホスト名 "www.contoso.com" を受け入れる準備は完了です。 次は、DNS の CNAME エントリを変更して Application Gateway の FQDN を指すようにします。 たとえば、"appgw.eastus.cloudapp.azure.com" です。

- DNS クエリを実行するときに、ドメイン "www.contoso.com" が Application Gateway の FQDN に解決されることを確認します。

- [Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\) を無効にするようにカスタム プローブを設定します。 ポータルのプローブ設定でチェックボックスをオフにして、PowerShell の Set-AzApplicationGatewayProbeConfig コマンドで -PickHostNameFromBackendHttpSettings スイッチを使用しないことで、これを実行できます。 プローブのホスト名フィールドに App Service の FQDNである「example.azurewebsites.net」を入力します。これは、Application Gateway から送信されたプローブ要求のホスト ヘッダーで送信されるためです。

  > [!NOTE]
  > この時点では、HTTP 設定でまだ [Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) スイッチが有効なため、次の手順を実行するときに、カスタム プローブがバックエンドの HTTP 設定に関連付けられていないことを確認してください。

- [Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) を無効にするように Application Gateway の HTTP 設定を指定します。 ポータルでチェックボックスをオフにして、PowerShell の Set-AzApplicationGatewayBackendHttpSettings コマンドで -PickHostNameFromBackendAddress スイッチを使用しないことで、これを実行できます。

- カスタム プローブを元どおりバックエンドの HTTP 設定に関連付け、バックエンドの正常性に問題がないことを確認します。

- これが完了すると、Application Gateway は同じホスト名 "www.contoso.com" を App Service に転送し、同じホスト名でリダイレクトが実行されます。 以下の要求と応答のヘッダー例を参照してください。

既存の構成に対し、PowerShell を使用して前述の手順を実装するには、下のサンプル PowerShell スクリプトに従ってください。 Probe と HTTP Settings の構成に -PickHostname スイッチを使用していないことに注目してください。

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>次の手順

前の手順で問題を解決できない場合は、 [サポート チケット](https://azure.microsoft.com/support/options/)を開きます。
