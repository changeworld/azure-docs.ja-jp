---
title: App Service URL へのリダイレクトのトラブルシューティング
titleSuffix: Azure Application Gateway
description: この記事では、Azure Application Gateway を Azure App Service と共に使用した場合のリダイレクトの問題のトラブルシューティング方法について説明します。
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 83ad2e7a8f138451063eef1746555563970e125e
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681814"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Application Gateway での App Service に関する問題のトラブルシューティング

Azure Application Gateway と共に Azure App Service をバックエンド ターゲットとして使用するときに発生する問題を診断し、解決する方法について説明します。

## <a name="overview"></a>概要

この記事では、次の問題のトラブルシューティング方法について説明します。

* リダイレクトが生じたときに App Service URL がブラウザーに公開されます。
* App Service の ARRAffinity Cookie ドメインが元のホストではなく App Service のホスト名 (example.azurewebsites.net) に設定されます。

バックエンド アプリケーションでリダイレクト応答が送信されるとき、バックエンド アプリケーションで指定されているものとは別の URL にクライアントをリダイレクトしたい場合があります。 App Service が Application Gateway の背後でホストされており、クライアントに相対パスへのリダイレクトを行わせる必要があるときに、これを行うことがあります。 たとえば、contoso.azurewebsites.net/path1 から contoso.azurewebsites.net/path2 へのリダイレクトです。 

App Service では、リダイレクト応答を送信するとき、その応答の location ヘッダーで、アプリケーション ゲートウェイから受信した要求のものと同じホスト名が使用されます。 たとえば、クライアントでは、Application Gateway contoso.com/path2 を経由するのではなく、contoso.azurewebsites.net/path2 に直接要求を行います。 Application Gateway の迂回は望まれません。

この問題は次のような主な理由から発生することがあります。

- App Service でリダイレクトを構成しています。 要求の末尾にスラッシュを追加するだけでリダイレクトが実行される場合があります。
- 導入している Azure Active Directory 認証がリダイレクトを発生させます。

また、Application Gateway の背後で App Service を使用するとき、Application Gateway (example.com) に関連付けられているドメイン名が App Service のドメイン名と異なっています (たとえば、example.azurewebsites.net になっています)。 App Service によって設定された ARRAffinity Cookie のドメイン値では example.azurewebsites.net というドメイン名が保持されますが、それは望ましくありません。 Cookie のドメイン名の値は、元のホスト名 (example.com) である必要があります。

## <a name="sample-configuration"></a>サンプル構成

- HTTP リスナー: 基本またはマルチサイト
- バックエンド アドレス プール: App Service
- HTTP 設定: **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** を有効にします
- プローブ: **[Pick Hostname from HTTP Settings]\(HTTP 設定からホスト名を選択する\)** を有効にします

## <a name="cause"></a>原因

App Service はマルチテナント サービスであるため、要求のホスト ヘッダーを使用して適切なエンドポイントに要求をルーティングします。 App Service の既定のドメイン名である *.azurewebsites.net (たとえば contoso.azurewebsites.net) は、Application Gateway のドメイン名 (たとえば contoso.com) とは異なります。 

クライアントからの元の要求には、ホスト名として Application Gateway のドメイン名 (contoso.com) があります。 App Service バックエンドに要求をルーティングするとき、元の要求にあるホスト名を App Service のホスト名に変更するように Application Gateway を構成する必要があります。 Application Gateway の HTTP 設定構成にある **[Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\)** というスイッチを使用します。 正常性プローブ構成にある **[Pick Hostname from Backend HTTP Settings]\(バックエンド HTTP 設定からホスト名を選択する\)** というスイッチを使用します。



![アプリケーション ゲートウェイでホスト名が変更される](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

App Service でリダイレクトが実行されるときは、特に構成されていない限り、location ヘッダーには元のホスト名 contoso.com ではなくオーバーライドされたホスト名 contoso.azurewebsites.net が使用されます。 次の要求と応答のヘッダー例を参照してください。
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
前の例では、応答ヘッダーにリダイレクトの状態コード 301 があることがわかります。 location ヘッダーには、元のホスト名 `www.contoso.com` ではなく、App Service のホスト名があります。

## <a name="solution-rewrite-the-location-header"></a>ソリューション: location ヘッダーを書き換える

location ヘッダーのホスト名をアプリケーション ゲートウェイのドメイン名に設定します。 これを行うには、応答の location ヘッダーに azurewebsites.net が含まれているかどうかを評価する条件で書き換え[規則](./rewrite-http-headers-url.md)を作成します。 また、location ヘッダーを書き換えてアプリケーション ゲートウェイのホスト名を含めるようにするアクションも、これによって実行する必要があります。 詳細は、[location ヘッダーの書き換え方法](./rewrite-http-headers-url.md#modify-a-redirection-url)に関する記事の手順を参照してください。

> [!NOTE]
> HTTP ヘッダーの書き換えのサポートは、Application Gateway の [Standard_v2 と WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) でのみ利用できます。 v2 SKU で利用可能なヘッダーの書き換えやその他の[高度な機能](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku)を使用するには、[v2 に移行する](./migrate-v1-v2.md)ことをお勧めします。

## <a name="alternate-solution-use-a-custom-domain-name"></a>代替ソリューション: カスタム ドメイン名を使用する

App Service のカスタム ドメイン機能を使用することは、トラフィックを Application Gateway のドメイン名 (この例では `www.contoso.com`) に常にリダイレクトするためのもう 1 つのソリューションです。 この構成は、ARR Affinity Cookie の問題の解決策としても機能します。 既定では、ARRAffinity Cookie ドメインは、Application Gateway のドメイン名ではなく、App Service の既定のホスト名 (example.azurewebsites.net) に設定されます。 そのため、そのような場合、ブラウザーでは、要求と Cookie のドメイン名の違いによって Cookie が拒否されます。

リダイレクトと ARRAffinity の Cookie ドメイン不一致の両方の問題については、所定の方法に従うことができます。 この方法では、カスタム ドメインの DNS ゾーンへのアクセス権が必要になります。

**手順 1**: App Service でカスタム ドメインを設定し、[CNAME および TXT DNS レコード](../app-service/app-service-web-tutorial-custom-domain.md#3-get-a-domain-verification-id)を追加してドメインの所有権を確認します。
レコードは次のようになります。
-  `www.contoso.com` IN CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` IN TXT "`<verification id string>`"


**手順 2**: 前の手順の CNAME レコードは、ドメインの検証にのみ必要でした。 最終的には、Application Gateway 経由でトラフィックをルーティングする必要があります。 したがって、`www.contoso.com` の CNAME を、Application Gateway の FQDN を指すように変更できます。 Application Gateway の FQDN を設定するには、そのパブリック IP アドレス リソースに移動し、それに "DNS 名ラベル" を割り当てます。 更新された CNAME レコードは次のようになります。 
-  `www.contoso.com` IN CNAME `contoso.eastus.cloudapp.azure.com`


**手順 3**: 関連する HTTP 設定の [Pick Hostname from Backend Address]\(バックエンド アドレスからホスト名を選択する\) を無効にします。

PowerShell では、`Set-AzApplicationGatewayBackendHttpSettings` コマンドで `-PickHostNameFromBackendAddress` スイッチを使用しないでください。


**手順 4**: プローブによって、バックエンドが正常で、運用可能なトラフィックであると判断されるためには、ホスト フィールドを App Service のカスタムまたは既定のドメインに指定して、カスタムの正常性プローブを設定します。

PowerShell では、`Set-AzApplicationGatewayProbeConfig` コマンドで `-PickHostNameFromBackendHttpSettings` スイッチを使用せずに、プローブの -HostName スイッチで App Service のカスタムまたは既定のドメインを使用してください。

既存の構成に対し、PowerShell を使用して前述の手順を実装するには、下のサンプル PowerShell スクリプトを使用します。 Probe と HTTP Settings の構成に **-PickHostname** スイッチを使用していないことに注目してください。

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
  ## <a name="next-steps"></a>次のステップ

前の手順で問題を解決できなかった場合、[サポート チケット](https://azure.microsoft.com/support/options/)を開きます。
