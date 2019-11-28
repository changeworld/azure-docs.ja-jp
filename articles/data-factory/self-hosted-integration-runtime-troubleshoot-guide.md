---
title: Azure Data Factory でセルフホステッド統合ランタイムのトラブルシューティングを行う
description: Azure Data Factory でセルフホステッド統合ランタイムの問題をトラブルシューティングする方法について説明します。
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/07/2019
ms.author: abnarain
ms.openlocfilehash: 9adbc3d7d30aeb8c7cb2b89c326ac2b39a2e8d2b
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907223"
---
# <a name="troubleshoot-self-hosted-integration-runtime"></a>セルフホステッド統合ランタイムのトラブルシューティング

この記事では、Azure Data Factory のセルフホステッド統合ランタイムの一般的なトラブルシューティング方法について説明します。

## <a name="common-errors-and-resolutions"></a>よくあるエラーと解決方法

### <a name="error-message-self-hosted-integration-runtime-is-unable-to-connect-to-cloud-service"></a>エラー メッセージ:セルフホステッド統合ランタイムがクラウド サービスに接続できません。

- **現象**: 

    ![セルフホステッド IR の接続問題](media/self-hosted-integration-runtime-troubleshoot-guide/unable-to-connect-to-cloud-service.png)

- **原因**:セルフホステッド統合ランタイムがデータ ファクトリ サービス (バックエンド) に接続できません。 ほとんどの場合、ファイアウォールのネットワーク設定が原因です。

- **解決方法**: 

    1. Windows サービス "Integration Runtime サービス" が実行されているかどうかを確認します。
    
        ![セルフホステッド IR サービスの実行状態](media/self-hosted-integration-runtime-troubleshoot-guide/integration-runtime-service-running-status.png)
    
    2. [1] で示した Windows サービスが実行されている場合は、必要に応じて以下の手順を実行します。

        1. セルフホステッド統合ランタイム上で "プロキシ" が構成されていない場合 (既定の設定はプロキシなしの構成です)、セルフホステッド統合ランタイムがインストールされているコンピューターで次の PowerShell コマンドを実行します。 
            
            ```powershell
            (New-Object System.Net.WebClient).DownloadString("https://wu2.frontend.clouddatahub.net/")
            ```
            > [!NOTE] 
            > サービス URL は、データ ファクトリの場所によって異なる場合があります。 サービス URL は、[ADF UI] -> [接続] -> [統合ランタイム] -> [セルフホステッド IR の編集] -> [ノード] -> [サービス URL の表示] で確認できます。
            
            次に示すのは、予期される応答です。
            
            ![PowerShell コマンドの応答](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)
            
            応答が異なる場合は、必要に応じて以下の手順を実行します。
            
            * "リモート名を解決できませんでした" というエラーが表示される場合は、DNS に問題があります。 DNS 解決の問題を解決するには、ネットワーク チームに連絡してください。 
            * "ssl/tls 証明書が信頼されていません" というエラーが発生した場合は、"https://wu2.frontend.clouddatahub.net/" の証明書がコンピューター上で信頼されているかどうかを確認し、証明書マネージャーを使用して公開証明書をインストールします。これにより、この問題が回避されます。
            * [Windows] -> [Event viewer (logs)]\(イベント ビューアー (ログ)\) -> [アプリケーションとサービス ログ] -> [Integration Runtime] に移動し、問題がないか確認します。多くの場合、DNS、ファイアウォール規則、および会社のネットワーク設定 (強制的に接続を閉じる) が原因です。 ネットワーク設定のカスタマイズはどの会社でも行われているので、この問題については、ネットワーク チームと連携して詳細なトラブルシューティングを行ってください。

        2. セルフホステッド統合ランタイム上で "プロキシ" が構成されている場合は、プロキシ サーバーがサービス エンドポイントにアクセスできるかどうかを確認します。 サンプル コマンドについては、[こちら](https://stackoverflow.com/questions/571429/powershell-web-requests-and-proxies)を参照してください。    
                
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

            次に示すのは、予期される応答です。
            
            ![PowerShell コマンドの応答 2](media/self-hosted-integration-runtime-troubleshoot-guide/powershell-command-response.png)

            > [!NOTE] 
            > プロキシに関する考慮事項：
            > * プロキシ サーバーでホワイトリスト登録が必要かどうかを確認してください。 その場合は、[これらのドメイン](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations#firewall-requirements-for-on-premisesprivate-network)をホワイトリストに登録してください。
            > * "wu2.frontend.clouddatahub.net/" の TLS/SSL 証明書がプロキシ サーバーで信頼されていることを確認してください。
            > * プロキシで Active Directory 認証を使用している場合は、サービス アカウントを、"Integration Runtime サービス" としてプロキシにアクセスできるユーザー アカウントに変更してください。

### <a name="error-message-self-hosted-integration-runtime-node-logical-shir-is-in-inactive-running-limited-state"></a>エラー メッセージ:セルフホステッド統合ランタイム ノード/論理 SHIR が非アクティブ/ "実行中 (制限あり)" の状態です

- **原因**:次のスクリーンショットに示すように、セルフホステッド IR ノードが非アクティブな状態になることがあります。

    ![非アクティブなセルフホステッド IR ノード](media/self-hosted-integration-runtime-troubleshoot-guide/inactive-self-hosted-ir-node.png)

    これは、ノードが相互に通信できない場合に発生します。 

- **解決方法**: 

    ノードでホストされている VM にログインし、イベント ビュー ([アプリケーションとサービス ログ] -> [Integration Runtime]) を開いて、フィルタリングですべてのエラー ログを表示します。 

     1. エラー ログに次の内容が含まれている場合: 
    
        **エラー ログ**:System.ServiceModel.EndpointNotFoundException:net.tcp://xxxxxxx.bwld.com:8060/ExternalService.svc/WorkerManager に接続できませんでした。 接続試行の継続時間は 00:00:00.9940994 でした。 TCP エラー コード 10061:対象のコンピュータによって拒否されたため、接続できませんでした 10.2.4.10:8060。  ---> System.Net.Sockets.SocketException:対象のコンピュータによって拒否されたため、接続できませんでした 10.2.4.10:8060
    
           System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
           
           System.Net.Sockets.Socket.Connect(EndPoint remoteEP)
           
           System.ServiceModel.Channels.SocketConnectionInitiator.Connect(Uri uri, TimeSpan timeout)
    
        **解決方法:** コマンド ラインを起動します: telnet 10.2.4.10 8060
        
        下記のエラーが返された場合は、IT チームに問題解決への協力を依頼してください。 telnet が正常に実行された後も、引き続き IR ノードの状態に問題がある場合は、Microsoft サポートに問い合わせてください。
        
        ![コマンド ライン エラー](media/self-hosted-integration-runtime-troubleshoot-guide/command-line-error.png)
        
     2. エラー ログに次の内容が含まれている場合:
     
        **エラー ログ:** ワーカー マネージャーに接続できません: net.tcp://xxxxxx:8060/ExternalService.svc/ ホスト azranlcir01r1 に対する DNS エントリが存在しません。 このようなホストには既知の例外の詳細がありません。System.ServiceModel.EndpointNotFoundException:ホスト xxxxx に対する DNS エントリが存在しません。 ---> System.Net.Sockets.SocketException:そのようなホストは不明です: System.Net.Dns.GetAddrInfo(String name)、System.Net.Dns.InternalGetHostByName(String hostName, Boolean includeIPv6)、System.Net.Dns.GetHostEntry(String hostNameOrAddress)、System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) --- 内部例外スタック トレースの終わり --- サーバー スタック トレース: System.ServiceModel.Channels.DnsCache.Resolve(Uri uri) 
    
        **解決策:** 次の 2 つの操作のいずれかが、問題の解決に役立つ可能性があります。
         1. すべてのノードを同じドメインに配置する。
         2. ホストされているすべての VM のホスト ファイルで、ホスト マッピングに IP を追加する。


## <a name="next-steps"></a>次の手順

トラブルシューティングのその他のヘルプについては、次のリソースを参照してください。

*  [Data Factory ブログ](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory の機能のリクエスト](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure のビデオ](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory の Stack Overflow フォーラム](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory に関する Twitter 情報](https://twitter.com/hashtag/DataFactory)
*  [ADF マッピング データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)
