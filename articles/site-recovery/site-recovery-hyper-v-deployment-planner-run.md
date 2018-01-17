---
title: "Hyper-V から Azure 用の Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "この記事では、Azure Site Recovery Deployment Planner の実行モードについて、Hyper-V から Azure へのシナリオを想定して説明します。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/02/2017
ms.author: nisoneji
ms.openlocfilehash: 5c7ff99c2f67f82f9a7d605d9960960f84e96900
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2018
---
# <a name="run-azure-site-recovery-deployment-planner-for-hyper-v-to-azure"></a>Azure Site Recovery Deployment Planner の実行 (Hyper-V から Azure)

## <a name="modes-of-running-deployment-planner"></a>Deployment Planner の実行モード
このコマンドライン ツール (ASRDeploymentPlanner.exe) は、次の 4 とおりのモードで実行できます。 
1.  [VM リストの取得](#get-vm-list-for-profiling-hyper-v-vms)
2.  [プロファイリング](#profile-hyper-v-vms)
3.  [レポートの生成](#generate-report)
4.  [スループットの取得](#get-throughput)

まずツールを実行して、1 つまたは複数の Hyper-V ホストから VM のリストを取得します。  次にプロファイリング モードでツールを実行し、VM のデータ変更頻度と IOPS を取得します。 そのうえでツールを実行してレポートを生成し、ネットワーク帯域幅やストレージの要件を把握してください。

## <a name="get-vm-list-for-profiling-hyper-v-vms"></a>Hyper-V VM のプロファイリング対象リストの取得
まず、プロファイリングの対象となる VM のリストを用意する必要があります。 Deployment Planner ツールの GetVMList モードを使用して、複数の Hyper-V ホストに存在する VM のリストを 1 回のコマンドで生成します。 完全なリストを生成したら、プロファイルする必要のない VM は出力ファイルから削除してかまいません。 その出力ファイルを、その他すべての操作 (プロファイリング、レポート生成、スループット取得) に使用することになります。

ツールの実行対象として Hyper-V クラスター、スタンドアロンの Hyper-V ホスト、またはその両方を指定して、VM リストを生成することができます。

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、GetVMList モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 
```
ASRDeploymentPlanner.exe -Operation GetVMList /?
```
| パラメーター名 | [説明] |
|---|---|
| -Operation | GetVMList |
| -User | Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。|
|-ServerListFile | プロファイリングの対象 VM を含む一連のサーバーが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 このファイルの各行には、次のいずれかの情報が記述されている必要があります。<ul><li>Hyper-V ホストの名前または IP アドレス</li><li>Hyper-V クラスターの名前または IP アドレス</li></ul><br>例: "ServerList.txt" ファイルに次のサーバーを記述します。<ul><li>Host_1</li><li>10.8.59.27</li><li>Cluster_1</li><li>Host_2</li>|
| -Directory|(省略可) この操作の実行中に生成されたデータの格納先となる UNC (汎用名前付け規則) パスまたはローカル ディレクトリ パス。 指定しなかった場合、現在のパス下の "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。|
|-OutputFile| (省略可) 指定された Hyper-V サーバーからフェッチされた VM のリストが保存されるファイル。 名前を指定しなかった場合は、"VMList.txt" に詳細が保存されます。  このファイルを使用してプロファイリングを開始します。プロファイリングする必要のない VM は、あらかじめファイルから削除してください。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。   パスワードをパラメーターとして指定しなかった場合は、後でコマンドの実行時に入力を求められます。|

### <a name="how-does-getvmlist-discovery-work"></a>GetVMList による検出の動作
**Hyper-V クラスター**: サーバー リスト ファイルに Hyper-V クラスターの名前を指定した場合、該当するクラスターからすべての Hyper-V ノードが検出され、その各 Hyper-V ホストに存在する VM が取得されます。

**Hyper-V ホスト**: Hyper-V ホストの名前を指定した場合は、まずそれがクラスターに属しているかどうかがチェックされます。 該当する場合は、クラスターに属しているノードがフェッチされます。 その後、各 Hyper-V ホストから VM が取得されます。 

プロファイリングの対象となる VM のフレンドリ名または IP アドレスを手動でファイルに列挙してもかまいません。

出力ファイルをメモ帳で開き、プロファイリングするすべての VM の名前を別のファイル (ProfileVMList.txt など) にコピーします。VM の名前は 1 行につき 1 つです。 このファイルは、他のあらゆる操作 (プロファイリング、レポート生成、スループット取得) で、ツールの -VMListFile パラメーターへの入力として使用されます。

### <a name="example-1-to-store-the-list-of-vms-in-a-file"></a>例 1: VM のリストをファイルに格納するには
```
ASRDeploymentPlanner.exe -Operation GetVMlist -ServerListFile “E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1 -OutputFile "E:\Hyper-V_ProfiledData\VMListFile.txt"
```

### <a name="example-2-to-store-the-list-of-vms-at-the-default-location-ie--directory-path"></a>例 2: VM のリストを既定の場所 (-Directory パス) に保存するには
```
ASRDeploymentPlanner.exe -Operation GetVMList -Directory "E:\Hyper-V_ProfiledData" -ServerListFile "E:\Hyper-V_ProfiledData\ServerList.txt" -User Hyper-VUser1
```

## <a name="profile-hyper-v-vms"></a>Hyper-V VM のプロファイリング
プロファイリング モードでは、Deployment Planner ツールが各 Hyper-V ホストに接続して VM に関するパフォーマンス データを収集します。 

* プロファイリングが運用環境の VM のパフォーマンスに影響を及ぼすことはありません (運用環境の VM に対して直接接続が確立されることはありません)。 Hyper-V ホストから、あらゆるパフォーマンス データが収集されます。
* このツールは、Hyper-V ホストを 15 秒おきに照会することでプロファイリングの精度を確保し、毎分のパフォーマンス カウンター データの平均値を保存します。
* クラスターのノード間における VM の移行とホスト内のストレージの移行がシームレスに処理されます。

### <a name="get-vm-list-to-profile"></a>プロファイリング対象 VM リストの取得
プロファイリングする VM のリストを作成する方法については、[GetVMList](#get-vm-list-for-profiling-hyper-v-vms) 操作を参照してください。

プロファイリングの対象となる VM のリストを作成したら、プロファイリング モードでツールを実行できます。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、プロファイリング モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 このツールは、VMware から Azure へのレプリケーションと Hyper-V から Azure へのレプリケーションの両方のシナリオに共通です。 Hyper-V では、次のパラメーターを適用できます。 
```
ASRDeploymentPlanner.exe -Operation StartProfiling /?
```
| パラメーター名 | [説明] |
|---|---|
| -Operation | StartProfiling |
| -User | Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。|
| -VMListFile | プロファイリングの対象となる VM のリストを含むファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合、サーバー名 (または IP アドレス) + 区切り文字 (\) + VM 名を 1 行につき 1 つ記述する必要があります。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<ul>たとえば "VMList.txt" ファイルに、次のように VM を記述することができます。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-NoOfMinutesToProfile|プロファイリングを実行する時間 (分)。 最小値は 30 分です。|
|-NoOfHoursToProfile|プロファイリングを実行する時間 (時)。|
|-NoOfDaysToProfile |プロファイリングを実行する日数。 プロファイリングの実行期間は 7 日間より長くすることをお勧めします。それだけの期間があれば、実際の環境のワークロード パターンを十分に観察し、正確な情報を把握することができます。|
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング中に生成されたプロファイリング データの格納先となる UNC (汎用名前付け規則) パスまたはローカル ディレクトリ パス。 指定しなかった場合、現在のパス下の "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。 ここで指定しなかった場合は、後でコマンドの実行時に入力を求められます。|
|-StorageAccountName|(省略可) オンプレミスから Azure へのデータのレプリケーションに関して達成可能なスループットの調査対象となるストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされてスループットが計算されます。|
|-StorageAccountKey|(省略可) ストレージ アカウントにアクセスするためのストレージ アカウント キー。 Azure Portal の [ストレージ アカウント]、[<Storage account name>]、[設定]、[アクセス キー]、[Key1] (クラシック ストレージ アカウントの場合は [プライマリ アクセス キー]) の順に移動します。|
|-Environment|(省略可) レプリケーション先となる Azure Storage アカウント環境。 AzureCloud、AzureUSGovernment、AzureChinaCloud の 3 つのうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先の Azure リージョンが Azure 米国政府機関または Azure China クラウドであるときに使用します。|

VM のプロファイリング期間は 7 日間より長くすることをお勧めします。 変更頻度のパターンが 1 か月間で変動する場合は、変更頻度が最大となる週をプロファイル期間とすることをお勧めします。 理想的なプロファイル期間は 31 日で、そうすれば、よりよいアドバイスが得られます。 プロファイリング期間中は ASRDeploymentPlanner.exe が実行状態となります。 ツールには、プロファイリングの時間を日数で入力します。 ツールの簡単なテストや概念実証が目的であれば、プロファイル期間は数時間や数分でもかまいません。 プロファイリング期間として許容される最短時間は 30 分です。 

Hyper-V サーバーから Azure へのレプリケーション時に Azure Site Recovery で得られるスループットの調査対象として、プロファイリング中、必要に応じてストレージ アカウントの名前とキーを指定することができます。 プロファイリング時にストレージ アカウントの名前とキーが渡されないと、達成可能なスループットは計算されません。

複数の VM グループを対象として、Deployment Planner ツールのインスタンスを複数実行することができます。 その場合、プロファイリングの対象となるグループ内やグループ間で、同じ VM 名が重複しないようにしてください。 たとえば 10 台の VM (VM1 ～ VM10) をプロファイリングし、数日後に別の 5 台の VM (VM11 ～ VM15) をプロファイリングする必要が生じたとします。この場合、2 つ目の VM グループ (VM11 ～ VM15) について、別のコマンド ライン コンソールからツールを実行することができます。 1 つ目のプロファイリング インスタンスの対象となっていた VM 名を 2 つ目の VM グループに含めることは避けてください。または 2 回目の実行時には、1 回目とは異なる出力ディレクトリを使用してください。 Deployment Planner ツールの 2 つのインスタンスで、同じ出力ディレクトリに対して同じ VM をプロファイリングした場合、生成されるレポートは正確ではなくなります。 

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、*ASRDeploymentPlanner.exe.config* ファイルの MaxVMsSupported キー値を変更します。
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```
既定の設定では、たとえば 1,500 台の VM をプロファイリングする場合に 2 つの VMList.txt ファイルを作成します。 1 つのリストには 1,000 台の VM が含まれており、もう 1 つのリストには 500 台の VM が含まれています。 一方に VMList1.txt を使用し、もう一方に VMList2.txt を使用して、ASR Deployment Planner で 2 つのインスタンスを実行します。 両方の VMList VM のプロファイリング データを格納するために、同じディレクトリ パスを使用できます。 

ハードウェア構成 (特にレポートを生成するためにツールが実行されるサーバーの RAM サイズ) によっては、メモリ不足で操作が失敗する可能性があることが確認されています。 優れたハードウェアを使用している場合、MaxVMsSupported をより高い値に変更できます。  

VM の構成は、プロファイリング処理の開始時に 1 回だけ捕捉されて、VMDetailList.xml というファイルに保存されます。 この情報は、レポートの生成時に使用されます。 プロファイリングの開始から終了までの間に生じた VM 構成の変化 (コア数、ディスク数、NIC 数が増えるなど) は捕捉されません。 プロファイリングの途中で、その対象となる VM の構成が変わった場合は、以下の回避策によって、レポートの生成時に最新の VM 情報を取得することができます。

* VMdetailList.xml をバックアップし、現在の場所からこのファイルを削除します。
* レポートの生成時に -User 引数と -Password 引数を指定します。

プロファイリング コマンドによって、いくつかのファイルがプロファイリング ディレクトリに生成されます。 これらのファイルは一切削除しないでください。削除すると、レポートの生成に影響が生じます。

### <a name="examples"></a>例
#### <a name="example-1-profile-vms-for-30-days-and-find-the-throughput-from-on-premises-to-azure"></a>例 1: VM を 30 日間プロファイリングし、オンプレミスから Azure へのレプリケーションのスループットを調査する
```
ASRDeploymentPlanner.exe -Operation StartProfiling -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile 30 -User Contoso\HyperVUser1 -StorageAccountName  asrspfarm1 -StorageAccountKey Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

#### <a name="example-2-profile-vms-for-15-days"></a>例 2: VM を 15 日間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\vCenter1_ProfiledData\ProfileVMList1.txt”  -NoOfDaysToProfile  15  -User contoso\HypreVUser1
```

#### <a name="example-3-profile-vms-for-60-minutes-for-a-quick-test-of-the-tool"></a>例 3: ツールの簡単なテストを行うために VM を 60 分間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfMinutesToProfile 60 -User Contoso\HyperVUser1
```

#### <a name="example-4-profile-vms-for-2-hours-for-a-proof-of-concept"></a>例 4: 概念実証のために VM を 2 時間プロファイリングする
```
ASRDeploymentPlanner.exe -Operation StartProfiling -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -NoOfHoursToProfile 2 -User Contoso\HyperVUser1
```

>注
>
* ツールを実行しているサーバーが再起動されるかクラッシュした場合、または Ctrl + C キーでツールを終了した場合、プロファイリング データは保持されます。 ただし直近 15 分間のプロファイリング データは失われる可能性があります。 そのような場合は、サーバーの再起動後に、プロファイリング モードでもう一度ツールを実行する必要があります。
* ストレージ アカウントの名前とキーを渡したときは、プロファイリングの最後の段階でスループットが測定されます。 プロファイリングが完了する前にツールを終了した場合、スループットは計算されません。 レポートが生成される前にスループットを調べるために、コマンド ライン コンソールから GetThroughput 操作を実行してください。 そのようにしないと、生成されるレポートにはスループットの情報が出力されません。
* Azure Site Recovery では、iSCSI ディスクやパススルー ディスクを含んだ VM がサポートされません。 しかし、VM に接続された iSCSI ディスクやパススルー ディスクをこのツールで検出してプロファイリングすることはできません。

## <a name="generate-report"></a>レポートの生成
Deployment Planner ツールでは、デプロイの推奨情報をすべてまとめたマクロ有効 Microsoft Excel ファイル (XLSM ファイル) がレポートの出力結果として生成されます。 このレポートは、指定されたディレクトリに DeploymentPlannerReport_<unique numeric identifier>.xlsm という名前で格納されます。
プロファイリングが完了したら、レポート生成モードでツールを実行できます。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター
次の表は、レポート生成モードでツールを実行するための必須のパラメーターと省略可能なパラメーターの一覧です。 このツールは、VMware から Azure へのレプリケーションと Hyper-V から Azure へのレプリケーションの両方のシナリオに共通です。 Hyper-V では、次のパラメーターを適用できます。
```
ASRDeploymentPlanner.exe -Operation GenerateReport /?
```
| パラメーター名 | [説明] |
|---|---|
| -Operation | GenerateReport |
|-VMListFile | レポートを生成するプロファイリング対象 VM のリストが記述されたファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合、サーバー名 (または IP アドレス) + 区切り文字 (\) + VM 名を 1 行につき 1 つ記述する必要があります。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<ul>たとえば "VMList.txt" ファイルに、次のように VM を記述することができます。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる汎用名前付け規則 (UNC) パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 名前を指定しなかった場合、現在のパス下の "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。|
| -User | (省略可) Hyper-V ホストまたは Hyper-V クラスターに接続するためのユーザー名。 ユーザーには管理アクセス権が必要です。<br>レポートに使用する VM の最新の構成情報 (ディスク数、コア数、NIC 数など) を取得するには、ユーザーとパスワードを指定します。 指定しなかった場合は、プロファイリング時に収集された構成情報が使用されます。|
|-Password|(省略可) Hyper-V ホストに接続するためのパスワード。 ここで指定しなかった場合は、後でコマンドの実行時に入力を求められます。|
| -DesiredRPO | (省略可) 必要な RPO (回復ポイントの目標) を分単位で指定します。 既定では 15 分です。|
| -Bandwidth | (省略可) 帯域幅 (Mbps)。 このパラメーターで指定した帯域幅で達成できる RPO が計算されます。 |
| -StartDate | (省略可) 開始日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 *StartDate* は *EndDate* と一緒に指定する必要があります。 StartDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -EndDate | (省略可) 終了日時を MM-DD-YYYY:HH:MM (24 時間形式) で指定します。 *EndDate* は *StartDate* と一緒に指定する必要があります。 EndDate を指定した場合、StartDate から EndDate までの間に収集されたプロファイリング データを対象にレポートが生成されます。 |
| -GrowthFactor | (省略可) 増加率 (%)。 既定値は 30% です。 |
| -UseManagedDisks | (省略可) UseManagedDisks (Yes/No)。 既定値は Yes です。 1 つのストレージ アカウントに配置できる仮想マシンの数は、仮想マシンのフェールオーバー/テスト フェールオーバーが、非管理対象ディスクではなく、管理ディスクに対して実行されることを想定して計算されます。 |
|-SubscriptionId |(省略可) サブスクリプションの GUID。 サブスクリプションやそれに関連付けられているプランに基づき、ターゲット Azure リージョンと通貨を指定して、最新の料金に関するコスト見積もりレポートを生成するには、このパラメーターを使用します。|
|-TargetRegion|(省略可) レプリケーション先となる Azure リージョン。 Azure のコストはリージョンによって異なります。そこで、特定のターゲット Azure リージョンでレポートを生成するために、このパラメーターを使用します。<br>WestUS2 または最近使用したターゲット リージョンが既定値となります。<br>「[サポートされるターゲット リージョン](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)」の一覧を参照してください。|
|-OfferId|(省略可) 指定されたサブスクリプションに関連付けられているプラン。 既定値は MS-AZR-0003P (従量課金) です。|
|-Currency|(省略可) 生成されたレポートでコストの表示に使用する通貨。 米ドル ($) または最近使用した通貨が既定値となります。<br>「[サポートされる通貨](site-recovery-hyper-v-deployment-planner-cost-estimation.md#supported-currencies)」の一覧を参照してください。|

既定では、このツールは最大で 1,000 台の VM をプロファイリングしてレポートを生成するよう構成されています。 上限を変更するには、*ASRDeploymentPlanner.exe.config* ファイルの MaxVMsSupported キー値を変更します。
```
<!-- Maximum number of vms supported-->
<add key="MaxVmsSupported" value="1000"/>
```

### <a name="examples"></a>例
#### <a name="example-1-generate-a-report-with-default-values-when-the-profiled-data-is-on-the-local-drive"></a>例 1: 既定値でレポートを生成する (プロファイリング データがローカル ドライブにある場合)
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-2-generate-a-report-when-the-profiled-data-is-on-a-remote-server"></a>例 2: レポートを生成する (プロファイリング データがリモート サーバーにある場合)
ユーザーには、リモート ディレクトリに対する読み取り/書き込みアクセス権が必要です。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V - -Directory “\\PS1-W2K12R2\Hyper-V_ProfiledData” -VMListFile “\\PS1-W2K12R2\vCenter1_ProfiledData\ProfileVMList1.txt”
```

#### <a name="example-3-generate-a-report-with-a-specific-bandwidth-that-you-will-be-provision-for-the-replication"></a>例 3: レプリケーション用にプロビジョニングする帯域幅を指定してレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -Bandwidth 100
```

#### <a name="example-4-generate-a-report-with-a-5-percent-growth-factor-instead-of-the-default-30-percent"></a>例 4: 増加率を既定値の 30% から 5% に変更してレポートを生成する 
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -GrowthFactor 5
```

#### <a name="example-5-generate-a-report-with-a-subset-of-profiled-data"></a>例 5: 一部のプロファイリング データでレポートを生成する
たとえば、30 日間分のプロファイリング データがあるときに 20 日間のみを対象としてレポートを生成します。
```
ASRDeploymentPlanner.exe -Operation GenerateReport -virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt” -StartDate  01-10-2017:12:30 -EndDate 01-19-2017:12:30
```

#### <a name="example-6-generate-a-report-for-5-minutes-rpo"></a>例 6: RPO を 5 分としてレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -DesiredRPO 5
```

#### <a name="example-7-generate-a-report-for-south-india-azure-region-with-indian-rupee-and-specific-offer-id"></a>例 7: インド ルピーと特定のプラン ID でインド南部 Azure リージョンのレポートを生成する
```
ASRDeploymentPlanner.exe -Operation GenerateReport -Virtualization Hyper-V -Directory “E:\Hyper-V_ProfiledData” -VMListFile “E:\Hyper-V_ProfiledData\ProfileVMList1.txt”  -SubscriptionID 4d19f16b-3e00-4b89-a2ba-8645edf42fe5 -OfferID MS-AZR-0148P -TargetRegion southindia -Currency INR
```


## <a name="percentile-value-used-for-the-calculation"></a>計算に使用されるパーセンタイル値
**プロファイリング時に収集されたパフォーマンス メトリックに対し、レポートの生成時に使用される既定のパーセンタイル値について**

すべての VM において、プロファイリング時に収集される読み取り/書き込みの IOPS、書き込み IOPS、データの変更頻度には、既定で 95 パーセンタイル値が使用されます。 そうすることで、レプリケーション先のストレージ アカウントとレプリケーション元の帯域幅の要件を決定する際に、VM の一時的な事象に起因する瞬間的な 100 パーセンタイルを判断から除外することができます。 そのような一時的な事象としては、1 日 1 回実行されるバックアップ ジョブや定期的なデータベース インデックス作成、分析レポート生成アクティビティなど、持続性のない瞬時性イベントが考えられます。

95 パーセンタイル値を使用することで、実際のワークロード特性の真の姿を捉え、それらのワークロードが Azure で実行されている状態でのベストなパフォーマンスを把握することができます。 通常この値を変更する必要はありません。 あえて値を変更する場合 (たとえば 90 パーセンタイルに下げたい場合) は、既定のフォルダーにある構成ファイル ASRDeploymentPlanner.exe.config を編集、保存すれば、既にあるプロファイリング データで新しいレポートを生成することができます。
```
<add key="WriteIOPSPercentile" value="95" />      
<add key="ReadWriteIOPSPercentile" value="95" />      
<add key="DataChurnPercentile" value="95" />
```

## <a name="growth-factor-considerations"></a>増加率に関する考慮事項
**デプロイ計画時に増加率を考慮すべき理由**
時間の経過と共に使用率が増える可能性を想定し、ワークロード特性の増大を考慮することはきわめて重要です。 いったん保護された状態を確保した後でワークロードの特性が変化した場合、保護に使用するストレージ アカウントを切り替えるためには、一度保護を無効にしてから再度有効にする必要があります。

たとえば現在、VM のレプリケーションが Standard ストレージ アカウントで問題なく行われているとします。 3 か月後には、おそらくさまざまな変化が生じています。

* たとえば、VM で実行されているアプリケーションのユーザー数が増加します。
* そうなれば VM の変更頻度が増えます。その増大に Azure Site Recovery のレプリケーションを追い付かせるためには、Premium Storage への移行が必要になるでしょう。
* その場合、一度保護を無効にしてから、Premium ストレージ アカウントに切り替えて再度、保護を有効にする必要があります。

そのために、デプロイ計画の段階で増加を考慮することが強く推奨されています。既定値は 30% となっていますが、アプリケーションの使用パターンや予想される新規ユーザーについて一番よく知っているのは皆さん自身です。この値は、レポートの生成時に適宜変更できます。 また、同じプロファイリング データで、増加率を変えながら複数のレポートを生成することができるほか、最も効果的なレプリケーション先のストレージとレプリケーション元の帯域幅を特定できます。 

生成された Microsoft Excel レポートには、次の情報が含まれています。

* [On-Premises Summary (オンプレミス サマリー)](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-summary)
* [Recommendations (推奨事項)](site-recovery-hyper-v-deployment-planner-analyze-report.md#recommendations)
* [VM-Storage placement (VM<->ストレージの配置)](site-recovery-hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)
* [Compatible VMs (適合 VM)](site-recovery-hyper-v-deployment-planner-analyze-report.md#compatible-vms)
* [Incompatible VMs (不適合 VM)](site-recovery-hyper-v-deployment-planner-analyze-report.md#incompatible-vms)
* [On-premises Storage Requirement (オンプレミス ストレージ要件)](site-recovery-hyper-v-deployment-planner-analyze-report.md#on-premises-storage-requirement)
* [IR Batching (IR バッチ分割)](site-recovery-hyper-v-deployment-planner-analyze-report.md#initial-replication-batching)
* [Cost Estimation (コスト見積もり)](site-recovery-hyper-v-deployment-planner-cost-estimation.md)

![Deployment Planner レポート](media/site-recovery-hyper-v-deployment-planner-run/deployment-planner-report-h2a.png)


## <a name="get-throughput"></a>スループットの取得
オンプレミスから Azure へのレプリケーション時に Azure Site Recovery が達成できるスループットを見積もるには、GetThroughput モードで Deployment Planner ツールを実行します。 このツールでは、それが実行されているサーバーを起点としてスループットが計算されます。 このサーバーは、保護対象の VM が存在する Hyper-V サーバーであることが理想です。 

### <a name="command-line-parameters"></a>コマンドライン パラメーター 
コマンド ライン コンソールを開いて、Azure Site Recovery のデプロイ プランニング ツール フォルダーに移動します。 以下のパラメーターを指定して ASRDeploymentPlanner.exe を実行してください。
```
ASRDeploymentPlanner.exe -Operation GetThroughput /?
```
 パラメーター名 | [説明] |
|---|---|
| -Operation | GetThroughtput |
|-Virtualization|仮想化の種類を指定します (VMware または Hyper-V)。|
|-Directory|(省略可) プロファイリング データ (プロファイリング中に生成されたファイル) の格納先となる汎用名前付け規則 (UNC) パスまたはローカル ディレクトリ パス。 レポートを生成するには、このデータが必要となります。 名前を指定しなかった場合、現在のパス下の "ProfiledData" という名前のディレクトリが既定のディレクトリとして使用されます。|
| -StorageAccountName | オンプレミスから Azure へのデータのレプリケーションに関して、使用帯域幅の調査に使うストレージ アカウントの名前。 このストレージ アカウントにテスト データがアップロードされて使用帯域幅が計算されます。 |
| -StorageAccountKey | ストレージ アカウントにアクセスするためのストレージ アカウント キー。 Azure Portal の [ストレージ アカウント]、[<*ストレージ アカウント名*>]、[設定]、[アクセス キー]、[Key1] の順に移動します。|
| -VMListFile | 使用帯域幅の計算に関して、プロファイリングの対象となる VM のリストを含んだファイル。 ファイルは、絶対パスまたは相対パスで指定できます。 Hyper-V の場合、このファイルが GetVMList 操作の出力ファイルとして得られます。 このファイルを手動で作成する場合、サーバー名 (または IP アドレス) + 区切り文字 (\) + VM 名を 1 行につき 1 つ記述する必要があります。 このファイルに指定する VM 名は、Hyper-V ホスト上の VM 名と同じであることが必要です。<ul>たとえば "VMList.txt" ファイルに、次のように VM を記述することができます。<ul><li>Host_1\VM_A</li><li>10.8.59.27\VM_B</li><li>Host_2\VM_C</li><ul>|
|-Environment|(省略可) レプリケーション先となる Azure Storage アカウント環境。 AzureCloud、AzureUSGovernment、AzureChinaCloud の 3 つのうち、いずれかの値を指定できます。 既定値は AzureCloud です。 このパラメーターは、レプリケーション先の Azure リージョンが Azure 米国政府機関または Azure China クラウドであるときに使用します。|


Deployment Planner ツールは、指定されたディレクトリに 64 MB の asrvhdfile<#>.vhd ファイル ("#" はファイルの番号) をいくつか作成します。 これらのファイルをストレージ アカウントにアップロードすることによってスループットが調査されます。 これらのファイルはすべて、スループットの測定後にストレージ アカウントとローカル サーバーから削除されます。 スループットの計算中に何らかの理由でツールが終了した場合、ストレージとローカル サーバーからファイルが削除されません。 この場合は、ファイルを手動で削除する必要があります。

スループットは、特定の時点で測定されるものです。そのときと同じ条件下で Azure Site Recovery がレプリケーション時に達成できる最大スループットと考えてください。 たとえば、いずれかのアプリケーションが同じネットワーク上で、それまでよりも多くの帯域幅を使い始めた場合、レプリケーション時の実際のスループットは変化します。 保護されている VM のデータ変更頻度が高いときに GetThroughput 操作を実行した場合、スループットの測定結果に差異が生じます。 さまざまな状況で得られるスループット レベルを把握するために、プロファイリング中はいろいろなタイミングでツールを実行することをお勧めします。 レポートには、最後に測定されたスループットが表示されます。
    
### <a name="example"></a>例
```
ASRDeploymentPlanner.exe -Operation GetThroughput -Virtualization Hyper-V -Directory E:\Hyp-erV_ProfiledData -VMListFile E:\Hyper-V_ProfiledData\ProfileVMList1.txt  -StorageAccountName  asrspfarm1 -StorageAccountKey by8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

注
>
> ストレージと CPU の特性が Hyper-V サーバーと同じサーバーでツールを実行してください。
>
> レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅を設定してください。 適切な帯域幅を設定したにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の作業を行ってください。
>
>  1. Azure Site Recovery のスループットを制限するネットワーク QoS (サービス品質) が存在しているかどうかを確認します。
>
>  2. 物理的にサポートされる最も近い Microsoft Azure リージョンに Azure Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。
>
>  3. ローカル ストレージの特性を確認し、ハードウェアの強化 (例: HDD から SSD など) が可能であるかどうかを調べます。
>

## <a name="next-steps"></a>次の手順
* [生成されたレポートの分析](site-recovery-hyper-v-deployment-planner-analyze-report.md)