<properties
   pageTitle="Operations Management Suite (OMS) での Application Dependency Monitor (ADM) | Microsoft Azure"
   description="Application Dependency Monitor (ADM) は、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite (OMS) ソリューションです。  この記事では、ADM を環境にデプロイして、さまざまなシナリオで使用する場合の詳細について説明します。"
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />


# <a name="using-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>Operations Management Suite (OMS) での Application Dependency Monitor ソリューションの使用
![Alert Management icon](media/operations-management-suite-application-dependency-monitor/icon.png) Application Dependency Monitor (ADM) は、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。  Application Dependency Monitor は、サーバー間の接続、プロセス、あらゆる TCP 接続アーキテクチャでのポートを表示します。必要なのはエージェントのインストールだけで、構成は不要です。

この記事では、Application Dependency Monitor の使い方の詳細について説明します。  ADM の構成とエージェントの利用方法については、「[Operations Management Suite (OMS) での Application Dependency Monitor ソリューションの構成](operations-management-suite-application-dependency-monitor-configure.md)」をご覧ください。

>[AZURE.NOTE]Application Dependency Monitor は現在、プライベート プレビューの状態です。  ADM プライベート プレビューへアクセスするには、 [https://aka.ms/getadm](https://aka.ms/getadm) からリクエストしてください。
>
>プライベート プレビューのあいだは、すべての OMS アカウントは ADM に無制限にアクセスできます。  ADM ノードは無料ですが、AdmComputer_CL と AdmProcess_CL の種類の Log Analytics データは、その他のソリューションと同様に計測されます。
>
>ADM がパブリック プレビューの状態になると、OMS 料金プランの Insight & Analytics の無償および有償ユーザーだけが使えるようになります。  Free レベル アカウントは、ADM の 5 つのノードまでに制限されます。  プライベート プレビューに参加していても、ADM がパブリック プレビューに移行した時点で OMS 料金プランに登録していない場合は、その時点で ADM が無効となります。 


## <a name="use-cases:-make-your-it-processes-dependency-aware"></a>使用例: IT プロセスの依存関係を明確にする

### <a name="discovery"></a>探索
ADM は、サーバー、プロセス、およびサード パーティのサービスの間での依存関係について、共通の参照マップを自動的に作成します。  すべての TCP 依存関係を探索してマップし、予想外の接続、依存しているリモートのサード パーティ システム、DNS と AD などの従来型のネットワークの暗領域の依存関係を識別します。  ADM は、管理システムが行おうとしたネットワーク接続の失敗を検出し、サーバー構成に関する潜在的な問題、サービスの停止、ネットワークの問題を識別する手助けをします。

### <a name="incident-management"></a>インシデント管理
ADM では、システムがどのように接続され互いに影響しているかを示すので、当て推量で問題を特定することが少なくなります。  失敗した接続だけでなく、接続されているクライアントに関する情報を提供するので、ロード バランサー構成の問題、重要なサービスへの予想外なまたは過剰な負荷、実稼働システムと通信する開発者のコンピューターなどの悪意のあるクライアントを特定するのに役立ちます。  また、OMS の変更の追跡との統合ワークフローでは、バックエンド コンピューターの変更イベントとサービスのどちらがインシデントの根本原因なのかがわかります。

### <a name="migration-assurance"></a>確実な移行
ADM を使用すると、Azure の移行を効果的に計画、迅速化、および検証することができ、必要な作業を網羅して予想外の障害が起きないようにできます。  同時に移行する必要のある、相互依存しているシステムをすべて検出し、システム構成と容量を評価し、稼働中のシステムがまだユーザーにサービス提供しているのか、あるいは移行せず使用中止する対象となるのかを特定することができます。  移行完了後は、クライアントの負荷と ID をチェックして、テスト システムと顧客が接続していることを確認できます。  サブネットの計画とファイアウォールの定義に問題がある場合は、ADM マップにある失敗した接続を見れば、接続が必要なシステムがわかります。

### <a name="business-continuity"></a>ビジネス継続性
Azure Site Recovery を使用していて、お使いのアプリケーション環境における復元シーケンスの定義が必要な場合、ADM は、どのようにシステムが相互依存しているかを自動的に表示して、信頼性の高い復旧計画を立てる手助けをします。  重要なサーバーを選択してそのクライアントを表示することで、そのサーバーが復元され使用可能になった後にのみ回復すべきフロント エンドのシステムを識別できます。  逆に、重要なサーバーのバック エンドの依存関係を調べることで、重点的システムを復元するより先に復元しなければいけないシステムを識別することができます。

### <a name="patch-management"></a>更新プログラムの管理
ADM は、OMS System Update Assessment の使用を強化します。どの他部署とサーバーが自分たちのサービスに依存しているかを示すので、更新プログラム適用のためにシステム停止する前に、事前に通知することができるのです。  また、ADM は、更新プログラムを適用して再起動した後にそのサービスが使用可能で正常に接続されているかを示すことにより、OMS における更新プログラム管理を強化します。 


## <a name="mapping-overview"></a>マッピングの概要
ADM エージェントは、インストールされている、サーバー上のすべての TCP 接続されているプロセスに関する情報に加え、各プロセスの受信および送信接続の詳細も収集します。  ADM ソリューションの左側にあるマシン一覧を使用して、ADM エージェントのコンピューターを選択し、選択した時間範囲での依存関係を視覚化することができます。  マシンの依存関係マップは、特定のコンピューターに焦点を当て、そのコンピューターの直接 TCP クライアントまたはサーバーであるすべてのコンピューターを表示します。

![ADM の概要](media/operations-management-suite-application-dependency-monitor/adm-overview.png)

マシンはマップ上で展開表示することができ、選択した期間内にアクティブなネットワーク接続で実行しているプロセスを表示することができます。  ADM エージェントのリモート コンピューターを展開してプロセスの詳細を表示すると、そのコンピューターと通信するプロセスのみが表示されます。  コンピューターに接続するエージェントレスのフロント エンド コンピューターの数が、接続しているプロセスの左側に示されます。  コンピューターがエージェントのないバック エンド コンピューターに接続していると、そのバックエンドは IPv4 アドレスを表示するマップ内にノードで示され、ノードを展開すると、そのコンピューターが通信している個々 のポートとサービスを表示します。

既定では、ADM マップは、直近 10 分間の依存関係情報を表示します。  左上にある時間コントロールを使用して、マップで過去の時間範囲 (最長 1 時間まで) を照会すると、インシデントの発生中や変更が発生する前などの過去における依存関係を表示することができます。    ADM データは、有料のワークスペースで 30 日間、無料のワークスペースでは 7 日間のあいだ保存されます。

![選択されたコンピューターのプロパティを表示するコンピューターのマップ](media/operations-management-suite-application-dependency-monitor/machine-map.png)

## <a name="failed-connections"></a>失敗した接続
失敗した接続は、プロセスやコンピューターの ADM マップに、クライアント システムがプロセスまたはポートのどちらのアクセスに失敗したかを示す赤色の破線で表示されます。  失敗した接続は、ADM がデプロイされているシステムから、その接続を試みたのがそのシステムである場合に、報告されます。  ADM は、接続確立に失敗した TCP ソケットを監視することで、これを測定します。  原因としては、ファイアウォール、クライアントまたはサーバーの構成の問題、あるいはリモート サービスが利用不可の状態であること、が考えられます。 

![失敗した接続](media/operations-management-suite-application-dependency-monitor/failed-connections.png)

失敗した接続を把握すると、トラブルシューティング、移行の検証、セキュリティ分析、および全体的なアーキテクチャの理解に役立ちます。  失敗した接続は無害な場合もありますが、多くの場合は問題を端的に示しています。たとえば、突然アクセスできなくなるフェールオーバー環境、クラウド移行後に通信できなくなった 2 つのアプリケーション層などです。  上の図では、IIS および WebSphere の両方が実行されていますが、互いの接続ができません。 

## <a name="computer-and-process-properties"></a>コンピューターとプロセスのプロパティ
ADM マップを見るときは、コンピューターとプロセスを選択して、それらのプロパティに関する追加情報を得ることができます。  コンピューターは、DNS 名、IPv4 アドレス、CPU とメモリ容量、VM の種類、オペレーティング システムのバージョン、最後の再起動時刻、OMS と ADM エージェントの ID、に関する情報を提供します。

プロセスの詳細は、実行プロセスに関するオペレーティング システム メタデータから収集されます。データには、プロセス名、プロセスの説明、ユーザー名とドメイン (Windows 上)、会社名、製品名、製品バージョン、作業ディレクトリ、コマンドライン、プロセス開始時刻が含まれます。

![プロセスのプロパティ](media/operations-management-suite-application-dependency-monitor/process-properties.png)

[Process Summary] (プロセスの概要) パネルでは、接続しているポート、受診と送信の接続、失敗した接続を含む、そのプロセスの接続に関する追加情報を提供します。 

![Process Summary (プロセスの概要)](media/operations-management-suite-application-dependency-monitor/process-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS の変更の追跡との統合
双方のソリューションが有効で OMS ワークスペースに構成されている場合は、ADM は自動的に変更の追跡と統合されます。

[Machine Summary] (コンピューターの概要) パネルでは、選択した期間内に選択したコンピューター上で変更の追跡イベントが発生したかどうかを示します。

![[Machine Summary] (コンピューターの概要) パネル](media/operations-management-suite-application-dependency-monitor/machine-summary.png)

[Machine Change Tracking] (コンピューター変更の追跡) パネルには、すべての変更の一覧が新しい順で表示されており、ログ検索へのリンクで追加情報を探すことができます。
![[Machine Change Tracking] (コンピューター変更の追跡) パネル](media/operations-management-suite-application-dependency-monitor/machine-change-tracking.png)

下記では、**[Show in Log Analytics] (Log Analytics で表示)** を選択して、構成の変更イベントの詳細を表示しています。
![構成の変更イベント](media/operations-management-suite-application-dependency-monitor/configuration-change-event.png)


## <a name="log-analytics-records"></a>Log Analytics のレコード
ADM のコンピューターとプロセスのインベントリ データは、Log Analytics で[検索](../log-analytics/log-analytics-log-searches.md) できます。  これは、移行計画、容量の分析、探索、暫定的なパフォーマンスのトラブルシューティングといった作業に適用できます。 

一意のコンピューターとプロセスごとに、そのプロセスまたはコンピューターが起動するまたは ADM にオンボードする時点で生成されるレコードに加え、1 時間ごとに 1 つのレコードが生成されます。  これらのレコードは、次の表に示したプロパティを持ちます。 

個々のプロセスとコンピューターの識別に使用できる、内部生成されたプロパティがあります。

- PersistentKey_s は、コマンド ラインとユーザー ID などのプロセス構成によって一意に定義されます。  特定のコンピューターに対して一意ですが、複数のコンピューターで繰り返すことができます。
- ProcessId_s と ComputerId_s は、ADM モデル内ではグローバルに一意です。



### <a name="admcomputer_cl-records"></a>AdmComputer_CL records
**AdmProcess_CL** 型を持つレコードには、ADM エージェントを有するサーバーのインベントリ データがあります。  これらのレコードは、次の表に示したプロパティを持ちます。  

| プロパティ | 説明 |
|:--|:--|
| 型 | *AdmComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Windows または Linux コンピューターの名前 |
| CPUSpeed_d | CPU 速度 (MHz) |
| DnsNames_s | このコンピューターのすべての DNS 名の一覧 |
| IPv4s_s | このコンピューターで使用中のすべての IPv4 アドレスの一覧 |
| IPv6s_s | このコンピューターで使用中のすべての IPv6 アドレスの一覧  (ADM は IPv6 アドレスを識別しますが、IPv6 の依存関係は検出しません。) |
| Is64Bit_b | OS の種類に基づいた true または false |
| MachineId_s | 内部 GUID (OMS ワークスペース全体で一意)  |
| OperatingSystemFamily_s | Windows または Linux |
| OperatingSystemVersion_s | OS バージョンの文字列 |
| TimeGenerated | レコードが作成された日付と時刻。 |
| TotalCPUs_d | CPU コアの数 |
| TotalPhysicalMemory_d | メモリ容量 (MB) |
| VirtualMachine_b | OS が VM ゲストであるかに基づいた true または false |
| VirtualMachineID_g | Hyper-V VM ID |
| VirtualMachineName_g | Hyper-V VM 名 |
| VirtualMachineType_s | Hyperv、Vmware、Xen、Kvm、Ldom、Lpar、virtual Pc |


### <a name="admprocess_cl-type-records"></a>AdmProcess_CL Type records 
**AdmProcess_CL** 型を持つレコードには、ADM エージェントを有するサーバー上での TCP 接続プロセスのインベントリ データがあります。  これらのレコードは、次の表に示したプロパティを持ちます。

| プロパティ | 説明 |
|:--|:--|
| 型 | *AdmProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | プロセスのフル コマンド ライン |
| CompanyName_s | 会社名 (Windows PE または Linux RPM から) |
| Description_s | プロセスの説明 (長いバージョン、Windows PE または Linux RPM から) |
| FileVersion_s | 実行可能ファイルのバージョン (Windows PE から、Windows のみ) |
| FirstPid_d | OS プロセス ID |
| InternalName_s | 実行可能ファイルの内部名 (Windows PE から、Windows のみ) |
| MachineId_s | 内部 GUID (OMS ワークスペース全体で一意)  |
| Name_s | 実行可能プロセス名 |
| Path_s | 実行可能プロセスのファイル システム パス |
| PersistentKey_s | 内部 GUID (このコンピューター内で一意) |
| PoolId_d | 内部 ID (同様のコマンドラインに基づいて、プロセスを集計するための)。 |
| ProcessId_s | 内部 GUID (OMS ワークスペース全体で一意)  |
| ProductName_s | 製品名の文字列 (Windows PE または Linux RPM から) |
| ProductVersion_s | 製品バージョンの文字列 (Windows PE または Linux RPM から) |
| StartTime_t | プロセスの開始時刻 (ローカル コンピューター クロックで) |
| TimeGenerated | レコードが作成された日付と時刻。 |
| UserDomain_s | プロセスの所有者のドメイン (Windows のみ) |
| UserName_s | プロセスの所有者の名前 (Windows のみ) |
| WorkingDirectory_s | プロセス作業ディレクトリ |


## <a name="sample-log-searches"></a>サンプル ログ検索

### <a name="list-the-physical-memory-capacity-of-all-managed-computers."></a>すべての管理されたコンピューターの物理メモリ容量を一覧表示。 
Type=AdmComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-01.png)

### <a name="list-computer-name,-dns,-ip,-and-os-version."></a>コンピューター名、DNS、IP、OS バージョンを一覧表示。
Type=AdmComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-02.png)

### <a name="find-all-processes-with-"sql"-in-the-command-line"></a>"sql" でコマンドラインのすべてのプロセスを検索
Type=AdmProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-03.png)

### <a name="after-viewing-event-data-for-given-process,-use-its-machine-id-to-retrieve-the-computer’s-name"></a>指定された処理のイベント データを表示した後、マシン ID を使用して、コンピューターの名前を取得する
Type=AdmComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-04.png)

### <a name="list-all-computers-running-sql"></a>SQL を実行しているすべてのコンピューターを一覧表示
Type=AdmComputer_CL MachineId_s IN {Type=AdmProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-05.png)

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>自分のデータ センター内にあるすべての製品バージョンのCurl を一覧表示する
Type=AdmProcess_CL Name_s=curl | Distinct ProductVersion_s

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-06.png)

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>CentOS を実行しているすべてのコンピューターのコンピューター グループを作成する

![ADM クエリの例](media/operations-management-suite-application-dependency-monitor/adm-example-07.png)



## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様による Application Dependency Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用することで、Application Dependency Monitor サービスの品質、セキュリティ、整合性を提供し続け、向上していきます。 データには、オペレーティング システムとバージョンのような、ソフトウェアの構成に関する情報が含まれています。また、正確で効果的なトラブルシューティングの機能を提供するために、IP アドレス、DNS 名、およびワークステーション名も含まれています。 名前や住所などの連絡先情報は収集されません。

データの収集と使用状況の詳細については、[Microsoft オンライン サービスのプライバシーに関する声明](hhttps://go.microsoft.com/fwlink/?LinkId=512132)を参照してください。



## <a name="next-steps"></a>次のステップ
- [ログ検索](../log-analytics/log-analytics-log-searches.md] in Log Analytics to retrieve data collected by Application Dependency Monitor.)の詳細について



<!--HONumber=Oct16_HO2-->


