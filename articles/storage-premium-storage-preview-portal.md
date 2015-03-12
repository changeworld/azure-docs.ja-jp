<properties 
	pageTitle="Premium ストレージ:Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ" 
	description="ディスク向け Azure Premium Storage を説明します。Premium Storag アカウントの作成方法について説明します。" 
	services="storage" 
	documentationCenter="" 
	authors="Selcin" 
	manager="adinah" 
	editor="tysonn"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/08/2014" 
	ms.author="selcint"/>


# Premium ストレージ:Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ

**ディスク向け Azure Premium Storage** のパブリック プレビューへようこそ。

新しい Premium Storage の導入により、Microsoft Azure では**Premium Storage** と **Standard Storage** という耐久性の高い 2 種類のストレージをご利用いただけるようになりました。Premium Storage は最新技術のソリッド ステート ドライブ (SSD) を使用してデータを格納し、Standard Storage はデータをハード ディスク ドライブ (HDD) に格納します。 

Premium Storage は、Azure 仮想マシンで実行される高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供します。仮想マシン (VM) には、複数の Premium Storage ディスクをアタッチできます。Premium Storage を使用すると、アプリケーションに VM あたり最大 32 TB のストレージを設定できます。さらに VM あたり 50,000 IOPS (1 秒あたりの入力/出力操作) を実現でき、読み取り操作の待ち時間も非常に短くなります。現在、Azure 仮想マシンが使用するディスクにデータを格納する場合にのみ Premium Storage を利用できます。 

Azure Premium Storage プレビューにサインアップするには、[Azure プレビュー](http://azure.microsoft.com/services/preview/)のページにアクセスしてください。

この記事では、Azure Premium Storage の概要を詳細に説明します。

**目次:**


* [Premium Storage について理解しておくべきこと](#important)

* [ディスク向け Premium Storage の使用](#using)

* [Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scale)	

* [Premium Storage を使用するときの調整](#throttling)	

* [Premium Storage を使用するときのスナップショットと Copy Blob](#restapi)	

* [Premium Storage を使用するときの料金と課金](#pricing)	

* [ディスク向け Premium Storage アカウントの作成と使用](#howto1)	

* [その他のリソース](#see)

###<a id="important">Premium Storage について理解しておくべきこと</a>


次は、Premium Storage の使用前や使用時に考慮すべき重要な事柄の一覧です。

- Premium Storage を使用するには、Premium Storage アカウントが必要です。Premium Storage アカウントの作成方法については、「[ディスク向け Premium Storage アカウントの作成と使用](#howto1)」を参照してください。

- Premium Storage は現在、[Microsoft Azure プレビュー ポータル](https://portal.azure.com/)で利用でき、[Storage REST API](http://msdn.microsoft.com//library/azure/dd179355.aspx) バージョン 2014-02-14 以降、[Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) バージョン 2014-10-01 以降、[Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) バージョン 0.8.10 以降の SDK ライブラリからアクセスできます。 

- Premium Storage は限定プレビュー版として米国西部、米国東部 2、西ヨーロッパでご利用になれます。

- Premium Storage は Azure ページ BLOB のみをサポートしています。これは、Azure 仮想マシン (VM) の永続ディスクを保持するために使用されます。Azure ページ BLOB については、「[ブロック BLOB およびページ BLOB について](http://msdn.microsoft.com/library/azure/ee691964.aspx)」を参照してください。Premium Storage は Azure ブロック BLOB、Azure ファイル、Azure テーブル、Azure キューをサポートしていません。

- Premium Storage アカウントローカル冗長 (LRS) であり、1 つのリージョン内にデータのコピーを 3 つ保持します。Premium Storage を使用するときに Geo レプリケーションについて考慮すべき点については、この記事の「[Premium Storage を使用するときのスナップショットと Copy Blob](#restapi)」セクションを 参照してください。

- VM ディスクで Premium Storage アカウントを使用する場合は、DS シリーズの VM を使用する必要があります。DS シリーズの VM では、Standard Storage ディスクと Premium Storage ディスクの両方を使用できます。DS シリーズ以外の VM では Premium Storage ディスクを使用できません。使用可能な Azure VM ディスクの種類とサイズについては、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。 

- VM 用の Premium Storage ディスクの設定方法は、Standard Storage ディスクの場合と同じです。使用する Azure ディスクと VM に最適な Premium Storage オプションを選択する必要があります。VM のサイズは、Premium 製品のパフォーマンス特性に基づき、ワークロードに最適なものにします。詳細については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scale)」を参照してください。
 
- Premium Storage アカウントをカスタムのドメイン名にマッピングすることはできません。

- 現在、Premium Storage では、Storage Analytics はサポートされていません。

###<a id="using">ディスク向け Premium Storage の使用</a>
ディスク向け Premium Storage は次のいずれかの方法で使用できます。

- 最初に新しい Premium Storage アカウントを作成し、VM の作成時にそのアカウントを使用します。
- 新しい DS シリーズの VM を作成します。VM の作成時には、以前に作成した Premium Storage アカウントを選択する、新しいアカウントを作成する、Azure ポータルで自動的に既定の Premium アカウントを作成する、のいずれかの方法を選択できます。

Azure は、ストレージ アカウントをオペレーティング システムとデータ ディスクのコンテナーとして使用します。つまり、Azure DS シリーズの VM を作成して Azure Premium Storage アカウントを選択すると、オペレーティング システムとデータ ディスクはそのストレージ アカウントに格納されます。

Premium Storage のメリットを利用するには、最初に  *Premium_LRS* のアカウント タイプを使用して Premium Storage アカウントを作成します。作成には、[Microsoft Azure プレビュー ポータル](https://portal.azure.com/)、[Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)、[Service Management REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) のいずれかを使用できます。詳細な手順については、「[ディスク向け Premium Storage アカウントの作成と使用](#howto1)」を参照してください。

<h4>重要:</h4>

- Premium Storage アカウントの容量および帯域幅のプレビュー版での制限については、「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scale)」セクションを参照してください。アプリケーションのニーズが単一のストレージ アカウントの拡張性ターゲットを上回った場合は、複数のストレージ アカウントを使用するようにアプリケーションを構築し、それらのストレージ アカウント間でデータを分割します。たとえば、51 TB のディスクを複数の VM にアタッチする場合、単一の Premium Storage アカウントの制限は 32 TB であるため、ディスクを 2 つのストレージ アカウントに分散させます。
- 既定では、ディスクのキャッシュ ポリシーは、すべてのPremium データ ディスクに対して「読み取り専用」、VM にアタッチされた Premium オペレーティング システム ディスクに対して「読み取り/書き込み」です。アプリケーションの I/O のパフォーマンスを最適化するには、この構成をお勧めします。書き込み量の多いディスクや書き込み専用のディスク (SQL Server ログ ファイルなど) の場合は、ディスク キャッシュを無効にすることで、アプリケーションのパフォーマンスを向上できる場合があります。
- ディスク トラフィックが流れるのに十分な帯域幅が VM で利用できることを確認します。各 VM サイズで使用可能なディスク帯域幅については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。
- 同じ DS シリーズの VM 内では、Premium Storage ディスクと Standard Storage ディスクの両方を使用できます。
- Premium Storage を使用すると、DS シリーズの VM をプロビジョニングし、複数の永続データ ディスクをVM に接続できます。必要に応じて、ディスク全体をストライピングして容量を増やし、ボリュームのパフォーマンスを高めることができます。[記憶域スペース](http://technet.microsoft.com/library/hh831739.aspx)を使用して Premium Storage データ ディスクをストライピングする場合は、使用するディスクごとに 1 つの列で構成する必要があります。そうしない場合は、ディスク全体のトラフィックの配分が不均等になるため、ストライプ ボリュームの全体的なパフォーマンスが低下する可能性があります。既定では、サーバー マネージャー ユーザー インターフェイス (UI) で最大 8 つのディスクの列を設定できます。ただし、8 つ以上のディスクがある場合は、PowerShell を使用してボリュームを作成し、列の数を手動で指定する必要があります。そうしない場合、サーバー マネージャー UI はそれ以上のディスクがある場合でも 8 つの列を使用し続けます。たとえば、1 つのストライプ セット内に 32 のディスクがある場合は、32 の列を指定する必要があります。[New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell コマンドレットの  *NumberOfColumns* パラメーターを使用して、仮想ディスクで使用される列の数を指定できます。詳細については、「[Storage Spaces Overview (記憶域スペースの概要)](http://technet.microsoft.com/library/jj822938.aspx)」および「[Storage Spaces Frequently Asked Questions (記憶域スペースに関してよく寄せられる質問)](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)」を参照してください。


###<a id="scale">Premium Storage を使用するときの拡張性とパフォーマンスのターゲット</a>
Premium Storage アカウントにディスクをプロビジョニングする場合、アカウントが利用可能な 1 秒あたりに処理できる入力/出力 (IOPS) の量やスループット (帯域幅) は、ディスクのサイズによって異なります。現在、Premium Storage ディスクには、P10、P20、P30 の 3 種類があります。次の表に示すように、それぞれに IOPS とスループットに固有の制限があります。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>Premium Storage ディスク タイプ</strong></td>
	<td><strong>P10</strong></td>
	<td><strong>P20</strong></td>
	<td><strong>P30</strong></td>
</tr>
<tr>
	<td><strong>ディスク サイズ</strong></td>
	<td>128 GB</td>
	<td>512 GB</td>
	<td>1024 GB (1 TB)</td>
</tr>
<tr>
	<td><strong>ディスクあたりの IOPS</strong></td>
	<td>500</td>
	<td>2300</td>
	<td>5000</td>
</tr>
<tr>
	<td><strong>ディスクあたりのスループット</strong></td>
	<td>100 MB/秒</td>
	<td>150 MB/秒</td>
	<td>200 MB/秒</td>
</tr>
</tbody>
</table>

Azure はディスク サイズ (切り上げたもの) を、表にある最も近い Premium Storage ディスク オプションにマッピングします。たとえば、サイズが 100 GB のディスクは P10 オプションに分類され、1 秒あたり最大 500 IO ユニットまで実行でき、1 秒あたりのスループットは 100 MB となります。同様に、サイズが 400 GB のディスクは P20 オプションに分類され、1 秒あたり最大 2300 IO ユニットまで実行でき、1 秒あたりのスループットは 150 MB となります。

入力/出力 (I/O) ユニット サイズは 256 KB です。転送されるデータが 256 KB に満たない場合は、単一の I/O ユニットとみなされます。I/O のサイズが大きくなると、サイズが 256 KB の複数の I/O としてカウントされます。たとえば、1100 KB I/O は 5 つの  I/O ユニットとしてカウントされます。

スループット制限には、ディスクの読み取りと書き込みの両方が含まれます。たとえば、P10 ディスクの場合、1 秒あたりの読み取りと書き込みは 100 MB 以下となります。同様に、単一の P10 ディスクは 1 秒あたりに 100 MB の読み取りか 100 MB の書き込みのいずれかを処理でき、例のように 1 秒あたりの書き込みが 60 MB、読み取りが 40 MB となる場合もあります。
 
Azure でディスクを作成する場合は、容量、パフォーマンス、拡張性、ピーク時の負荷に関するアプリケーションのニーズに基づいた最適な Premium Storage ディスク製品を選択します。

次の表は、Premium Storage アカウントの拡張性ターゲットを示しています。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tbody>
<tr>
	<td><strong>合計アカウント容量</strong></td>
	<td><strong>ローカル冗長ストレージ アカウントの合計帯域幅</strong></td>
</tr>
<tr>
	<td>
	<ul>
       <li type=round>ディスク容量:32 TB</li>
       <li type=round>スナップショット容量:10 TB</li>
    </ul>
	</td>
	<td>受信と送信を合わせて最大 50 GB/秒</td>
</tr>
</tbody>
</table>

- 受信とはストレージ アカウントに送信されるすべてのデータ (要求) のことです。
- 送信とはストレージ アカウントから送信されるすべてのデータ (応答) のことです。

詳細については、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](http://msdn.microsoft.com/library/dn249410.aspx)」を参照してください。


###<a id="throttling">Premium Storage を使用するときの調整</a>
アプリケーションの IOPS やスループットが Premium Storage ディスクに割り当てられた制限を超過すると、調整が発生することがあります。調整を回避するには、プロビジョニングしたディスクの拡張性やパフォーマンス ターゲットに基づいて、ディスクの保留中の I/O 要求の数を制限することをお勧めします。  

調整を回避できれば、アプリケーションの待ち時間を最短にすることができます。一方で、ディスクの保留中の I/O 要求の数が少なすぎる場合は、ディスクで利用可能な最大 IOPS とスループット レベルのメリットを活用できません。

次の例は、調整レベルの計算方法を示しています。

<h4>例 1:</h4>
アプリケーションは、P10 ディスク上で、1 秒間に 16 KB ディスク サイズの I/O を 495 回実行しました (これは 495 I/O ユニットに等しくなります)。同じ 1 秒内に 2 MB の I/O を実行すると、I/O の合計ユニットは 495 + 8 となります。これは、I/O ユニット サイズが 256 KB であることから、2 MB の I/O は 2048 KB / 256 KB = 8 I/O ユニットとなるためです。合計の 495 + 8 はディスク制限の 500 IOPS を超過するため、調整が発生します。 

<h4>注:</h4> 
すべての計算は、256 KB の I/O ユニット サイズに基づいて行われます。

<h4>例 2:</h4>
アプリケーションが P10 ディスク上で、256 KB ディスク サイズの I/O を 400 回実行しました。合計消費帯域幅は、(400 * 256) / 1024 = 100 MB/秒となります。100 MB/秒は、P10 ディスクのスループット制限です。アプリケーションが 1 秒間あたりにより多くの I/O を実行しようとすると、割り当てられた制限を超過してしまうため、調整が発生します。

<h4>注:</h4>
ディスク トラフィックの大部分が小さなサイズの I/O で構成されていると、アプリケーションがスループット制限に達する前に、IOPS 制限に達してしまう場合がほとんどです。逆に、ディスク トラフィックの大部分が大きなサイズの I/O で構成されている場合は、IOPS 制限ではなくスループット制限に達してしまうことがほとんどです。最適なサイズの I/O を使用し、ディスクの保留中の I/O 要求の数を制限することで、アプリケーションの IOPS とスループットを最大化できます。


###<a id="restapi">Premium Storage を使用するときのスナップショットと Copy Blob</a>
Premium Storage の場合も、Standard Storage を使用してスナップショットを作成するときと同じ方法でスナップショットを作成できます。Premium Storage はローカル冗長であるため、スナップショットを作成して、それらのスナップショットを Geo 冗長 Standard Storage アカウントにコピーすることをお勧めします。詳細については、「[Azure Storage 冗長オプション](http://msdn.microsoft.com/library/azure/dn727290.aspx)」を参照してください。

ディスクが VM にアタッチされている場合、ディスクをバックアップするページ BLOB で特定の API 操作が許可されなくなります。たとえば、ディスクが VM にアタッチされている間は、[Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) 操作を実行できません。その代わりに、最初に [Snapshot Blob](http://msdn.microsoft.com/library/azure/ee691971.aspx) REST API メソッドを使用して BLOB のスナップショットを作成してから、スナップショットの [Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx) を実行してアタッチされたディスクをコピーします。または、ディスクのアタッチを解除してから、基盤となる BLOB に対して必要な操作を実行します。

<h4>重要:</h4>

- Premium Storage での Copy Blob 操作でコピー先にある既存の BLOB が上書きされる場合は、上借りされる BLOB のスナップショットを作成しないようにします。Premium Storage アカウント内またはアカウント間でコピーを作成する場合は、コピー開始時にコピー先の BLOB がスナップショットを持つことができません。
- 単一の BLOB のスナップショットの数は 100 に制限されています。スナップショットは最大で 10 分間隔で取得できます。
- Premium Storage アカウントあたりのスナップショットの最大容量は 10 TB です。スナップショット容量とは、スナップショットに存在する一意のデータのことです。つまり、スナップショット容量にはベース BLOB のサイズは含まれません。
- AzCopy か Copy Blob を使用すると、Premium Storage アカウントから Geo 冗長 Standard Storage アカウントにスナップショットをコピーできます。詳細については、「[Microsoft Azure Storage での AzCopy の使用方法](http://azure.microsoft.com/documentation/articles/storage-use-azcopy/)」と「[Copy Blob](http://msdn.microsoft.com/library/azure/dd894037.aspx)」を参照してください。
- Premium Storage アカウントでページ BLOB に REST 操作を実行する方法の詳細については、MSDN ライブラリの「[Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)」を参照してください。


###<a id="pricing">Premium Storage を使用するときの料金と課金</a>
Premium Storage を使用するときには、課金に関する次の考慮事項が適用されます。

- Premium Storage ディスクへの課金はプロビジョニングしたディスクのサイズによって異なります。Azure はディスク サイズ (切り上げたもの) を「[Premium Storage を使用するときの拡張性とパフォーマンスのターゲット](#scale)」にある表で指定されている、最も近い Premium Storage ディスク オプションにマッピングします。プロビジョニングされたディスクには、Premium Storage 製品の月額料金を使用して、時間割りで計算して課金されます。たとえば、P10 ディスクをプロビジョニングし、20 時間後にそのディスクを削除した場合は、P10 製品の 20 時間分に対して課金されます。これは、実際にディスクに書き込まれたデータの量や、使用した IOPS/スループットには関係ありません。
- Premium Storage でのスナップショットについては、スナップショットで使用した追加の容量に対して課金されます。詳細については、「[BLOB のスナップショットの作成](http://msdn.microsoft.com/library/azure/hh488361.aspx)」を参照してください。
- [送信データの転送](http://azure.microsoft.com/pricing/details/data-transfers/) (Azure データ センターから送信されるデータ) には、帯域幅の使用量に対して課金されます。

Premium Storage と DS シリーズの VM の料金についての詳細は、次の項目を参照してください。

- [Azure Storage の料金](http://azure.microsoft.com/pricing/details/storage/)
- [仮想マシンの料金](http://azure.microsoft.com/pricing/details/virtual-machines/)

###<a id="howto1">ディスク向け Premium Storage アカウントの作成と使用</a>
このセクションでは、Azure プレビュー ポータルと Azure PowerShell を使用した Premium Storag アカウントの作成方法を説明します。さらに、Premium Storage を使用するときの仮想マシンの作成や仮想マシンへのデータ ディスクのアタッチといった、Premium Storage アカウントの使用例を紹介します。

このセクションの内容:

* [Azure プレビュー ポータル:Premium Storage アカウントの作成](#howto3)	

* [Azure PowerShell:Premium Storage アカウントの作成と、基本的な VM 操作での使用](#howto2)	
 
###<a id="howto3">Azure プレビュー ポータル:Premium Storage アカウントの作成</a>
このセクションでは、Azure プレビュー ポータルを使用した Premium Storage アカウントの作成方法を説明します。

1.	[Azure プレビュー ポータル](https://portal.azure.com/)にサインインします。まだサブスクリプションをお持ちでない場合は、[無料評価版](http://azure.microsoft.com/pricing/free-trial/)をお試しください。 


    >[AZURE.NOTE] Azure 管理ポータルにログインする場合は、ポータルの右上にあるユーザー アカウント名をクリックします。次に、**[新しいポータルに切り替え]** をクリックします。
        

2.	ハブ メニューの **[新規]** をクリックします。

3.	**[新規]** で、**[すべて]** をクリックします。**[データ、ストレージ、キャッシュ、+ バックアップ]** を選択します。そこから **[ストレージ]**、**[作成]** の順にクリックします。

4.	[ストレージ アカウント] ブレードで、ストレージ アカウントの名前を入力します。**[料金レベル]** をクリックします。**[推奨される料金レベル]** ブレードで、**[すべての料金レベルの参照]** をクリックします。**[料金レベルの選択]** ブレードで、**[Premium ローカル冗長]** を選択します。**[選択]** をクリックします。**[ストレージ アカウント]** ブレードには、既定で **[料金レベル]** に **[Standard-GRS]** と表示されます。**[選択]** クリックすると、**[料金レベル]** に **[Premium-LRS]** と表示されます。
	
	![Pricing Tier][Image1]

	
5.	**[ストレージ アカウント]** ブレードの **[リソース グループ]**、**[サブスクリプション]**、**[場所]**、**[診断]** には規定値をそのまま使用します。**[作成]** をクリックします。

Azure 環境に関する詳細なチュートリアルは、「[Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)」を参照してください。

###<a id="howto2">Azure PowerShell:Premium Storage アカウントの作成と、基本的な VM 操作での使用</a>
このセクションでは、Azure PowerShell を使用した Premium Storage アカウントの作成方法、仮想マシンを作成しデータ ディスクを VM にアタッチする際のアカウントの使用方法を説明します。

1. 「[Azure PowerShell のインストールおよび構成方法](http://azure.microsoft.com/documentation/articles/install-configure-powershell/)」の手順に従って、PowerShell 環境を設定します。
2. PowerShell コンソールを起動してサブスクリプションに接続し、コンソール ウィンドウで次の PowerShell コマンドレットを実行します。この PowerShell ステートメントで分かるように、Premium Storage アカウントを作成する場合は、**Type** パラメーターを **Premium_LRS** に指定する必要があります。

		New-AzureStorageAccount -StorageAccountName "yourpremiumaccount" -Location "West US" -Type "Premium_LRS"

3. 次に、DS シリーズの VM を作成し、コンソール ウィンドウで次の PowerShell コマンドレットを実行して Premium Storage を指定します。

    	$storageAccount = "yourpremiumaccount"
    	$adminName = "youradmin"
    	$adminPassword = "yourpassword"
    	$vmName ="yourVM"
    	$location = "West US"
    	$imageName = "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201409.01-en.us-127GB.vhd"
    	$vmSize ="Standard_DS2"
    	$OSDiskPath = "https://" + $storageAccount + ".blob.core.windows.net/vhds/" + $vmName + "_OS_PIO.vhd"
    	$vm = New-AzureVMConfig -Name $vmName -ImageName $imageName -InstanceSize $vmSize -MediaLocation $OSDiskPath
    	Add-AzureProvisioningConfig -Windows -VM $vm -AdminUsername $adminName -Password $adminPassword
    	New-AzureVM -ServiceName $vmName -VMs $VM -Location $location

4. VM のディスク領域を増やしたい場合は、VM の作成後に、コンソール ウィンドウで次の PowerShell コマンドレットを実行して既存の DS シリーズの VM に新しいデータ ディスクをアタッチします。

    	$storageAccount = "yourpremiumaccount"
    	$vmName ="yourVM"
    	$vm = Get-AzureVM -ServiceName $vmName -Name $vmName
    	$LunNo = 1
    	$path = "http://" + $storageAccount + ".blob.core.windows.net/vhds/" + "myDataDisk_" + $LunNo + "_PIO.vhd"
    	$label = "Disk " + $LunNo
    	Add-AzureDataDisk -CreateNew -MediaLocation $path -DiskSizeInGB 128 -DiskLabel $label -LUN $LunNo -HostCaching ReadOnly -VM $vm | Update-AzureVm

###<a id="see">その他のリソース</a>
[Azure Premium Storage での BLOB サービス操作の使用](http://go.microsoft.com/fwlink/?LinkId=521969)

[Windows を実行する仮想マシンの作成](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial-azure-preview/)

[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)

[ストレージのドキュメント](http://azure.microsoft.com/documentation/services/storage/)

[MSDN リファレンス](http://msdn.microsoft.com/library/azure/gg433040.aspx)

[Image1]: ./media/storage-premium-storage-preview-portal/Azure_pricing_tier.png
<!--HONumber=42-->
