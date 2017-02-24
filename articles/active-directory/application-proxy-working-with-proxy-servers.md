---
title: "Azure AD で既存のオンプレミス プロキシ サーバーと連携する| Microsoft Docs"
description: "既存のオンプレミス プロキシ サーバーと連携する方法について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2fab64ef8bf07532c4fd2fff4f4af68dc0b44b45
ms.openlocfilehash: 659edc19c9f043d6a388f1bb293c72e379fa6e4a


---

# <a name="work-with-existing-on-premises-proxy-servers"></a>既存のオンプレミス プロキシ サーバーと連携する

この記事では、送信プロキシ サーバーと連携するようにアプリケーション プロキシ コネクタを構成する方法について説明します。 この記事は、既存のプロキシがあるネットワーク環境のお客様を対象としています。

まず、次の主なデプロイ シナリオを見ていきます。
* オンプレミスの送信プロキシをバイパスするようにコネクタを構成する。
* 送信プロキシを使用して Azure AD アプリケーション プロキシにアクセスにするようにコネクタを構成する。

コネクタの仕組みの詳細については、「[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-application-proxy-get-started)」を参照してください。

## <a name="configure-your-connectors"></a>コネクタの構成

中核となるコネクタ サービスでは、Azure Service Bus を使用して Azure AD アプリケーション プロキシ サービスへの基本的な通信の一部を処理します。 Service Bus の使用により、構成要件が追加されます。

Azure AD の接続の問題を解決する方法については、「[How to troubleshoot Azure AD Application Proxy connectivity problems (Azure AD アプリケーション プロキシの接続に関する問題のトラブルシューティング方法)](https://blogs.technet.microsoft.com/applicationproxyblog/2015/03/24/how-to-troubleshoot-azure-ad-application-proxy-connectivity-problems)」を参照してください。 

## <a name="configure-the-outbound-proxy"></a>送信プロキシの構成

お使いの環境に送信プロキシがある場合は、インストールを実行するアカウントが、送信プロキシを使用するように適切に構成されていることを確認します。 インストーラーはインストールを実行するユーザーのコンテキストで実行されるため、Microsoft Edge または他のインターネット ブラウザーを使用して構成します。 

Microsoft Edge を使用してプロキシ設定を構成するには、[設定]、[詳細設定を表示]、[プロキシ セットアップを開く]、[手動プロキシ セットアップ] の順に進みます。 [プロキシ サーバーを使用する] をオンにして、[ローカル (イントラネット) のアドレスにはプロキシ サーバーを使わない] にチェック マークを入れて、ローカル プロキシ サーバーを反映するようにアドレスとポートを変更します。

次のオプション ボックスに示すように、必要なプロキシ設定を入力します。

 ![AzureAD でローカルのアドレスをバイパスする](./media/application-proxy-working-with-proxy-servers/proxy-bypass-local-addresses.png)
 
## <a name="bypass-outbound-proxies"></a>送信プロキシをバイパス

既定では、送信要求を自動的に行うためにコネクタの使用する基本的な OS コンポーネントは、Web プロキシ自動発見 (WPAD) が環境で有効になっている場合、これを使用してネットワーク上のプロキシ サーバーを検索します。

これは通常、wpad.domainsuffix の DNS 参照を実行することで機能します。 これが DNS で解決すると、環境内のプロキシ構成スクリプトとなる wpad.dat の IP アドレスに対して HTTP 要求が作成されます。 コネクタはこれを使用して送信プロキシ サーバーを選択します。 ただし、プロキシで追加の構成設定が必要な場合は、コネクタのトラフィックがプロキシを経由しないことがあります。 

次のセクションでは、トラフィックが送信プロキシを経由するようにするために必要な、送信プロキシの構成手順について説明します。 その前に、オンプレミス プロキシをバイパスして Azure サービスへの直接接続を使用するようにコネクタを構成する方法について見ていきましょう。 これは管理する構成が&1; つ少なくて済むため、(ネットワーク ポリシーで許可されている限り) 推奨される方法です。

コネクタによる送信プロキシの使用を無効にするには、次のコード例に示すように、C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config ファイルを編集し、[system.net] セクションを追加します。

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>
<defaultProxy enabled="false"></defaultProxy>
</system.net>
 <runtime>
<gcServer enabled="true"/>
  </runtime>
  <appSettings>
<add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```
コネクタ アップデーター サービスもプロキシをバイパスするようにするには、C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config に格納されている ApplicationProxyConnectorUpdaterService.exe.config ファイルに同じような変更を加えます。

既定の .config ファイルに戻す必要がある場合に備えて、元のファイルのコピーを作成するようにしてください。 

## <a name="use-the-outbound-proxy-server"></a>送信プロキシ サーバーの使用

先ほど説明したように、一部のお客様の環境では、すべての送信トラフィックが例外なく送信プロキシを経由する必要がある場合があります。 そのような場合は、プロキシのバイパスは選択肢にありません。

次に示すように、コネクタのトラフィックが送信プロキシを経由するように構成できます。

 ![AzureAD でローカルのアドレスをバイパスする](./media/application-proxy-working-with-proxy-servers/configure-proxy-settings.png)

送信トラフィックしかないため、コネクタ間で負荷分散をセットアップしたり、ファイアウォール経由で受信アクセスを構成する必要はありません。

いずれの場合も、次の手順を実行する必要があります。
1. コネクタとアップデーター サービスが送信プロキシを経由するように構成します。
2. Azure AD アプリケーション プロキシ サービスへの接続を許可するようにプロキシ設定を構成します。

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>手順 1: コネクタと関連サービスが送信プロキシを経由するように構成する

先ほど説明したように、環境内で WPAD を有効にし、適切に構成している場合、コネクタは送信プロキシ サーバーを自動的に検出して使用を試みます。 一方で、送信プロキシを経由するようにコネクタを明示的に構成することができます。 

これを行うには、次のコード例に示すように、C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config ファイルを編集し、[system.net] セクションを追加します。

```xml
 <?xml version="1.0" encoding="utf-8" ?>
<configuration>
<system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
</system.net>
  <runtime>
     <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

>[!NOTE]
>ローカルのプロキシ サーバー名、または IP アドレスとリッスンしているポートを反映するように _proxyserver:8080_ を変更します。
>

次に、C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config に格納されているファイルに同じような変更を加えて、コネクタ アップデーター サービスがプロキシを使用するように構成する必要があります。

###<a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>手順 2: プロキシがコネクタと関連サービスのトラフィックの通過を許可するように構成する

送信プロキシで考慮すべき点は次の 4 つです。
* プロキシ送信規則
* プロキシの認証
* プロキシ ポート
* SSL インスペクション

#### <a name="2a-proxy-outbound-rules"></a>2.A: プロキシ送信規則
コネクタ サービスへのアクセスに、次のエンドポイントへのアクセスを許可します。

* *.msappproxy.net 
* *.servicebus.microsoft.net 

新規登録では、次のエンドポイントへのアクセスを許可します。

* login.windows.net 
* login.microsoftonline.com. 

コネクタ サービスで使用する Service Bus の基本的な管理チャネルでは、特定の IP アドレスへの接続がさらに必要です。 Azure では、Service Bus チームと連携して、完全な FQDN への移行に取り組んでいますが 、現在は&2; つのオプションを提供しています。 
 
* すべてのアクセス先に対するコネクタの送信アクセスを許可する。または、
* Azure データセンターの IP アドレス範囲 (https://www.microsoft.com/en-gb/download/details.aspx?id=41653 を参照) に対する、コネクタの送信アクセスを許可する。

>[!NOTE]
>Azure データセンターの IP アドレス範囲の一覧を使用する際に注意すべき点は、この一覧は毎週更新されるため、アクセス規則が適宜更新されるようにプロセスを整備する必要があることです。
>

#### <a name="2b-proxy-authentication"></a>2.B: プロキシの認証

プロキシの認証は現在サポートされていません。 現在はインターネット上のアクセス先に対して、コネクタの匿名アクセスを許可することをお勧めします。

#### <a name="2c-proxy-ports"></a>2.C: プロキシ ポート

コネクタは、CONNECT メソッドを使用して SSL ベースの送信接続を確立します。 つまりこれによって送信プロキシを経由するトンネルが設定されます。 一部のプロキシ サーバーは、既定では、標準の SSL ポート (443 など) への送信トンネリングしか許可しません。 この場合、追加のポートへのトンネリングを許可するようにプロキシ サーバーを構成する必要があります。

プロキシ サーバーが、標準以外の SSL ポート (8080、9090、9091、および 10100-10120) へのトンネリングを許可するように構成します。

>[!NOTE]
>Service Bus を HTTPS 経由で実行する場合は、ポート 443 が使用されます。 ただし既定では、Service Bus は直接 TCP 接続を試みて、直接接続に失敗した場合にのみ HTTPS に戻ってきます。 
> 

Service Bus のトラフィックも送信プロキシ サーバー経由で送信されるようにするには、コネクタが、ポート 9350、9352、および 5671 を使用した Azure サービスへの直接接続をできないようにする必要があります。

#### <a name="2d-ssl-inspection"></a>2.D: SSL インスペクション
コネクタのトラフィックに問題が生じるため、コネクタのトラフィックには SSL インスペクションを使用しないでください。 

つまりこれで終了です。 これですべてのトラフィックがプロキシを経由します。 問題が生じた場合は、次のトラブルシューティングの手順が役に立ちます。

### <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>コネクタのプロキシの問題とサービスの接続の問題のトラブルシューティング

コネクタの接続の問題を特定し、問題を解決する最善の方法は、コネクタ サービスの開始時に、サービス上でネットワーク キャプチャを実行することです。 これは手間のかかる作業なため、ネットワーク トレースのキャプチャおよびフィルター処理に関する簡単なヒントを見てみましょう。

任意の監視ツールを使用することができます。 この記事で紹介する用途には、Microsoft ネットワーク モニター 3.4 を使用しています。これは https://www.microsoft.com/en-us/download/details.aspx?id=4865 でダウンロードできます。

ここで使用する例とフィルターはネットワーク モニター限定ですが、紹介する原則はどの分析ツールにも適用できます。

### <a name="take-a-capture-using-microsoft-network-monitor"></a>Microsoft ネットワーク モニターを使ってキャプチャを実行する

キャプチャを開始するには、ネットワーク モニターを開いて [New Capture](新規キャプチャ) をクリックします。 [スタート] ボタンをクリックして開始します。

 ![AzureAD ネットワーク モニター](./media/application-proxy-working-with-proxy-servers/network-capture.png)

キャプチャが完了したら、[停止] ボタンをクリックしてキャプチャを終了します。

### <a name="take-a-capture-of-connector-traffic"></a>コネクタ トラフィックのキャプチャを実行する

最初のトラブルシューティングでは、次の手順を実行します。 

1. Services.msc で、Microsoft AAD アプリケーション プロキシ コネクタ サービスを停止します (下を参照)。
2. ネットワーク キャプチャを開始します。
3. Microsoft AAD アプリケーション プロキシ コネクタ サービスを開始します。
4. ネットワーク キャプチャを停止します。

 ![AzureAD ネットワーク モニター](./media/application-proxy-working-with-proxy-servers/services-local.png)

### <a name="look-at-the-connector-to-proxy-server-requests"></a>コネクタのプロキシ サーバーへの要求を調べる

ネットワーク キャプチャを取得したので、さっそくフィルター処理します。 トレースを調べるカギは、キャプチャをフィルター処理する方法を理解することです。

ここでは次のフィルターを使用します (8080 はプロキシ サービスのポート)。 

(http.Request or http.Response) and tcp.port==8080

このフィルターを [ディスプレイ フィルター] ウィンドウに入力して [適用] を選択すると、フィルターに基づいてキャプチャされたトラフィックがフィルター処理されます。

上記のフィルターでは、プロキシ ポート間との HTTP 要求および応答のみが表示されます。 プロキシ サーバーを使用するよう構成されたコネクタの起動時には、次のように表示されます。

 ![AzureAD ネットワーク モニター](./media/application-proxy-working-with-proxy-servers/http-requests.png)

特に、プロキシ サーバーへの通信を示す CONNECT 要求を調べます。 成功すると、HTTP OK (200) の応答が表示されます。

他の応答コード (407 または 502 など) が表示される場合は、プロキシが認証を必要としているか、何らかの理由でトラフィックを許可しないことを示します。 その場合は、プロキシ サーバー サポート チームのサポートを得てください。

### <a name="identify-failed-tcp-connection-attempts"></a>失敗した TCP 接続の試行を特定する

興味深いその他のよくあるシナリオには、コネクタが直接接続を試みて失敗するケースがあります。 

ネットワーク モニターの次のフィルターを使用すると、この問題を簡単に特定できます。

property.TCPSynRetransmit

SYN パケットは、TCP 接続を確立するために最初に送信されるパケットです。 パケットにより応答が返されない場合は、SYN パケットの送信が再試行されます。 上記のフィルターを使用すると、再送信されたすべての SYN パケットが表示されます。 これらがコネクタ関連のトラフィックに対応するかどうかを調べることができます。 

次の例は、Service Bus (ポート 9352) への失敗した接続の試みを示しています。

 ![AzureAD ネットワーク モニター](./media/application-proxy-working-with-proxy-servers/failed-connection-attempt.png)

上記のような応答が表示される場合は、コネクタが Azure Service Bus サービスに直接接続しようとしていることを示します。 コネクタが Azure サービスに直接接続することが考えられる場合、これはネットワークやファイアウォールの問題が発生していることを明確に示しています。

>[!NOTE]
>プロキシ サーバーを使用するよう構成した場合は、Service Bus が HTTPS 経由の接続試行に切り替える前に直接 TCP 接続を試みている可能性があるため、この点を留意してください。
>

ネットワーク トレースの分析はすべてのユーザー向きではありませんが 、ネットワークで起きていることに関する概要情報を取得できる、非常に価値のあるツールです。 

コネクタの接続の問題に悩んでいるお客様は、サポート チケットを作成してください。Azure サポート チームが問題の解決をお手伝いします。

アプリケーション プロキシ コネクタのエラーを解決する方法については、「[アプリケーション プロキシのトラブルシューティング](https://azure.microsoft.com/en-us/documentation/articles/active-directory-application-proxy-troubleshoot)」を参照してください。

## <a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシ コネクタを理解する](application-proxy-understand-connectors.md)<br>
[Azure AD アプリケーション プロキシ コネクタをサイレント インストールする方法](active-directory-application-proxy-silent-installation.md)







<!--HONumber=Feb17_HO1-->


