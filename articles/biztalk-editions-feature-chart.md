<properties linkid="manage-services-biztalk-services-editions-chart" urlDisplayName="エディションのチャート" pageTitle="BizTalk サービスのエディションに含まれる機能の詳細情報 | Azure" metaKeywords="BizTalk サービス, 開始, Azure, エディション" description="BizTalk サービスのエディション (開発者、基本、標準、およびプレミアム) の機能を比較します。" metaCanonical="" services="biztalk-services" documentationCenter="" title="基本" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />




# BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート

Azure BizTalk サービスには、開発者、基本、標準、およびプレミアムの 4 つのレベルがあります。

**開発者**: EAI および EDI メッセージ処理、BizTalk Adapter Pack を使用したハイブリッド接続、HTTP/S、REST、FTP、WCF、および SFTP プロトコルを使用してメッセージの読み取りと書き込みを行うクラウド内のサービスを接続する一般的な EAI シナリオなどの機能が含まれます。すべての機能は、開発とデプロイを容易にする Visual Studio ツールを使用した開発者中心の環境で使用できます。開発とテストの目的に限定されており、SLA はありません。

**基本**: 使い易い取引先管理ポータルを備えた EAI および EDI メッセージ処理、一般的な EDI スキーマのサポート、X12 や AS2 でのリッチな EDI 処理などの機能が含まれます。HTTP/S、REST、FTP、WCF、および SFTP プロトコルを使用してメッセージの読み取りと書き込みを行うクラウド内のサービスを接続する一般的な EAI シナリオを作成できます。2. 構成主導の開発ツールによってリッチなメッセージ処理および仲介がサポートされています。すぐに使える SAP、Oracle eBusiness、Oracle DB、Siebel および SQL Server のアダプターによる内部設置型の LOB システムへのハイブリッド接続が活用されます。

**標準**: 基本機能のすべてが含まれるのに加え、増大するニーズに対応して展開を拡張できます。

**プレミアム**: 標準機能のすべてが含まれるのに加え、スケール、EAI ブリッジ、EDI 契約、ハイブリッド接続が強化されており、アーカイブも追加されています。


次の表に、それらのエディションの違いを示します。

<table border="1">
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>開発者</th>
        <th>基本</th>
        <th>標準</th>
        <th>プレミアム</th>
</tr>
<tr>
<td><strong>開始価格</strong></td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
</tr>
<tr>
<td><strong>既定の最小構成</strong></td>
<td>1 開発者ユニット</td>
<td>1 基本ユニット</td>
<td>1 標準 ユニット</td>
<td>1 プレミアム ユニット</td>
</tr>
<tr>
<td><strong>スケール</strong></td>
<td>該当なし</td>
<td>該当なし</td>
<td>あり (1 標準 ユニットずつ増加)</td>
<td>あり (1 プレミアム ユニットずつ増加)</td>
</tr>
<tr>
<td><strong>許容される最大スケール アウト</strong></td>
<td>該当なし</td>
<td>該当なし</td>
<td>最大 4 ユニット</td>
<td>最大 8 ユニット</td>
</tr>
<tr>
<td><strong>EAI ブリッジ/ユニット</strong></td>
<td>30</td>
<td>50</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI、AS2
<br/><br/>
EDI、AS2  契約、メッセージの種類、BizTalk サービス ポータルが含まれます。</strong></td>
<td>あり。10 契約/ユニット。</td>
<td>あり。25 契約/ユニット。</td>
<td>あり。250 契約/ユニット。</td>
<td>あり。1000 契約/ユニット。</td>
</tr>
<tr>
<td><strong>内部設置型 LOB システムへの BizTalk Adapter サービス接続</strong></td>
<td>1 接続</td>
<td>2 接続</td>
<td>5 接続</td>
<td>25 接続</td>
</tr>
<tr>
<td align="left"><strong>サポートされるプロトコル/システム:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>サービス バス (SB)</li>
<li>Azure BLOB</li>
<li>REST API</li>
</ul>
</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>高可用性</strong>
<br/><br/>
サービス レベル アグリーメント (SLA) については、「<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">BizTalk サービスの料金詳細</a>」を参照してください。
</td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>バックアップと復元</strong></td>
<td>なし</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>追跡</strong></td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>アーカイブ</strong><br/><br/>
NRR (Non-repudiation of Receipt) と追跡されたメッセージのダウンロードが含まれます。</td>
<td>あり</td>
<td>なし</td>
<td>なし</td>
<td>あり</td>
</tr>
<tr>
<td><strong>カスタム コードの使用</strong></td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
<tr>
<td><strong>変換の使用 (カスタム XSLT を含む)</strong></td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
<td>あり</td>
</tr>
</table>

**注**
<br/>ハードウェアの障害に対する復元性の場合、高可用性は 1 つの BizTalk ユニット内に複数の VM を備えていることを意味します。

## FAQ
#### BizTalk ユニットとは何ですか。
「ユニット」は、Azure BizTalk サービス展開のアトミックなレベルです。各エディションには、計算能力とメモリが異なるユニットが用意されています。たとえば、基本ユニットは開発者ユニットより計算能力が高く、標準ユニットは基本ユニットより計算能力が高くなります。BizTalk サービスをスケールする場合は、ユニット単位でスケールします。

#### BizTalk サービスと Azure BizTalk VM の違いは何ですか
BizTalk サービスは、クラウドでの統合ソリューションの構築のために真のサービスとしてのプラットフォーム (PaaS) を提供します。PaaS モデルでは、完全にアプリケーション ロジックに集中し、次のようなインフラストラクチャ管理はすべて Microsoft に任せることができます。

- 仮想マシンの管理やパッチ適用の必要がありません。
- 可用性は Microsoft により確保されます。
- Azure 管理ポータルを通じて容量の増減を要求するだけで、スケールをオンデマンドで管理できます。

Azure の仮想マシン上の BizTalk Server は、サービスとしてのインフラストラクチャ (IaaS) アーキテクチャを提供します。仮想マシンを作成し、内部設置型環境のように正確に構成することで、コードを変更しなくても既存のアプリケーションをクラウドで実行しやすくなります。IaaS では、仮想マシンの構成、仮想マシンの管理 (ソフトウェアや OS 修正プログラムなどのインストール)、および高可用性のためのアプリケーションの設計は、依然としてユーザーの責任です。

インフラストラクチャの管理作業を最小化する新しい統合ソリューションの構築を検討している場合は、BizTalk サービスをご利用ください。既存の BizTalk ソリューションをすばやく移行することを検討している場合、または BizTalk Server アプリケーションを開発しテストするためのオンデマンドの環境を探している場合は、Azure の仮想マシンで BizTalk Server をご利用ください。

#### BizTalk サービスで契約を作成すると、ブリッジの数が 1 つではなく 2 つ増えるのはなぜですか。

各契約は、送信側の通信ブリッジと受信側の通信ブリッジの 2 つの異なるブリッジで構成されます。

####  ブリッジ数または契約数のクォータの上限に達した場合はどうなりますか。

新しいブリッジを展開したり、新しい契約を作成したりすることができなくなります。追加の展開を行うためには、BizTalk サービスのユニット数をスケール アップするか、上位のエディションにアップグレードする必要があります。

#### BizTalk サービスの 1 つのレベルから別のレベルに移行するにはどのようにしますか。

1 つのレベルから別のレベルへの移行には、バックアップと復元のフローを使用します。一部の移行パスのみサポートされます。サポートされる移行パスの詳細については、[BizTalk サービスのバックアップと復元](http://go.microsoft.com/fwlink/p/?LinkID=329873)に関するページを参照してください。

#### BizTalk Adapter サービスはサービスに含まれていますか。ソフトウェアはどのようにして取得しますか。

はい、BizTalk Adapter Pack 付きの BizTalk Adapter サービスは、Azure BizTalk サービス SDK の[ダウンロード](http://www.microsoft.com/download/details.aspx?id=39087)に含まれています。

## 次のステップ

Azure BizTalk サービスを Azure 管理ポータルでプロビジョニングするには、[Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)に関するページを参照してください。アプリケーションの作成を開始するには、[Azure BizTalk サービス](http://go.microsoft.com/fwlink/p/?LinkID=235197)に関するページを参照してください。

## 関連項目
- [BizTalk サービス: Azure 管理ポータルを使用した BizTalk サービスのプロビジョニングに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: プロビジョニングの状態のチャートに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk サービス: バックアップと復元に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk サービス: 調整に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk サービス: 発行者名および発行者キーに関するページ](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Azure BizTalk サービス SDK の使用開始に関するページ](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

