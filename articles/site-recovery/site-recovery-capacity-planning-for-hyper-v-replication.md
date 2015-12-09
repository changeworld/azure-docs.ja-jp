<properties
	pageTitle="Hyper-V 仮想マシンのレプリケーションの容量計画"
	description="この記事には、Azure Site Recovery の Hyper-V Capacity Planner ツールの使用方法を示す手順が含まれています。"
	services="site-recovery"
	documentationCenter="na"
	authors="rayne-wiselman"
	manager="jwhit"
	editor="" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew" />

# Hyper-V 仮想マシンのレプリケーションの容量計画

Azure Site Recovery では、Hyper-V レプリカを使用して、Hyper-V 仮想マシンをオンプレミスのサイトから Azure またはセカンダリ データ センターにレプリケートします。Site Recovery の Capacity Planner ツールを使用すると、Hyper-V 仮想マシンのレプリケーションにおけるレプリケーション要件および帯域幅要件を容易に算出することができます。

## 開始する前に

プライマリ サイトの Hyper-V サーバーまたはクラスター ノードでツールを実行します。ツールを実行するには、Hyper-V ホスト サーバーが次の要件を満たしている必要があります。

- オペレーティング システム: Windows Server ® 2012 または Windows Server ® 2012 R2
- メモリ: 20 MB (最小)
- CPU: 5% オーバーヘッド (最小)
- ディスク領域: 5 MB (最小)

このツールを実行する前に、プライマリ サイトの準備を行う必要があります。2 つのオンプレミスのサイト間でレプリケートする場合に帯域幅を確認するには、レプリカ サーバーの準備も必要です。


## 手順 1: プライマリ サイトを準備します。
1. プライマリ サイトでは、レプリケートする Hyper-V 仮想マシンと、それらが配置されている Hyper-V ホスト/クラスターとをすべて掲載した一覧を作成します。ツールは毎回、複数のスタンドアロン サーバー ホストに対して、または単一のクラスターに対して実行できますが、両方に対して同時に実行することはできません。また、ツールはオペレーティング システムごとに別々に実行する必要があります。したがって、Hyper-V サーバーを次のようにまとめてメモしておく必要があります。 

  - Windows Server ® 2012 スタンドアロン サーバー
  - Windows Server ® 2012 クラスター
  - Windows Server ® 2012 R2 スタンドアロン サーバー
  - Windows Server ® 2012 R2 クラスター

3. すべての Hyper-V ホストおよびクラスターで WMI へのリモート アクセスを有効にします。次のコマンドを各サーバー/クラスターで実行して、ファイアウォール規則とユーザー アクセス許可が設定されていることを確認します。

        netsh firewall set service RemoteAdmin enable

5. 次のように、サーバーおよびクラスター上でパフォーマンスの監視を有効にします。

  - **高度なセキュリティ** スナップインで Windows ファイアウォールを開き、次の受信ルールを有効にします: **COM + ネットワーク アクセス (DCOM-IN)** と **リモート イベント ログの管理グループ**のすべての規則。

## 手順 2: レプリカ サーバーを準備します (オンプレミス間のレプリケーション)

Azure にレプリケートする場合、この手順は必要ありません。

1 つの Hyper-V ホストを復旧サーバーとしてセットアップすることをお勧めします。そうすれば、そのサーバーにダミーの VM をレプリケートすることで帯域幅を調べることができます。この作業は省略しても構いませんが、この作業を行わない限り帯域幅を測定することはできません。

1. クラスター ノードをレプリカとして使用する場合は、Hyper-V レプリカ ブローカーを構成します。

	- **[サーバー マネージャー]** で、**[フェールオーバー クラスター マネージャー]** を開きます。
	- クラスターに接続し、クラスター名を強調表示し、**[アクション]**、**[ロールの構成]** の順にクリックし、[高可用性ウィザード] を開きます。
	- **[ロールの選択]** で、**[Hyper-V レプリカ ブローカー]** をクリックします。このウィザードでは、**[NetBIOS 名]** とクラスターの接続ポイントとして使用する **[IP アドレス]** (クライアント アクセス ポイントと呼ばれます) を指定します。**Hyper-V レプリカ ブローカー**が構成され、クライアント アクセス ポイント名が生成されます。この名前はメモする必要があります。 
	- Hyper-V レプリカ ブローカーのロールがオンラインになることとクラスターの全ノード間でフェールオーバーできることを検証します。これを行うには、ロールを右クリックし、**[移動]** をポイントし、**[ノードの選択]** をクリックします。ノードを選択し、**[OK]** を選択します。 
	- 証明書ベースの認証を使用する場合は、各クラスター ノードとクライアント アクセス ポイントのすべてに証明書がインストールされていることを確認します。
2.  レプリカ サーバーを有効にします。

	- クラスターの場合は、障害クラスター マネージャーを開き、クラスターに接続し、**[ロール]** をクリックしてロールを選択し、**[レプリケーション設定]**、**[レプリカ サーバーとしてこのクラスターを有効にする]** の順にクリックします。クラスターをレプリカとして使用する場合は、プライマリ サイト内のクラスターにも Hyper-V レプリカ ブローカーのロールが存在する必要があるので注意してください。
	- スタンドアロン サーバーの場合は、Hyper-V マネージャーを開きます。**[アクション]** ウィンドウで、有効にするサーバーの **[Hyper-V の設定]** をクリックし、**[レプリケーションの構成]** で **[レプリカ サーバーとしてこのコンピューターを有効にする]** をクリックします。
3. 認証をセットアップします。

	- **[認証とポート]** で、プライマリ サーバーを認証する方法と、認証ポートを選択します。証明書を使用する場合は、**[証明書の選択]** をクリックしていずれかを選択します。プライマリと復旧の両方の Hyper-V ホストが同じドメインにあるか、信頼できるドメインにある場合は、Kerberos を使用します。ドメインまたはワークグループのデプロイメントが異なる場合は証明書を使用します。
	- **[認証とストレージ]** セクションで、**任意**の認証済み (プライマリ) サーバーにこのレプリカ サーバーに複製データを送信することを許可します。**[OK]** または **[適用]** をクリックします。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

	- **netsh http show servicestate** を実行して、指定したプロトコル/ポートに対してリスナーが実行されていることを確認します。  
4. ファイアウォールをセットアップします。Hyper-V のインストール時には、既定のポートでトラフィックを許可するように (443 では HTTPS、80 では Kerberos)、ファイアウォール規則が作成されます。次のように、これらのルールを有効にします。
	
		- Certificate authentication on cluster (443): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}**
		- Kerberos authentication on cluster (80): **Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}**
		- Certificate authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"**
		- Kerberos authentication on standalone server: **Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"**

## 手順 3: 容量計画ツールを実行します。

プライマリ サイトを準備し、回復サーバーをセットアップすると、ツールを実行できます。

1. Microsoft Download Center からツールを[ダウンロード](https://www.microsoft.com/download/details.aspx?id=39057)します。
2. いずれかのプライマリ サーバー (またはプライマリ クラスターのノード) からツールを実行します。.exe ファイルを右クリックし、**[管理者として実行]** を選択します。
3. **[開始する前に]** で、データを収集する期間を指定します。代表的なデータが対象となるように、業務時間中にツールを実行することをお勧めします。ネットワーク接続を検証するだけである場合は、数分の収集にとどめることもできます。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

4. **[プライマリ サイトの詳細]** で、スタンドアロン ホストの場合はサーバー名または FQDN を指定し、クラスターの場合はクライアント アクセス ポイントの FQDN、クラスター名、またはクラスター内の任意のノードを指定してから、**[次へ]** をクリックします。ツールは、それが実行されているサーバーの名前を自動的に検出します。このツールは、指定されたサーバーについて監視できる仮想マシンを取得します。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)

5. Azure にレプリケートする場合またはセカンダリ データ センターにレプリケートする場合で、レプリカ サーバーをセットアップしていないときは、**[レプリカ サイトの詳細]** の **[レプリカ サイトに関連するテストをスキップする]** を選択します。セカンダリ データ センターにレプリケートする場合で、レプリカ サーバーをセットアップ済みであるときは、**[サーバー名 (または) Hyper-V レプリカ ブローカー CAP]** にスタンドアロン サーバーの FQDN またはクラスターのクライアント アクセス ポイントの FQDN を入力します。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

6. **[拡張レプリカの詳細]** の **[拡張レプリカ サイトに関連するテストをスキップする]** を有効にします。これらは、Site Recovery によってサポートされていません。
7. **[レプリケートする VM を選択]** では、ツールは **[プライマリ サイトの詳細]** ページで指定された設定に従って、サーバーまたはクラスターに接続し、プライマリ サーバー上で動作している VM およびディスクを表示します。レプリケーションが既に有効になっている VM または動作していない VM は表示されないので注意してください。メトリックを収集する VM を選択します。VHD を選択すると、VM のデータも自動的に収集されます。
9. レプリカ サーバーまたはクラスターが構成済みである場合、**[ネットワーク情報]** では、プライマリ サイトとレプリカ サイト間で使用されると考えられるおおよその WAN 帯域幅を指定し、さらに証明書認証が構成済みである場合には証明書を選択します。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. **[サマリー]** では、設定内容を確認し、**[次へ]** をクリックしてメトリックスの収集を開始します。**[容量の計算]** ページにはツールの進行状況と状態が表示されます。ツールの実行が完了したら、**[レポートの表示]** をクリックし、出力を確認します。既定では、レポートとログは **%systemdrive%\\Users\\Public\\Documents\\Capacity Planner** に格納されます。

	![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)


## 手順 4: 結果を解釈します。
重要なメトリックスは次のとおりです。ここに一覧されていないメトリックスは無視して構いません。Site Recovery とは関連がないからです。

### オンプレミス間のレプリケーション
  - プライマリ ホストのコンピューティングとメモリに対するレプリケーションの影響
  - プライマリ、復旧ホストのストレージ ディスク領域、IOPS に対するレプリケーションの影響
  - デルタ レプリケーション (Mbps) に必要な帯域幅の合計
  - プライマリ ホストと復旧ホストの間で観察されたネットワーク帯域幅 (Mbps)
  - 2 つのホスト/クラスター間の有効な並列転送の理想的な数に関する提案

### オンプレミスから Azure へのレプリケーション
  - プライマリ ホストのコンピューティングとメモリに対するレプリケーションの影響
  - プライマリ ホストのストレージ ディスク領域と IOPS に対するレプリケーションの影響
  - デルタ レプリケーション (Mbps) に必要な帯域幅の合計

## その他のリソース

- ツールの詳細については、ダウンロード時にツールに付いてきたドキュメントを参照してください。
- ツールのチュートリアルについては、Keith Mayer の [TechNet ブログ](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)を参照してください。
- オンプレミス間の Hyper-V レプリケーションに関する弊社のパフォーマンス テストの結果については、[こちら](http://blogs.technet.com/b/keithmayer/archive/2014/02/27/guided-hands-on-lab-capacity-planner-for-windows-server-2012-hyper-v-replica.aspx)を参照してください。



## 次のステップ

- [オンプレミスの VMM サイトと Azure 間の保護の設定](site-recovery-vmm-to-azure.md)
- [Set up protection between an on-premises Hyper-V site and Azure (オンプレミスの Hyper-V サイトと Azure 間の保護の設定)](site-recovery-hyper-v-site-to-azure.md)
- [Set up protection between two on-premises VMM sites (2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-to-vmm.md)

<!---HONumber=AcomDC_1203_2015-->