---
title: Azure Migrate での VMware サーバー評価サポート
description: VMware 環境内のサーバーの Azure Migrate 検出および評価サポートについて学習します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: de4d66f3ef8195e13ff8b67538901d1ebc7d88aa
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971063"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評価のサポートマトリックス 

この記事は、[Azure Migrate: Discovery and Assessment](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool) ツールを使用して、VMware 環境内のサーバーを、Azure に移行するために検出して評価する場合の前提条件とサポート要件をまとめたものです。

サーバーを評価するには、まず Azure Migrate プロジェクトを作成します。 Azure Migrate: Discovery and Aassessment ツールは自動的にプロジェクトに追加されます。 その後、Azure Migrate アプライアンスをデプロイします。 アプライアンスでオンプレミス サーバーが継続的に検出され、構成とパフォーマンスのメタデータが Azure に送信されます。 検出が終了したら、検出されたサーバーをグループにまとめ、グループごとに評価を実行します。

Azure への VMware サーバーの移行を計画するときは、[移行のサポート マトリックス](migrate-support-matrix-vmware-migration.md)を確認してください。

## <a name="limitations"></a>制限事項

要件 | 詳細
--- | ---
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数の Azure Migrate プロジェクトを作成できます。<br /><br /> 1 つの[プロジェクト](migrate-support-matrix.md#project)で、VMware 環境内のサーバーを最大 50,000 台検出して評価することができます。 1 つのプロジェクトには、物理サーバー、および Hyper-V 環境にあるサーバーを、評価の上限に達するまで含めることができます。
**検出** | Azure Migrate アプライアンスで、vCenter Server で実行されているサーバーを最大 10,000 台検出できます。
**評価** | 1 つのグループに最大 35,000 台のサーバーを追加できます。<br /><br /> 1 回の評価で最大 35,000 台のサーバーを評価できます。

[評価](concepts-assessment-calculation.md)についての詳細を参照してください。

## <a name="vmware-requirements"></a>VMware の要件

VMware | 詳細
--- | ---
**vCenter Server** | 検出および評価対象のサーバーは、vCenter Server バージョン 7.0、6.7、6.5、6.0、または 5.5 で管理されている必要があります。<br /><br /> アプライアンスで ESXi ホストの詳細を指定することによるサーバーの検出は、現在サポートされていません。
**アクセス許可** | Azure Migrate: Discovery and Assessment ツールには、vCenter Server の読み取り専用アカウントが必要です。<br /><br /> ソフトウェア インベントリおよびエージェントレスの依存関係の分析にそのツールを使用する場合、アカウントには VMware VM でのゲスト操作に対する特権が必要です。

## <a name="server-requirements"></a>サーバーの要件

VMware | 詳細
--- | ---
**オペレーティング システム** | すべての Windows および Linux オペレーティング システムを、移行のために評価することができます。
**Storage** | SCSI、IDE、および SATA ベースのコントローラーに接続されているディスクがサポートされています。

## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 アプライアンスを VMware 環境内のサーバーとしてデプロイするには、vCenter Server にインポートされた VMware Open Virtualization Appliance (OVA) テンプレートを使うか、[PowerShell スクリプト](deploy-appliance-script.md)を使用します。 [VMware のアプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。

アプライアンスにはさらに以下の要件があります。

- Azure Government では、[スクリプト](deploy-appliance-script-government.md)を使用してアプライアンスをデプロイする必要があります。
- そのアプライアンスから、[パブリック クラウド](migrate-appliance.md#public-cloud-urls)および [Government クラウド](migrate-appliance.md#government-cloud-urls)の特定の URL にアクセスできる必要があります。

## <a name="port-access-requirements"></a>ポートのアクセス要件

Device | Connection
--- | ---
**Azure Migrate アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br /><br /> ポート 44368 で、`https://<appliance-ip-or-name>:44368` という URL を使用してアプライアンス管理アプリにリモートでアクセスするための受信接続。 <br /><br />ポート 443 (HTTPS) で検出とパフォーマンスのメタデータを Azure Migrate に送信するための送信接続。
**vCenter Server** | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br /><br /> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server で別のポートをリッスンしている場合は、検出の設定時にそのポートを変更できます。
**ESXi ホスト** | [ソフトウェア インベントリの検出](how-to-discover-applications.md)または[エージェントレスの依存関係の分析](concepts-dependency-visualization.md#agentless-analysis)の場合、アプライアンスが TCP ポート 443 上の ESXi ホストに接続されると、サーバー上のソフトウェア インベントリと依存関係が検出されます。

## <a name="application-discovery-requirements"></a>アプリケーションの検出の要件

サーバーの検出に加えて、Azure Migrate: Discovery and Assessment では、サーバーで実行されているソフトウェアのインベントリを完了できます。 アプリケーションを検出することで、オンプレミスのワークロードに合わせて調整された移行パスを特定し、計画することができます。

サポート | 詳細
--- | ---
**サポートされているサーバー** | 現在、VMware 環境内のサーバーに対してのみサポートされています。 アプリケーションの検出は、各 Azure Migrate アプライアンスから最大 10,000 台のサーバーに対して実行できます。
**オペレーティング システム** | すべてのバージョンの Windows および Linux を実行しているサーバーがサポートされています。
**VM 要件** | ソフトウェア インベントリの検出の場合、VMware ツールがサーバーにインストールされ、実行されている必要があります。 <br /><br /> VMware ツールのバージョンは、バージョン 10.2.1 以降である必要があります。<br /><br /> Windows サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**検出** | サーバー上のアプリケーション検出は、サーバーにインストールされている VMware ツールを使用して、vCenter Server から実行されます。 アプライアンスでは、vSphere API を使用して、vCenter Server を実行しているサーバーからソフトウェア インベントリについての情報を収集します。 アプリケーションの検出はエージェントレスです。 サーバーにエージェントはインストールされず、アプライアンスはサーバーに直接接続されません。 Windows および Linux サーバーで、WMI と SSH がそれぞれ有効化され、使用可能になっている必要があります。
**vCenter Server ユーザー アカウント** | アプリケーション検出のためにサーバーとやりとりするには、評価に使用される vCenter Server の読み取り専用アカウントに、VMware VM でのゲスト操作に対する権限が必要です。
**サーバー アクセス** | アプリケーション検出のために、アプライアンス構成マネージャーにドメインおよびドメイン以外 (Windows または Linux) の複数の資格情報を追加することができます。<br /><br /> Windows サーバー用にゲスト ユーザー アカウントと、すべての Linux サーバー用に標準ユーザー アカウント (非 `sudo` アクセス) が必要です。
**ポート アクセス** | アプリケーション検出を実行する対象のサーバーが実行されている ESXi ホスト上で、Azure Migrate アプライアンスから TCP ポート 443 への接続ができる必要があります。 ソフトウェア インベントリの詳細を含むファイルをダウンロードするために、vCenter Server を実行しているサーバーから ESXi ホスト接続が返されます。

## <a name="sql-server-instance-and-database-discovery-requirements"></a>SQL Server インスタンスとデータベース検出の要件

[アプリケーション検出](how-to-discover-applications.md)により、SQL Server インスタンスが識別されます。 この情報を使用し、アプライアンス構成マネージャーで提供される Windows 認証または SQL Server 認証の資格情報を介して、アプライアンスからそれぞれの SQL Server インスタンスへの接続が試みられます。 アプライアンスが接続された後、SQL Server インスタンスとデータベースの構成とパフォーマンスのデータが収集されます。 SQL Server 構成データは 24 時間に 1 回更新されます。 パフォーマンス データは 30 秒ごとに取り込まれます。

サポート | 詳細
--- | ---
**サポートされているサーバー** | 現在、VMware 環境内の SQL Server を実行しているサーバーに対してのみサポートされています。 最大 300 個の SQL Server インスタンスまたは 6,000 個の SQL データベースのいずれか少ない方を検出できます。
**Windows サーバー** | Windows Server 2008 以降がサポートされています。
**Linux サーバー** | 現在サポートされていません。
**認証メカニズム** | Windows と SQL Server の両方の認証がサポートされています。 アプライアンス構成マネージャーで両方の種類の認証の資格情報を指定できます。
**SQL Server へのアクセス** | Azure Migrate には、sysadmin サーバー ロールのメンバーである Windows ユーザー アカウントが必要です。
**SQL Server のバージョン** | SQL Server 2008 以降がサポートされています。
**SQL Server のエディション** | Enterprise、Standard、Developer、Express の各エディションがサポートされています。
**サポートされている SQL 構成** | 現在、スタンドアロンの SQL Server インスタンスと対応するデータベースの検出のみがサポートされています。<br /><br /> フェールオーバー クラスターと Always On 可用性グループの識別はサポートされていません。
**サポートされている SQL サービス** | SQL Server データベース エンジンのみがサポートされています。 <br /><br /> SQL Server Reporting Services (SSRS)、SQL Server Integration Services (SSIS)、SQL Server Analysis Services (SSAS) の検出はサポートされていません。

> [!NOTE]
> [TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) プロパティが `true` に設定されている場合は、Azure Migrate により、Azure Migrate アプライアンスとソース SQL Server インスタンス間の通信が暗号化されます。 トランスポート層では SSL を使用してチャネルを暗号化し、証明書チェーンをバイパスして信頼を確認します。 アプライアンス サーバーは、[証明書のルート証明機関を信頼](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)するように設定する必要があります。
>
> サーバーの起動時に証明書がプロビジョニングされていない場合は、SQL Server により、ログオン パケットの暗号化に使用される自己署名証明書が生成されます。 [詳細については、こちらを参照してください](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。
>

## <a name="dependency-analysis-requirements-agentless"></a>依存関係の分析の要件 (エージェントレス)

[依存関係の分析](concepts-dependency-visualization.md)は、評価して Azure に移行するオンプレミス サーバー間の依存関係を特定するのに役立ちます。 以下の表は、エージェントレスの依存関係の分析を設定するための要件をまとめたものです。

サポート | 詳細
--- | --- 
**サポートされているサーバー** | 現在、VMware 環境内のサーバーに対してのみサポートされています。
**Windows サーバー** | Windows Server 2016<br /> Windows Server 2012 R2<br /> Windows Server 2012<br /> Windows Server 2008 R2 (64 ビット)<br />Microsoft Windows Server 2008 (32 ビット)
**Linux サーバー** | Red Hat Enterprise Linux 7、6、5<br /> Ubuntu Linux 16.04、14.04<br /> Debian 8、7<br /> Oracle Linux 7、6<br /> CentOS 7、6、5<br /> SUSE Linux Enterprise Server 11 以降
**サーバーの要件** | 分析するサーバーに VMware ツール (10.2.1 以降) がインストールされ、実行されている必要があります。<br /><br /> サーバーには、PowerShell バージョン 2.0 以降がインストールされている必要があります。
**検出方法** |  サーバー間の依存関係情報は、vCenter Server を実行しているサーバーにインストールされている VMware ツールを使用して収集されます。 vSphere API を使用して、アプライアンスによってサーバーから情報が収集されます。 サーバーにエージェントはインストールされず、アプライアンスはサーバーに直接接続されません。 Windows および Linux サーバーで、WMI と SSH がそれぞれ有効化され、使用可能になっている必要があります。
**vCenter アカウント** | 評価のために Azure Migrate によって使用される読み取り専用アカウントには、VMware VM でのゲスト操作に対する権限が必要です。
**Windows サーバーのアクセス許可** |  サーバーに対する管理者権限を持つユーザー アカウント (ローカルまたはドメイン)。
**Linux アカウント** | ルート ユーザー アカウント、または /bin/netstat および /bin/ls ファイルに対して次のアクセス許可を持つアカウント。 <br />CAP_DAC_READ_SEARCH<br /> CAP_SYS_PTRACE<br /><br /> これらの機能は次のコマンドを使用して設定します。<br /><code>sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br /> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat</code>
**ポート アクセス** | 依存関係を検出するサーバーを実行している ESXi ホスト上で、Azure Migrate アプライアンスから TCP ポート 443 に接続できる必要があります。 依存関係データを含むファイルをダウンロードするため、vCenter Server を実行しているサーバーから ESXi ホスト接続が返されます。

## <a name="dependency-analysis-requirements-agent-based"></a>依存関係の分析の要件 (エージェント ベース)

[依存関係の分析](concepts-dependency-visualization.md)は、評価して Azure に移行するオンプレミス サーバー間の依存関係を特定するのに役立ちます。 次の表は、エージェント ベースの依存関係の分析を設定するための要件をまとめたものです。

要件 | 詳細
--- | ---
**デプロイ前** | Azure Migrate: Discovery and Assessment ツールがプロジェクトに追加された状態で、プロジェクトを準備する必要があります。<br /><br />オンプレミス サーバーを検出するには、Azure Migrate アプライアンスを設定した後、依存関係の視覚化をデプロイします。<br /><br />初めてプロジェクトを作成する方法については、[こちら](create-manage-projects.md)を参照してください。<br /> 既存のプロジェクトに検出および評価ツールを追加する方法については、[こちら](how-to-assess.md)を参照してください。<br /> [Hyper-V](how-to-set-up-appliance-hyper-v.md)、[VMware](how-to-set-up-appliance-vmware.md)、または物理サーバーの評価のために Azure Migrate アプライアンスを設定する方法について説明します。
**サポートされているサーバー** | オンプレミス環境にあるすべてのサーバーに対してサポートされています。
**Log Analytics** | Azure Migrate は、依存関係の視覚化のために [Azure Monitor ログ](../azure-monitor/logs/log-query-overview.md)の [Service Map](../azure-monitor/vm/service-map.md) ソリューションを使用します。<br /><br /> 新規または既存の Log Analytics ワークスペースをプロジェクトに関連付けます。 プロジェクトのワークスペースは、ワークスペースの追加後に変更することはできません。 <br /><br /> ワークスペースは、プロジェクトと同じサブスクリプションに含まれている必要があります。<br /><br /> ワークスペースは、米国東部、東南アジア、または西ヨーロッパ リージョンにある必要があります。 他のリージョンにあるワークスペースをプロジェクトに関連付けることはできません。<br /><br /> ワークスペースは、[Service Map がサポートされているリージョン](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)に存在する必要があります。<br /><br /> Log Analytics では、Azure Migrate に関連付けられたワークスペースに、プロジェクト キーとプロジェクト名のタグが付けられます。
**必要なエージェント** | 分析する各サーバーに、次のエージェントをインストールします。<br />- [Microsoft Monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)<br />- [Dependency エージェント](../azure-monitor/agents/agents-overview.md#dependency-agent)<br /><br /> オンプレミス サーバーがインターネットに接続されていない場合は、Log Analytics ゲートウェイをダウンロードしてインストールします。<br /><br /> [依存関係エージェント](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)と [MMA](how-to-create-group-machine-dependencies.md#install-the-mma) のインストールの詳細を確認してください。
**Log Analytics ワークスペース** | ワークスペースは、プロジェクトと同じサブスクリプションに含まれている必要があります。<br /><br /> Azure Migrate では、米国東部、東南アジア、および西ヨーロッパの各リージョンにあるワークスペースがサポートされます。<br /><br />  ワークスペースは、[Service Map がサポートされている](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)リージョンに存在する必要があります。<br /><br /> プロジェクトのワークスペースは、ワークスペースの追加後に変更することはできません。
**コスト** | Service Map ソリューションでは、(Log Analytics ワークスペースをプロジェクトに関連付けた日から) 最初の 180 日間は料金が発生しません。<br /><br /> 180 日が経過すると、Log Analytics の標準の料金が適用されます。<br /><br /> この関連付けられた Log Analytics ワークスペース内で Service Map 以外のソリューションを使用すると、Log Analytics の[標準の料金](https://azure.microsoft.com/pricing/details/log-analytics/)が発生します。<br /><br /> プロジェクトが削除されるときに、ワークスペースは自動的に削除されません。 プロジェクトの削除後に、Service Map を無料で使用することはできず、Log Analytics ワークスペースの有料階層ごとに各ノードの料金が請求されます。<br /><br />Azure Migrate の一般提供 (2018 年 2 月 28 日) 前に作成したプロジェクトがある場合、Service Map の追加料金が発生する可能性があります。 確実に 180 日後にのみ支払いが発生するようにするために、新しいプロジェクトを作成することをお勧めします。 GA の前に作成されたワークスペースは引き続き課金対象となります。
**管理** | エージェントをワークスペースに登録する場合、プロジェクトで提供される ID とキーを使用します。<br /><br /> Azure Migrate 以外で Log Analytics ワークスペースを使用できます。<br /><br /> 関連付けられたプロジェクトを削除しても、ワークスペースは自動的に削除されません。 [手動で削除してください](../azure-monitor/logs/manage-access.md)。<br /><br /> プロジェクトを削除する場合を除き、Azure Migrate で作成されたワークスペースは削除しないでください。 そのようにすると、依存関係可視化機能が期待どおりに動作しなくなります。
**インターネット接続** | サーバーがインターネットに接続されていない場合は、そのサーバーに Log Analytics ゲートウェイをインストールします。
**Azure Government** | エージェントベースの依存関係の分析はサポートされていません。

## <a name="next-steps"></a>次のステップ

- [評価のベスト プラクティス](best-practices-assessment.md)を確認します。
- [VMware 評価のための準備を行う](./tutorial-discover-vmware.md)方法について学習します。