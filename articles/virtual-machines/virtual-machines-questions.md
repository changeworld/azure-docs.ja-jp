<properties
	pageTitle="Azure Virtual Machines についてのよく寄せられる質問"
	description="Azure 仮想マシンについて特に多く寄せられる質問への回答を示します。"
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager, azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/19/2015"
	ms.author="kathydav"/>

# Azure Virtual Machines FAQ (Azure 仮想マシンの FAQ)

この記事は、Azure VM のサポート チームや、フォーラム、ニュースグループ、およびその他の記事内のコメントに基づいて、Azure 仮想マシンに関するユーザーからの一般的な質問に回答するものです。基本的な情報については、まず「[仮想マシンについて](virtual-machines-about.md)」を参照してください。

## Azure VM では何を実行できますか。

すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。また、MSDN サブスクリプション会員は Azure によって提供される特定の Windows クライアント イメージにアクセスできします。

サーバー ソフトウェアについては、Windows Server (最近のバージョン) や各種の Linux ディストリビューションを実行し、さまざまなサーバー ワークロードやサービスをホストできます。サポートの詳細については、次の項目を参照してください。

• Windows VM の場合 -- [Microsoft Azure 仮想マシンのマイクロソフト サーバー ソフトウェアのサポート](http://go.microsoft.com/fwlink/p/?LinkId=393550)

• Linux VM の場合 -- [Azure での動作保証済み Linux ディストリビューション](http://go.microsoft.com/fwlink/p/?LinkId=393551)

Windows クライアント イメージについては、 MSDN Azure 特典のサブスクライバーと MSDN 開発テスト用従量課金制プラン (開発およびテスト用) のサブスクライバーを対象に、特定のバージョンの Windows 7 および Windows 8.1 が利用可能となっています。詳細については、「[Windows Client images for MSDN subscribers](http://azure.microsoft.com/blog/2014/05/29/windows-client-images-on-azure/)」を参照してください

## 仮想マシンではどれくらいのストレージ容量を使用できますか。

各データ ディスクで最大 1 TB を利用できます。使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。詳細については、「[Sizes for Virtual Machines (仮想マシンのサイズ)](virtual-machines-size-specs.md)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージを利用できます。各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。料金は、使用可能なディスク領域ではなく、ストレージ アカウントで使用されているストレージ容量に応じて課金されます。料金の詳細については、「[Azure Storage 料金](http://go.microsoft.com/fwlink/p/?LinkId=396819)」を参照してください。

## どのようなタイプの仮想ハード ディスクを使用できますか。

Azure では、VHD 形式の仮想ハード ディスク (固定型) をサポートしています。Azure で VHDX 形式のディスクを使用したい場合は、Hyper-V Manager または [convert-VHD](http://go.microsoft.com/fwlink/p/?LinkId=393656) コマンドレット を使用して形式を変換してください。その後、[Add-AzureVHD](https://msdn.microsoft.com/library/azure/dn495173.aspx) コマンドレットを (サービス管理モードで) 使用して、Azure のストレージ アカウントに VHD をアップロードし、仮想マシンで使用できるようにしてください。コマンドレットでは、動的 VHD を固定 VHD に変換することはできますが、VHDX を VHD に変換することはできません。

- Linux での手順ついては、「[Linux オペレーティング システムを格納した仮想ハード ディスクの作成とアップロード](virtual-machines-linux-create-upload-vhd.md)」を参照してください。

- Windows での手順については、「[Windows Server VHD の作成と Azure へのアップロード](virtual-machines-create-upload-vhd-windows-server.md)」を参照してください。

データ ディスクのアップロード方法については、Linux または Windows の記事を参照し、Azure への接続手順から確認してください。

## これらの仮想マシンは、HYPER-V 仮想マシンと同じですか。

多くの点で "第 1 世代" の Hyper-V VM と似ていますが、まったく同じというわけではありません。いずれのタイプも仮想化されたハードウェアを提供し、VHD 形式の仮想ハードディスクと互換性があります。つまり、Hyper-V と Azure の間で移動させることができます。Hyper-V ユーザーに驚かれることの多い主な違いは次の 2 点です。 <!-- In previous para, last sentence, s/b "Three key differences" correct? Also, since the colon provide adequate context for user, you might omit "are".  --> - Azure では、仮想マシンにアクセスするためのコンソールが提供されません。 - ほとんどの [サイズ](virtual-machines-size-specs.md) の Azure VM では、仮想ネットワーク アダプターが 1 つしかないため、外部 IP アドレスも 1 つしか使用できません。(A8 サイズと A9 サイズでは、インスタンス間でのアプリケーション通信に 2 つ目のネットワーク アダプターが使用される場合があります。) - Azure VM では、第 2 世代の Hyper-V VM の機能はサポートされていません。これらの機能の詳細については、「[Virtual Machine Specifications for Hyper-V (Hyper-V の仮想マシンの仕様)](http://technet.microsoft.com/library/dn592184.aspx)」を参照してください。

## これらの仮想マシンで、既存のオンプレミス ネットワーク インフラストラクチャを使用することはできますですか。

この質問への答えは、サービス管理ベースの VM とリソース管理ベースの VM とで異なります。

サービス管理ベースの VM の場合は、Azure Virtual Network を使用して既存のインフラストラクチャを拡張できます。このアプローチは、ブランチ オフィスのセットアップに似ています。Azure 上で仮想プライベート ネットワーク (VPN) をプロビジョニングして管理できるだけでなく、それらの VPN をオンプレミスの IT インフラストラクチャにセキュアに接続することもできます。詳細については、「[仮想ネットワークの概要](https://msdn.microsoft.com/library/jj156007.aspx)」を参照してください。

仮想マシンを作成する際には、仮想マシンの参加先となるネットワークを指定する必要があります。つまり、既存の仮想マシンを仮想ネットワークに参加させることなどはできません。ただし、仮想ハードディスク (VHD) を既存の仮想マシンからデタッチし、それを使用して、目的のネットワーク構成で新しい仮想マシンを作成することにより、この問題を回避することは可能です。

リソース管理ベースの VM の場合、VM を既存のオンプレミス インフラストラクチャに含めることは現時点ではできません。

## 仮想マシンへのアクセス方法を教えてください。

Windows 仮想マシンまたは Linux VM 用の Secure Shell (SSH) に対するリモート デスクトップ接続を使用してリモート接続を確立し、仮想マシンにログオンする必要があります。手順については、次の項目を参照してください。

- [Windows Server が実行されている仮想マシンにログオンする方法](virtual-machines-log-on-windows-server.md)。最大 2 つの同時接続がサポートされます (サーバーがリモート デスクトップ サービスのセッション ホストとして構成されている場合を除く)。  
- [Linux が実行されている仮想マシンにログオンする方法](virtual-machines-linux-how-to-log-on.md)。SSH では、既定で最大 10 の同時接続が可能です。この接続数は構成ファイルを編集することで増やすことができます。

リモート デスクトップまたは SSH について問題が発生する場合は、[VMAccess](http://go.microsoft.com/fwlink/p/?LinkId=396856) 拡張機能をインストールして使用し、問題を修正してください。Windows VM の場合は、次のオプションもあります: <!--  In next paragraph, omit "the" or add a modifier, for example, "click the X button". To enhance instructional design, you might use: "...the VM, and then on the X bar, click X"--> - Azure プレビュー ポータルで VM を検索し、コマンド バーで **[リモート アクセスのリセット]** をクリックする。-「[Windows ベースの Azure 仮想マシンへのリモート デスクトップ接続に関するトラブルシューティング](virtual-machines-troubleshoot-remote-desktop-connections.md)」を参照する。- Windows PowerShell リモート処理を使用して VM に接続するか、その他のリソースに対する追加のエンドポイントを作成して VM に接続する。詳細については、「[仮想マシンに対してエンドポイントを設定する方法](virtual-machines-set-up-endpoints.md)」を参照してください。<!-- Previous para, added END punctuation --> Hyper-V に慣れている場合は、仮想マシン接続と同様のツールを検討されるかもしれませんが、Azure では、仮想マシンへのコンソール アクセスがサポートされていないため、類似のツールは提供されていません。

## D: ドライブ (Windows) または /dev/sdb1 (Linux) は使用できますか。

D: ドライブ (Windows) や /dev/sdb1 (Linux) は使用しないでください。これらのデバイスでは一時ストレージしか提供されないため、データ損失の発生時にデータを復旧できない恐れがあります。この問題は、仮想マシンを別のホストに移動する際に多く発生します。仮想マシンが移動される理由としては、ホストの更新、仮想マシンのサイズ変更、ホスト上のハードウェア障害などが挙げられます。

## 一時ディスクのドライブ文字を変更する方法について教えてください。

Windows 仮想マシンでは、ページ ファイルを移動してドライブ文字を再割り当てすることでドライブ文字を変更することができますが、正しい順序で手順を実行するよう注意する必要があります。手順については、「[Windows 一時ディスクのドライブ文字を変更する方法](virtual-machines-windows-change-drive-letter.md)」を参照してください。

## ゲスト オペレーティング システムのアップグレード方法について教えてください。

アップグレードという言葉は一般に、オペレーティング システムを現在のハードウェアのままで新しいリリースに移行することを指します。Azure VM の場合、新しいリリースに移行するプロセスは Windows や Linux の場合と異なります。

- Linux VM の場合、配布には適切なパッケージ管理ツールと手順を使用してください。
- Windows 仮想マシンの場合は、Windows Server 移行ツールを使用します。ゲスト OS が Azure 上に存在する状態でアップグレードを行うことは避けてください。仮想マシンへのアクセスが失われる恐れがあるため、この操作はサポートされていません。アップグレード中に問題が発生すると、リモート デスクトップ セッションを開始できなくなり、問題のトラブルシューティングができなくなる可能性があります。ツールとプロセスの詳細については、「[Windows Server への役割と機能の移行](http://go.microsoft.com/fwlink/p/?LinkId=396940)」を参照してください。 Windows Server 2012 R2 へのアップグレードに関する詳細は、「[Windows Server 2012 R2 のアップグレード オプション](https://technet.microsoft.com/library/dn303416.aspx)」を参照してください。
<!--In previous para, last two sentences. Omit "general" since it adds questionable value for customer? -->
## 仮想マシンでの既定のユーザー名とパスワードを教えてください。

Azure によって提供されるイメージには、事前に構成されたユーザー名とパスワードはありません。これらのイメージのいずれかを使用して仮想マシンを作成する際は、仮想マシンへのログオンに使用するユーザー名とパスワードを指定する必要があります。

ユーザー名やパスワードを忘れてしまった場合、VM エージェントが既にインストールされていれば、VMAccess 拡張機能をインストールして使用し、問題を解決することができます。

追加情報:

- Linux イメージの場合、管理ポータルを使用すると既定のユーザー名として「azureuser」が指定されますが、仮想マシンの作成に [簡易作成] ではなく [ギャラリーから] を使用すれば、ユーザー名をを変更できます。また [ギャラリーから] では、ログインにパスワードを使用するか、SSH キーを使用するか、それとも両方を使用するかを指定することもできます。既定のユーザー アカウントは、特権のあるコマンドを実行するための "sudo" アクセス権を付与された、特権のないユーザーです。"root" アカウントは無効化されます。
- Windows イメージの場合は、VM の作成時にユーザー名とパスワードを指定する必要があります。アカウントは Administrators グループに追加されます。

## Azure では、仮想マシン上でウイルス対策を実行できますか。

Azure ではウイルス対策ソリューションとしていくつかのオプションが提供されていますが、管理はユーザーに委ねられています。これは、マルウェア対策ソフトウェアを個別のサブスクリプションで導入したい場合や、スキャン実行、更新プログラム インストールのタイミングを指定したい場合などを考慮したものです。ユーザーは、Windows 仮想マシンの作成時やそれ以降のタイミングで、Microsoft マルウェア対策、Symantec Endpoint Protection、または TrendMicro Deep Security Agent 用の VM 拡張機能により、ウイルス対策サポートを追加することができます。Symantec と TrendMicro 用の拡張機能では、無償の期間限定の試用版サブスクリプションか、既存のエンタープライズ サブスクリプションを使用することができます。Microsoft マルウェア対策は無償です。詳細については、次のリンクを参照してください。

- [Azure VM に Symantec Endpoint Protection をインストールし、構成する方法](http://go.microsoft.com/fwlink/p/?LinkId=404207)
- [Azure VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法](http://go.microsoft.com/fwlink/p/?LinkId=404206)
- [Azure Virtual Machines へのマルウェア対策ソリューションのデプロイ](http://azure.microsoft.com/blog/2014/05/13/deploying-antimalware-solutions-on-azure-virtual-machines/)

## バックアップと回復についてはどのようなオプションがありますか。

Azure Backup は、特定の地域でプレビューとして提供されています。詳細については、「[Back up Azure virtual machines (Azure 仮想マシンのバックアップ)](backup-azure-vms.md)」を参照してください。その他のソリューションは認定パートナーから利用できます。利用可能なソリューションについては、Azure Marketplace を検索して確認してください。

追加オプションとして、BLOB ストレージのスナップショット機能を使用することもできます。これを使用する場合は、BLOB スナップショットに依存する操作を実行する前に、VM を停止する必要があります。これにより、保留中のデータの書き込みが回避され、ファイル システムが一貫性のある状態に維持されます。

## Azure では VM の料金はどのように課金されますか。

Azure では、VM のサイズおよびオペレーティング システムに基づいて時間単位の料金を請求します。時間単位を満たさない場合は、分単位でのみ請求します。特定の事前インストール済みソフトウェアを含んだ VM イメージで VM を作成する場合は、1 時間ごとの追加ソフトウェア料金が適用される場合があります。また Azure では、VM のオペレーティング システムとデータ ディスクについて、別途のストレージ料金が課金されます。一時ディスク ストレージは無料です。

VM が実行中または停止状態のときには料金が発生しますが、VM が停止 (割り当て解除) 状態のときには料金は課金されません。VM を停止 (割り当て解除) 状態にするには、次のいずれかを行います。

- 管理ポータルから VM をシャット ダウンまたは削除する。
- Stop-AzureVM コマンドレットを使用する (Azure PowerShell モジュールで利用可能)。
- サービス管理 REST API でロールのシャット ダウン操作を使用し、PostShutdownAction 要素に StoppedDeallocated を指定する。

詳細については、「[Virtual Machines 料金](http://azure.microsoft.com/pricing/details/virtual-machines/)」を参照してください。

## Azure では、メンテナンスのために VM が再起動されることはありますか。

通常、VM の起動、停止、および再起動はユーザーが必要に応じて行います。(詳細については、「 [Azure VM の起動、停止、再起動について](https://msdn.microsoft.com/library/azure/dn763934.aspx)」を参照してください。)Azure では、Azure データ センターでの定期的な計画メンテナンス更新の一環として、VM が再起動されることがあります。また、VM に影響する重大なハードウェア問題が Azure で検出された場合には、計画外のメンテナンス イベントが発生する場合もあります。計画外イベントの場合、Azure は VM を正常な状態のホストへと自動的に移行し、VM を再起動します。

スタンドアロン VM (可用性セットに含まれない VM) については、計画メンテナンスの 1 週間前までに Azure からサブスクリプションのサービス管理者に電子メールが送られ、更新中に VM が再起動される可能性がある旨が通知されます。その場合、VM 上で実行されているアプリケーションにダウンタイムが発生する可能性があります。

計画メンテナンスのために再起動が発生した場合、利用者は Azure ポータルまたは Azure PowerShell を使用して再起動のログを確認できます。詳細については、「[Viewing VM Reboot Logs (VM の再起動ログの確認)](http://azure.microsoft.com/blog/2015/04/01/viewing-vm-reboot-logs/)」を参照してください。

冗長性を確保する必要がある場合は、同様に構成された VM を同じ可用性セット内に 2 つ以上配置してください。そうすることで、計画メンテナンスや計画外メンテナンスの際にも、最低 1 つの VM を利用できるようになります。Azure では、この構成について一定レベルの VM 可用性を保証しています。詳細については、「[仮想マシンの可用性管理](virtual-machines-manage-availability.md)」を参照してください。<!-- Promotion, referrals, customer empathy (this tactic likely requires signoff from individual that manages "look and feel" of these assets): Consider a new section like "Need more help" and within new section include link to Developer Portal, or perhaps new product features, troubleshooting, or maintenance?-->

<!---HONumber=July15_HO2-->