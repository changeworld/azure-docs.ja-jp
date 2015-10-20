<properties
	pageTitle="Hyper-V レプリケーションの容量計画"
	description="この記事には Azure Site Recovery の容量計画ツールを使用する方法と Hyper-V レプリケーションの容量計画のためのリソースが記載されています。"
	services="site-recovery"
	documentationCenter="na"
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn" />
<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/05/2015"
	ms.author="lauraa" />

# Hyper-V レプリケーションの容量計画

Azure Site Recovery は Hyper-V レプリカを利用し、オンプレミスの VMM サイト間またはオンプレミス VMM サーバーと Azure ストレージの間で複製を行います。この記事では、Capacity planner for Azure Site Recovery (ASR) - Hyper-V Replica ツールの利用方法について段階的に説明します。Capacity Planner for ASR - Hyper-V Replica ツールを利用すれば、IT 管理者は Hyper-V レプリカを展開し、2 つのサイト間のネットワーク接続を検証するために必要なサーバー、ストレージ、ネットワークのインフラストラクチャを設計できます。

## システム要件
- オペレーティング システム: Windows Server ® 2012 または Windows Server ® 2012 R2
- メモリ: 20 MB (最小)
- CPU: 5% オーバーヘッド (最小)
- ディスク領域: 5 MB (最小)

## チュートリアルの手順
- 手順 1: プライマリ サイトを準備します。
- 手順 2: 復旧サイトがオンプレミスの場合、復旧サイトを準備します。
- 手順 3: 容量計画ツールを実行します。
- 手順 4: 結果を解釈します。

## 手順 1: プライマリ サイトを準備します。
1. 複製を可能にするために必要な Hyper-V 仮想マシンとそれに対応するプライマリ Hyper-V ホスト/クラスターの全リストを作成します。
2. プライマリ Hyper-V のホストとクラスターを次のいずれかのグループに分けます。

  - Windows Server ® 2012 スタンドアロン サーバー
  - Windows Server ® 2012 クラスター
  - Windows Server ® 2012 R2 スタンドアロン サーバー
  - Windows Server ® 2012 R2 クラスター

3. スタンドアロン サーバー グループごとに 1 回とクラスターごとに 1 回、容量計画ツールを実行する必要があります。
4. すべてのプライマリ ホスト/クラスターで WMI へのリモート アクセスを有効にします。一連のファイアウォール ルールとユーザー アクセス許可が正しく設定されていることを確認します。

        netsh firewall set service RemoteAdmin enable

5. プライマリ ホストでパフォーマンス監視を有効にします。

  - **高度なセキュリティ** スナップインで **Windows ファイアウォール**を開き、次の受信ルールを有効にします。
    - COM+ ネットワーク アクセス (DCOM-IN)
    - リモート イベント ログ管理グループのすべてのルール

## 手順 2: 復旧サイトを準備します。
Azure を復旧サイトとして利用している場合、あるいはオンプレミスの復旧サイトがまだ用意できていない場合、このセクションは省略できます。ただし、省略した場合、2 つのサイト間で利用できる帯域幅を測定できなくなります。

1. 認証方法の特定

	a.Kerberos: プライマリと復旧の両方の Hyper-V ホストが同じドメインにあるか、互いに信頼できるドメインにあるときに使用します。

	b.証明書: プライマリと復旧の Hyper-V ホストが異なるドメインにあるときに使用します。証明書は makecert を利用して作成できます。この手法で証明書を展開するための手順については、ブログ投稿の「[Hyper-V Replica Certificate Based Authentication - makecert](http://blogs.technet.com/b/virtualization/archive/2013/04/13/hyper-v-replica-certificate-based-authentication-makecert.aspx)」を参照してください。

2. 復旧サイトの復旧 Hyper-V ホスト/クラスターを **1 つ**特定します。

	a.この復旧ホスト/クラスターはダミー仮想マシンを複製し、プライマリ サイトとセカンダリ サイトの間で利用できる帯域幅を見積もるために利用されます。

	b.**推奨**: 単一復旧 Hyper-V ホストを利用してテストを実行します。

### 復旧サーバーとして Hyper-V ホストを 1 つ準備します。
1. Hyper-V Manager の **[アクション]** ウィンドウで **[Hyper-V 設定]** をクリックします。
2. **[Hyper-V 設定]** ダイアログで、**[レプリケーション構成]** をクリックします。
3. [詳細] ウィンドウで、**[レプリカ サーバーとしてこのコンピューターを有効にする]** を選択します。
4. **[認証とポート]** セクションで、前に選択した認証方法を選択します。いずれの認証方法の場合でも、使用するポートを指定します (HTTP の Kerberos の場合、既定のポートは 80 で、HTTPS の証明書基準認証の場合、既定のポートは 443 です)。
5. 証明書基準認証を利用している場合、**[証明書の選択]** をクリックし、証明書情報を入力します。
6. **[認証とストレージ]** セクションで、**任意**の認証済み (プライマリ) サーバーにこのレプリカ サーバーに複製データを送信することを許可します。
7. **[OK]** または **[適用]** をクリックします。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image1.png)

8. コマンド「netsh http show servicestate」を実行し、https リスナーが実行されていることを検証します。
9. ファイアウォール ポートを開きます。

        Port 443 (certificae-based authentication):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"


        Port 80 (Kerberos):
          Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"

### 復旧ターゲットとして Hyper-V クラスターを 1 つ準備します。
復元サーバーとしてスタンドアロン Hyper-V ホストを既に用意している場合、このセクションを省略できます。

1. Hyper-V レプリカ ブローカーを構成します。

	a.**[サーバー マネージャー]** で、**[フェールオーバー クラスター マネージャー]** を開きます。

	b.左ウィンドウで、クラスターに接続します。クラスター名が強調表示されている状態で **[アクション]** ウィンドウの **[役割の構成]** をクリックします。**[高可用性ウィザード]** が開きます。

	c.**[役割の選択]** 画面で、**[Hyper-V レプリカ ブローカー]** を選択します。

	d.**[NetBIOS 名]** とクラスターの接続ポイントとして使用する **[IP アドレス]** (クライアント アクセス ポイントと呼ばれます) を指定し、ウィザードを完了します。 **Hyper-V レプリカ ブローカー**が構成されます。クライアント アクセス ポイント名が指定されます。クライアント アクセス ポイント名をメモしておいきます。後でレプリカの構成時に使用します。

	e.Hyper-V レプリカ ブローカーのロールがオンラインになることとクラスターの全ノード間でフェールオーバーできることを検証します。これを行うには、ロールを右クリックし、**[移動]** をポイントし、**[ノードの選択]** をクリックします。ノードを選択し、**[OK]** を選択します。

	f.証明書基準の認証を使用する場合、各クラスター ノードと Hyper-V レプリカ ブローカーのクライアント アクセス ポイントに証明書がインストールされていることを確認します。

2. レプリカ設定を構成します。

	a.**[サーバー マネージャー]** で、**[フェールオーバー クラスター マネージャー]** を開きます。
	
	b.左ウィンドウで、クラスターに接続します。クラスター名が強調表示されている状態で **[詳細]** ウィンドウの **[移動]** カテゴリの **[役割]** をクリックします。
	
	c.ロールを右クリックし、**[レプリケーション設定]** を選択します。
	
	d.**[詳細]** ウィンドウで、**[レプリカ サーバーとしてこのクラスターを有効にする]** を選択します。

	e.**[認証とポート]** セクションで、前に選択した認証方法を選択します。いずれの認証方法の場合でも、使用するポートを指定します (HTTP の Kerberos の場合、既定のポートは 80 で、HTTPS の証明書基準認証の場合、既定のポートは 443 です)。

	f.証明書基準認証を利用している場合、**[証明書の選択]** をクリックし、要求された証明書情報を入力します。

	g.**[認証とストレージ]** セクションで、**任意**の認証済み (プライマリ) サーバーにこのレプリカ サーバーに複製データを送信することを許可するかどうか、あるいは特定のプライマリ サーバーからのデータに受け入れを制限するかどうかを指定します。ワイルドカード文字を利用し、特定のドメインからのサーバーに受け入れを制限できます。個々に指定する必要がありません (たとえば、*.contoso.com)。

	h.すべての復旧 Hyper-V ホストでファイアウォール ポートを開きます。Port 443 (Certificate auth): Get-ClusterNode | ForEach-Object {Invoke-command -computername \\$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTPS Listener (TCP-In)"}}


          Port 80 (Kerberos auth):
              Get-ClusterNode | ForEach-Object {Invoke-command -computername \$\_.name -scriptblock {Enable-Netfirewallrule -displayname "Hyper-V Replica HTTP Listener (TCP-In)"}}


## 手順 3: 容量計画ツールを実行します。
1. 容量計画ツールをダウンロードします。
2. いずれかのプライマリ サーバー (またはプライマリ クラスターのノード) からツールを実行します。.exe ファイルを右クリックし、**[管理者として実行]** を選択します。
3. **[利用規約]** を受諾し、**[次へ]** をクリックします。
4. **[メトリック収集の実行時間]** を選択します。最も代表的なデータが収集されるように本稼動時間にツールを実行することが推奨されます。メトリック収集の推奨時間は 30 分です。ネットワーク接続を検証するだけの場合、時間として 1 分を選択できます。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image2.png)

5. 画像のように **[プライマリ サイトの詳細]** を指定し、**[次へ]** をクリックします。

	スタンドアロン ホストの場合、サーバー名または FQDN を入力します。

	プライマリ ホストがクラスターに属する場合、次のいずれかの FQDN を入力できます。

	a.Hyper-V レプリカ ブローカーのクライアント アクセス ポイント (CAP)

	b.クラスター名

	c.クラスターの任意のノード

  ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image3.png)


6. **[レプリカ サイトの詳細]** を入力します (オンプレミス サイト間のレプリケーションのみ)

  Azure へのレプリケーションを有効にするか、Hyper-V ホストまたはクラスターを復旧サイトとして準備しなかった場合 (手順 2 に説明あり)、レプリカ サイトが関連するテストを省略してください。

  **[レプリカ サイト]** 詳細を指定し、**[次へ]** をクリックします。

i.スタンドアロン ホストの場合、サーバー名または FQDN を入力します。

ii.プライマリ ホストがクラスターに属する場合、次のいずれかの FQDN を入力できます。

a.Hyper-V レプリカ ブローカーのクライアント アクセス ポイント (CAP)

b.クラスター名

c.クラスターの任意のノード

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image4.png)

7. **拡張レプリカ サイト**が関連するテストを省略します。そのようなテストには ASR は対応していません。
8. プロファイルする仮想マシンを選択します。このツールは **[プライマリ サイトの詳細]** に指定されたクラスターまたはスタンドアロン サーバーに接続し、実行されている仮想マシンを列挙します。メトリックを収集する仮想マシンと仮想ディスクを選択します。

次の仮想マシンは列挙されず、表示されません。

- レプリケーションが既に有効になっている仮想マシン
- 実行されていない仮想マシン

9. **[ネットワーク情報]** を入力します (これはオンプレミス間のレプリケーションのときとレプリカ サイト詳細が指定されているときにのみ該当します)。

要求されたネットワーク情報を指定し、**[次へ]** をクリックします。

- WAN 帯域幅の見積もり
- 認証に使用される証明書 (任意): 証明書基準の認証を利用する場合、このページに必要な証明書を指定する必要があります。

   ![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image5.png)

10. 次の一連の画面で、**[次へ]** をクリックしてツールを起動します。

![](./media/site-recovery-capacity-planning-for-hyper-v-replication/image6.png)

11. ツールの実行が完了したら、**[レポートの表示]** をクリックし、出力を確認します。

    既定のレポートの場所:

    %systemdrive%\\Users\\Public\\Documents\\Capacity Planner

    ログの場所:

    %systemdrive%\\Users\\Public\\Documents\\CapacityPlanner

## 手順 4: 結果を解釈します。
次の 2 つのシナリオのいずれかでリストアップされないメトリックはこのシナリオが関連しないため、無視できます。

### オンプレミス サイト間のレプリケーション
  - プライマリ ホストのコンピューティングとメモリに対するレプリケーションの影響
  - プライマリ、復旧ホストのストレージ ディスク領域、IOPS に対するレプリケーションの影響
  - デルタ レプリケーション (Mbps) に必要な帯域幅の合計
  - プライマリ ホストと復旧ホストの間で観察されたネットワーク帯域幅 (Mbps)
  - 2 つのホスト/クラスター間の有効な並列転送の理想的な数に関する提案

### オンプレミス サイトと Azure の間のレプリケーション
  - プライマリ ホストのコンピューティングとメモリに対するレプリケーションの影響
  - プライマリ ホストのストレージ ディスク領域と IOPS に対するレプリケーションの影響
  - デルタ レプリケーション (Mbps) に必要な帯域幅の合計

詳細なガイダンスは [Capacity Planner for Hyper-V Replica ダウンロード ページ](http://go.microsoft.com/?linkid=9876170)にあります。

## その他のリソース
次のリソースは Hyper-V レプリケーションの容量計画に役立ちます。

- [更新: Capacity Planner for Hyper-V Replica](http://go.microsoft.com/fwlink/?LinkId=510891) - この新しいツールの概要についてはこのブログをお読みください。

- [Capacity Planner for Hyper-V Replica](http://go.microsoft.com/fwlink/?LinkId=510892) - このツールの最新版をダウンロードします。

- [Guided Hands-On Lab](http://go.microsoft.com/fwlink/?LinkId=510893) - Keith Mayer の TechNet ブログでは、このツールで容量を計画する方法が段階的に説明されています。

- [Performance and Scaling Testing - On-Premises to On-Premises](https://msdn.microsoft.com/library/azure/dn760892.aspx) - オンプレミス間のデプロイのレプリケーション テストの結果についてお読みください。


## 次のステップ

ASR のデプロイを開始する際は、次の記事を参照してください。

- [オンプレミスの VMM サイトと Azure 間の保護の設定](site-recovery-vmm-to-azure.md)
- [Set up protection between an on-premises Hyper-V site and Azure (オンプレミスの Hyper-V サイトと Azure 間の保護の設定)](site-recovery-hyper-v-site-to-azure)
- [Set up protection between two on-premises VMM sites (2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-to-vmm)
- [Set up protection between two on-premises VMM sites with SAN (SAN を使用した 2 つのオンプレミスの VMM サイト間の保護の設定)](site-recovery-vmm-san)
- [単一の VMM サーバーを使用した保護の設定](site-recovery-single-vmm)
 

<!---HONumber=Oct15_HO3-->