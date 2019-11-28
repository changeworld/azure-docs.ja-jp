---
title: Azure Migrate Server Assessment でインポートされたサーバー データを使用してサーバーを評価する
description: Azure Migrate Server Assessment でインポートされたデータを使用して、Azure への移行についてオンプレミス サーバーを評価する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 7bf47731f2a3621e7bbdc1b104d94e97f2d03099
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158655"
---
# <a name="assess-servers-using-imported-data"></a>インポートされたデータを使用してサーバーを評価する

この記事では、オンプレミス サーバーを評価するために、[Azure Migrate:Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) を使用し、CSV を使用してサーバーのメタデータをインポートする方法について説明します。 この評価方法では、評価を作成するために Azure Migrate アプライアンスを設定する必要はありません。 これは、次の場合に便利です。

- アプライアンスを展開する前に、クイック初期評価を作成する必要があります。
- Azure Migrate アプライアンスを組織内に展開することはできません。
- オンプレミス サーバーへのアクセスを許可する資格情報を共有することはできません。
- セキュリティの制約があるため、アプライアンスによって収集されたデータを収集して Azure に送信することはできません。 インポートされたファイルを使用すると、共有するデータを制御できます。多くのデータ (IP アドレスの指定など) は省略可能です。


## <a name="before-you-start"></a>開始する前に

以下の点に注意してください。

- 1 つの CSV ファイルで最大 20,000 台のサーバーを追加できます。
- CSV を使用して Azure Migrate プロジェクトに最大 20,000 台のサーバーを追加できます。
- CSV を使用してサーバー情報を Azure Migrate Server Assessment に複数回アップロードできます。
- 移行についてオンプレミス環境を評価する場合、アプリケーション情報の収集は便利ですが、現在、Azure Migrate Server Assessment ではアプリケーション レベルの評価が実行されず、評価の作成時にアプリケーションが考慮されません。

このチュートリアルでは、以下の内容を学習します。
> [!div class="checklist"]
> * Azure Migrate プロジェクトを設定する。
> * CSV ファイルにサーバー情報を入力します。
> * ファイルをインポートし、サーバー情報を Azure Migrate Server Assessment に追加します。
> * 評価を作成して確認します。

> [!NOTE]
> チュートリアルでは、概念実証をすばやく設定できるように、シナリオの最も簡単なデプロイ パスを示します。 チュートリアルではできるだけ既定のオプションを使用しており、使用可能な設定とパスをすべて示しているわけではありません。 詳細な手順については、ハウツー記事を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/pricing/free-trial/) を作成してください。


## <a name="set-azure-permissions-for-azure-migrate"></a>Azure Migrate の Azure アクセス許可を設定する

ご自分の Azure アカウントには、Azure Migrate プロジェクトを作成するためのアクセス許可が必要です。

1. Azure portal でサブスクリプションを開き、 **[アクセス制御 (IAM)]** を選択します。
2. **[アクセスの確認]** で関連するアカウントを探し、それをクリックしてアクセス許可を表示します。
3. **共同作成者**または**所有者**のアクセス許可を持っている必要があります。
    - 無料の Azure アカウントを作成したばかりであれば、自分のサブスクリプションの所有者になっています。
    - サブスクリプションの所有者でない場合は、所有者と協力してロールを割り当てます。


## <a name="set-up-an-azure-migrate-project"></a>Azure Migrate プロジェクトを設定する

次のように、新しい Azure Migrate プロジェクトを設定します。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。
3. **[概要]** の **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/tutorial-assess-import/assess-migrate.png)

4. **[作業の開始]** で、 **[ツールの追加]** をクリックします。
5. **[移行プロジェクト]** で、Azure サブスクリプションを選択し、リソース グループがない場合は作成します。     
6. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。

    - サポートされている地域を[確認する](migrate-support-matrix.md#supported-geographies) プロジェクトの地理的な場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。
    - 移行を実行するときは、任意のターゲット リージョンを選択できます。

    ![Azure Migrate プロジェクトを作成する](./media/tutorial-assess-import/migrate-project.png)


7. **[次へ]** をクリックします。
8. **[評価ツールの選択]** で、次を選択します: **[Azure Migrate: Server Assessment]**  >  **[次へ]** 。

    ![Azure Migrate プロジェクトを作成する](./media/tutorial-assess-import/assessment-tool.png)

9. **[移行ツールの選択]** で、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** の順に選択します。
10. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします。
11. Azure Migrate プロジェクトがデプロイされるまで数分待ちます。 プロジェクトのページが表示されます。 プロジェクトが表示されない場合は、Azure Migrate ダッシュボードの **[サーバー]** からアクセスできます。


## <a name="prepare-the-csv"></a>CSV を準備する

CSV テンプレートをダウンロードし、サーバー情報を追加します。


### <a name="download-the-template"></a>テンプレートをダウンロードする

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]** で **[Import using .CSV]\(.CSV を使用してインポート\)** を選択します。
3. **[ダウンロード]** をクリックして .CSV テンプレートをダウンロードします。 また、[直接ダウンロードする](https://go.microsoft.com/fwlink/?linkid=2108404)こともできます。

    ![.CSV テンプレートをダウンロードする](./media/tutorial-assess-import/download-template.png)


### <a name="add-server-information"></a>サーバー情報を追加する

サーバー データを収集し、CSV ファイルに追加します。

- データを収集するには、VMware vSphere や構成管理データベース (CMDB) など、オンプレミスのサーバー管理に使用するツールからデータをエクスポートします。
- サンプル データを確認するには、[サンプル ファイル](https://go.microsoft.com/fwlink/?linkid=2108405)をダウンロードします。


次の表は、入力するファイル フィールドをまとめたものです。

**フィールド名** | **必須** | **詳細**
--- | --- | ---
**サーバー名** | はい | FQDN を指定することをお勧めします。
**IP アドレス** | いいえ | サーバー アドレス。
**コア数** | はい | サーバーに割り当てられているプロセッサ コアの数。
**メモリ** | はい | サーバーに割り当てられている合計 RAM (MB)。
**OS 名** | はい | サーバーのオペレーティング システム。
**OS バージョン** | いいえ | サーバーのオペレーティング システムのバージョン。
**ディスクの数** | いいえ | 個々のディスクの詳細が指定されている場合は必要ありません。
**Disk 1 size (ディスク 1 のサイズ)**  | いいえ | ディスクの最大サイズ (GB)<br/> テンプレートに[列を追加](#add-multiple-disks)することで、さらにディスクの詳細を追加できます。 最大 8 つのディスクを追加できます。
**Disk 1 read ops (ディスク 1 の読み取り操作)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Disk 1 write ops (ディスク 1 の書き込み操作)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Disk 1 read throughput (ディスク 1 の読み取りスループット)** | いいえ | 1 秒あたりにディスクから読み取られたデータ (MB/ 秒)。
**Disk 1 write throughput (ディスク 1 の書き込みスループット)** | いいえ | 1 秒あたりにディスクに書き込まれたデータ (MB/ 秒)。
**CPU utilization percentage (CPU 使用率)** | いいえ | CPU の使用率。
**Memory utilization percentage (メモリ使用率)** | いいえ | RAM の使用率。
**Total disks read ops (ディスク読み取り操作の合計)** | いいえ | 1 秒あたりのディスク読み取り操作。
**Total disks write ops (ディスク書き込み操作の合計)** | いいえ | 1 秒あたりのディスク書き込み操作。
**Total disks read throughput (ディスク読み取りスループットの合計)** | いいえ | ディスクから読み取られたデータ (MB/ 秒)。
**Total disks write throughput (ディスク書き込みスループットの合計)** | いいえ | ディスクに書き込まれたデータ (MB/ 秒)。
**Network in throughput (スループットのネットワーク受信)** | いいえ | サーバーによって受信されたデータ (MB/秒)。
**Network out throughput (スループットのネットワーク送信)** | いいえ | サーバーによって送信されたデータ (MB/秒)。
**Firmware type (ファームウェアの種類)** | いいえ | サーバーのファームウェア。 値には「BIOS」または「UEFI」を指定できます。
**サーバーの種類** | いいえ | 値には「物理」または「仮想」を指定できます。
**ハイパーバイザー** | いいえ | マシンが実行されているハイパーバイザー。 <br/> 値には、「VMware」、「Hyper-V」、「Xen」、「AWS」、「GCP」、または「その他」を指定できます。
**Hypervisor version number (ハイパーバイザーのバージョン番号)** | いいえ | ハイパーバイザーのバージョン。
**仮想マシン ID** | いいえ | VM 識別子。 これは、VMware vCenter VM の場合は **InstanceUUid**、Hyper-V の場合は **Hyper-V VM ID** です。
**Virtual Machine Manager ID** | いいえ | これは VMWare vCenter の **InstanceUUid** です。 Hyper-V には必要ありません。
**MAC アドレス**| いいえ | サーバーの MAC アドレス。
**BIOS ID** | いいえ | サーバーの BIOS ID。
**Custom server ID (カスタム サーバー ID)**| いいえ | オンプレミスの一意のローカル サーバー ID。 <br/> インポートされたサーバーをローカル ID で追跡する場合に便利です。
**Application 1 name (アプリケーション 1 の名前)** | いいえ | サーバーで実行されているワークロードの名前。<br/> テンプレートに[列を追加](#add-multiple-applications)することで、さらにアプリの詳細を追加できます。 最大 5 つのアプリケーションを追加できます。
**Application 1 type (アプリケーション 1 の種類)** | いいえ | サーバーで実行されているワークロードの種類
**Application 1 version (アプリケーション 1 のバージョン)** | いいえ | サーバーで実行されているワークロードのバージョン。
**Application 1 license expiry (アプリケーション 1 のライセンスの有効期限)** | いいえ | ワークロードのライセンスの有効期限 (該当する場合)。
**部署** | いいえ | サーバーが属している部署。
**Business owner (ビジネス オーナー)** | いいえ | 部署の所有者。
**Business application name (ビジネス アプリケーション名)** | いいえ | アプリが属しているアプリケーションの名前。
**Location** | いいえ | サーバーが配置されているデータセンター。
**Server decommission date (サーバーの使用停止日)** | いいえ | 物理サーバー、または仮想サーバーの基となる物理サーバーの使用停止日

### <a name="add-operating-systems"></a>オペレーティング システムを追加する

評価では特定のオペレーティング システム名が認識されます。 指定するオペレーティング システム名は、[サポートされている名前](#supported-operating-system-names)一覧のオプションのいずれかと一致している必要があります。


### <a name="add-multiple-disks"></a>複数のディスクを追加する

このテンプレートには、最初のディスクの既定フィールドが用意されています。  同様の列を最大 8 個のディスクに追加できます。

たとえば、2 番目のディスクのすべてのフィールドを指定するには、次の列を追加します。

ディスク 2 のサイズ、ディスク 2 の読み取り操作、ディスク 2 の書き込み操作、ディスク 2 の読み取りスループット、ディスク 2 の書き込みスループット

必要に応じて、1 つのディスク専用のフィールドを追加できます。


### <a name="add-multiple-applications"></a>複数のアプリケーションを追加する

テンプレートには、単一のアプリケーション用のフィールドが用意されています。 同様の列を最大 5 つのアプリに追加できます。  

たとえば、2 番目のアプリのすべてのフィールドを指定するには、次の列を追加します。

アプリケーション 2 の名前、アプリケーション 2 の種類、アプリケーション 2 のバージョン、アプリケーション 2 のライセンスの有効期限


必要に応じて、1 つのアプリ専用のフィールドを追加できます。

> [!NOTE]
> アプリ情報は、移行についてオンプレミス環境を評価するときに役立ちます。 ただし、現在、Azure Migrate Server Assessment ではアプリ レベルの評価が実行されず、評価の作成時にアプリが考慮されません。


## <a name="upload-the-server-information"></a>サーバー情報をアップロードする

CSV テンプレートに情報を追加したら、Azure Migrate にサーバーをインポートします。Server Assessment を使用して作成する方法について説明します。

1. [Azure Migrate] > **[マシンの検出]** で、入力したテンプレートを参照します。
2. **[インポート]** をクリックします。
3. インポートの状態が表示されます。
    - 状態に警告が表示される場合は、それらを修正するか、対処せずに続行することができます。
    - 警告の提案に従ってサーバー情報を修正すると、評価の精度が向上します。
    - 警告が表示された場合に表示して修正するには、 **[Download warning details .CSV]\(警告の詳細の .CSV のダウンロード\)** をクリックします。 これにより、警告が追加された CSV がダウンロードされます。 警告を確認し、必要に応じて問題を修正することができます。
    状態にエラーが表示される場合 (インポートの状態が **[失敗]** の場合)、インポートを続行する前にこれらを修正する必要があります。 これを行うには、エラーの詳細が追加されている CSV をダウンロードします。 必要に応じて、エラーを確認して対処します。 次に、変更したファイルを再度アップロードします。
4. インポートの状態が **[完了]** の場合は、サーバーの情報がインポートされます。


> [!NOTE]
> Azure Migrate にアップロードされたサーバー情報を更新するには、同じ **[サーバー名]** を使用してサーバーのデータを再度アップロードします。 テンプレートをインポートした後は、 **[サーバー名]** フィールドを変更できないことに注意してください。 サーバーの削除は現在サポートされていません。

## <a name="updating-server-information"></a>サーバー情報の更新

サーバー情報を更新するには、サーバーのデータを同じ **[サーバー名]** を使用してもう一度アップロードします。 **[サーバー名]** フィールドを変更することはできません。

サーバーの削除は現在サポートされていません。

### <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出後、サーバーが Azure portal に表示されていることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。
3. **[Import based]\(インポート ベース\)** タブをクリックします。

## <a name="set-up-an-assessment"></a>評価を設定する

Azure Migrate: Server Assessment を使用して作成できる評価には、次の2種類があります。

**評価** | **詳細** | **データ**
--- | --- | ---
**パフォーマンスベース** | 指定されたパフォーマンス データ値に基づく評価 | **推奨される VM サイズ**: CPU とメモリの使用率データに基づきます。<br/><br/> **推奨されるディスクの種類 (Standard または Premium マネージド ディスク)** : オンプレミス ディスクの IOPS とスループットに基づきます。
**オンプレミス** | オンプレミスのサイズ設定に基づく評価。 | **推奨される VM サイズ**: 指定されたサーバーのサイズに基づきます<br/><br> **推奨されるディスクの種類**: 評価用に選択するストレージの種類の設定に基づきます。


### <a name="run-an-assessment"></a>評価を実行する

評価を実行するには次のようにします。

1. 評価を作成するための[ベスト プラクティス](best-practices-assessment.md)を確認します。
2. **[サーバー]** タブの **[Azure Migrate: Server Assessment]** タイルで、 **[評価]** をクリックします。

    ![評価](./media/tutorial-assess-physical/assess.png)

2. **[サーバーの評価]** で、評価の名前を指定します。
3. **[Discovery source]\(検出ソース\)** で、 **[Machines added via import to Azure Migrate]\(インポートで Azure Migrate に追加されたマシン\)** を選択します
3. **[すべて表示]** をクリックして、評価のプロパティを確認します。

    ![評価のプロパティ](./media/tutorial-assess-physical/view-all.png)

3. **[グループを選択または作成します]** で **[新規作成]** を選択し、グループ名を指定します。 グループで、評価のために 1 つ以上の VM をまとめます。
4. **[グループにマシンを追加します]** で、グループに追加するサーバーを選択します。
5. **[評価を作成します]** をクリックして、グループを作成し、評価を実行します。

    ![評価を作成する](./media/tutorial-assess-physical/assessment-create.png)

6. 評価が作成されたら、それを表示します ( **[サーバー]**  >  **[Azure Migrate: Server Assessment]**  >  **[評価]** )。
7. **[評価のエクスポート]** をクリックし、Excel ファイルとしてダウンロードします。



## <a name="review-an-assessment"></a>評価を確認する

評価には以下が記述されています。

- **Azure 対応性**: サーバーが Azure への移行に適しているかどうか。
- **月間コスト見積もり**: Azure でサーバーを実行するためのコンピューティングとストレージの月間推定コスト。
- **月間ストレージ コスト見積もり**: 移行後のディスク ストレージの推定コスト。

### <a name="view-an-assessment"></a>評価を表示する

1. **[移行の目標]**  >   **[サーバー]** で、 **[評価]** を **[Azure Migrate: Server Assessment]** でクリックします。
2. **[評価]** で、評価をクリックして開きます。

    ![評価の概要](./media/tutorial-assess-physical/assessment-summary.png)

### <a name="review-azure-readiness"></a>Azure 対応性の確認

1. **[Azure 対応性]** で、VM が Azure への移行の準備ができているかどうかを確認します。
2. 状態を確認します。
    - **[Azure に対応]** : Azure Migrate によって、評価内の VM の VM サイズとコスト見積もりが推奨されます。
    - **[条件付きで対応]** : 問題と推奨される修復方法が示されます。
    - **[Azure に未対応]** : 問題と推奨される修復方法が示されます。
    - **[Readiness 不明]** : データの可用性の問題により、Azure Migrate が対応性を評価できない場合に使用されます。

2. **[Azure 対応性]** の状態をクリックします。 サーバー対応性の詳細を表示し、ドリルダウンしてサーバーの詳細 (コンピューティング、ストレージ、ネットワークの設定など) を表示できます。

### <a name="review-cost-details"></a>コスト詳細の確認

このビューには、Azure で VM を実行する際のコンピューティングおよびストレージの推定コストが表示されます。

1. 月間のコンピューティングおよびストレージのコストを確認します。 評価対象のグループ内のすべてのサーバーのコストが集計されます。

    - コストの見積もりは、マシンの推奨サイズ、およびそのディスクとプロパティに基づいています。
    - コンピューティングとストレージの月間推定コストが表示されます。
    - コストの見積もりは、IaaS VM としてのオンプレミス サーバーの実行に対するものです。 Azure Migrate Server Assessment では、PaaS または SaaS のコストは考慮されません。

2. 月間ストレージ コストの見積もりを確認できます。 このビューには、評価されたグループの集計されたストレージ コストが、ストレージ ディスクの種類ごとに分けて表示されます。
3. ドリルダウンして、特定の VM の詳細を見ることができます。

> [!NOTE]
> CSV を使用して Azure Migrate Server Assessment にインポートされたサーバーの評価には、信頼度の評価は割り当てられません。


## <a name="supported-operating-system-names"></a>サポートされるオペレーティング システム名

名前 | 名前
--- | ---
**A - H** |
Apple Mac OS X 10 | Asianux 3<br/>Asianux 4<br/>Asianux 5
CentOS<br/>CentOS 4/5 | CoreOS Linux
Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8 | FreeBSD
**I - R** |
IBM OS/2 | MS-DOS |
Novell NetWare 5<br/>Novell NetWare 6 | Oracle Linux<br/> Oracle Linux 4/5<br/>Oracle Solaris 10<br/> Oracle Solaris 11
Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora |
**S-T** |
SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7 | Serenity Systems eComStation 1<br/>Serenity Systems eComStation 2
Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9 | SUSE Linux Enterprise 10<br/> SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE
**U-Z** |
Ubuntu Linux | VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6
Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003 | Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional


## <a name="next-steps"></a>次の手順

このチュートリアルでは、次のことを行いました。

> [!div class="checklist"]
> * Azure Migrate するためにインポートされたサーバー:CSV を使用したサーバー評価。
> * 評価を作成して確認する

次は、より正確な評価のために[アプライアンスをデプロイ](./migrate-appliance.md)し、[依存関係分析](./concepts-dependency-visualization.md)を使用してより深い評価のためにサーバーをグループにまとめます。
