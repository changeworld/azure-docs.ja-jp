<properties 
   pageTitle="オペレーション インサイトでの問題のトラブルシューティング"
   description="オペレーション インサイトでの問題のトラブルシューティングについて説明します"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

#オペレーション インサイトでの問題のトラブルシューティング
以降のセクションの情報は、問題のトラブルシューティングにお役立てください。該当する問題がこの記事に見つからない場合は、[オペレーション インサイト チームのブログ](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx)で探してみてください。

## オペレーション インサイトの接続に関する問題の診断

Microsoft Azure オペレーション インサイトは、クラウドとの間でデータをやり取りすることが前提となっているため、接続の問題は致命的です。以下の情報を参考に接続の問題を把握し、解決してください。



<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>エラー メッセージ</b></td>
		<td><b>考えられる原因</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>インターネット接続が確認されましたが、オペレーション インサイト サービスへの接続を確立できませんでした。後でもう一度やり直してください。</td>
		<td>オペレーション インサイト サービスがメンテナンス中です。オペレーション インサイトのメンテナンスが終了するまでお待ちください。<p>ご使用のネットワークによって、オペレーション インサイトがブロックされています。ネットワーク管理者に問い合わせてオペレーション インサイトへのアクセスをリクエストするか、別のサーバーをゲートウェイとして使用してください。</td>
    </tr>
    <tr align="left" valign="top">
		<td>インターネット接続を確立できませんでした。プロキシの設定を確認してください。</td>
		<td>このサーバーはインターネットに接続されていません。インターネット接続の状態を確認し、サーバーをインターネットに接続してください。<p>プロキシ設定が正しくありません。プロキシの設定と変更の方法については、<A HREF="operational-insights-proxy-filewall.md">プロキシとファイアウォール設定の構成</A>に関するページを参照してください。<p>プロキシ サーバーは認証が必要です。プロキシ サーバーを使用するように Operations Manager を構成する方法については、<A HREF="operational-insights-proxy-filewall.md">プロキシとファイアウォール設定の構成</A>に関するページを参照してください。</td>
    </tr>
    </tbody>
    </table>

## SQL Server 検出のトラブルシューティング

Microsoft SQL Server 2008 R2 を実行している場合に、Operations Manager エージェントがデプロイされているにもかかわらず、このサーバーのアラートが表示されないときは、検出の問題が発生している可能性があります。

それが問題の根本原因であるかどうかを確認するには、次の 2 点をチェックしてください。

- Operations Manager のイベント ログで、イベント ID 4001 が表示される。このイベントは、無効なクラスが存在することを示しています。

- SQL Server 構成マネージャーで、SQL Server のサービスを表示したとき、"リモート プロシージャ コールに失敗しました。[0x0800706be]" というエラー メッセージが表示される。

両方の問題に該当する場合は、SQL Server 2008 R2 Service Pack 2 をインストールする必要があります。このサービス パックをダウンロードするには、[SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) (Microsoft ダウンロード センター) にアクセスしてください。

サービス パックのインストール後 24 時間以内に、対象サーバーに関して、オペレーション インサイトのデータが表示されるようになります。



<!--HONumber=52-->