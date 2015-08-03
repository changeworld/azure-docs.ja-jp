<properties pageTitle="Oracle 仮想マシンのイメージに関するその他の考慮事項" description="Microsoft Azure へ Oracle 仮想マシンを導入する前に、さらに多くの考慮事項について説明します。" services="virtual-machines" authors="bbenz" documentationCenter=""/>
<tags ms.service="virtual-machines" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="infrastructure-services" ms.date="06/22/2015" ms.author="bbenz" />
#Oracle 仮想マシンのイメージに関するその他の考慮事項
この項では、オペレーティング システムとして Windows Server を利用し、Microsoft 提供の Oracle ソフトウェア イメージに基づく Oracle 仮想マシンを Azure で実行することに関する考慮事項について説明します。

-  Oracle データベース仮想マシン イメージ
-  Oracle WebLogic Server の仮想マシン イメージ
-  Oracle JDK 仮想マシンのイメージ

##Oracle データベース仮想マシン イメージ
### クラスタリング (RAC) はサポートされていません

現在 Azure では、Oracle データベースのクラスタ リングをサポートしていません。スタンドアローンの Oracle データベースのインスタンスのみ可能です。これは、Azure では現在のところ、複数の VM インスタンス間での仮想ディスク共有 (読み取り/書き込み) をサポートしていないためです。UDP マルチキャストもサポートされていません。

### 静的内部 IP はありません

Azure は各仮想マシンに内部 IP アドレスを割り当てます。VM が仮想ネットワークの一部ではない限り、VM の IP アドレスは動的であり VM の再起動後に変更されることがあります。Oracle データベースは静的な IP アドレスを要求するため、問題が生じることがあります。こういった問題を避けるために、VM を Azure 仮想ネットワークへ追加することを検討します。詳細については[仮想ネットワーク](http://azure.microsoft.com/documentation/services/virtual-network/)および[Azure で仮想ネットワークを作成する](create-virtual-network.md)を参照してください。

### 接続ディスクの設定オプション

VM のオペレーティングシステム (OS) のディスク上、またはデータ ディスクとも呼ばれる接続ディスクのいずれかにデータファイルを配置することができます。接続ディスクは OS ディスクよりもパフォーマンスに優れ、サイズを柔軟に構築できます。OS ディスクは 10 GB 未満のデータベースでのみ推奨しています。

接続ディスクは Azure Blob Storage サービスと依存関係にあります。各ディスクは理論上、毎秒最大約 500 回 の入出力の操作 (IOPS) が可能です。最初は接続ディスクのパフォーマンスが最適でない場合があります。IOPS パフォーマンスは約 60 ～ 90 分の連続操作 「バーンイン」期間の後に大幅に改善されます。その後、ディスクのアイドル状態が続く場合、もう一度連続操作 (バーンイン期間) するまで IOPS パフォーマンスは低下します。つまり、ディスクがアクティブであるほど、最適な IOPS パフォーマンスに近づく可能性が高くなります。

最も簡単な方法は 1 つのディスクを VM に接続し、そのディスクにデータベース ファイルを置くことですが、その方法はパフォーマンスの観点では最も制約があります。代わりに、多くの場合、複数の接続ディスクを使い、それらの間でデータベースのデータを分散させ、Oracle Automatic Storage Management (ASM) を利用すると、効果的に IOPS パフォーマンスを改善できます詳細については[Oracle Automatic Storage の概要](http://www.oracle.com/technetwork/database/index-100339.html)を参照してください。複数のディスクの OS レベルでのストライピングを使用することができますが、IOPS パフォーマンスを向上させるか不明なため推奨はされいません。

読み込み操作、またはデータベースへの書き込み操作のいずれを重視するかに基づいて、複数のディスクを接続する場合に向けて 2 つの手法を検討してください。

- **Oracle ASM のみを使用する場合**は、書き込み操作のパフォーマンスは上がりますが、Windows Server 2012 の記憶域プールを利用する手法と比較して読み取り操作の IOPS が下がります。次の図は理論的にこの組み合わせを示したものです。![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image2.png)

- **Oracle ASM と Windows Server 2012 の記憶域プールを比較する**と、データベースが主に読み取り操作を実行する場合に、または読み取り操作のパフォーマンスを書き込み操作のパフォーマンスより重視する場合に、読み取り操作 IOPS パフォーマンスが上がります。Windows Server 2012 オペレーティング システムに基づくイメージが必要です。記憶域プールに関する詳細は[スタンドアローンサーバーへの記憶域スペースの展開](http://technet.microsoft.com/library/jj822938.aspx)を参照して下さい。この配置の場合、接続ディスクの 2 つの等しいサブセットが最初に 2 つの記憶域プール ボリュームの物理ディスクとしてまとめて「ストライプ化」され、そのボリュームが ASM ディスク グループに追加されます。次の図は理論的にこの配置を示したものです。

	![](media/virtual-machines-miscellaneous-considerations-oracle-virtual-machine-images/image3.png)

>[AZURE.IMPORTANT]書き込み性能と読み取り性能のトレードオフは個別で評価します。これらの方法を使用する場合、実際の結果に相違がある可能性があります。

### 高可用性と障害復旧に関する考慮

Azure 仮想マシンで Oracle データベースを使用する場合、いかなるダウンタイムも回避するために高可用性と障害復旧ソリューションを実装する責任があります。また、ご自身のデータやアプリケーションをバックアップする責任も負うことになります。

Azure の Oracle Database Enterprise Edition (RAC なし) には [Data Guard, Active Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html) または [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate) と 2 つの異なる仮想マシン (VM) の 2 つのデータベースを使用することで高可用性と障害復旧を実現できます。両方の仮想マシンを同じ[クラウド サービス](cloud-services-connect-virtual-machine.md)と同じ[仮想ネットワーク](http://azure.microsoft.com/documentation/services/virtual-network/) に置き、プライベートの固定 IP アドレスで互いにアクセスできるようにする必要があります。さらに、Azure が仮想マシンを個別のフォールト ドメインやアップグレード ドメインに配置できるように、仮想マシンを同じ[可用性セット](manage-availability-virtual-machines.md)へ配置することを推奨します。同じクラウドサービス上にある VM のみが同じ可用性セットに属することができることにご注意ください。各 VM は 2GB 以上のメモリ、5GB 以上のディスク領域が必要です。

Oracle Data Guard では、1 つの VM にプライマリ データベース、別の VM にセカンダリ データベース (待機)、そしてその間に一方向のレプリケーションセットを配置することで高可用性を実現できます。データベースのコピーへのアクセスを結果として読み込みます。Oracle GoldenGate では、2 つのデータベース間に双方向レプリケーションを構成することができます。これらのツールを使用してデータベース用に高可用性ソリューションを設定する方法は、Oracle のウェブサイトにある [Active Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) および [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文書を参照して下さい。データベースのコピーに読み込み/書き込みアクセスをする必要がある場合は、[Oracle Active Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html) もご利用いただけます。

##Oracle WebLogic Server の仮想マシン イメージ

-  **クラスタリングは Enterprise エディションでのみサポートされています。** WebLogic Server (具体的には、Windows Server をオペレーティング システムとするイメージ) の Microsoft ライセンス イメージを使用している場合は、WebLogic Server の Enterprise エディションを使用している場合にのみ、WebLogic クラスタリングを使用するライセンスが付与されます。WebLogic Server Standard Edition の場合、クラスタリングを使用しないでください。

-  **接続タイムアウト:** アプリケーションが別の Azure のクラウド サービス (データベース層のサービスなど) のパブリックエンドポイントへの接続に依存している場合、4 分間の無活動の後 Azure はこれらの開いている接続を閉じることがあります。無活動時間がこの上限を超えると、接続が無効になるため、接続プールに依存している機能やアプリケーションに影響を与えることがあります。アプリケーションに影響がある場合は、接続プール上の「キープアライブ」 ロジックの有効化を検討してください。

	エンドポイントが Azure クラウド サービス デプロイの *内部*になる場合 (WebLogic 仮想マシンと*同じ*クラウド サービス内にあるスタンドアロンのデータベース仮想マシンなど)、接続は直接的であり、Azure ロード バランサーに依存しません。そのため、接続タイムアウトの対象とはなりません。

-  **UDP マルチキャストはサポートされていません。** Azure は UDP ユニキャストをサポートしていますが、マルチキャストおよびブロードキャストはサポートしていません。WebLogic Server は Azure の UDP ユニキャスト機能に依存できます。UDP ユニキャストへの依存で最適な結果を得るには、WebLogic クラスターのサイズを静的に保ち、クラスターに 10 以上の管理サーバーを置かないことを推奨します。

-  **WebLogic Server は T3 アクセスに対し、パブリック ポートとプライベート ポートを同じにすることを要求します (Enterprise JavaBeans を使用する場合など)。** **SLWLS** というクラウド サービス上で、2 つ以上の管理サーバーで構成される WebLogic Server クラスターでサービス層 (EIB) アプリケーションが実行している多層シナリオを検討してみてください。クライアント層は別のクラウド サービスにあり、サービス層にある EJB を呼び出すためにシンプルな Java プログラムを実行します。サービス層の負荷を分散する必要があるため、負荷分散されたパブリックなエンドポイントを WebLogic Server クラスター内の仮想マシンに作成する必要があります。そのエンドポイント用に指定したプライベートポートがパブリックポート (7006:7008 など) と異なる場合、次のようなエラーが発生します。

		[java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

		Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

	どのようなリモート T3 アクセスにおいても、WebLogic Server に対してロード バランサー ポートおよび WebLogic 管理サーバーのポートが同じでなければいけないためです。上記の場合、クライアントはポート 7006 (ロード バランサー ポート) へ接続し、管理サーバーは 7008 (プライベート ポート) で待機します。この制限は、 HTTP ではなく T3 アクセスにのみ適用されることに注意してください。

	この問題を避けるには、次の回避策のいずれかを実施してください。

	-  T3 アクセス専用の負荷分散エンドポイントに対して、同じプライベートおよびパブリック ポート番号を使用します。

	-  WebLogic Server を開始する場合、次の JVM パラメーターを含めてください。

			-Dweblogic.rjvm.enableprotocolswitch=true

関連情報については、<http://support.oracle.com> にあるサポート技術情報 **860340.1** を参照してください。

-  **動的なクラスタリングと負荷分散の制限事項。** WebLogic Server で動的なクラスターを使用し、Azure 上の単一のパブリック負荷分散エンドポイントでそれを公開する事を考えてみてください。これは、それぞれの管理サーバーに固定のポート番号を使用し (範囲から動的に割り当てられていない)、管理者が追跡記録しているマシンより多く管理サーバーを起動しない (つまり、1 つの仮想マシンに対する管理サーバーが複数にならない) 限り、実現できます。設定の結果、起動する WebLogic Server の数が VM より多くなる場合 (つまり、複数の WebLogic Server インスタンスが同じ VM を共有する)、指定したボート番号に複数の WebLogic Server インスタンス サーバーをバインドすることはできません。その VM のその他の WebLogic Server インスタンスはフェールします。 

	一方、管理サーバーに個別のポート番号を自動的に割り当てるように管理サーバーを設定した場合負荷分散はできません。そのような設定では、単一のパブリック ポートから複数のプライベート ポートにマッピングする必要がありますが、Azure ではサポートされていないためです。

-  **単一 VM における複数の WebLogic インスタンス** VM が十分に大きい場合、デプロイ要件によりますが、同じ仮想マシン上で複数の WebLogic Server のインスタンスを実行するオプションを検討できます。たとえば、2 つのコアを持つ中程度のサイズの VM では、WebLogic Server で 2 つのインスタンスを実行できます。ただし、1 つの VM で複数の WebLogic Server インスタンスを実行する場合のように、単一障害点をアーキテクチャに導入することは避けることが推奨されます。2 つ以上の VM を使うことでより優れたアプローチができ、各 VM は複数の WebLogic Server のインスタンスを実行できるようになります。それでもなお、これらの WebLogic Server のインスタンスは同じクラスターの一部ととして属することができます。ただし、現在は、Azure のロード バランサーを利用する場合、負荷を分散するサーバーを個別の VM 間で配布する必要があるため、同じ VM 内の WebLogic Server のデプロイにより公開されるエンドポイントの負荷を Azure で分散することはできません。

##Oracle JDK 仮想マシンのイメージ

-  **JDK 6 および 7 の最新アップデート。** パブリックをサポートする Java の最新バージョン (現在は Java 8) の使用を推奨していますが、Azure は JDK 6 および JDK 7 のイメージもご利用いただけます。これは JDK 8 へのアップグレードの準備が整っていない従来のアプリケーションのための対処としてです。一般向けには以前の JDK の一般公開は終了していますが、Microsoft と Oracle の業務提携により、Azure 提供の JDK 6 および 7 のイメージには、通常、Oracle のサポート対象顧客の選ばれたグループにのみ Oracle が提供する最近の非公開更新が含まれます。新しいバージョンの JDK イメージは JDK 6 および 7 の更新版がリリースされた後に利用可能になります。

	この JDK 6 および JDK 7 のイメージで利用可能な JDK 、そしてそこから派生した仮想マシンおよびイメージは Azure 内でのみ使用可能だということに注意してください。

-  **64-bit JDK.** Azure により提供される Oracle WebLogic Server 仮想マシンおよび Oracle JDK 仮想マシンのイメージは、Windows Server と JDK 両方の 64 ビット版を含んでいます。

##その他のリソース
[Azure 用の Oracle 仮想マシンのイメージ](virtual-machines-oracle-list-oracle-virtual-machine-images.md)

<!---HONumber=July15_HO4-->