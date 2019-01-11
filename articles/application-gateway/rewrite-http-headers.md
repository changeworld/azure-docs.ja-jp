---
title: Azure Application Gateway で HTTP ヘッダーを書き換える | Microsoft Docs
description: この記事では、Azure Application Gateway で HTTP ヘッダーを書き換える機能の概要を説明します
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 12/20/2018
ms.author: absha
ms.openlocfilehash: 6750276cf31d0c804b38cdf3ea6e41a4505c93f1
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971820"
---
# <a name="rewrite-http-headers-with-application-gateway-public-preview"></a>Application Gateway で HTTP ヘッダーを書き換える (パブリック プレビュー)

クライアントとサーバーは、HTTP ヘッダーを使用して、要求または応答に追加の情報を渡すことができます。 これらの HTTP ヘッダーの書き換えによって、HSTS/X-XSS-Protection などのセキュリティ関連ヘッダー フィールドを追加する、バックエンド サーバー名のような機密情報が漏れる可能性がある応答ヘッダー フィールドを削除するなど、いくつかの重要なシナリオを実現できます。

Application Gateway では、受信 HTTP 要求と送信 HTTP 応答のヘッダーを書き換える機能がサポートされるようになりました。 要求/応答パケットがクライアントとバックエンド プールの間を移動する間に、HTTP 要求および応答ヘッダーを追加、削除、または更新することができます。 標準ヘッダー フィールド ([RFC 2616](https://www.ietf.org/rfc/rfc2616.txt) で定義) と非標準ヘッダー フィールドの両方を書き換え可能です。

> [!NOTE] 
>
> HTTP ヘッダーの書き換えは、[新しい SKU の [Standard_V2\]](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) でのみサポートされます

Application Gateway でのヘッダー書き換えのサポートにより、以下が提供されます。

- **グローバルなヘッダーの書き換え**: サイトに関係があるすべての要求と応答の特定のヘッダーを書き換えることができます。
- **パス ベースのヘッダーの書き換え**: この種類の書き換えでは、特定のサイト領域にのみ関係がある要求や応答のヘッダー書き換えが可能になります。たとえば、/cart/* で示されるショッピング カート領域に関係があるヘッダーです。

この変更により、以下を行う必要があります。

1. http ヘッダーの書き換えに必要な新しいオブジェクトを作成します。 
   - **RequestHeaderConfiguration**: このオブジェクトは、書き換えようとしている要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
   - **ResponseHeaderConfiguration**: このオブジェクトは、書き換えようとしている応答ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
   - **ActionSet**: このオブジェクトには、上で指定した要求ヘッダーと応答ヘッダーの構成が格納されます。 
   - **RewriteRule**: このオブジェクトには、上で指定したすべての *ActionSet* が格納されます。 
   - **RewriteRuleSet**: このオブジェクトは、すべての *RewriteRule* を含み、基本またはパス ベースの要求ルーティング規則にアタッチする必要があります。
2. その後、書き換えルール セットをルーティング規則にアタッチする必要があります。 この書き換えの構成は、作成されると、ルーティング規則によってソース リスナーにアタッチされます。 基本ルーティング規則の使用時には、書き換えの構成は、ソース リスナーに関連付けられている、グローバルなヘッダーの書き換えとなります。 パスベースのルーティング規則を使用する場合、ヘッダーの書き換えの構成は、URL パス マップで定義されています。 そのため、サイトの特定のパス領域にのみ適用されます。

http ヘッダーの書き換えルール セットを複数作成し、それぞれの書き換えルール セットを複数のリスナーに適用することができます。 ただし、特定のリスナーに対して適用できる http 書き換えルール セットは 1 つだけです。

ヘッダーでは、以下に対する値を書き換えることができます。

- テキスト値。 

  *例:* 

  ```azurepowershell-interactive
  $responseHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -  HeaderValue "max-age=31536000")
  ```

- 別のヘッダーの値。 

  *例 1:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-RequestHeader" -HeaderValue {http_req_oldHeader}
  ```

  > [!Note] 
  > 要求ヘッダーを指定するには次の構文を使用する必要があります: {http_req_headerName}

  *例 2*:

  ```azurepowershell-interactive
  $responseHeaderConfiguration= New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-New-ResponseHeader" -HeaderValue {http_resp_oldHeader}
  ```

  > [!Note] 
  > 応答ヘッダーを指定するには次の構文を使用する必要があります: {http_resp_headerName}

- サポートされているサーバー変数の値。

  *例:* 

  ```azurepowershell-interactive
  $requestHeaderConfiguration = New-AzureRmApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Ciphers-Used" -HeaderValue "{var_ciphers_used}"
  ```

  > [!Note] 
  > サーバー変数を指定するには次の構文を使用する必要があります: {var_serverVariable}

- 上記の組み合わせ。

上で触れたサーバー変数は、サーバー、クライアントとの接続、およびその接続での現在の要求に関する情報を提供する変数です。 この機能は、以下のサーバー変数に対するヘッダーの書き換えをサポートしています。

| サポートされているサーバー変数 | 説明                                                  |
| -------------------------- | :----------------------------------------------------------- |
| ciphers_supported          | クライアントでサポートされている暗号の一覧を返します          |
| ciphers_used               | 確立された SSL 接続で使用される暗号の文字列を返します |
| client_latitude            | クライアントの IP アドレスに応じて国、地域、および市区町村を特定します |
| client_longitude           | クライアントの IP アドレスに応じて国、地域、および市区町村を特定します |
| client_port                | クライアント ポート                                                  |
| client_tcp_rtt             | クライアントの TCP 接続に関する情報。TCP_INFO ソケット オプションをサポートするシステムで使用できます |
| client_user                | HTTP 認証の使用時に、認証のために提供されるユーザー名 |
| host                       | 優先順位は次の通りです: 要求行のホスト名、"Host" 要求ヘッダー フィールドのホスト名、要求に一致するサーバー名 |
| http_method                | URL 要求を行うために使用されたメソッド。 たとえば、GET、POST などです。 |
| http_status                | セッションの状態。例: 200、400、403 など                       |
| http_version               | 要求のプロトコル。通常は "HTTP/1.0"、"HTTP/1.1"、または "HTTP/2.0" です |
| query_string               | 要求された URL 内で "?" の後にある、変数と値のペアから成る一覧。 |
| received_byte              | 要求の長さ (要求行、ヘッダー、および要求本文を含む) |
| request_query              | 要求行内の引数                                |
| request_scheme             | 要求スキーム。"http" または "https" です                            |
| request_uri                | 完全な元の要求 URI (引数を含む)                   |
| sent_bytes                 | クライアントに送信されたバイト数                             |
| server_port                | 要求を受け付けたサーバーのポート                 |
| ssl_connection_protocol    | 確立された SSL 接続のプロトコルを返します        |
| ssl_enabled                | 接続が SSL モードで動作している場合は "on"、それ以外の場合は空の文字列 |

## <a name="limitations"></a>制限事項

- HTTP ヘッダーを書き換えるこの機能は、現時点で、Azure PowerShell、Azure API、および Azure SDK を通じてのみ使用できます。 ポータルと Azure CLI でのサポートは間もなく提供される予定です。

- お使いのアプリケーション ゲートウェイでヘッダーの書き換えを適用したら、ポータルでこの機能がサポートされるまで、ポータルを使用してそのアプリケーション ゲートウェイに対する以降の変更を行わないようにしてください。 書き換えルールの適用後に、ポータルを使用してアプリケーション ゲートウェイに変更を加えると、ヘッダー書き換えルール。 引き続き、Azure PowerShell、Azure API、または Azure SDK を使用して変更を行えます。

- HTTP ヘッダーの書き換えは、新しい SKU の [Standard_V2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant) でのみサポートされます この機能は古い SKU ではサポートされません。

- Connect、Upgrade、Host の各ヘッダーの書き換えはまだサポートされていません。

- 2 つの重要なサーバー変数である client_ip (要求元のクライアントの IP アドレス) と cookie_*name* (*name* cookie) はまだサポートされていません。 client_ip サーバー変数は、お客様が、Application Gateway によって設定された x-forwarded-for ヘッダーを書き換える予定があるシナリオで特に有用です。この方法により、ヘッダーには、ポート情報は含まれず、クライアントの IP アドレスのみが含まれるようになります。

  これらのサーバー変数はどちらも、間もなくサポートされる予定です。

- 条件に応じて http ヘッダーを書き換える機能が間もなく提供されます。

- ヘッダー名には、任意の英数字と、[RFC 7230](https://tools.ietf.org/html/rfc7230#page-27) で定義されている特定の記号を含めることができます。 ただし、現在はヘッダー名内で特殊文字の "アンダー スコア" (\_) がサポートされていません。 

## <a name="need-help"></a>お困りの際は、

この機能についてお困りの際は、[AGHeaderRewriteHelp@microsoft.com](mailto:AGHeaderRewriteHelp@microsoft.com) でお問い合わせください。

## <a name="next-steps"></a>次の手順

HTTP ヘッダーを書き換える機能について学習した後は、[HTTP ヘッダーを書き換える、自動スケールおよびゾーン冗長のアプリケーション ゲートウェイの作成](tutorial-http-header-rewrite-powershell.md)に関するページ、または[既存の自動スケールおよびゾーン冗長のアプリケーション ゲートウェイでの HTTP ヘッダーの書き換え](add-http-header-rewrite-rule-powershell.md)に関するページに進みます
