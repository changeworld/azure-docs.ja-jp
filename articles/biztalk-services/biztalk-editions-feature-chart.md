---
title: "BizTalk サービス エディションの機能について説明 |Microsoft ドキュメント"
description: "BizTalk サービス エディションの機能の比較: Developer、Basic、Standard、および Premium を解放します。 MABS、WABS です。"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: c589629f-06b1-44bb-b8ca-1db71826ea59
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 718b57a801a9ba62a0154ae42da2ac0c0741f203
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="biztalk-services-editions-chart"></a>BizTalk サービス: エディション グラフ

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk サービスでは、いくつかのエディションを提供します。 この記事の内容を使用して、シナリオとビジネスのニーズに適したエディションを判断します。

## <a name="compare-the-editions"></a>エディションを比較します。
**解放 (プレビュー)**

作成し、ハイブリッド接続を管理できます。 ハイブリッド接続は、Azure web サイトを SQL Server と同様に、内部設置型システムに接続する簡単な方法です。

**開発者**

X12 および AS2 経由で使いやすい取引パートナー管理ポータルと一般的な EDI スキーマのサポートがあるハイブリッド接続、EAI と EDI メッセージの処理と高度な EDI 処理が含まれています。 読み取りし、書き込みのメッセージを HTTP/S、REST、FTP、WCF および SFTP のプロトコルを使用した、クラウド内のサービスを接続の一般的な EAI シナリオを作成できます。  すぐに使用できる SAP、Oracle eBusiness、Oracle DB、Siebel、および SQL Server のアダプターで、オンプレミス LOB システムへの接続を利用します。 Visual Studio tools で開発者中心環境を使用して、容易に開発および展開します。 開発およびテスト目的でのみでないサービス レベル アグリーメント (SLA) に制限されます。

**基本的な**

ハイブリッド接続、EAI ブリッジ、EDI アグリーメント、および BizTalk Adapter Pack の接続の増加と開発者用の機能のほとんどが含まれます。 高可用性、およびサービス レベル アグリーメント (SLA) とをスケーリングするオプションも提供します。

**標準**

ハイブリッド接続、EAI ブリッジ、EDI アグリーメント、および BizTalk Adapter Pack の接続の増加とのすべての基本的な機能が含まれています。 高可用性、およびサービス レベル アグリーメント (SLA) とをスケーリングするオプションも提供します。

**Premium**

ハイブリッド接続、EAI ブリッジ、EDI アグリーメント、および BizTalk Adapter Pack の接続が増えると、標準的な機能がすべて含まれます。 アーカイブ、高可用性、およびサービス レベル アグリーメント (SLA) とをスケーリングするオプションも含まれています。

## <a name="editions-chart"></a>エディションのグラフ
次の表は、相違点を一覧表示します。

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>解放 (プレビュー)</th>
        <th>Developer</th>
        <th>基本</th>
        <th>標準</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>最低価格</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk Services の料金詳細</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Azure 料金計算ツール</a></td>
</tr>
<tr>
<td><strong>既定の最小構成</strong></td>
<td>1 空きユニット</td>
<td>開発者の 1 単位</td>
<td>1 の基本単位</td>
<td>1 の標準的な単位</td>
<td>1 premium ユニット</td>
</tr>
<tr>
<td><strong>小数点以下桁数</strong></td>
<td>拡大/縮小なし</td>
<td>拡大/縮小なし</td>
<td>1 の基本単位の間隔で、[はい]</td>
<td>1 の標準的な単位の間隔で、[はい]</td>
<td>Premium ユニットが 1 の単位で、[はい]</td>
</tr>
<tr>
<td><strong>スケール アウトを許可される最大</strong></td>
<td>拡大/縮小なし</td>
<td>拡大/縮小なし</td>
<td>最大 8 個のユニット</td>
<td>最大 8 個のユニット</td>
<td>最大 8 個のユニット</td>
</tr>
<tr>
<td><strong>単位あたりの EAI ブリッジ</strong></td>
<td>含まれない</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDI、AS2</strong>
<br/><br/>
TPM の契約が含まれています</td>
<td>含まれない</td>
<td>含まれています。 ユニットあたり 10 の契約です。</td>
<td>含まれています。 単位あたり 50 契約。</td>
<td>含まれています。 単位あたり 250 契約。</td>
<td>含まれています。 単位あたり 1000 契約。</td>
</tr>
<tr>
<td><strong>単位あたりのハイブリッド接続</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>ハイブリッド接続のデータ転送 (GB) 単位あたり</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>オンプレミス LOB システムへの BizTalk アダプター サービスの接続</strong></td>
<td>含まれない</td>
<td>1 の接続</td>
<td>2 つの接続</td>
<td>接続を 5 つ</td>
<td>25 の接続</td>
</tr>
<tr>
<td align="left"><strong>サポートされるプロトコル/システム:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Service Bus (SB)</li>
<li>Azure Blob</li>
<li>REST Api</li>
</ul>
</td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>高可用性</strong>
<br/><br/>
サービス レベル アグリーメント (SLA) を参照してください<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Azure BizTalk サービスの料金</a>です。
</td>
<td>含まれない</td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>バックアップと復元</strong></td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>追跡</strong></td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>アーカイブ</strong><br/><br/>
否認不可 Receipt (NRR) および追跡メッセージをダウンロードするが含まれています</td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれていません</td>
<td>含まれていません</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>カスタム コードの使用</strong></td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
<tr>
<td><strong>カスタム XSLT を含む変換の使用</strong></td>
<td>含まれない</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
<td>含まれています。</td>
</tr>
</table>

> [!NOTE]
> ハードウェア障害に対する回復性は、の場合、高可用性では、1 つの BizTalk 単位内の複数の Vm のことを意味します。
> 
> 

## <a name="faqs"></a>よく寄せられる質問
#### <a name="what-is-a-biztalk-unit"></a>BizTalk ユニットとは何ですか。
「単位」は、Azure BizTalk サービスの展開のアトミック レベルです。 各エディションを別の計算容量とメモリを持つ単位が付属します。 たとえば、基本単位はより多くのコンピューティング開発者よりも、標準の詳細のより基本的なコンピューティングというようにします。 BizTalk サービスをスケールする場合は、単位でスケールします。

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>BizTalk サービスと Azure BizTalk 仮想マシンの間で違いは何ですか。
BizTalk サービスは、クラウド統合ソリューションを構築するための true プラットフォーム-as a Service (PaaS) アーキテクチャを提供します。 PaaS モデルと、アプリケーション ロジックに完全に専念し、すべてのままにインフラストラクチャの管理を Microsoft に含みます。

* 仮想マシンを更新プログラムを管理する必要はありません。
* Microsoft では、可用性がなります。
* 単純に要求することによってオンデマンドまたは Azure ポータルを介して容量未満のスケールを制御します。

BizTalk Server を Azure Virtual Machines では、インフラストラクチャの as a Service (IaaS) アーキテクチャを提供します。 仮想マシンを作成して構成して、内部設置型環境と同じようにコード変更せずにクラウドで既存のアプリケーションを実行しやすきます。 IaaS、仮想マシンの構成、(たとえば、ソフトウェアのインストールおよび OS の修正プログラム) の仮想マシンの管理、および高可用性のため、アプリケーションの設計には。

インフラストラクチャ管理の作業量を最小限に抑える新しい統合ソリューションの構築で検索する場合は、BizTalk サービスを使用します。 場合は、既存の BizTalk ソリューションを迅速に移行するか、BizTalk Server アプリケーションを開発およびテストがオンデマンドで環境を探しては、Azure の仮想マシンで BizTalk Server を使用します。

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>BizTalk アダプター サービスとのハイブリッド接続の違いは何ですか。
BizTalk アダプター サービスは、Azure BizTalk サービスによって使用されます。 BizTalk アダプター サービスでは、BizTalk Adapter Pack を使用して、オンプレミス基幹業務 (LOB) システムに接続します。 ハイブリッド接続は、Azure App Service と Azure のモバイル サービスで Web アプリの機能と同様の Azure アプリケーションを内部設置型リソースに接続する簡単で便利な方法を提供します。

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>"ハイブリッド接続のデータ転送 (GB) 単位あたり"どういう意味ですか。 これは、1 分/時間/日/週/月あたりですか。 制限に達したときの動作ですか。
単位あたりのハイブリッド接続のコストは、BizTalk サービス エディションによって異なります。 コストの依存を簡単に言えば、転送するデータの量。 たとえば、毎日 10 GB のデータを転送するよりも低いコスト毎日 100 GB を転送します。 使用して、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/?scenario=full)BizTalk へのサービスの特定のコストを判断します。 通常、制限が日常的に適用されます。 制限を超えた場合、超過は $1 GB あたりのレートで課金されます。

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>BizTalk サービスで、アグリーメントを作成するとき理由ブリッジの数まで進む 2 ではなく 1 つだけですか。
各アグリーメントは 2 つの異なるブリッジの構成されています。 送信側の通信のブリッジと受信側の通信のブリッジです。

#### <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>ブリッジまたは契約の数のクォータの上限をヒットしたときの動作ですか。
されない任意の新しいブリッジを配置する新しい契約を作成します。 詳細を展開するには、BizTalk サービスの複数の単位をスケール アップまたは上位のエディションにアップグレードする必要があります。

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>別の BizTalk サービスの 1 つの階層から移行方法
無償のエディションにすることはできません移行または 'をスケール アップ' を別の層とできませんするバックアップおよび復元を別の層。 別の層を必要がある場合は、新しいアプリケーション層を使用して新しい BizTalk サービスを作成します。 ハイブリッド接続を含む、無償のエディションを使用して作成されたすべての成果物は、新しい BizTalk サービスで再作成する必要があります。 

残りのエディションでは、バックアップを使用し、別の 1 つの層から、アイテムを移行するため復元します。 たとえば、層では、標準的なアーティファクトをバックアップし、Premium 階層に復元し、します。 [BizTalk サービス: バックアップし、復元](biztalk-backup-restore.md)サポートされている移行パスをについて説明し、どのような成果物のバックアップを一覧表示します。 ハイブリッド接続がバックアップされないことに注意してください。 バックアップと復元を新しい階層に、後にもう一度作成するハイブリッド接続します。  

#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>BizTalk アダプター サービスのサービスに含まれるですか。 ソフトウェアを受信する方法は?
BizTalk Adapter Pack での BizTalk アダプター サービスは、Azure BizTalk Services SDK に含まれてはい、[ダウンロード](http://www.microsoft.com/download/details.aspx?id=39087)です。

## <a name="next-steps"></a>次のステップ
Azure ポータルで Azure BizTalk サービスを作成するには[BizTalk サービス: Azure ポータルを使用したプロビジョニング](biztalk-provision-services.md)です。 アプリケーションの作成を開始するには[Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197)です。

## <a name="additional-resources"></a>その他のリソース
* [Azure ポータルを使用して BizTalk サービス: のプロビジョニング](biztalk-provision-services.md)<br/>
* [BizTalk サービス: プロビジョニング状態チャート](biztalk-service-state-chart.md)<br/>
* [BizTalk サービス: ダッシュ ボード、モニター、およびスケール タブ](biztalk-dashboard-monitor-scale-tabs.md)<br/>
* [BizTalk サービス: バックアップと復元](biztalk-backup-restore.md)<br/>
* [BizTalk サービス: 調整](biztalk-throttling-thresholds.md)<br/>
* [BizTalk Services: 発行者名および発行者キー](biztalk-issuer-name-issuer-key.md)<br/>
* [操作方法を使用して開始 Azure BizTalk サービス SDK](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>

