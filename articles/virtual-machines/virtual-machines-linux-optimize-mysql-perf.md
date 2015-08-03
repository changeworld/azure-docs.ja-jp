<properties 
	pageTitle="Azure Linux VM 上での MySQL のパフォーマンスを最適化する" 
	description="Linux を実行する Azure の仮想マシン (VM) で実行されている MySQL を最適化する方法について説明します。" 
	services="virtual-machines" 
	documentationCenter="" 
	authors="NingKuang" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="ningk"/>

#Azure Linux VM 上での MySQL のパフォーマンスを最適化する 

Azure では、仮想ハードウェアの選択、およびソフトウェアの構成の両方で MySQL のパフォーマンスに影響を与える多くの要素があります。この記事では、ストレージ、システム、およびデータベースの構成でのパフォーマンスの最適化について説明します。

##Azure の仮想マシン上での RAID の使用 
ストレージは、クラウド環境でデータベースのパフォーマンスに影響を与える重要な要素です。RAID は、1 つのディスクと比較して、同時実行制御を使用して高速アクセスを提供できます。詳細については、[標準の RAID レベル](http://en.wikipedia.org/wiki/Standard_RAID_levels)に関するページを参照してください。

ディスク I/O のスループットと Azure での I/O の応答時間は、RAID によって大幅に向上されます。ラボ テストでは、RAID のディスクの数が 2 倍 (2 から 4、4 から 8 など) になると、スループットが 2 倍になり I/O の応答時間は半分に短縮されました。詳細については、[付録 A](#AppendixA) を参照してください。

ディスク I/O の他に、RAID レベルを上げると、MySQL のパフォーマンスが向上します。詳細については、[付録 B](#AppendixB) を参照してください。

チャンク サイズを考慮することもできます。一般にチャンクのサイズを大きくした場合、特に大量の書き込みに対してオーバーヘッドが低くなります。ただし、チャンク サイズが大きすぎる場合は、追加のオーバーヘッドが追加され、RAID を活用することはできません。現在の既定のサイズは 512 KB で、最も一般的な運用環境に最適であることがわかっています。詳細については、[付録 C](#AppendixC) を参照してください。

別の仮想マシン タイプに追加できるディスクの数には制限があることに注意してください。これらの制限の詳細については、「[Azure の仮想マシンおよびクラウド サービスのサイズ](http://msdn.microsoft.com/library/azure/dn197896.aspx)」を参照してください。より少ないディスクで RAID を設定することもできますが、この記事では RAID 例の説明に、4 つの接続されたデータ ディスクが必要です。

この記事では、Linux 仮想マシンを既に作成していること、および MYSQL をインストールして構成していることを前提としています。作業の開始の詳細については、「Azure で MySQL をインストールする方法」を参照してください。
  
###Azure における RAID の構成
次の手順は、Windows Azure の管理ポータルを使用して Azure に RAID を作成する方法を説明しています。Windows PowerShell スクリプトを使用して RAID を設定することもできます。この例では 4 つのディスクで RAID 0 を構成します。

####手順 1. 仮想マシンにデータ ディスクを追加する  

Azure の管理ポータルの [仮想マシン] ページで、データ ディスクを追加する仮想マシンをクリックします。この例では、仮想マシンは mysqlnode1 です。

![][1]

仮想マシンのページで、**[ダッシュボード]** をクリックします。

![][2]
 

タスク バーで、**[ディスクの接続]** をクリックします。
 
![][3]

**[空のディスクの接続]** をクリックします。

![][4]
 
データ ディスクの場合、**[ホスト キャッシュ設定]** を **[なし]** に設定する必要があります。

これにより、1 つの空のディスクが仮想マシンに追加されます。この手順を 3 回繰り返して RAID に 4 つのデータ ディスクを追加します。

カーネル メッセージ ログを参照して、仮想マシンに追加されたドライブを確認できます。たとえば、Ubuntu でこれを参照するには、次のコマンドを実行します。

	sudo grep SCSI /var/log/dmesg

####手順 2. 追加のディスクで RAID を作成する
詳細な RAID 設定の手順については、この記事で説明しています。

[http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-configure-RAID/)

>[AZURE.NOTE]XFS ファイル システムを使用している場合は RAID を作成した後に次の手順に従ってください。

Debian、Ubuntu、または Linux Mint に XFS をインストールするには、次のコマンドを使用します。

	apt-get -y install xfsprogs  

Fedora、CentOS、または RHEL に XFS をインストールするには、次のコマンドを使用します。

	yum -y install xfsprogs  xfsdump 


####手順 3. 新しいストレージ パスを設定する
次のコマンドを使用します。

	root@mysqlnode1:~# mkdir -p /RAID0/mysql

####手順 4. 新しいストレージ パスに元のデータをコピーする
次のコマンドを使用します。

	root@mysqlnode1:~# cp -rp /var/lib/mysql/* /RAID0/mysql/

####手順 5. MySQL がデータ ディスクにアクセス (読み取り、書き込み) できるようにアクセス許可を変更する
次のコマンドを使用します。

	root@mysqlnode1:~# chown -R mysql.mysql /RAID0/mysql && chmod -R 755 /RAID0/mysql


##ディスク I/O のスケジューリング アルゴリズムを調整する
Linux では次の 4 つの種類の I/O スケジューリング アルゴリズムを実装します。

-	NOOP アルゴリズム (操作なし)
-	期限アルゴリズム (期限)
-	完全に均等化されたキューイング アルゴリズム (CFQ)
-	予算期間アルゴリズム (予想)  

パフォーマンスを最適化するためにさまざまなシナリオでさまざまな I/O スケジューラを選択することができます。完全にランダムなアクセス環境では、CFQ アルゴリズムと期限アルゴリズムにはパフォーマンス上の大きな差はありません。安定性の期限に MySQL データベース環境を設定することをお勧めします。順次入出力が多くがある場合は、CFQ はディスク I/O パフォーマンスを低減することがあります。

SSD およびその他の機器では、NOOP アルゴリズムまたは期限アルゴリズムが既定のスケジューラよりも優れたパフォーマンスを実現できます。

カーネル 2.5 以降は、既定の I/O スケジューリング アルゴリズムは期限アルゴリズムです。カーネル 2.6.18 以降は、CFQ が既定の I/O スケジューリング アルゴリズムになりました。カーネルの起動時にこの設定を指定したり、システムが実行されているときにこの設定を動的に変更できます。

次の例では、既定のスケジューラを確認して NOOP アルゴリズムに設定する方法を説明しています。

Debian 配布ファミリ:

###手順 1. 現在の I/O スケジューラを表示します。
次のコマンドを使用します。

	root@mysqlnode1:~# cat /sys/block/sda/queue/scheduler 

現在のスケジューラを示す次の出力が表示されます。

	noop [deadline] cfq 


###手順 2.I/O スケジューリング アルゴリズムの現在のデバイス (/dev/sda) を変更します。
次のコマンドを使用します。

	azureuser@mysqlnode1:~$ sudo su -
	root@mysqlnode1:~# echo "noop" >/sys/block/sda/queue/scheduler
	root@mysqlnode1:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
	root@mysqlnode1:~# update-grub

>[AZURE.NOTE]これを /dev/sda だけに設定するのは役に立ちません。データベースが存在するすべてのデータ ディスクに設定する必要があります。

次の出力では、grub.cfg が正常に再構築され、既定のスケジューラが NOOP に更新されたことが参照できます。

	Generating grub configuration file ...
	Found linux image: /boot/vmlinuz-3.13.0-34-generic
	Found initrd image: /boot/initrd.img-3.13.0-34-generic
	Found linux image: /boot/vmlinuz-3.13.0-32-generic
	Found initrd image: /boot/initrd.img-3.13.0-32-generic
	Found memtest86+ image: /memtest86+.elf
	Found memtest86+ image: /memtest86+.bin
	done

Redhat 配布ファミリでは、次のコマンドのみが必要です。

	echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local

##システム ファイルの操作設定を構成する
1 つのベスト プラクティスは、ファイル システム上の atime ログ機能を無効にすることです。Atime はファイルの最後のアクセス時刻です。ファイルがアクセスされるたびに、ファイル システムは、ログにタイムスタンプを記録します。ただし、この情報はほとんど使用しません。必要としない場合は、無効にして全体的なディスク アクセス時間を短縮することができます。
 
Atime のログ記録を無効にするには、ファイル システムの構成ファイルの /etc/fstab を変更し、**noatime** オプションを追加する必要があります。

たとえば、次のように vim/etc/fstab ファイルを編集して、noatime を追加します。

	# CLOUD_IMG: This file was created/modified by the Cloud Image build process
	UUID=3cc98c06-d649-432d-81df-6dcd2a584d41       /        ext4   defaults,discard        0 0
	#Add the “noatime” option below to disable atime logging
	UUID="431b1e78-8226-43ec-9460-514a9adf060e"     /RAID0   xfs   defaults,nobootwait, noatime 0 0
	/dev/sdb1       /mnt    auto    defaults,nobootwait,comment=cloudconfig 0       2

次に、以下のコマンドで、ファイル システムを再マウントします。

	mount -o remount /RAID0

変更された結果をテストします。テスト ファイルを変更すると、アクセス時間が更新されないことに注意してください。

実行前の例:

![][5]
 
実行後の例:

![][6]

##高い同時実行性を実現するためにシステム処理の最大数を増やす
MySQL は同時実行性の高いデータベースです。同時実行処理の既定数は、Linuxでは 1024 ですが、必ずしも十分ではありません。**次の手順で、システムの同時実行処理の最大数を増やして、MySQL の高い同時実行性をサポートします**。

###手順 1. limits.conf ファイルを変更する
/etc/security/limits.conf ファイルで、次の 4 つの行を追加して、許可される同時実行処理の最大数を増やします。システムがサポートできる最大数は 65536 であることに注意してください。

	* soft nofile 65536
	* hard nofile 65536
	* soft nproc 65536
	* hard nproc 65536

###手順 2. 新しい制限値にシステムを更新する
次のコマンドを実行します。

	ulimit -SHn 65536
	ulimit -SHu 65536 

###手順 3. 制限値が起動時に更新されることを確認する
起動するたびに有効になるように /etc/rc.local ファイルに次の起動コマンドを入力します。

	echo “ulimit -SHn 65536” >>/etc/rc.local
	echo “ulimit -SHu 65536” >>/etc/rc.local

##MySQL データベースの最適化 
オンプレミスのマシンに設定するのと同様に、パフォーマンス チューニング戦略を使用して Azure に MySQL を設定できます。

メインの I/O の最適化のルールは、次のとおりです。

-	キャッシュ サイズを増加する。
-	I/O 応答時間を短縮する。  

MySQL サーバーの設定を最適化するために、サーバーとクライアント コンピューターの両方の既定の構成ファイルである my.cnf file を更新することができます。

次の構成項目は、MySQL のパフォーマンスに影響する主な要素です。

-	**innodb_buffer_pool_size**: バッファー プールには、バッファー内のデータとインデックスが含まれています。通常、これは物理メモリの 70% に設定します。
-	**innodb_log_file_size**: これは、再実行ログのサイズです。再実行ログを使用して、書き込み操作が高速かつ信頼性が高く、クラッシュ後に回復可能なことを確認します。これは、書き込み操作を記録するのに十分な領域である 512 MB に設定されています。
-	**max_connections**: アプリケーションは適切に接続を終了しない場合があります。大きな値では、サーバーがアイドル状態の接続をリサイクルするのに時間がかかります。最大接続数は 10000 ですが、推奨される最大値は 5000 です。
-	**innodb_file_per_table**: この設定は、個別のファイルにテーブルを保存する InnoDB の機能を有効または無効にします。このオプションを有効にすると、いくつかの高度な管理操作を効率的に適用できます。パフォーマンスの観点では、テーブルの領域の転送を高速化し、ごみ管理のパフォーマンスを最適化できます。そのため、推奨設定では ON です。</br> MySQL 5.6 以降では、既定の設定は ON です。そのため、操作は必要ありません。5.6 以前のその他のバージョンでは、既定の設定は OFF です。この設定を ON にする必要があります。また、新しく作成されたテーブルだけが影響を受けるため、データを読み込む前に適用する必要があります。
-	**innodb_flush_log_at_trx_commit**: 既定値は 1 に、範囲は 0 ～ 2 に設定されています。既定値は、スタンドアロン MySQL DB の最も適切なオプションです。2 の設定は、ほとんどのデータの整合性を実現し、MySQL のクラスターのマスターに適しています。0 の設定は、信頼性に影響を与えるデータ損失が発生する可能性がありますが、優れたパフォーマンスを実現する場合もあり、MySQL のクラスター内のスレーブに適してします。
-	**innodb_log_buffer_size**: ログ バッファーによって、トランザクションがコミットする前にログをディスクにフラッシュすることなくトランザクションを実行できます。ただし、ラージ バイナリ オブジェクトまたはテキスト フィールドがある場合、キャッシュが非常に簡単に使用され、頻繁にディスク I/O がトリガーされます。Innodb_log_waits 状態変数が 0 でない場合は、バッファー サイズを大きくすることが推奨されます。
-	**query_cache_size**: 最善のオプションは、最初から無効にすることです。query_cache_size は、0 (これは MySQL 5.6 では既定の設定です) に設定し、その他のメソッドを使用してクエリを高速化します。  
  
最適化後のパフォーマンスを比較するには、[付録 D](#AppendixD) を参照してください。


##パフォーマンスのボトルネックを分析するためには、MySQL 低速クエリ ログを有効にします。
MySQL 低速クエリ ログによって、MySQL の低速のクエリを特定できます。MySQL 低速クエリ ログを有効にすると、**mysqldumpslow** のような MySQL ツールを使用して、パフォーマンスのボトルネックを特定できます。

既定ではこれが有効ではないことに注意してください。低速クエリ ログを有効化すると CPU リソースを消費する可能性があります。そのため、この機能はパフォーマンスのボトルネックのトラブルシューティングのために、一時的に有効にすることを推奨します。

###手順 1. 次の行を最後に追加して my.cnf ファイルを変更する   

	long_query_time = 2
	slow_query_log = 1
	slow_query_log_file = /RAID0/mysql/mysql-slow.log

###手順 2. MySQL サーバーを再起動する
	service  mysql  restart

###手順 3. "show" コマンドを使用して設定が有効になっているかどうかを確認する
 
![][7]
   
![][8]
 
この例では、低速クエリ ログ機能が有効になっています。**mysqldumpslow** ツールを使用して、パフォーマンスのボトルネックを特定し、インデックスを追加するなど、パフォーマンスを最適化することができます。





##付録

以下は、対象となるラボ環境で生成されたサンプル パフォーマンス テストのデータで、さまざまなパフォーマンス チューニング アプローチによる一般的な情報を提供していますが、結果は環境または製品バージョンによって異なる可能性があります。

<a name="AppendixA"></a>付録 A: **さまざまな RAID レベルでのディスク パフォーマンス (IOPS)**


![][9]
 
**テスト コマンド:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=5G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite

>AZURE.NOTE: このテストの作業負荷は、RAID の上限に到達するために、64 のスレッドを使用します。

<a name="AppendixB"></a>付録 B: **さまざまな RAID レベルでの MySQL のパフォーマンス (スループット) の比較** (XFS ファイル システム)

 
![][10] ![][11]

**テスト コマンド:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb

**さまざまな RAID レベルでの MySQL のパフォーマンス (OLTP) の比較** ![][12]

**テスト コマンド:**

	time sysbench --test=oltp --db-driver=mysql --mysql-user=root --mysql-password=0ps.123  --mysql-table-engine=innodb --mysql-host=127.0.0.1 --mysql-port=3306 --mysql-socket=/var/run/mysqld/mysqld.sock --mysql-db=test --oltp-table-size=1000000 prepare

<a name="AppendixC"></a>付録 C: **さまざまなチャンク サイズのディスク パフォーマンス (IOPS) の比較** (XFS ファイル システム)

 
![][13]

**テスト コマンド:**

	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=30G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite
	fio -filename=/path/test -iodepth=64 -ioengine=libaio -direct=1 -rw=randwrite -bs=4k -size=1G -numjobs=64 -runtime=30 -group_reporting -name=test-randwrite  

このテストに使用するファイルのサイズは RAID 0 (4 ディスク) XFS ファイル システムで、それぞれ 30 GB、1 GB であることに注意してください。


<a name="AppendixD"></a>付録 D: **最適化前後の MySQL のパフォーマンス (スループット) の比較** (XFS ファイル システム)

  
![][14]

**テスト コマンド:**

	mysqlslap -p0ps.123 --concurrency=2 --iterations=1 --number-int-cols=10 --number-char-cols=10 -a --auto-generate-sql-guid-primary --number-of-queries=10000 --auto-generate-sql-load-type=write –engine=innodb,misam

**既定と最適化の構成設定は、次のとおりです。**

|パラメーター |既定値 |最適化
|-----------|-----------|-----------
|**innodb_buffer_pool_size** |なし |7 G
|**innodb_log_file_size** |5 M |512 M
|**max_connections** |100 |5000
|**innodb_file_per_table** |0 |1
|**innodb_flush_log_at_trx_commit** |1 |2
|**innodb_log_buffer_size** |8 M |128 M
|**query_cache_size** |16 M |0


詳細な最適化構成パラメーターは、mysql の正式な説明を参照してください。

[http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html](http://dev.mysql.com/doc/refman/5.6/en/innodb-configuration.html)

[http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method](http://dev.mysql.com/doc/refman/5.6/en/innodb-parameters.html#sysvar_innodb_flush_method)

**テスト環境**

|ハードウェア |詳細
|-----------|-------
|CPU |AMD Opteron(tm) プロセッサ 4171 HE/4 コア
|メモリ |14 G
|disk |10 G/ディスク
|OS |Ubuntu 14.04.1 LTS



[1]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-01.png
[2]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-02.png
[3]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-03.png
[4]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-04.png
[5]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-05.png
[6]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-06.png
[7]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-07.png
[8]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-08.png
[9]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-09.png
[10]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-10.png
[11]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-11.png
[12]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-12.png
[13]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-13.png
[14]: ./media/virtual-machines-linux-optimize-mysql-perf/virtual-machines-linux-optimize-mysql-perf-14.png
 

<!---HONumber=July15_HO4-->