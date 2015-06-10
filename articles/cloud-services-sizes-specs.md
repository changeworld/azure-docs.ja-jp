<properties 
 pageTitle="クラウド サービスのサイズ" 
 description="Web ロールと worker ロールのさまざまなサイズの一覧" 
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
 ms.date="05/20/2015" 
 ms.author="adegeo"/>
 
# クラウド サービスのサイズ

## 概要 

このトピックでは、クラウド サービスのロール インスタンス (Web ロールと worker ロール) で使用できるサイズとオプションについて説明します。また、これらのリソースの使用を計画するときに注意するデプロイメントに関する考慮事項も示します。

Azure 仮想マシンとクラウド サービスは、Azure が提供するコンピューティング リソースのいくつかの種類の中の 2 つです。詳細については、「[Azure 実行モデル](http://go.microsoft.com/fwlink/p/?LinkID=311926)」をご覧ください。

>[AZURE.NOTE]関連する Azure の制限については、「[Azure サブスクリプションとサービスの制限、クォータ、および制約](azure-subscription-service-limits.md)」をご覧ください。

###Web ロールと worker ロールのインスタンスのサイズ

次の考慮事項は、サイズを決定する際に役立つ場合があります。

*   インスタンスは、D シリーズ VM を使用するように構成できるようになりました。これらは、より高い計算能力と一時ディスクのパフォーマンスを必要とするアプリケーションを実行するように設計されています。D シリーズ VM は、より高速なプロセッサ、より高いメモリ対コア比、一時ディスク用ソリッド ステート ドライブ (SSD) を提供します。詳細については、Azure ブログの投稿「[新しい D シリーズ仮想マシンのサイズ](http://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/)」をご覧ください。  

*   Web ロールと worker ロールは、システム要件により Azure 仮想マシンよりも多くの一時ディスク領域が必要です。システム ファイルは、Windows ページファイルに 4 GB、Windows ダンプ ファイルに 2 GB の領域を予約します。

*   OS ディスクには Windows ゲスト OS が含まれており、Program Files フォルダー (別のディスクを指定していない場合はスタートアップ タスクで実行されたインストールを含む)、レジストリの変更、System32 フォルダー、.NET Framework も含まれています。

*   ローカル リソース ディスクには、Azure ログと構成ファイル、Azure 診断 (IIS ログを含む)、ユーザーが定義したローカル ストレージ リソースが含まれています。

*   アプリ (アプリケーション) ディスクには、.cspkg ファイルが抽出される場所で、Web サイト、バイナリ、ロール ホスト プロセス、スタートアップ タスク、web.config などが含まれています。

*   A8/A10 と A9/A11 の仮想マシンのサイズは、同じ容量です。A8 と A9 の仮想マシンのインスタンスには、仮想マシン間の高速通信用のリモート ダイレクト メモリ アクセス (RDMA) ネットワークに接続された追加のネットワーク アダプターが含まれています。A8 と A9 のインスタンスは、実行中にノード間で持続的で待ち時間の少ない通信を必要とする高パフォーマンス コンピューティング アプリケーション (Message Passing Interface (MPI) を使用するアプリケーションなど) 向けに設計されています。A10 と A11 の仮想マシン インスタンスには、追加のネットワーク アダプターが含まれていません。A10 と A11 のインスタンスは、ノード間で一定した待ち時間の少ない通信を必要としない高パフォーマンス コンピューティング アプリケーション (パラメトリックまたは Embarrassingly Parallel アプリケーションとも呼ばれる) 向けに設計されています。

|サイズ|CPU<br>コア|メモリ|ディスク サイズ|
|---|---|---|---|
|ExtraSmall|1|768 MB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 19 GB</p><p>アプリ = 約 1.5 GB</p>|
|Small|1|1.75 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 224 GB</p><p>アプリ = 約 1.5 GB</p>|
|Medium|2|3.5 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 489 GB</p><p>アプリ = 約 1.5 GB</p>|
|Large|4|7 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 999 GB</p><p>アプリ = 約 1.5 GB</p>|
|ExtraLarge|8|14 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 2,039 GB</p><p>アプリ = 約 1.5 GB</p>|
|A5|2|14 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 489 GB</p><p>アプリ = 約 1.5 GB</p>|
|A6|4|28 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 999 GB</p><p>アプリ = 約 1.5 GB</p>|
|A7|8|56 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 2,039 GB</p><p>アプリ = 約 1.5 GB</p>
|A8|8|56 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 1.77 TB</p><p>アプリ = 約 1.5 GB</p><blockquote><p>[AZURE.NOTE]このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8/A9 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|
|A9|16|<p>112 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 1.77 TB</p><p>アプリ = 約 1.5 GB</p><blockquote><p>[AZURE.NOTE]このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8/A9 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|
|A10|8|<p>56 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 1.77 TB</p><p>アプリ = 約 1.5 GB</p><blockquote><p>[AZURE.NOTE]このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8/A9 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|
|A11|16|<p>112 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 1.77 TB</p><p>アプリ = 約 1.5 GB</p><blockquote><p>[AZURE.NOTE]このサイズの使用に関する情報と考慮事項については、「<a href="http://go.microsoft.com/fwlink/p/?linkid=328042">A8/A9 コンピューティング集中型インスタンスについて</a>」をご覧ください。</p></blockquote>|
|Standard_D1|1|3.5 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 50 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D2|2|7 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 100 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D3|4|14 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 200 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D4|8|28 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 400 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D11|2|14 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 100 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D12|4|28 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 200 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D13|8|56 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 400 GB</p><p>アプリ = 約 1.5 GB</p>|
|Standard_D14|16|112 GB|<p>OS = ゲスト OS のサイズ</p><p>ローカル リソース = 800 GB</p><p>アプリ = 約 1.5 GB</p>|

### 関連項目

#### 概念

[Azure のクラウド サービスのセットアップ](https://msdn.microsoft.com/library/hh124108)

[クラウド サービスのサイズを構成する](https://msdn.microsoft.com/library/ee814754)

#### その他のリソース

[Azure サブスクリプションとサービスの制限、クォータ、制約](azure-subscription-service-limits.md)

 [A8、A9、A10、A11 コンピューティング集中型インスタンスに関するページ](http://go.microsoft.com/fwlink/p/?linkid=328042)

 [仮想マシンのサイズ](virtual-machines-size-specs.md)

<!---HONumber=58-->