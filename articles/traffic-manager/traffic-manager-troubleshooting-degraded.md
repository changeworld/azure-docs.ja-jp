<properties
   pageTitle="Azure Traffic Manager での機能低下状態のトラブルシューティング"
   description="Traffic Manager プロファイルが機能低下状態と示されるときのトラブルシューティング方法について説明します。"
   services="traffic-manager"
   documentationCenter=""
   authors="sdwheeler"
   manager="carmonm"
   editor="joaoma" />

<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# Azure Traffic Manager での機能低下状態のトラブルシューティング

ここでは、機能低下状態が示されている Azure Traffic Manager プロファイルをトラブルシューティングする方法、およびトラフィック マネージャーのプローブについて理解するための重要な点について説明します。

.cloudapp.net ホステッド サービスの一部を参照するようにTraffic Manager プロファイルを構成し、数秒後にステータスが機能低下を示したものとします。

![degradedstate](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

そのプロファイルの [エンドポイント] タブに移動すると、1 つまたは複数のエンドポイントがオフライン状態になっています。

![offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## Traffic Manager のプローブに関する重要な注意事項

- Traffic Manager は、プローブがプローブ パスからステータス 200 を取得した場合にのみ、エンドポイントをオンラインとみなします。
- 30x のリダイレクト (または他の 200 以外の応答すべて) は、リダイレクト先の URL が 200 を返した場合であってもエラーです。

- HTTPs プローブの場合、証明書エラーは無視されます。
 
- 200 が返される限り、プローブ パスの実際の内容は関係ありません。実際の Web サイトのコンテンツが 200 を返さない場合 (つまり、ASP ページが ACS ログイン ページまたは他の CNAME URL にリダイレクトする場合) の一般的な技法は、パスを “/favicon.ico” などに設定することです。
 
- ベスト プラクティスとしては、サイトが稼動しているかどうかを判断するのに十分なロジックを持っているパスに、プローブ パスを設定します。パスを “/favicon.ico” に設定する上の例では、w3wp.exe が応答するかどうかだけをテストしており、Web サイトが正常かどうかはテストしていません。さらによい方法としては、“/Probe.aspx” などにパスを設定し、サイトが正常かどうかを判断するのに十分なロジックを Probe.aspx に含めます (つまり、パフォーマンス カウンターをチェックして CPU が 100% になっていないとか多数のエラー要求を受け取らないことを確認する、またはデータベースやセッション ステートなどのリソースにアクセスしてアプリケーションのロジックが動作していることを確認する、など)。
 
- プロファイル内のすべてのエンドポイントが機能低下している場合、Traffic Manager はすべてのエンドポイントを正常として扱い、トラフィックをすべてのエンドポイントにルーティングします。これは、プローブ メカニズムの潜在的な問題のために、失敗したプローブによってサービスが完全に停止するのを防ぐためです。

  

## トラブルシューティング

Traffic Manager のプローブのエラーをトラブルシューティングするためのツールの 1 つは wget です。バイナリと依存するもののパッケージを、[wget](http://gnuwin32.sourceforge.net/packages/wget.htm) から入手できます。wget の代わりに Fiddler や curl などの他のプログラムも使用できることに注意してください。基本的に、必要なものは未加工の HTTP 応答がわかるツールです。

wget をインストールした後、コマンド プロンプトに移動し、URL に Traffic Manager で構成されているプローブ ポートとパスを付加したもので wget を実行します。この例では、http://watestsdp2008r2.cloudapp.net:80/Probe のようになります。

![troubleshooting](./media/traffic-manager-troubleshooting-degraded/traffic-manager-troubleshooting.png)

wget を使用します。

![wget](./media/traffic-manager-troubleshooting-degraded/traffic-manager-wget.png)

 

wget で URL が http://watestsdp2008r2.cloudapp.net/Default.aspx に対して 301 リダイレクトを返したことが示されていることに注意してください。前の「Traffic Manager のプローブに関する重要な注意事項」セクションからわかるように、Traffic Manager プローブでは 30x リダイレクトはエラーとみなされ、プローブはオフラインを報告します。ここまでくれば、Web サイトの構成を調べて、/Probe パスから 200 が返されるようにするのは簡単です (または、Traffic Manager のプローブを再構成して、200 を返すパスを参照します)。

 

プローブが HTTPs プロトコルを使用している場合は、wget に “--no-check-certificate” パラメーターを追加して、cloudapp.net URL での証明書の不一致を無視することができます。


## 次のステップ


[Traffic Manager のトラフィック ルーティング方法について](traffic-manager-routing-methods.md)

[Traffic Manager について](traffic-manager-overview.md)

[Cloud Services](http://go.microsoft.com/fwlink/?LinkId=314074)

[Websites](http://go.microsoft.com/fwlink/p/?LinkId=393327)

[Traffic Manager の操作 (REST API リファレンス)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Azure Traffic Manager コマンドレット](http://go.microsoft.com/fwlink/p/?LinkId=400769)
 

<!---HONumber=AcomDC_0824_2016-->