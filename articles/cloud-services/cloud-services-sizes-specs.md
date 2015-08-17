<properties 
 pageTitle="クラウド サービスのサイズ" 
 description="Azure のクラウド サービスの Web ロールと worker ロールのさまざまなサイズを一覧表示します。" 
 services="cloud-services" 
 documentationCenter="" 
 authors="Thraka" 
 manager="timlt" 
 editor=""/>
<tags 
 ms.service="cloud-services" 
 ms.devlang="na" 
 ms.topic="article" 
 ms.tgt_pltfrm="na" 
 ms.workload="tbd"
 ms.date="06/04/2015" 
 ms.author="adegeo"/>
 
# クラウド サービスのサイズ

このトピックでは、クラウド サービスのロール インスタンス (Web ロールと worker ロール) で使用できるサイズとオプションについて説明します。また、これらのリソースの使用を計画するときに注意するデプロイメントに関する考慮事項も示します。

Azure Virtual Machines と Cloud Services は、Azure が提供する数種類のコンピューティング リソースの中の 2 つです。詳細については、[Azure が提供するコンピューティング ホスティング オプション](fundamentals-application-models.md)に関するページを参照してください。

> [AZURE.NOTE]関連する Azure の制限については、[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)に関するページを参照してください。

## Web ロールと worker ロールのインスタンスのサイズ

サイズを決定する際に役立つ考慮事項は次のとおりです。

* インスタンスは、D シリーズ VM を使用するように構成できるようになりました。これらは、より高いコンピューティング能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。詳細については、Azure ブログの投稿「[新しい D シリーズ仮想マシンのサイズ](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)」をご覧ください。  

* Web ロールと worker ロールは、システム要件により Azure 仮想マシンよりも多くの一時ディスク領域が必要です。システム ファイルは、Windows ページファイルに 4 GB、Windows ダンプ ファイルに 2 GB の領域を予約します。

* OS ディスクには Windows ゲスト OS が含まれており、Program Files フォルダー (別のディスクを指定していない場合はスタートアップ タスクで実行されたインストールを含む)、レジストリの変更、System32 フォルダー、.NET Framework も含まれています。

* ローカル リソース ディスクには、Azure ログと構成ファイル、Azure 診断 (IIS ログを含む)、ユーザーが定義したローカル ストレージ リソースが含まれています。

* アプリ (アプリケーション) ディスクには、.cspkg ファイルが抽出される場所で、Web サイト、バイナリ、ロール ホスト プロセス、スタートアップ タスク、web.config などが含まれています。

* A8/A10 と A9/A11 の仮想マシンのサイズは、同じ容量です。A8 と A9 の仮想マシンのインスタンスには、仮想マシン間の高速通信用のリモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された追加のネットワーク アダプターが含まれています。A8 と A9 のインスタンスは、実行中にノード間で持続的で待ち時間の少ない通信を必要とする高パフォーマンス コンピューティング アプリケーション (Message Passing Interface (MPI) を使用するアプリケーションなど) 向けに設計されています。A10 と A11 の仮想マシン インスタンスには、追加のネットワーク アダプターが含まれていません。A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない高パフォーマンス コンピューティング アプリケーション (パラメトリックまたは Embarrassingly Parallel アプリケーションとも呼ばれる) 向けに設計されています。

|サイズ|CPU<br>コア|メモリ|ディスク サイズ|
|---|---|---|---|
|ExtraSmall|1|768 MB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 19 GB<br/>アプリ = 約 1.5 GB|
|Small|1|1\.75 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 224 GB<br/>アプリ = 約 1.5 GB|
|Medium|2|3\.5 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 489 GB<br/>アプリ = 約 1.5 GB|
|Large|4|7 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 999 GB<br/>アプリ = 約 1.5 GB|
|ExtraLarge|8|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 2,039 GB<br/>アプリ = 約 1.5 GB|
|A5|2|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 489 GB<br/>アプリ = 約 1.5 GB|
|A6|4|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 999 GB<br/>アプリ = 約 1.5 GB|
|A7|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 2,039 GB<br/>アプリ = 約 1.5 GB
|A8|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1.77 TB<br/>アプリ = 約 1.5 GB<blockquote>注: このサイズの使用に関する情報と考慮事項については、<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンス</a>に関するページを参照してください。</blockquote>|
|A9|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1.77 TB<br/>アプリ = 約 1.5 GB<blockquote>注: このサイズの使用に関する情報と考慮事項については、<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンス</a>に関するページを参照してください。</blockquote>|
|A10|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1.77 TB<br/>アプリ = 約 1.5 GB<blockquote>注: このサイズの使用に関する情報と考慮事項については、<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンス</a>に関するページを参照してください。</blockquote>|
|A11|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 1.77 TB<br/>アプリ = 約 1.5 GB<blockquote>注: このサイズの使用に関する情報と考慮事項については、<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8、A9、A10、A11 コンピューティング集中型インスタンス</a>に関するページを参照してください。</blockquote>|
|Standard\_D1|1|3\.5 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 50 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D2|2|7 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 100 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D3|4|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 200 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D4|8|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 400 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D11|2|14 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 100 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D12|4|28 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 200 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D13|8|56 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 400 GB<br/>アプリ = 約 1.5 GB|
|Standard\_D14|16|112 GB|OS = ゲスト OS のサイズ<br/>ローカル リソース = 800 GB<br/>アプリ = 約 1.5 GB|

## クラウド サービスのサイズの構成

ロール インスタンスの仮想マシンのサイズを、サービス定義ファイルに記述されているサービス モデルの一部として指定できます。ロールのサイズによって、CPU コアの数、メモリ容量、および実行中のインスタンスに割り当てられるローカル ファイル システムのサイズが決まります。ロールのサイズは、アプリケーションのリソース要件に基づいて選択します。

Web ロール インスタンスのロール サイズを Small に設定する例を次に示します。


    <WebRole name="WebRole1" vmsize="Small">
    …
    </WebRole>
## 次のステップ

[Azure のクラウド サービスのセットアップ](https://msdn.microsoft.com/library/hh124108)

<!---HONumber=August15_HO6-->