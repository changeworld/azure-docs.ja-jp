<properties linkid="manage-services-biztalk-services-editions-chart" urlDisplayName="Editions chart" pageTitle="Learn about features in BizTalk Services editions | Azure" metaKeywords="BizTalk Services, get started, Azure, editions" description="Compare the capabilities of the BizTalk Services editions: Free, Developer, Basic, Standard, and Premium." metaCanonical="" services="biztalk-services" documentationCenter="" title=" Basic" authors="mandia" solutions="" manager="dwrede" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/10/2014" ms.author="mandia" />

# BizTalk サービス: エディションのチャート

Azure BizTalk サービスのエディションは、"無料"、"開発者"、"基本"、"標準"、および "プレミアム" があります。

**無料 (プレビュー)**: ハイブリッド接続の作成と管理が可能です。ハイブリッド接続とは、Azure Web サイトと内部設置型システム (たとえば SQL Server) とを簡単に接続するための手段です。

**開発者**: ハイブリッド接続、EAI および EDI メッセージ処理 (使いやすい取引先管理ポータルあり)、一般的な EDI スキーマのサポート、X12 および AS2 でのリッチな EDI 処理などの機能が含まれます。HTTP/S、REST、FTP、WCF、および SFTP プロトコルを使用してメッセージの読み取りと書き込みを行うクラウド内のサービスを接続する一般的な EAI シナリオを作成できます。内部設置型の LOB システムへの接続が可能であり、SAP、Oracle eBusiness、Oracle DB、Siebel、および SQL Server のためのすぐに使えるアダプターが用意されています。すべての機能は、開発とデプロイを容易にする Visual Studio ツールを使用した開発者中心の環境で使用できます。目的は開発とテストに限定されており、サービス レベル アグリーメント (SLA) はありません。

**基本**: 開発者エディションのほとんどの機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。高可用性と拡張オプションに加えてサービス レベル アグリーメント (SLA) も提供されます。

**標準**: 基本エディションのすべての機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。高可用性と拡張オプションに加えてサービス レベル アグリーメント (SLA) も提供されます。

**プレミアム**: 標準エディションのすべての機能が含まれており、ハイブリッド接続、EAI ブリッジ、EDI 契約、BizTalk Adapter Pack 接続の数が増えています。さらに、アーカイブと高可用性の機能も含まれており、サービス レベル アグリーメント (SLA) に基づくスケールも可能です。

次の表に、それらのエディションの違いを示します。

<table>
<colgroup>
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
<col width="16%" />
</colgroup>
<thead>
<tr class="header">
<th align="left"></th>
<th align="left">無料 (プレビュー)</th>
<th align="left">開発者</th>
<th align="left">基本</th>
<th align="left">標準</th>
<th align="left">プレミアム</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>開始価格</strong></td>
<td align="left"><a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td align="left"><a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td align="left"><a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td align="left"><a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
<td align="left"><a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金に関するページ</a>を参照してください。</td>
</tr>
<tr class="even">
<td align="left"><strong>既定の最小構成</strong></td>
<td align="left">1 無料ユニット</td>
<td align="left">1 開発者ユニット</td>
<td align="left">1 基本ユニット</td>
<td align="left">1 標準ユニット</td>
<td align="left">1 プレミアム ユニット</td>
</tr>
<tr class="odd">
<td align="left"><strong>スケール</strong></td>
<td align="left">該当なし</td>
<td align="left">該当なし</td>
<td align="left">あり (1 基本ユニットずつ増加)</td>
<td align="left">あり (1 標準ユニットずつ増加)</td>
<td align="left">あり (1 プレミアム ユニットずつ増加)</td>
</tr>
<tr class="even">
<td align="left"><strong>許容される最大スケール アウト</strong></td>
<td align="left">該当なし</td>
<td align="left">該当なし</td>
<td align="left">最大 8 ユニット</td>
<td align="left">最大 8 ユニット</td>
<td align="left">最大 8 ユニット</td>
</tr>
<tr class="odd">
<td align="left"><strong>EAI ブリッジ/ユニット</strong></td>
<td align="left">なし</td>
<td align="left">25</td>
<td align="left">25</td>
<td align="left">125</td>
<td align="left">500</td>
</tr>
<tr class="even">
<td align="left"><strong>EDI、AS2</strong><br /><br /> TPM 契約が含まれます。</td>
<td align="left">なし</td>
<td align="left">あり。10 契約/ユニット。</td>
<td align="left">あり。50 契約/ユニット。</td>
<td align="left">あり。250 契約/ユニット。</td>
<td align="left">あり。1000 契約/ユニット。</td>
</tr>
<tr class="odd">
<td align="left"><strong>ユニットあたりのハイブリッド接続数</strong></td>
<td align="left">5</td>
<td align="left">5</td>
<td align="left">10</td>
<td align="left">50</td>
<td align="left">100</td>
</tr>
<tr class="even">
<td align="left"><strong>ユニットあたりのハイブリッド接続データ転送量 (GB)</strong></td>
<td align="left">5</td>
<td align="left">5</td>
<td align="left">50</td>
<td align="left">250</td>
<td align="left">500</td>
</tr>
<tr class="odd">
<td align="left"><strong>内部設置型 LOB システムへの BizTalk Adapter サービス接続</strong></td>
<td align="left">なし</td>
<td align="left">1 接続</td>
<td align="left">2 接続</td>
<td align="left">5 接続</td>
<td align="left">25 接続</td>
</tr>
<tr class="even">
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
</ul></td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
<tr class="odd">
<td align="left"><strong>高可用性</strong><br /><br /> サービス レベル アグリーメント (SLA) については、「<a href="http://go.microsoft.com/fwlink/p/?LinkID=304011">BizTalk サービスの料金詳細</a>」を参照してください。</td>
<td align="left">なし</td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
<tr class="even">
<td align="left"><strong>バックアップと復元</strong></td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
<tr class="odd">
<td align="left"><strong>追跡</strong></td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
<tr class="even">
<td align="left"><strong>アーカイブ</strong><br /><br /> NRR (Non-repudiation of Receipt) と追跡されたメッセージのダウンロードが含まれます。</td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">なし</td>
<td align="left">なし</td>
<td align="left">あり</td>
</tr>
<tr class="odd">
<td align="left"><strong>カスタム コードの使用</strong></td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
<tr class="even">
<td align="left"><strong>変換の使用 (カスタム XSLT を含む)</strong></td>
<td align="left">なし</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
<td align="left">あり</td>
</tr>
</tbody>
</table>

**注**
ハードウェアの障害が発生しても復元可能とするための "高可用性" 機能を使用するときは、複数の VM を 1 つの BizTalk ユニット内に持つことになります。

## FAQ

#### BizTalk ユニットとは何ですか。

「ユニット」は、Azure BizTalk サービス展開のアトミックなレベルです。各エディションには、計算能力とメモリが異なるユニットが用意されています。たとえば、基本ユニットは開発者ユニットより計算能力が高く、標準ユニットは基本ユニットより計算能力が高くなります。BizTalk サービスをスケールする場合は、ユニット単位でスケールします。

#### BizTalk サービスと Azure BizTalk VM の違いは何ですか。

BizTalk サービスは、クラウドでの統合ソリューションの構築のために真のサービスとしてのプラットフォーム (PaaS) を提供します。PaaS モデルでは、完全にアプリケーション ロジックに集中し、次のようなインフラストラクチャ管理はすべて Microsoft に任せることができます。

-   仮想マシンの管理やパッチ適用の必要がありません。
-   可用性は Microsoft により確保されます。
-   Azure 管理ポータルを通じて容量の増減を要求するだけで、スケールをオンデマンドで管理できます。

Azure の仮想マシン上の BizTalk Server は、サービスとしてのインフラストラクチャ (IaaS) アーキテクチャを提供します。仮想マシンの作成と構成は内部設置型環境とまったく同じようにできるので、コードを変更しなくても既存のアプリケーションをクラウドで実行しやすくなります。IaaS では、仮想マシンの構成、仮想マシンの管理 (ソフトウェアや OS 修正プログラムなどのインストール)、および高可用性のためのアプリケーションの設計は、依然としてユーザーの責任です。

インフラストラクチャの管理作業を最小化する新しい統合ソリューションの構築を検討している場合は、BizTalk サービスをご利用ください。既存の BizTalk ソリューションをすばやく移行することを検討している場合、または BizTalk Server アプリケーションを開発しテストするためのオンデマンドの環境を探している場合は、Azure の仮想マシンで BizTalk Server をご利用ください。

#### BizTalk Adapter サービスとハイブリッド接続の違いは何ですか。

BizTalk Adapter サービスは、Azure BizTalk サービスで使用されます。BizTalk Adapter サービスでは、内部設置型の基幹業務 (LOB) システムへの接続を目的として BizTalk Adapter Pack が使用されます。ハイブリッド接続とは、Azure のアプリケーション、たとえば Web サイトや Mobile Services から内部設置型リソースに簡単に接続するための手段です。

#### BizTalk サービスで契約を作成すると、ブリッジの数が 1 つではなく 2 つ増えるのはなぜですか。

各契約は、送信側の通信ブリッジと受信側の通信ブリッジの 2 つの異なるブリッジで構成されます。

#### ブリッジ数または契約数のクォータの上限に達した場合はどうなりますか。

新しいブリッジを展開したり、新しい契約を作成したりすることができなくなります。追加の展開を行うためには、BizTalk サービスのユニット数をスケール アップするか、上位のエディションにアップグレードする必要があります。

#### BizTalk サービスの 1 つのレベルから別のレベルに移行するにはどのようにしますか。

1 つのレベルから別のレベルへの移行には、バックアップと復元のフローを使用します。一部の移行パスのみサポートされます。サポートされる移行パスの詳細については、[BizTalk サービスのバックアップと復元][BizTalk サービスのバックアップと復元]に関するページを参照してください。

#### BizTalk Adapter サービスはサービスに含まれていますか。ソフトウェアはどのようにして取得しますか。

はい、BizTalk Adapter Pack 付きの BizTalk Adapter サービスは、Azure BizTalk サービス SDK の[ダウンロード][ダウンロード]に含まれています。

## 次へ

Azure BizTalk サービスを Azure 管理ポータルでプロビジョニングするには、[Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング][Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]に関するページを参照してください。アプリケーションの作成を開始するには、[Azure BizTalk サービス][Azure BizTalk サービス]に関するページを参照してください。

## 関連項目

-   [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング][Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]
-   [BizTalk サービス: プロビジョニングの状態のチャート][BizTalk サービス: プロビジョニングの状態のチャート]
-   [BizTalk サービス: ダッシュボード、監視、およびスケールタブ][BizTalk サービス: ダッシュボード、監視、およびスケールタブ]
-   [BizTalk サービス: バックアップと復元][BizTalk サービスのバックアップと復元]
-   [BizTalk サービス: 調整][BizTalk サービス: 調整]
-   [BizTalk サービス: 発行者名および発行者キー][BizTalk サービス: 発行者名および発行者キー]
-   [Azure BizTalk サービス SDK の使用開始に関するページ][Azure BizTalk サービス SDK の使用開始に関するページ]

  [BizTalk サービスのバックアップと復元]: http://go.microsoft.com/fwlink/p/?LinkID=329873
  [ダウンロード]: http://www.microsoft.com/download/details.aspx?id=39087
  [Azure 管理ポータルを使用した BizTalk サービスのプロビジョニング]: http://go.microsoft.com/fwlink/p/?LinkID=302280
  [Azure BizTalk サービス]: http://go.microsoft.com/fwlink/p/?LinkID=235197
  [BizTalk サービス: プロビジョニングの状態のチャート]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [BizTalk サービス: ダッシュボード、監視、およびスケールタブ]: http://go.microsoft.com/fwlink/p/?LinkID=302281
  [BizTalk サービス: 調整]: http://go.microsoft.com/fwlink/p/?LinkID=302282
  [BizTalk サービス: 発行者名および発行者キー]: http://go.microsoft.com/fwlink/p/?LinkID=303941
  [Azure BizTalk サービス SDK の使用開始に関するページ]: http://go.microsoft.com/fwlink/p/?LinkID=302335
