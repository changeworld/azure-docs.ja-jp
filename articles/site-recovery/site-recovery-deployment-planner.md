---
title: "VMware から Azure 用の Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "Azure Site Recovery Deployment Planner のユーザー ガイドです。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 2/21/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2575621d72b7db2b090ba923324697b7fa7b8308
ms.lasthandoff: 03/15/2017


---
#<a name="azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner
本書は、VMware から Azure へのレプリケーションを行う運用環境のデプロイに関する Azure Site Recovery Deployment Planner のユーザー ガイドです。


##<a name="overview"></a>概要

Azure Site Recovery を使用して VMware 仮想マシンを保護する前に、必要な RPO (回復ポイントの目標) を達成するうえで十分な帯域幅を日次データ変化率に基づいて割り当てる必要があります。 適切な台数の構成サーバーとプロセス サーバーをオンプレミスにデプロイする必要があります。 また、レプリケーション元となる運用サーバーの使用率が時間と共に増えるようであれば、その増加率を見越してレプリケーション先の Azure Storage アカウントの種類 (Standard または Premium) と数を見極め、それらのアカウントを作成することも必要となります。 ストレージの種類は、ワークロードの特性 (読み取り/書き込みの IOPS、データの変更頻度など) と Azure Site Recovery の制限に基づいて仮想マシンごとに判断します。  

Azure Site Recovery Deployment Planner Public Preview はコマンド ライン ツールです。現時点では、VMware から Azure にレプリケーションを行うシナリオでのみ利用することができます。 このツールを使用すると、(運用環境には一切影響を与えることなく) リモートから VMware 仮想マシンをプロファイリングして、レプリケーションとテスト フェールオーバーに必要な帯域幅要件と Azure Storage 要件を把握することができます。  Deployment Planner ツールは、Azure Site Recovery のコンポーネントをオンプレミスにインストールせずに実行できますが、達成スループットの実績値を正確に把握するために、本番デプロイの最初のステップの&1; つとして、最終的にデプロイすることになる Azure Site Recovery 構成サーバーの最低限の要件を満たした Windows Server で Planner を実行することをお勧めします。

このツールで把握できる情報は次のとおりです。

**適合性の評価**<br>
* ディスク数、ディスク サイズ、IOPS、変更頻度に基づく仮想マシンの適格性評価

**ネットワーク帯域幅ニーズと RPO の評価**<br>
* 差分レプリケーションに必要な推定ネットワーク帯域幅<br>
* オンプレミスから Azure への間で Azure Site Recovery が得られるスループット<br>
* 束ねる仮想マシンの数 (初回レプリケーションを所定の時間内に完了するための推定帯域幅に基づく)<br>

**Microsoft Azure のインフラストラクチャ要件**<br>
* 各仮想マシンに必要なストレージの種類 (Standard または Premium)<br>
* レプリケーション用にプロビジョニングする Standard Storage アカウントと Premium Storage アカウントの総数<br>
* ストレージ アカウントの命名に関する推奨事項 (Azure Storage ガイダンスに基づく)<br>
* 各仮想マシンのストレージ アカウントの配置<br>
* サブスクリプションのテスト フェールオーバー/フェールオーバーの前にプロビジョニングする Microsoft Azure コアの数<br>
* Microsoft Azure 仮想マシンの推奨サイズ (オンプレミスの仮想マシンごと)<br>

**オンプレミス インフラストラクチャの要件**<br>
* オンプレミスにデプロイする必要のある構成サーバーとプロセス サーバーの台数<br>

>[!IMPORTANT]
>
>このツールにおける上記の計算はいずれも、時間経過に伴う使用率の増加を見込んで、実際のワークロード特性における増加率を 30% と仮定して実行されます。また、プロファイリングのメトリック (読み取り/書き込みの IOPS、変更頻度など) はすべて 95 パーセンタイルが採用されます。この 2 つのパラメーター (増加率とパーセンタイル計算) は、設定により変更することができます。 詳細については、「[増加率](site-recovery-deployment-planner.md#growth-factor)」と「[計算に使用されるパーセンタイル値](site-recovery-deployment-planner.md#percentile-value-used-for-the-calculation)」セクションを参照してください。
>


## <a name="requirements"></a>必要条件
このツールの処理は、プロファイリングとレポート生成という大きく&2; つの段階に分けられます。 加えて第&3; の選択肢として、スループットの計算のみを行うこともできます。 以下に示したのは、プロファイリング/スループット測定が開始されるサーバーの要件です。

| 要件 | Description|
|---|---|
|プロファイリングとスループット測定| <br>オペレーティング システム: Microsoft Windows Server 2012 R2 <br>少なくとも次の構成サーバー [サイズ](https://aka.ms/asr-v2a-on-prem-components)が満たされていることが望ましい<br>マシン構成: 8 vCPU、16 GB RAM、300 GB HDD<br>[Microsoft .NET Framework 4.5](https://aka.ms/dotnet-framework-45)<br>[VMware vSphere PowerCLI 6.0 R3](https://developercenter.vmware.com/tool/vsphere_powercli/6.0)<br>[Visual Studio 2012 の Microsoft Visual C++ 再頒布可能パッケージ](https://aka.ms/vcplusplus-redistributable)<br> このサーバーから Microsoft Azure へのインターネット アクセス<br> Microsoft Azure ストレージ アカウント<Br>サーバー上の管理者アクセス権<br>100 GB 以上の空きディスク領域 (平均 3 枚のディスクがある仮想マシン 1,000 台をそれぞれ 30 日間プロファイリングすることを想定)|
| レポートの生成| Microsoft Excel 2013 以降がインストールされた任意の Windows PC/Windows Server |
| ユーザーのアクセス許可 | 読み取り専用アクセス許可 (プロファイリング中、VMware vCenter/vSphere サーバーにアクセスするユーザー アカウントに適用)|


> [!NOTE]
>
> このツールでプロファイリングできるのは、VMDK ディスクと RDM ディスクを使用した仮想マシンのみです。 iSCSI ディスクまたは NFS ディスクを使用した仮想マシンをプロファイリングすることはできません。 Azure Site Recovery は VMware サーバーの iSCSI ディスクと NFS ディスクをサポートしていますが、Deployment Planner がゲスト内に存在せず、プロファイリングが vCenter のパフォーマンス カウンターのみを使用して行われることを考えると、これらの種類のディスクをこのツールで十分把握することはできません。
>


##<a name="download"></a>ダウンロード
最新バージョンの Azure Site Recovery Deployment Planner Public Preview を[ダウンロード](https://aka.ms/asr-deployment-planner)してください。  ツールは zip 形式でパッケージされています。  VMware から Azure へのレプリケーション シナリオに対応しているのは、このツールの最新バージョンのみです。

ツールの実行場所となる Windows Server に zip ファイルをコピーしてください。 このツールは、プロファイリングの対象となる仮想マシンのホスト (VMware vSphere ESXi ホストまたは VMware vCenter サーバー) にネットワークで接続されている任意の Windows Server 2012 R2 から実行できますが、[構成サーバーのサイズ ガイドライン](https://aka.ms/asr-v2a-on-prem-components)に準拠したハードウェア構成のサーバーで実行することをお勧めします。  Azure Site Recovery のコンポーネントをオンプレミスにデプロイ済みである場合は、その構成サーバーからツールを実行する必要があります。 Azure Site Recovery の実際のレプリケーション時のスループットを正確に反映したレポートを得るために、ツールの実行起点となるサーバーには、(プロセス サーバーを一体化した) 構成サーバーと同じハードウェア構成を使用するようお勧めします。つまりスループットの計算は、サーバーで利用可能なネットワーク帯域幅とそのサーバーのハードウェア構成 (CPU、記憶域など) によって左右されます。 その他のサーバーからツールを実行した場合、計算されるのは、そのサーバーから Microsoft Azure へのスループットです。しかもそのサーバーには、構成サーバーとは異なるハードウェア構成が使用されていることが考えられます。そうなると、達成スループットとしてツールから報告される値の精度が低下してしまいます。

zip フォルダーを展開します。 複数のファイルとサブフォルダーが表示されます。 実行可能ファイルは、親フォルダーにある ASRDeploymentPlanner.exe です。

例: .zip ファイルを E:\ ドライブにコピーして展開します。
E:\ASR Deployment Planner-Preview_v1.1.zip

E:\ASR Deployment Planner-Preview_v1.1\ ASR Deployment Planner-Preview_v1.1\ ASRDeploymentPlanner.exe

##<a name="capabilities"></a>機能
このコマンド ライン ツール (ASRDeploymentPlanner.exe) は、次の&3; とおりのモードで実行できます。

1.    プロファイリング  
2.    レポートの生成
3.    スループットの取得

最初はプロファイリング モードでツールを実行し、仮想マシンのデータの変更頻度と IOPS を取得する必要があります。  そのうえでツールを実行してレポートを生成し、ネットワーク帯域幅やストレージの要件を把握します。

##<a name="profiling"></a>プロファイリング
プロファイリング モードでは、Deployment Planner ツールが vCenter サーバーまたは vSphere ESXi ホストに接続して仮想マシンに関するパフォーマンス データを収集します。

* 運用仮想マシンへの直接接続が確立されるわけではないので、プロファイリングが運用仮想マシンのパフォーマンスに影響を及ぼすことはありません。 すべてのパフォーマンス データは vCenter サーバー/vSphere ESXi ホストから収集されます。
* vCenter サーバー/vSphere EXSi ホストが 15 分に 1 回の頻度で照会されます。そうすることでサーバーに対するプロファイリングの影響は、無視できる範囲に抑えられています。 ただし、パフォーマンス カウンター データは絶えず保存されるため、そのことでプロファイリングの精度が損なわれることはありません。

####<a name="create-a-list-of-virtual-machines-to-profile"></a>プロファイリングする仮想マシンのリストを作成する
まず、プロファイリングの対象となる仮想マシンのリストを用意する必要があります。 以下の VMware vSphere PowerCLI コマンドを使用すると、VMware vCenter または VMware vSphere ESXi ホスト上にあるすべての仮想マシンの名前を取得できます。 または、プロファイリングの対象となる仮想マシンのフレンドリ名または IP アドレスを手動でファイルにリストアップしてもかまいません。

1.    VMware vSphere PowerCLI がインストールされている仮想マシンにログオンします。
2.    VMware vSphere PowerCLI コンソールを開きます。
3.    スクリプトの実行ポリシーが無効になっていないことを確認します。 無効になっている場合は、VMware vSphere PowerCLI コンソールを管理者モードで起動し、次のコマンドを実行して有効にします。

            Set-ExecutionPolicy –ExecutionPolicy AllSigned

4.    次の&2; つのコマンドを実行して、VMware vCenter または VMware vSphere ESXi 上にあるすべての仮想マシンの名前を取得し、.txt ファイルに保存します。
&lsaquo;server name&rsaquo;、&lsaquo;user name&rsaquo;、&lsaquo;password&rsaquo;、&lsaquo;outputfile.txt&rsaquo; は、実際の値に置き換えてください。

            Connect-VIServer -Server <server name> -User <user name> -Password <password>

            Get-VM |  Select Name | Sort-Object -Property Name >  <outputfile.txt>


5.    出力ファイルをメモ帳で開きます。 プロファイリングするすべての仮想マシンの名前を別のファイル (ProfileVMList.txt など) にコピーします。仮想マシンの名前は&1; 行につき&1; つです。 このファイルは、コマンド ライン ツールの -VMListFile パラメーターの入力として使用します。

    ![Deployment Planner](./media/site-recovery-deployment-planner/profile-vm-list.png)


####<a name="start-profiling"></a>プロファイリングの開始
プロファイリングの対象となる VM のリストを作成したら、プロファイリング モードでツールを実行できるようになります。 プロファイリング モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧を次に示します。 角かっこ ([]) で囲まれたパラメーターは省略可能です。

ASRDeploymentPlanner.exe -Operation StartProfiling /?

| パラメーター名 | Description |
|---|---|
| -Operation |      StartProfiling |
| -Server | プロファイリングの対象となる仮想マシンがある vCenter サーバー/ESXi ホストの完全修飾ドメイン名または IP アドレス。|
| -User | vCenter サーバー/ESXi ホストに接続するためのユーザー名。 ユーザーには、少なくとも読み取り専用アクセス権が必要です。|
| -VMListFile |    プロファイリングの対象となる仮想マシンのリストを含むファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき&1; つの仮想マシン名または IP アドレスが記述されている必要があります。 このファイルに指定する仮想マシン名は、vCenter サーバーまたは ESXi ホスト上の VM 名と一致している必要があります。 <br> たとえば、"VMList.txt" ファイルに、次のように仮想マシン名を記述することができます。<br>virtual_machine_A <br>10.150.29.110<br>virtual_machine_B |
| -NoOfDaysToProfile | プロファイリングを実行する日数。 プロファイリングの実行期間は 15 日間より長くすることをお勧めします。それだけの期間があれば、実際の環境のワークロード パターンを十分に観察し、正確な情報を把握することができます。 |
| [-Directory] |    プロファイリング中に生成されたプロファイリング データの格納先となる UNC パスまたはローカル ディレクトリ パス。 指定しなかった場合、現在のパス下の "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。 |
| [-Password ] | vCenter サーバー/ESXi ホストに接続するためのパスワード。 ここで指定しなかった場合は、コマンドの実行時に入力を求められます。|
|  [-StorageAccountName]  | オンプレミスから Azure へのデータのレプリケーションに関して達成可能なスループットの調査対象となる Azure Storage アカウントの名前。 このストレージ アカウントにテスト データがアップロードされてスループットが計算されます。|
| [-StorageAccountKey] | ストレージ アカウントにアクセスするための Azure Storage アカウント キー。 Azure Portal の [ストレージ アカウント]、[<ストレージ アカウント名>]、[設定]、[アクセス キー]、[Key1] (クラシック ストレージ アカウントの場合は [プライマリ アクセス キー]) の順に移動します。 |

仮想マシンのプロファイリングは、少なくとも 15 ～ 30 日間は実行することをお勧めします。 プロファイリング期間中は ASRDeploymentPlanner.exe が実行状態となります。 ツールには、プロファイリングの時間を日数で入力します。 Public Preview 版ツールの簡単なテストを行うためにプロファイリング期間を数時間または数分とする場合は、日数の尺度に換算する必要があります。  たとえばプロファイリング期間が 30 分である場合、入力する値は 30 / (60*24) = 0.021 日となります。  プロファイリング期間として許容される最短時間は 30 分です。

構成サーバー/プロセス サーバーから Azure へのレプリケーション時に Azure Site Recovery で得られるスループットの調査対象として、プロファイリング中、必要に応じて Azure Storage アカウントの名前とキーを指定することができます。 プロファイリング時に Azure Storage アカウントの名前とキーが渡されないと、達成可能なスループットは計算されません。


複数の仮想マシン グループを対象として、Deployment Planner ツールのインスタンスを複数実行することができます。 その場合、プロファイリングの対象となるグループ内やグループ間で、同じ仮想マシン名が重複しないようにしてください。 たとえば&10; 台の仮想マシン (VM1 ～ VM10) をプロファイリングし、数日後に別の&5; 台の仮想マシン (VM11 ～ VM15) をプロファイリングする必要が生じたとします。この場合、2 つ目の仮想マシン グループ (VM11 ～ VM15) について、別のコマンド ライン コンソールからツールを実行することができます。 ただし、1 つ目のプロファイリング インスタンスの対象となっていた仮想マシン名を&2; つ目の仮想マシン グループに含めることは避けてください。または&2; 回目の実行時には、1 回目とは異なる出力ディレクトリを使用してください。 Deployment Planner ツールの&2; つのインスタンスで、同じ出力ディレクトリに対して同じ仮想マシンをプロファイリングした場合、生成されるレポートは正確ではなくなります。

仮想マシンの構成は、プロファイリング処理の開始時に&1; 回だけ捕捉されて、VMDetailList.xml というファイルに保存されます。 この情報は、レポートの生成時に使用されます。 プロファイリングの開始から終了までの間に生じた VM 構成の変化 (コア数、ディスク数、NIC 数が増えるなど) は捕捉されません。 Public Preview を使用したプロファイリングの途中で、その対象となる仮想マシンの構成が変更された場合は、以下の回避策によって、レポートの生成時に最新の仮想マシン情報を取得することができます。   

* "VMdetailList.xml" をバックアップし、現在の場所からこのファイルを削除します。
* レポートの生成時に -User 引数と -Password 引数を指定します。

プロファイリング コマンドによって、いくつかのファイルがプロファイリング ディレクトリに生成されます。これらのファイルは一切削除しないでください。削除すると、レポートの生成に影響が生じます。

#####<a name="example-1-to-profile-virtual-machines-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>例 1: 仮想マシンを 30 日間プロファイリングし、オンプレミスから Azure へのレプリケーションのスループットを調査する
ASRDeploymentPlanner.exe **-Operation** StartProfiling -Directory “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  30  **-User** vCenterUser1 **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

#####<a name="example-2-to-profile-virtual-machines-for-15-days"></a>例 2: 仮想マシンを 15 日間プロファイリングする
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  15  -User vCenterUser1

#####<a name="example-3-to-profile-virtual-machines-for-1-hour-for-a-quick-test-of-the-tool"></a>例3: ツールの簡単なテストを行うために仮想マシンを 1 時間プロファイリングする
ASRDeploymentPlanner.exe **-Operation** StartProfiling **-Directory** “E:\vCenter1_ProfiledData” **-Server** vCenter1.contoso.com **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-NoOfDaysToProfile**  0.04  **-User** vCenterUser1


>[!NOTE]
>
> * ツールを実行したサーバーが再起動されるかクラッシュした場合、または Ctrl + C キーでツールを終了した場合、プロファイリングされたデータは保持されます。 この場合、直近 15 分間のプロファイリング データは失われる可能性があります。 サーバーがバックアップを開始したら、プロファイリング モードでもう一度ツールを実行する必要があります。
>
> * Azure Storage アカウントの名前とキーが渡されると、プロファイリングの最後の段階でスループットが測定されます。 プロファイリングが正常完了する前にツールが強制終了された場合、スループットは計算されません。 レポートが生成される前であれば、コマンド ライン コンソールから GetThroughput 操作を実行することで、いつでもスループットを調査することができます。それ以外の場合、生成されるレポートには、達成スループットの情報が出力されません。
>

##<a name="generate-report"></a>レポートの生成
Deployment Planner ツールでは、デプロイの推奨情報をすべてまとめた XLSM (マクロ有効 Microsoft Excel ファイル) がレポートの出力結果として生成され、指定されたディレクトリに DeploymentPlannerReport_<Unique Numeric Identifier>.xlsm という名前で格納されます。

プロファイリングが完了したら、レポート生成モードでツールを実行できます。 レポート生成モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧を次に示します。 角かっこ ([]) で囲まれたパラメーターは省略可能です。

ASRDeploymentPlanner.exe -Operation GenerateReport /?

|パラメーター名 | Description |
|-|-|
| -Operation | GenerateReport |
| -Server |  レポートを生成するプロファイリング対象の仮想マシンがある vCenter/vSphere サーバーの完全修飾ドメイン名または IP アドレス (プロファイリング時とまったく同じ名前または IP アドレスを使用します)。 プロファイリング時に vCenter サーバーを使用した場合は、レポートの生成対象として vSphere サーバーを使用することはできません (その逆も同様)。|
| -VMListFile | レポートを生成するプロファイリング対象仮想マシンのリストが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき&1; つの仮想マシン名または IP アドレスが記述されている必要があります。 このファイルに指定する仮想マシン名は、vCenter サーバーまたは ESXi ホスト上の仮想マシン名と同じであること、またプロファイリング時に使用されたものと一致している必要があります。|
| [-Directory] | プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 指定しなかった場合は、"ProfiledData" ディレクトリが使用されます。 |
| [-GoalToCompleteIR] |    プロファイリング対象仮想マシンの初回レプリケーションが完了するまでの目標所要時間。 生成されたレポートには、指定した時間内に初回レプリケーションを完了するために必要な仮想マシンの台数が記載されます。 既定値は 72 時間です。 |
| [-User] | vCenter/vSphere サーバーに接続するユーザーの名前。 レポートに使用する仮想マシンの最新の構成情報 (ディスク数、コア数、NIC 数など) を取得するには、このパラメーターを指定します。 指定しなかった場合は、プロファイリングの開始時に収集された構成情報が使用されます。 |
| [-Password] | vCenter サーバー/ESXi ホストに接続するためのパスワード。 パラメーターとして指定しなかった場合は、後でコマンドの実行時に入力を求められます。 |
| [-DesiredRPO] | 必要な RPO (回復ポイントの目標) を分単位で指定します。 既定では 15 分です。|
| [-Bandwidth] | 帯域幅 (Mbps)。 このパラメーターで指定した帯域幅で達成できる RPO が計算されます。 |
| [-StartDate]  | 開始日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 "StartDate" は "EndDate" と一緒に指定する必要があります。 指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| [-EndDate] | 終了日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 "EndDate" は "StartDate" と一緒に指定する必要があります。 指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| [-GrowthFactor] |増加率 (%)。 既定値は 30% です。  |


##### <a name="example-1-to-generate-report-with-default-values-when-profiled-data-is-on-the-local-drive"></a>例 1: 既定値でレポートを生成する (プロファイリング データがローカル ドライブにある場合)
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”


##### <a name="example-2-to-generate-report-when-profiled-data-is-on-a-remote-server-user-should-have-readwrite-access-on-the-remote-directory"></a>例 2: レポートを生成する (プロファイリング データがリモート サーバーにある場合)。 ユーザーには、リモート ディレクトリに対する読み取り/書き込みアクセス権が必要です。
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “\\\\PS1-W2K12R2\vCenter1_ProfiledData” **-VMListFile** “\\\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”

##### <a name="example-3-generate-report-with-specific-bandwidth-and-goal-to-complete-ir-within-specified-time"></a>例 3: 特定の帯域幅と初回レプリケーションの目標所要時間を指定してレポートを生成する
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-Bandwidth** 100 **-GoalToCompleteIR** 24

##### <a name="example-4-generate-report-with-5-growth-factor-instead-of-the-default-30"></a>例 4: 増加率を既定値の 30% から 5% に変更してレポートを生成する
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-GrowthFactor** 5

##### <a name="example-5-generate-report-with-a-subset-of-profiled-data-say-you-have-30-days-of-profiled-data-and-want-to-generate-the-report-for-only-20-days"></a>例 5: 一部のプロファイリング データでレポートを生成する。 たとえば、30 日間分のプロファイリング データがあるときに 20 日間のみを対象としてレポートを生成します。
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt” **-StartDate**  01-10-2017:12:30 -**EndDate** 01-19-2017:12:30

##### <a name="example-6-generate-report-for-5-minutes-rpo"></a>例 6: RPO を 5 分としてレポートを生成する
ASRDeploymentPlanner.exe **-Operation** GenerateReport **-Server** vCenter1.contoso.com **-Directory** “E:\vCenter1_ProfiledData” **-VMListFile** “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  **-DesiredRPO** 5

### <a name="percentile-value-used-for-the-calculation"></a>計算に使用されるパーセンタイル値
**プロファイリング時に収集されたパフォーマンス メトリックに対し、レポートの生成時に使用される既定のパーセンタイル値について**

すべての VM において、プロファイリング時に収集される読み取り/書き込みの IOPS、書き込み IOPS、データの変更頻度には、既定で 95 パーセンタイル値が使用されます。 そうすることで、レプリケーション先の Azure Storage とレプリケーション元の帯域幅の要件を決定する際に、VM の一時的な事象 (1 日 1 回実行されるバックアップ ジョブや定期的なデータベース インデックス作成、分析レポート生成アクティビティなど、プロファイリング期間中に発生する持続性のない瞬時性イベント) に起因する瞬間的な 100 パーセンタイルを判断から除外することができます。 95 パーセンタイル値を使用することで、実際のワークロード特性の真の姿を捉え、それらのワークロードが Microsoft Azure で実行されている状態でのベストなパフォーマンスを把握することができるのです。 通常この値を変更する必要はありませんが、もっと小さい値を使用したい場合 (たとえば 90 パーセンタイルに下げたい場合) は、既定のフォルダーにある構成ファイル "ASRDeploymentPlanner.exe.config" を編集、保存すれば、既にあるプロファイリング データで新しいレポートを生成することができます。

        &lsaquo;add key="WriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="ReadWriteIOPSPercentile" value="95" /&rsaquo;>      
        &lsaquo;add key="DataChurnPercentile" value="95" /&rsaquo;

### <a name="growth-factor"></a>増加率
**デプロイ計画時に増加率を考慮すべき理由**

時間の経過と共に使用率が増える可能性を想定し、ワークロード特性の増大を考慮することはきわめて重要です。 なぜなら、いったん保護された状態を確保した後でワークロードの特性が変化した場合、現時点では、保護に使用する Azure Storage アカウントを切り替えることができないためです。一度保護を無効にしてから再度有効にする必要があります。 例: 現在、仮想マシンのレプリケーションは Standard Storage アカウントで問題なく行われています。3 か月後、仮想マシンで実行されているアプリケーションのユーザー数が増加し、VM の変更頻度が増え、Premium Storage への移行が必要になったとします。Azure Site Recovery のレプリケーションを変更頻度の増大に追い付かせるためには、一度保護を無効にしてから、レプリケーション先を Premium Storage アカウントに切り替えて再度、保護を有効にする必要があります。 そのために、デプロイ計画の段階で増加を考慮することが強く推奨されており、既定値は 30% となっています。 アプリケーションの使用パターンや予想される新規ユーザーについて一番よく知っているのは皆さん自身です。この値はレポートの生成時に適宜変更してください。 同じプロファイリング データで、増加率を変えながら複数のレポートを生成することもできるので、皆さんにとって最も効果的なレプリケーション先の Azure Storage とレプリケーション元の帯域幅を特定してください。

生成された Microsoft Excel レポートには、次のシートがあります。

* [Input (入力)](site-recovery-deployment-planner.md#input)
* [Recommedations (推奨事項)](site-recovery-deployment-planner.md#recommendations-with-desired-rpo-as-input)
* [Recommedations-Bandwidth Input (推奨事項 - 帯域幅入力)](site-recovery-deployment-planner.md#recommendations-with-available-bandwidth-as-input)
* [VM<->Storage Placement (VM<->ストレージの配置)](site-recovery-deployment-planner.md#vm-storage-placement)
* [Compatible VMs (適合 VM)](site-recovery-deployment-planner.md#compatible-vms)
* [Incompatible VMs (不適合 VM)](site-recovery-deployment-planner.md#incompatible-vms)

![Deployment Planner](./media/site-recovery-deployment-planner/dp-report.png)


##<a name="get-throughput"></a>スループットの取得
オンプレミスから Azure へのレプリケーション時に Azure Site Recovery が達成できるスループットを見積もるには、GetThroughput モードで Deployment Planner ツールを実行します。 このツールでは、それが実行されているサーバー (理想的には、構成サーバーのサイズ ガイドラインに準拠したサーバー) を起点としてスループットが計算されます。  Azure Site Recovery のインフラストラクチャ コンポーネントをオンプレミスにデプロイ済みである場合は、その構成サーバーでツールを実行してください。

コマンド ライン コンソールを開いて、ASR のデプロイ プランニング ツール フォルダーに移動します。  以下のパラメーターを指定して ASRDeploymentPlanner.exe を実行してください。 角かっこ ([]) で囲まれたパラメーターは省略可能です。

ASRDeploymentPlanner.exe -Operation GetThroughput /?

|パラメーター名 | 説明 |
|-|-|
| -operation | GetThroughput |
| [-Directory] | プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる UNC パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 指定しなかった場合は、"ProfiledData" ディレクトリが使用されます。  |
| -StorageAccountName | オンプレミスから Azure へのデータのレプリケーションに関して、使用帯域幅の調査対象となる Azure Storage アカウントの名前。 このストレージ アカウントにテスト データがアップロードされて使用帯域幅が計算されます。 |
| -StorageAccountKey | ストレージ アカウントにアクセスするための Azure Storage アカウント キー。 Azure Portal の [ストレージ アカウント]、[<ストレージ アカウント名>]、[設定]、[アクセス キー]、[Key1] (クラシック ストレージ アカウントの場合は [プライマリ アクセス キー]) の順に移動します。 |
| -VMListFile | 使用帯域幅の計算に関して、プロファイリングの対象となる仮想マシンのリストを含んだファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルには、1 行につき&1; つの仮想マシン名または IP アドレスが記述されている必要があります。 このファイルに指定する仮想マシン名は、vCenter サーバーまたは ESXi ホスト上の仮想マシン名と同じである必要があります。<br>例: "VMList.txt" ファイルに、次のように仮想マシン名を記述することができます。<br>virtual machine_A <br>10.150.29.110<br>virtual machine_B|

Deployment Planner ツールは、指定されたディレクトリに 64 MB の "asrvhdfile<#>.vhd" (# は番号) ファイルをいくつか作成します。  これらのファイルを Azure Storage アカウントにアップロードすることによってスループットが調査されます。 これらのファイルはすべて、スループットの測定後に Azure Storage アカウントおよびローカル サーバーから削除されます。 スループットの計算中に何らかの理由でツールが終了した場合、Azure Storage とローカル サーバーからファイルが削除されません。この場合は、ファイルを手動で削除する必要があります。

スループットは、特定の時点で測定されるものです。そのときと同じ条件下で Azure Site Recovery がレプリケーション時に達成できる最大スループットと考えてください。 たとえば、いずれかのアプリケーションが同じネットワーク上で、それまでよりも多くの帯域幅を使い始めた場合、レプリケーション時の実際のスループットは変化します。 GetThroughput コマンドを構成サーバーから実行する場合、保護されている仮想マシンや実行中のレプリケーションのことまでは考慮されません。 スループットの測定結果は、保護されている仮想マシンのデータの変更頻度が高いときに GetThroughput 操作を実行した場合と低いときに実行した場合とで異なります。  さまざまな状況で得られるスループットを把握するために、プロファイリング中はいろいろなタイミングでツールを実行することをお勧めします。 レポートには、最後に測定されたスループットが表示されます。


##### <a name="example"></a>例
ASRDeploymentPlanner.exe **-Operation** GetThroughput **-Directory**  E:\vCenter1_ProfiledData **-VMListFile** E:\vCenter1_ProfiledData\ProfileVMList1.txt  **-StorageAccountName**  asrspfarm1 **-StorageAccountKey** by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==

>[!NOTE]
>
> * Deployment Planner ツールは、ストレージと CPU の特性が構成サーバーと同じサーバーで実行してください。
>
> * レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅をプロビジョニングしてください。 適切な帯域幅をプロビジョニングしたにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の点を確認してください。
>
> a. Azure Site Recovery のスループットを制限するネットワーク QoS (サービス品質) が存在しているかどうかを確認します。
>
> b. 物理的にサポートされる最も近い Microsoft Azure リージョンに Azure Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。
>
> c. ローカル ストレージの特性を確認し、ハードウェアの強化を図ります (例: HDD から SSD など)。
>
> d. [レプリケーションに使用されるネットワーク帯域幅を増やす](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)ために、プロセス サーバーで Azure Site Recovery の設定を変更します。
>

##<a name="recommendations-with-desired-rpo-as-input"></a>必要な RPO の入力に関する推奨事項

###<a name="profiled-data"></a>プロファイリング データ

![Deployment Planner](./media/site-recovery-deployment-planner/profiled-data-period.png)

**[Profiled data period (プロファイリング データ期間)]**: プロファイリングが実行された期間です。 レポートの生成時に StartDate オプションと EndDate オプションを使用して生成対象期間を指定しない限り、既定では、すべてのプロファイリング データが計算に使用されます。

**[サーバー名]**: レポートの生成対象の仮想マシンがある VMware vCenter または ESXi ホストの名前または IP アドレスです。

**[Desired RPO (必要な RPO)]**: 実際の環境で採用する回復ポイントの目標 (RPO) です。 既定では、15 分、30 分、60 分の 3 とおりの RPO 値について、必要なネットワーク帯域幅が計算されます。 選択した値に応じて、シート上で関連する値が更新されます。 レポートの生成中に DesiredRPOinMin パラメーターを使用した場合、その値がこの [Desired RPO (必要な RPO)] ボックスの一覧に表示されます。

###<a name="profiling-overview"></a>Profiling Overview (プロファイリングの概要)

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview.png)

**[Total Profiled Virtual Machines (プロファイリングされた仮想マシンの合計)]**: プロファイリング データがある仮想マシンの総数です。 プロファイリングされなかった仮想マシンの名前が VMListFile に含まれている場合、それらの仮想マシンはレポートの生成時に考慮されず、プロファイリングされた仮想マシンの合計からは除外されます。

**[Compatible Virtual Machines (適合仮想マシン)]**: Azure Site Recovery を使用して Azure で保護できる仮想マシンの数です。 適合する (必要なネットワーク帯域幅、Azure Storage アカウント数、Microsoft Azure コア数、構成サーバー数、追加プロセス サーバー数が計算される) 仮想マシンの総数になります。 適合する仮想マシンごとの詳しい情報は、レポートの [Compatible VMs (適合 VM)] シートに表示されます。

**[Incompatible Virtual Machines (不適合仮想マシン)]**: プロファイリングされた仮想マシンのうち、Azure Site Recovery を使用した保護に適合しない仮想マシンの数です。 不適合の理由については、以降の「Incompatible VMs (不適合 VM)」セクションで取り上げます。 プロファイリングされなかった仮想マシンの名前が VMListFile に含まれている場合、それらの仮想マシンは、不適合仮想マシンの数から除外されます。 そのような仮想マシンは、[Incompatible VMs (不適合 VM)] シートの最後にある [Data not found (データが見つかりません)] に列挙されます。

**[Desired RPO (必要な RPO)]**: 分単位で指定した回復ポイントの目標です。 15 分、30 分、60 分の 3 とおりの RPO 値についてレポートが生成されます (既定値は 15 分)。 シートの右上にある [Desired RPO (必要な RPO)] ボックスの一覧での選択に応じて、レポートされる推奨帯域幅が変化します。 "-DesiredRPO" パラメーターに独自の値を指定してレポートを生成した場合、[Desired RPO (必要な RPO)] ボックスの一覧には、その独自の値が既定値として表示されます。

###<a name="required-network-bandwidth-mbps"></a>Required Network Bandwidth (Mbps) (必要なネットワーク帯域幅 (Mbps))

![Deployment Planner](./media/site-recovery-deployment-planner/required-network-bandwidth.png)

**[To meet RPO 100% of the time (RPO を 100% の時間満たす場合)]**: 必要な RPO を 100% の時間満たす場合に割り当てる必要のある推奨帯域幅 (Mbps) です。 適合するすべての仮想マシンについて、RPO からの逸脱 (RPO 違反) を防止し、安定した状態の差分レプリケーションを行うためには、この帯域幅を専用に確保する必要があります。

**[To meet RPO 90% of the time (RPO を 90% の時間満たす場合)]**: 必要な RPO を 100% の時間満たす帯域幅を、ブロードバンドのコストなど何らかの理由でプロビジョニングできない場合は、必要な RPO を 90% の時間満たすことのできる水準まで帯域幅を下げてプロビジョニングすることが選択肢として考えられます。 プロビジョニングする帯域幅を引き下げたことによって生じる影響を明らかにするために、レポートには RPO 違反の件数とその期間についての what-if 分析が示されます。

**[Achieved Throughput (達成スループット)]**: GetThroughput コマンドが実行されたサーバーから、Azure Storage アカウントが存在する Microsoft Azure リージョンまでのスループットです。 構成サーバー/プロセス サーバーのストレージとネットワークの特性が、Deployment Planner ツールを実行したサーバーのそれと同じ状態に保たれていることを前提とし、Azure Site Recovery を使用して適合仮想マシンを保護した場合に得られるおおよそのスループットを表しています。    

レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅をプロビジョニングしてください。 適切な帯域幅をプロビジョニングしたにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の点を確認してください。

a.    Azure Site Recovery のスループットを制限するネットワーク QoS (サービス品質) が存在しているかどうかを確認します。

b.    物理的にサポートされる最も近い Microsoft Azure リージョンに Azure Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。

c.    ローカル ストレージの特性を確認し、ハードウェアの強化を図ります (例: HDD から SSD など)。

d. [レプリケーションに使用されるネットワーク帯域幅を増やす](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)ために、プロセス サーバーで Azure Site Recovery の設定を変更します。

既に保護された仮想マシンがある構成サーバー/プロセス サーバーで Deployment Planner ツールを実行する場合は、複数回にわたってツールを実行してください。特定の時点で実行されている処理に伴う変更の頻度によって、達成スループットの値は変化します。

企業による Azure Site Recovery のデプロイでは、常に [ExpressRoute](https://aka.ms/expressroute) を使用することをお勧めします。

###<a name="required-azure-storage-accounts"></a>Required Azure Storage Accounts (必要な Azure Storage アカウント)
このグラフは、すべての適合仮想マシンを保護するうえで必要な Azure Storage アカウントの総数です (Standard および Premium)。  使用すべきストレージ アカウントを仮想マシンごとに把握するには、[[Recommended VM placement plan (推奨 VM 配置プラン)]](site-recovery-deployment-planner.md#vm-storage-placement) をクリックしてください。  

![Deployment Planner](./media/site-recovery-deployment-planner/required-azure-storage-accounts.png)

###<a name="required-number-of-azure-cores"></a>Required Number of Azure Cores (必要な Azure コア数)
適合するすべての仮想マシンのフェールオーバー (またはテスト フェールオーバー) 前にプロビジョニングされるコアの総数です。 サブスクリプションに使用できる十分なコアがない場合、フェールオーバー (またはテスト フェールオーバー) 時に Azure Site Recovery が仮想マシンを作成できません。

![Deployment Planner](./media/site-recovery-deployment-planner/required-number-of-azure-cores.png)

###<a name="required-on-premises-infrastructure"></a>Required On-premises Infrastructure (必要なオンプレミス インフラストラクチャ)
適合するすべての仮想マシンを保護するために必要な構成サーバーと追加のプロセス サーバーの総数です。 最大構成でサポートされる[制限](https://aka.ms/asr-v2a-on-prem-components)に基づいて、追加のサーバーが提案されます。制限には、1 日あたりの変更頻度と保護対象仮想マシンの最大数 (仮想マシンごとに平均&3; つのディスクを想定) があり、構成サーバーまたは追加のプロセス サーバーで、最初に到達した方の制限が適用されます。 1 日あたりの総変更頻度と保護対象ディスクの総数の詳細については、[[Input (入力)]](site-recovery-deployment-planner.md#input) シートに記載されています。

![Deployment Planner](./media/site-recovery-deployment-planner/required-on-premises-infrastructure.png)

###<a name="what-if-analysis"></a>What-if 分析
What-if 分析は、プロビジョニングする帯域幅を引き下げて、必要な RPO を 90% の時間だけ満たすようにしたとき、プロファイリング期間中、おおよそ何件の違反が発生するかを可能性として提示するものです。 RPO 違反は日々生じることが考えられ、グラフにはそれぞれの日のピーク RPO が表示されます。
この分析結果を見て、RPO 違反がすべての日にわたって存在しているかどうか、また、帯域幅を&100;% から引き下げた場合の&1; 日のピーク RPO が許容範囲内にあるかどうかを判断することができます。 許容範囲内であれば、レプリケーションに割り当てる帯域幅を引き下げることができます。そうでなければ、必要な RPO を 100% の時間満たすことのできる水準まで帯域幅を引き上げます。

![Deployment Planner](./media/site-recovery-deployment-planner/what-if-analysis.png)

###<a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (初回レプリケーションの推奨 VM バッチ サイズ)
このセクションには、必要な RPO を 100% の時間満たすことのできる推奨帯域幅をプロビジョニングした状態で、初回レプリケーションを 72 時間以内 (この値はレポートの生成時に GoalToCompleteIR パラメーターを使用して変更可能) で完了するという前提で保護することができる仮想マシンの推奨台数が表示されます。  グラフには、適合するすべての仮想マシンについて検出された平均仮想マシン サイズに基づき、初回レプリケーションを 72 時間以内に完了する場合の、帯域幅値の範囲と算出された仮想マシンのバッチ サイズ カウントが表示されます。  

Public Preview のレポートでは、どの仮想マシンをバッチに含めるべきかは明記されません。 [Compatible VMs (適合 VM)] シートに示されたディスク サイズをもとに、それぞれの仮想マシンのサイズを調べ、1 つのバッチに含める仮想マシンを選択するか、既知のワークロード特性に基づいて仮想マシンを選択してください。  初回レプリケーションの完了時間は、仮想マシンの実際のディスク サイズや使用済みディスク領域、ネットワーク スループットに比例して変化します。

![Deployment Planner](./media/site-recovery-deployment-planner/recommended-vm-batch-size.png)

###<a name="growth-factor-and-percentile-values-used"></a>増加率と使用パーセンタイル値
このセクションのシートの一番下には、プロファイリング対象仮想マシンに対するすべてのパフォーマンス カウンターのパーセンタイル値 (既定では 95 パーセンタイル) と、すべての計算に使用される増加率 (%) (既定では 30%) が表示されます。

![Deployment Planner](./media/site-recovery-deployment-planner/max-iops-and-data-churn-setting.png)

##<a name="recommendations-with-available-bandwidth-as-input"></a>使用可能な帯域幅の入力に関する推奨事項

![Deployment Planner](./media/site-recovery-deployment-planner/profiling-overview-bandwidth-input.png)

Azure Site Recovery のレプリケーション用にプロビジョニングできる帯域幅 (Mbps) の上限があらかじめわかっているケースもあるでしょう。 Deployment Planner ツールでは、使用可能な帯域幅を (レポートの生成中に -Bandwidth パラメーターで) 入力し、達成可能な RPO (分) を取得することができます。 こうして得た達成可能 RPO 値をもとに、帯域幅を追加でプロビジョニングすべきか、または障害復旧ソリューションとしてその RPO で問題がないかを判断することができます。

![Deployment Planner](./media/site-recovery-deployment-planner/achievable-rpos.png)

##<a name="input"></a>入力
[Input (入力)] ページには、プロファイリングの対象となった VMware 環境の概要が表示されます。

![Deployment Planner](./media/site-recovery-deployment-planner/Input.png)

**[Start Date (開始日)]/[End Date (終了日)]**: レポートの生成時に考慮されたプロファイリング データの開始日と終了日です。 既定では、プロファイリングを開始した日付が開始日に、プロファイリングを終了した日付が終了日になります。  この値は、レポートの生成時に StartDate パラメーターと EndDate パラメーターで指定できます。 [Start Date (開始日)] と [End Date (終了日)]: レポートの生成時に考慮されたプロファイリング データの開始日と終了日です。 既定では、プロファイリングを開始した日付が開始日に、プロファイリングを終了した日付が終了日になります。  この値は、レポートの生成時に StartDate パラメーターと EndDate パラメーターで指定できます。

**[Total number of profiling days (プロファイリングの合計日数)]**: レポートの生成対象期間 (開始日から終了日まで) におけるプロファイリングの合計日数です。 [Total number of profiling days (プロファイリングの合計日数)]: レポートの生成対象期間 (開始日から終了日まで) におけるプロファイリングの合計日数です。

**[Number of compatible virtual machines (適合する仮想マシンの数)]**: 適合する (必要なネットワーク帯域幅、Azure Storage アカウント数、Microsoft Azure コア数、構成サーバー数、追加プロセス サーバー数が計算される) 仮想マシンの総数です。
[Total number of disks across all compatible virtual machines (すべての適合仮想マシンにおけるディスクの総数)] は、適合するすべての仮想マシンにおけるディスクの総数です。 この数は、デプロイ後の環境で使用する構成サーバー数と追加プロセス サーバー数を決定するための入力値の&1; つとして使用されます。

**[Average number of disks per compatible virtual machine (適合する仮想マシン&1; 台あたりの平均ディスク数)]**: 適合するすべての仮想マシンから割り出される平均ディスク数です。

**[Average disk size (GB) (平均ディスク サイズ (GB))]**: 適合するすべての仮想マシンから割り出される平均ディスク サイズです。

**[Desired RPO (minutes) (必要な RPO (分))]**: 必要な帯域幅の見積もりに使用された RPO です。既定の RPO またはレポートの生成時に "DesiredRPO" パラメーターに指定された値が該当します。

**[Desired bandwidth (Mbps) (必要な帯域幅 (Mbps))]**: レポートの生成時に、達成可能な RPO を見積もる目的で "Bandwidth" パラメーターに指定した値です。

**[Observed typical data churn per day (GB) (観察された&1; 日あたりの標準的なデータの変更頻度 (GB))]**: プロファイリングの全日数にわたって観察されたデータ変更頻度の平均値です。 この数は、デプロイ後の環境で使用する構成サーバー数と追加プロセス サーバー数を決定するための入力値の&1; つとして使用されます。


##<a name="vm-storage-placement"></a>VM-Storage placement (VM<->ストレージの配置)

![Deployment Planner](./media/site-recovery-deployment-planner/vm-storage-placement.png)

**[Disk Storage Type (ディスク ストレージの種類)]**: [VMs to Place (配置する VM)] 列に示されている該当仮想マシンをすべてレプリケートするために使用される "Standard" または "Premium" の Azure Storage アカウントです。

**[Suggested Prefix (推奨プレフィックス)]**: Azure Storage アカウントの名前に使用できる&3; 文字のプレフィックスです。 独自のプレフィックスを使用することもできますが、ツールからは、[Azure Storage アカウントのパーティションの名前付け規則](https://aka.ms/storage-performance-checklist)に準拠したプレフィックスが提示されます。

**[Suggested Account Name (推奨アカウント名)]**: 推奨されるプレフィックスを付けた場合の Azure Storage アカウントの名前が表示されます。 < > で囲まれた名前は、カスタムの入力値に置き換えてください。

**[Log Storage Account (ログ ストレージ アカウント)]**: レプリケーションのログはすべて Standard Azure Storage アカウントに格納されます。 レプリケーション先が Premium Azure Storage アカウントである仮想マシンについては、ログの格納用として別途 Standard Azure Storage アカウントをプロビジョニングする必要があります。 1 つの Standard ログ ストレージ アカウントを複数の Premium レプリケーション ストレージ アカウントで使用することができます。 レプリケーション先が Standard ストレージ アカウントである仮想マシンでは、同じストレージ アカウントがログに使用されます。

**[Suggested Log Account Name (推奨ログ アカウント名)]**: 推奨されるプレフィックスを付けた場合の Azure Storage アカウントの名前が表示されます。 < > で囲まれた名前は、カスタムの入力値に置き換えてください。

**[Placement Summary (配置の概要)]**: レプリケーションとフェールオーバー (テスト フェールオーバーを含む) 時に Azure Storage アカウントにかかる仮想マシンの総負荷の概要が表示されます。 たとえば、Azure Storage アカウントに関連付けられた仮想マシンの総数や、その Azure Storage アカウントに配置されている全仮想マシンにおける読み取り/書き込み IOPS の合計、書き込み (レプリケーション) IOPS の合計、全ディスクの合計プロビジョニング サイズ、ディスクの総数が表示されます。

**[Virtual Machines to Place (配置する仮想マシン)]**: パフォーマンスと稼働率を最大限に引き出すために、指定の Azure Storage アカウントに配置すべき全仮想マシンが一覧表示されます。

## <a name="compatible-vms"></a>Compatible VMs (適合 VM)
![Deployment Planner](./media/site-recovery-deployment-planner/compatible-vms.png)

**[VM 名]**: レポートの生成時に VMListFile で使った仮想マシンの名前または IP アドレスです。 また、仮想マシンにアタッチされたディスク (VMDK) もこの列に一覧表示されます。 VCenter 上の仮想マシンの名前または IP アドレスに重複がある場合、各仮想マシンを区別するために ESXi ホスト名が一緒に表示されます。 一覧表示されている ESXi ホストは、プロファイリング期間中にツールが仮想マシンを最初に検出したとき、その仮想マシンが配置されていたホストです。

**[VM Compatibility (VM 適合性)]**: Yes / Yes* の&2; つの値があります。変更頻度が高く IOPS が P20 または P30 カテゴリのディスクを備えた [Premium Azure Storage](https://aka.ms/premium-storage-workload) が適していることをプロファイリング結果は示しているものの、ディスクのサイズが原因で、それよりも低い P10 または P20 にマッピングされている仮想マシンには、Yes* が表示されます。 Azure Storage では、Premium Storage のディスク タイプが、そのサイズに基づいて決定されます (128 GB 未満の場合は P10、128 ～ 512 GB の場合は P20、512 GB ～ 1023 GB の場合は P30)。 したがって、ディスクのワークロード特性上は P20 または P30 に分類されるものの、サイズ上はそれよりも低い Premium Storage ディスク タイプに対応している仮想マシンは、Deployment Planner ツールによって Yes* として表示されます。そのうえで、推奨される適切な Premium Storage ディスク タイプに合わせてレプリケーション元のディスク サイズを変更するか、またはレプリケーション先のディスク タイプをフェールオーバー後に変更するように促されます。
[ストレージの種類] には、Standard と Premium があります。

**[Suggested Prefix (推奨プレフィックス)]**: Azure Storage アカウントの&3; 文字のプレフィックスです。

**[Storage Account (ストレージ アカウント)]**: 推奨プレフィックスを使用した名前です。

**[R/W IOPS (with Growth Factor) (読み取り/書き込みの IOPS (増加率を考慮))]**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対するピーク ワークロードの IOPS (既定では 95 パーセンタイル) です。 仮想マシンのトータルな読み取り/書き込み IOPS は、必ずしもその個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。これは仮想マシンの読み取り/書き込みのピーク IOPS が、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークであるためです。

**[Data Churn in Mbps (with Growth Factor) (データの変更頻度 (Mbps) (増加率を考慮))]**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 仮想マシンの全体的なデータ変更頻度は、必ずしもその個々のディスクのデータ変更頻度を足した値になるとは限りません。これは仮想マシンのデータ変更頻度のピークが、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークであるためです。

**[Azure VM Size (Azure VM サイズ)]**: 対象のオンプレミス仮想マシンに使用する Azure コンピューティングとして理想的な仮想マシン サイズです。 この判断は、オンプレミス仮想マシンのメモリ、ディスク数/コア数/NIC 数、読み取り/書き込みの IOPS に基づいて行われます。必ずオンプレミスの仮想マシンが備えているこうした特性をすべて満たした最小の Azure 仮想マシン サイズが提案されます。

**[Number of Disks (ディスク数)]**: 仮想マシンのディスク (VMDK) の総数です。

**[Disk size (GB) (ディスク サイズ (GB))]**: 仮想マシンの全ディスクの合計プロビジョニング サイズです。 仮想マシンに含まれる個々のディスクのサイズも表示されます。

**[Cores (コア)]**: 仮想マシンに搭載されている CPU コアの数です。

**[Memory (MB) (メモリ (MB))]**: 仮想マシンに搭載されている RAM です。

**[NICs (NIC)]**: 仮想マシンに搭載されている NIC の数です。

##<a name="incompatible-vms"></a>Incompatible VMs (不適合 VM)

![Deployment Planner](./media/site-recovery-deployment-planner/incompatible-vms.png)

**[VM 名]**: レポートの生成時に VMListFile で使った仮想マシンの名前または IP アドレスです。 また、仮想マシンにアタッチされたディスク (VMDK) もこの列に一覧表示されます。 VCenter 上の仮想マシンの名前または IP アドレスに重複がある場合、各仮想マシンを区別するために ESXi ホスト名が一緒に表示されます。 一覧表示されている ESXi ホストは、プロファイリング期間中にツールが仮想マシンを最初に検出したとき、その仮想マシンが配置されていたホストです。

**[VM Compatibility (VM 適合性)]**: 指定された仮想マシンが、Azure Site Recovery での使用に不適合である理由が表示されます。 理由は仮想マシンの不適合ディスクごとに記述され、公開されている Azure Storage の[制限](https://aka.ms/azure-storage-scalbility-performance)に基づく次のいずれかの状況に該当します。

* ディスク サイズが 1,023 GB を超えている。Azure Storage では現在、1 TB を超えるディスク サイズがサポートされません。
* VM サイズの合計 (レプリケーション + テスト フェールオーバー) が、サポートされている Azure Storage アカウントの上限サイズ (35 TB) を超えている。これは通常、仮想マシンにあるいずれか&1; 台のディスクのパフォーマンス特性が、Standard ストレージの Microsoft Azure / Azure Site Recovery でサポートされる上限を超えているために、仮想マシンが Premium Storage の領域に分類されるようなケースで発生します。 一方、Premium Azure Storage アカウントでサポートされる最大サイズは 35 TB です。保護対象となる単一の仮想マシンを、複数のストレージ アカウントにまたがって保護することはできません。 また、保護対象となる仮想マシンで実行される TFO (テスト フェールオーバー) は、レプリケーション処理と同じストレージ アカウントで実行されることに注意してください。レプリケーションの進行と同時にテスト フェールオーバーが正常完了するためには、対象となるディスク サイズの 2 倍の容量をプロビジョニングする必要があります。
* レプリケーション元の IOPS が、Azure Storage でサポートされている IOPS の上限 (ディスクあたり 5,000 IOPS) を超えている。
* レプリケーション元の IOPS が、Azure Storage でサポートされている IOPS の上限 (VM あたり 80,000 IOPS) を超えている。
* 平均データ変更頻度が、Azure Site Recovery でサポートされているデータ変更頻度の上限 (ディスクの平均 IO サイズで 10 MBps) を超えている。
* VM 上の全ディスクにおけるトータルなデータ変更頻度が、Azure Site Recovery でサポートされるデータ変更頻度の上限 (VM あたり 54 MBps) を超えている。
* 平均実効書き込み IOPS が、Azure Site Recovery でサポートされるディスク IOPS の上限 (840) を超えている。
* 算出されたスナップショット ストレージが、スナップショット ストレージに関してサポートされている上限 (10 TB) を超えている。

**[R/W IOPS (with Growth Factor) (読み取り/書き込みの IOPS (増加率を考慮))]**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対するピーク ワークロードの IOPS (既定では 95 パーセンタイル) です。 仮想マシンのトータルな読み取り/書き込み IOPS は、必ずしもその個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。これは仮想マシンの読み取り/書き込みのピーク IOPS が、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークであるためです。

**[Data Churn in Mbps (with Growth Factor) (データの変更頻度 (Mbps) (増加率を考慮))]**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 仮想マシンの全体的なデータ変更頻度は、必ずしもその個々のディスクのデータ変更頻度を足した値になるとは限りません。これは仮想マシンのデータ変更頻度のピークが、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークであるためです。

**[Number of Disks (ディスク数)]**: 仮想マシンのディスク (VMDK) の総数です。

**[Disk size (GB) (ディスク サイズ (GB))]**: 仮想マシンの全ディスクの合計プロビジョニング サイズです。 仮想マシンに含まれる個々のディスクのサイズも表示されます。

**[Cores (コア)]**: 仮想マシンに搭載されている CPU コアの数です。

**[Memory (MB) (メモリ (MB))]**: 仮想マシンに搭載されている RAM です。

**[NICs (NIC)]**: 仮想マシンに搭載されている NIC の数です。


##<a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限

**レプリケーション先のストレージ** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの&1; 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり)&168; GB
Premium P10 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり)&168; GB
Premium P10 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり)&336; GB
Premium P10 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり)&672; GB
Premium P20/P30 ディスク | 8 KB    | 5 MB/s | (ディスクあたり)&421; GB
Premium P20/P30 ディスク | 16 KB 以上 |10 MB/s    | (ディスクあたり)&842; GB


前述の数値は、IO のオーバーラップを 30% とした場合の平均値です。 Azure Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。 上記の数値には、標準的なバックログとして約 5 分が想定されています。つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。

上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なります。 理想的な結果を得るために、デプロイ計画後も必ず、テスト フェールオーバーを使用してアプリケーションのテストを徹底し、パフォーマンスの真の姿を把握することをお勧めします。

## <a name="how-to-update-the-deployment-planner"></a>Deployment Planner を更新する方法
最新バージョンの Azure Site Recovery Deployment Planner を[ダウンロード](site-recovery-deployment-planner.md#download)します。 zip ファイルを実行するサーバーにコピーします。 zip ファイルを解凍します。
既に以前のバージョンの Deployment Planner を所持しており、プロファイリングが実行中である場合は、新しいバージョンにプロファイリングの修正がある場合を除き、そのプロファイリングを停止する必要はありません。 リリースにプロファイル コンポーネントの修正プログラムが含まれている場合、以前のバージョンを使用したプロファイリングを停止し、新しいバージョンを使用してプロファイリングを再起動することをお勧めします。 新しいバージョンを使用してプロファイリングを開始するときは、同じ出力ディレクトリ パスを渡す必要があることに注意してください。これにより、ツールがプロファイリング データを既存のファイルに追加できるようになり、プロファイリング データの完全なセットがレポートの生成に使用されるようになります。 異なる出力ディレクトリを渡すと、新しいファイルが作成され、古いプロファイリング データがレポートの生成で使用できなくなります。<br> どの更新も、zip ファイルを使用した累積的な更新プログラムです。 使用にあたり、以前のバージョンのフォルダーに新しいバージョンのファイルをコピーする必要はありません。 新しいフォルダーを使用できます。


##<a name="version-history"></a>バージョン履歴
### <a name="11"></a>1.1
更新日: 2017 年 3 月 9 日 <br>

次の問題が修正されました。<br>

* 同じ名前または IP アドレスの複数の仮想マシンが異なる ESXi ホストにまたがって vCenter に存在する場合に、仮想マシンをプロファイリングすることができない。<br>
* 適合 VM シートと不適合 VM シートのコピーと検索が無効。


### <a name="10"></a>1.0 
更新日: 2017 年 2 月 23 日 

Azure Site Recovery Deployment Planner Public Preview 1.0 には、以下に示す既知の問題があります。これらの問題は今後の更新プログラムで解決される予定です。

* このツールの利用は VMware から Azure へのレプリケーション シナリオに限られます。Hyper-V から Azure へのデプロイには利用できません。 Hyper-V から Azure へのシナリオでは、[Hyper-V Capacity Planner ツール](./site-recovery-capacity-planning-for-hyper-v-replication.md)をご利用ください。
* 米国政府および中国の Microsoft Azure リージョンでは、GetThroughput 操作がサポートされていません。
* 同じ名前または IP アドレスの複数の仮想マシンが異なる ESXi ホストにまたがって vCenter に存在する場合、このツールで仮想マシンをプロファイリングすることはできません。 このバージョンでは、VMListFile 内の重複する仮想マシン名または IP アドレスに対するプロファイリングがスキップされます。 回避策として、vCenter サーバーではなく ESXi ホストを対象にして仮想マシンをプロファイリングします。 ESXi ホストごとに&1; つのインスタンスを実行する必要があります。

