---
title: Azure Data Factory でセルフホステッド統合ランタイムのトラブルシューティングを行う
description: Azure Data Factory でセルフホステッド統合ランタイムの問題をトラブルシューティングする方法について説明します。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 94e214c55a0109beb85cd08ce87303e5bd0f8016
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835429"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのトラブルシューティング

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

この記事では、Azure Data Factory のセルフホステッド統合ランタイムの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-resolutions"></a>よくあるエラーと解決方法

### <a name="error-message-self-hosted-integration-runtime-cant-connect-to-cloud-service"></a>エラー メッセージ:Self-hosted integration runtime can't connect to cloud service (セルフホステッド統合ランタイムはクラウド サービスに接続できません)

![セルフホステッド IR の接続問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイムが Data Factory サービス (バックエンド) に接続できません。 この問題は、通常、ファイアウォールのネットワーク設定によって発生します。

#### <a name="resolution"></a>解像度

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

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>エラー メッセージ:セルフホステッド統合ランタイム ノード/論理 SHIR が非アクティブ/ "実行中 (制限あり)" の状態です

#### <a name="cause"></a>原因 

セルフホステッド統合ランタイム ノードは、次のスクリーンショットに示すように、 **[非アクティブ]** 状態になる場合があります。

![非アクティブなセルフホステッド IR ノード](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

この動作は、ノードが相互に通信できない場合に発生します。

#### <a name="resolution"></a>解像度

1. ノードでホストされている VM にログインします。 イベント ビューアーを開き、 **[アプリケーションとサービス ログ]**  >  **[統合ランタイム]** 以下のすべてのエラー ログをフィルター処理します。

1. エラー ログに次のエラーが含まれているかどうかを確認します。 
    
    ```System.ServiceModel.EndpointNotFoundException: Could not connect to net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager. The connection attempt lasted for a time span of 00:00:00.9940994. TCP error code 10061: No connection could be made because the target machine actively refused it 10.2.4.10:8060. 
    System.Net.Sockets.SocketException: No connection could be made because the target machine actively refused it. 
    10.2.4.10:8060
        
    at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
               
    at System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
               
    at System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
       
1. If you see this error, run the following on a command line: 

   **telnet 10.2.4.10 8060**.
1. If you receive the following error, contact your IT department for help with fixing this issue. After you can successfully telnet, contact Microsoft Support if you still have issues with the integrative runtime node status.
        
   ![Command-line error](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
1.    Check whether the error log contains the following:

    ```Error log: Cannot connect to worker manager: net.tcp://xxxxxx:8060/ExternalService.svc/ No DNS entries exist for host azranlcir01r1. No such host is known Exception detail: System.ServiceModel.EndpointNotFoundException: No DNS entries exist for host xxxxx. ---> System.Net.Sockets.SocketException: No such host is known at System.Net.Dns.GetAddrInfo(String name) at System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6) at System.Net.Dns.GetHostEntry(String hostNameOrAddress) at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- End of inner exception stack trace --- Server stack trace: at System.ServiceModel.Channels.DnsCache.Resolve(Uri uri)```
    
1. To resolve the issue, try one or both of the following methods:
    - Put all the nodes in the same domain.
    - Add the IP to host mapping in all the hosted VM's host files.


## Next steps

For more help with troubleshooting, try the following resources:

*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&A question page](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Stack overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Mapping data flows performance guide](concepts-data-flow-performance.md)
