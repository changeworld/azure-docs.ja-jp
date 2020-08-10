---
title: Azure Data Factory でセルフホステッド統合ランタイムのトラブルシューティングを行う
description: Azure Data Factory でセルフホステッド統合ランタイムの問題をトラブルシューティングする方法について説明します。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 07/19/2020
ms.author: abnarain
ms.openlocfilehash: 521756081db938e749849e6f3630dbd60700d24f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87023861"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のセルフホステッド統合ランタイムの一般的なトラブルシューティング方法について説明します。

## <a name="gather-self-hosted-integration-runtime-logs-from-azure-data-factory"></a>Azure Data Factory からセルフホステッド統合ランタイム ログを収集する

セルフホステッド IR/共有 IR で実行したアクティビティが失敗した場合、Azure Data Factory ではエラー ログを表示してアップロードすることができます。 以下の手順に従ってエラー レポート ID を取得したら、レポート ID を入力して関連する既知の問題を特定できます。

1. **[アクティビティの実行]** ページに移動します。

1. **[エラー]** 列の下にあるボタンをクリックします。

    ![[アクティビティの実行] ページ](media/self-hosted-integration-runtime-troubleshoot-guide/activity-runs-page.png)

1. 失敗したアクティビティの実行に関連するログが表示されます。 支援が必要な場合は、 **[ログの送信]** ボタンをクリックしてください。

    ![[ログの送信]](media/self-hosted-integration-runtime-troubleshoot-guide/send-logs.png)

1. 送信するログを選択できます。 *セルフホステッド IR* では、失敗したアクティビティに関連するログ、またはセルフホステッド IR ノードのすべてのログをアップロードできます。 *共有 IR* では、失敗したアクティビティに関連するログのみをアップロードできます。

    ![ログの選択](media/self-hosted-integration-runtime-troubleshoot-guide/choose-logs.png)

1. 問題解決のためにさらに支援が必要な場合は、ログをアップロードする際に、レポート ID を記録しておきます。

    ![ログのアップロード](media/self-hosted-integration-runtime-troubleshoot-guide/upload-logs.png)

> [!NOTE]
> ログの表示とアップロードの要求は、すべてのオンライン セルフホステッド IR インスタンスで実行されます。 ログが見つからない場合に備えて、すべてのセルフホステッド IR インスタンスがオンラインになっていることを確認してください。 


## <a name="common-errors-and-resolutions"></a>よくあるエラーと解決方法

### <a name="error-message"></a>エラー メッセージ: 

`Self-hosted integration runtime can't connect to cloud service`

![セルフホステッド IR の接続問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイムが Data Factory サービス (バックエンド) に接続できません。 この問題は、通常、ファイアウォールのネットワーク設定によって発生します。

#### <a name="resolution"></a>解決方法

1. 統合ランタイム サービスが実行されているかどうかを確認します。
    
   ![セルフホステッド IR サービスの実行状態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
1. サービスが実行されている場合は、手順 3 に進みます。

1. セルフホステッド統合ランタイム (既定の設定) にプロキシが構成されていない場合は、セルフホステッド統合ランタイムがインストールされているマシン上で次の PowerShell コマンドを実行します。

    ```powershell
    (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
    ```
        
   > [!NOTE]     
   > サービスの URL は、Data Factory の場所によって変わる場合があります。 サービスの URL は、 **[ADF UI]**  >  **[接続]**  >  **[統合ランタイム]**  >  **[Edit Self-hosted IR]\(セルフホステッド IR の編集\)**  >  **[ノード]**  >  **[View Service URLs]\(サービスの URL の表示\)** で確認できます。
            
    想定される応答を次に示します。
            
    ![PowerShell コマンドの応答](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
1. 想定される応答が得られない場合は、状況に応じて次のいずれかの方法を使用します。
            
    * "リモート名を解決できませんでした" というメッセージを受け取る場合は、ドメイン ネーム システム (DNS) の問題が発生しています。 この問題を解決するには、ネットワーク チームに問い合わせてください。
    * "ssl/tls cert is not trusted"(ssl/tls 証明書は信頼されていません) というメッセージを受け取る場合は、マシン上で https://wu2.frontend.clouddatahub.net/ の証明書が信頼されているかどうかを確認し、証明書マネージャーを使用して公開証明書をインストールします。 この操作により、問題が軽減されます。
    * **[Windows]**  >  **[イベント ビューアー (ログ)]**  >  **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** に移動し、DNS、ファイアウォール規則、または会社のネットワーク設定が原因でエラーが発生していないかを確認します (そのようなエラーが見つかった場合は、接続を強制的に閉じてください)。どのような企業でもネットワーク設定をカスタマイズしているため、ネットワーク チームに問い合わせてこのような問題を解決してください。

1. セルフホステッド統合ランタイムで "プロキシ" が構成されている場合は、プロキシ サーバーからサービス エンドポイントにアクセスできることを確認します。 サンプル コマンドについては、「[PowerShell, web requests, and proxies](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)」(PowerShell、Web 要求、プロキシ) を参照してください。    
                
    ```powershell
    $user = $env:username
    $webproxy = (get-itemproperty 'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet
    Settings').ProxyServer
    $pwd = Read-Host "Password?" -assecurestring
    $proxy = new-object System.Net.WebProxy
    $proxy.Address = $webproxy
    $account = new-object System.Net.NetworkCredential($user,[Runtime.InteropServices.Marshal]::PtrToStringAuto([Runtime.InteropServices.Marshal]::SecureStringToBSTR($pwd)), "")
    $proxy.credentials = $account
    $url = "https://wu2.frontend.clouddatahub.net/"
    $wc = new-object system.net.WebClient
    $wc.proxy = $proxy
    $webpage = $wc.DownloadData($url)
    $string = [System.Text.Encoding]::ASCII.GetString($webpage)
    $string
    ```

想定される応答を次に示します。
            
![PowerShell コマンドの応答 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

> [!NOTE] 
> プロキシに関する考慮事項：
> *    プロキシ サーバーを [信頼できる宛先のリスト] に含める必要があるかどうかを確認します。 その場合は、必ず[このようなドメイン](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)を [信頼できる宛先のリスト] に含めます。
> *    TLS/SSL 証明書 "wu2.frontend.clouddatahub.net/" がプロキシ サーバー上で信頼されているかどうかを確認します。
> *    プロキシで Active Directory 認証を使用している場合は、サービス アカウントを、"Integration Runtime サービス" としてプロキシにアクセスできるユーザー アカウントに変更してください。

### <a name="error-message"></a>エラー メッセージ: 
`Self-hosted integration runtime node/ logical SHIR is in Inactive/ "Running (Limited)" state`

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイム ノードは、次のスクリーンショットに示すように、 **[非アクティブ]** 状態になる場合があります。

![非アクティブなセルフホステッド IR ノード](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

この動作は、ノードが相互に通信できない場合に発生します。

#### <a name="resolution"></a>解決方法

1. ノードでホストされている VM にログインします。 イベント ビューアーを開き、 **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** 以下のすべてのエラー ログをフィルター処理します。

1. エラー ログに次のエラーが含まれているかどうかを確認します。 
    
    ```
    System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    ```
       
1. このエラーが表示されている場合は、コマンド ラインで次を実行します。 

   ```
   telnet 10.2.4.10 8060
   ```
   
1. 次のエラーが発生した場合は、IT 部門に連絡して、この問題の解決の支援を依頼してください。 telnet が正常に実行された後も、引き続き統合ランタイム ノードの状態に問題がある場合は、Microsoft サポートに問い合わせてください。
        
   ![コマンド ライン エラー](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1. エラー ログに次が含まれているかどうかを確認します。

    ```
    Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)
    ```
    
1. この問題を解決するには、次の方法のいずれかまたは両方を試してください。
    - すべてのノードを同じドメインに配置する。
    - ホストされているすべての VM のホスト ファイルで、IP と ホストのマッピングを追加する。


## <a name="troubleshoot-connectivity-issue"></a>接続の問題のトラブルシューティング

### <a name="troubleshoot-connectivity-issue-between-self-hosted-ir-and-data-factory-or-self-hosted-ir-and-data-sourcesink"></a>セルフホステッド IR と Data Factory 間、またはセルフホステッド IR とデータソース/シンク間の接続に関する問題のトラブルシューティング

ネットワーク接続の問題のトラブルシューティングを行うには、[ネットワーク トレースの収集](#how-to-collect-netmon-trace)方法を確認して、その使用方法を理解し、[Netmon トレースを分析](#how-to-analyze-netmon-trace)してから、実際のケースでセルフホステッド IR から Netmon ツールを適用する必要があります。

接続の問題のトラブルシューティングを行う場合 (セルフホステッド IR と Data Factory 間、 

![HTTP 要求に失敗しました](media/self-hosted-integration-runtime-troubleshoot-guide/http-request-error.png)

またはセルフホステッド IR とデータソース/シンク間など)、次のエラーが発生することがあります。

**エラー メッセージ:** 
`Copy failed with error:Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,Message=Cannot connect to SQL Server: ‘IP address’`

**エラー メッセージ:** 
`One or more errors occurred. An error occurred while sending the request. The underlying connection was closed: An unexpected error occurred on a receive. Unable to read data from the transport connection: An existing connection was forcibly closed by the remote host. An existing connection was forcibly closed by the remote host Activity ID.`

**解決策:** 上記の問題が発生した場合は、次の手順に従ってトラブルシューティングを行うことができます。

Netmon トレースを取得して、さらに分析します。
- まず、フィルターを設定して、サーバーからクライアント側へのリセットをすべて表示することができます。 次の例では、サーバー側が Data Factory サーバーであることがわかります。

    ![Data factory サーバー](media/self-hosted-integration-runtime-troubleshoot-guide/data-factory-server.png)

- リセット パッケージを取得したら、TCP を追跡してメッセージ交換を見つけることができます。

    ![メッセージ交換の検索](media/self-hosted-integration-runtime-troubleshoot-guide/find-conversation.png)

- 次に、フィルターを削除することにより、次のクライアントと Data Factory サーバー間の変換を取得できます。

    ![メッセージ交換の取得](media/self-hosted-integration-runtime-troubleshoot-guide/get-conversation.png)
- 収集された netmon トレースに基づき、TTL (TimeToLive) の合計が 64 であることがわかります。 [この記事](https://packetpushers.net/ip-time-to-live-and-hop-limit-basics/)に記載されている「**既定の TTL とホップの制限値**」(下に抜粋) によると、パッケージをリセットして切断を発生させたのは Linux システムであることがわかります。

    既定の TTL とホップの制限値は、オペレーティング システムによって異なります。既定値の一部を次に示します。
    - Linux カーネル 2.4 (2001 年ごろ):TCP、UDP、および ICMP の場合は 255
    - Linux カーネル 4.10 (2015):TCP、UDP、および ICMP の場合は 64
    - Windows XP (2001):TCP、UDP、および ICMP の場合は 128
    - Windows 10 (2015):TCP、UDP、および ICMP の場合は 128
    - Windows Server 2008:TCP、UDP、および ICMP の場合は 128
    - Windows Server 2019 (2018):TCP、UDP、および ICMP の場合は 128
    - MacOS (2001):TCP、UDP、および ICMP の場合は 64

    ![TTL 61](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-61.png)
    
    ただし、上記の例では 64 ではなく 61 と表示されています。これは、ネットワーク パッケージが宛先に到達すると、ルーターやネットワーク デバイスなどのさまざまなホップを経由する必要があるためです。 最終的な TTL では、ルーター/ネットワーク デバイスの数が差し引かれます。
    この場合は、リセットが Linux システムから TTL 64 で送信された可能性があることがわかります。

- リセット デバイスの発信元を確認するには、セルフホステッド IR から 4 番目のホップを確認する必要があります。
 
    *Linux システム A からの TTL 64 のネットワーク パッケージ -> B TTL 64-1 = 63 -> C TTL 63-1 = 62 -> TTL 62-1 = 61 セルフホステッド IR*

- 理想的な状況では、TTL は 128 になります。これは、Windows システムで Data Factory が実行されていることを意味します。 次の例に示すように、*128 – 107 = 21 ホップ*になります。つまり、TCP 3 ハンドシェイク中に、そのパッケージに関して 21 ホップが Data Factory からセルフホステッド IR に送信されたことを意味します。
 
    ![TTL 107](media/self-hosted-integration-runtime-troubleshoot-guide/ttl-107.png)

    このため、セルフホステッド IR からの 4 番目のホップを確認するには、ネットワーク チームと協力する必要があります。 Linux システムのファイアウォールである場合は、TCP 3 ハンドシェイクの後にそのデバイスがパッケージをリセットした理由をログで確認します。 ただし、調査する場所がわからない場合は、セルフホステッド IR とファイアウォールから問題が発生したときの netmon トレースを取得して、どのデバイスによってこのパッケージがリセットされて切断されるのかを特定してください。 この場合も、ネットワーク チームと協力して作業を進める必要があります。

### <a name="how-to-collect-netmon-trace"></a>Netmon トレースを収集する方法

1.  [この Web サイト](https://www.microsoft.com/en-sg/download/details.aspx?id=4865)から Netmon ツールをダウンロードし、サーバー マシン (問題が発生しているサーバー) とクライアント (セルフホステッド IR など) にインストールします。

2.  フォルダーを作成します (たとえば、*D:\netmon* というパスに作成します)。 ログを保存するための十分な領域があることを確認します。

3.  IP とポートの情報をキャプチャします。 
    1. コマンド プロンプトを起動します。
    2. [管理者として実行] を選択し、次のコマンドを実行します。
       
        ```
        Ipconfig /all >D:\netmon\IP.txt
        netstat -abno > D:\netmon\ServerNetstat.txt
        ```

4.  Netmon トレース (ネットワーク パッケージ) をキャプチャします。
    1. コマンド プロンプトを起動します。
    2. [管理者として実行] を選択し、次のコマンドを実行します。
        
        ```
        cd C:\Program Files\Microsoft Network Monitor 3
        ```
    3. 次の 3 種類のコマンドを使用して、ネットワーク ページをキャプチャできます。
        - オプション A: ラウンドロビン ファイル コマンド (ファイルが 1 つだけキャプチャされ、古いログが上書きされます)。

            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.cap:200M
            ```         
        - オプション B: チェーン ファイル コマンド (200 MB に達すると、新しいファイルが作成されます)。
        
            ```
            nmcap /network * /capture /file D:\netmon\ServerConnection.chn:200M
            ```          
        - オプション C:スケジュール ファイル コマンド。

            ```
            nmcap /network * /capture /StartWhen /Time 10:30:00 AM 10/28/2011 /StopWhen /Time 11:30:00 AM 10/28/2011 /file D:\netmon\ServerConnection.chn:200M
            ```  

5.  Netmon トレースのキャプチャを停止するには、**Ctrl + C** を押します。
 
> [!NOTE]
> クライアント コンピューターでのみ netmon トレースを収集できる場合は、サーバーの IP アドレスを取得してトレースの分析に役立ててください。

### <a name="how-to-analyze-netmon-trace"></a>Netmon トレースを分析する方法

上記で収集された netmon トレースを使用して telnet **8.8.8.8 888** を実行しようとすると、次のトレースが表示されます。

![Netmon トレース 1](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-1.png)

![Netmon トレース 2](media/self-hosted-integration-runtime-troubleshoot-guide/netmon-trace-2.png)
 

これは、ポート **888** で **8.8.8.8** のサーバー側に TCP 接続を確立できなかったことを意味し、2 つの **SynReTransmit** 追加パッケージが表示されています。 最初のパッケージでソース **SELF-HOST2** が **8.8.8.8**に接続できなかったため、接続の試行が継続されます。

> [!TIP]
> - **[フィルターの読み込み]**  ->  **[標準フィルター]**  ->  **[アドレス]**  ->  **[IPv4 アドレス]** をクリックします。
> - フィルターとして **IPv4.Address == 8.8.8.8** を入力し、 **[適用]** をクリックします。 その後は、ローカル コンピューターから宛先 **8.8.8.8** への通信のみが表示されます。

![フィルター アドレス 1](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-1.png)
        
![フィルター アドレス 2](media/self-hosted-integration-runtime-troubleshoot-guide/filter-addresses-2.png)

次の例は、良好な場合のシナリオを示しています。 

- Telnet **8.8.8.8 53** が問題なく動作している場合は、TCP 3 ハンドシェイクが行われ、その後、TCP 4 ハンドシェイクでセッションが終了したことを確認できます。

    ![良好なシナリオの例 1](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-1.png)
     
    ![良好なシナリオの例 2](media/self-hosted-integration-runtime-troubleshoot-guide/good-scenario-2.png)

- 上記の TCP 3 ハンドシェイクに基づき、次のワークフローを確認できます。

    ![TCP 3 ハンドシェイクのワークフロー](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-3-handshake-workflow.png)
 
- セッションを完了するための TCP 4 ハンドシェイクとそのフローは、次のようになります。

    ![TCP 4 ハンドシェイク](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake.png)

    ![TCP 4 ハンドシェイクのワークフロー](media/self-hosted-integration-runtime-troubleshoot-guide/tcp-4-handshake-workflow.png) 


## <a name="next-steps"></a>次のステップ

トラブルシューティングの詳細について、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A 質問ページ](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Data Factory に関する Stack overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
