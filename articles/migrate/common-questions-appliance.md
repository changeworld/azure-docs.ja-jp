---
title: Azure Migrate アプライアンスに関する一般的な質問
description: Azure Migrate アプライアンスに関する一般的な質問の回答を示します
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 3bb066b08447a951665e629da5ebcb75714b9f1e
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425357"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate アプライアンス:一般的な質問

この記事では、Azure Migrate アプライアンスに関する一般的な質問の回答を示します。 その他の質問については、次の記事を確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- 検出、評価、依存関係の視覚化に関する[質問](common-questions-discovery-assessment.md)
- サーバー移行に関する[質問](common-questions-server-migration.md)
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問への回答を得る。 


## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスとは何ですか。

Azure Migrate アプライアンスは、オンプレミス サーバーを検出および評価するために Azure Migrate: Server Assessment ツールによって使用される軽量アプライアンスです。 このアプライアンスは、オンプレミスの VMware VM のエージェントレス移行のために、Azure Migrate: Server Migration ツールでも使用されます。 

- アプライアンスは、VM または物理マシンとしてオンプレミスにデプロイされます。
- アプライアンスはオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure Migrate に継続的に送信します。
- アプライアンスによる検出はエージェントレスです。 検出されたマシンには何もインストールされません。

アプライアンスの[詳細をご覧ください](migrate-appliance.md)。

## <a name="how-does-the-appliance-connect-to-azure"></a>アプライアンスはどのように Azure に接続しますか。

アプライアンスはインターネット経由で接続できます。または、Azure ExpressRoute をパブリックまたは Microsoft ピアリングで使用できます。

## <a name="does-appliance-analysis-impact-performance"></a>アプライアンス分析はパフォーマンスに影響しますか。

Azure Migrate アプライアンスでは、パフォーマンス データを測定するために、オンプレミスのマシンが継続的にプロファイルされます。 このプロファイル作成は、プロファイル対象のコンピューターのパフォーマンスにほとんど影響を与えません。

### <a name="can-i-harden-the-appliance-vm"></a>アプライアンス VM を強化することはできますか。

ダウンロードしたテンプレートを使用してアプライアンス VM を作成する場合、Azure Migrate アプライアンスに必要な通信規則およびファイアウォール規則を設定したままにしておく限り、コンポーネント (ウイルス対策など) をテンプレートに追加できます。


## <a name="what-network-connectivity-is-needed"></a>どのようなネットワーク接続が必要ですか。

ネットワーク接続に関する情報を確認します。
- Azure Migrate アプライアンスを使用した VMware 評価: [URL](migrate-appliance.md#url-access) と[ポート](migrate-support-matrix-vmware.md#port-access)のアクセス要件。
- Azure Migrate アプライアンスを使用した VMware エージェントレス移行: [URL](migrate-appliance.md#url-access) と[ポート](migrate-support-matrix-vmware-migration.md#agentless-ports)のアクセス要件。
- Azure Migrate アプライアンスを使用した Hyper-V 評価: [URL](migrate-appliance.md#url-access) と[ポート](migrate-support-matrix-hyper-v.md#port-access)のアクセス要件。


## <a name="what-data-does-the-appliance-collect"></a>アプライアンスはどのようなデータを収集しますか。

収集されたデータを確認してください。

- VMware VM の[パフォーマンス データ](migrate-appliance.md#collected-performance-data-vmware)と[メタデータ](migrate-appliance.md#collected-metadata-vmware)。
- Hyper-V VM の[パフォーマンス データ](migrate-appliance.md#collected-performance-data-hyper-v)と[メタデータ](migrate-appliance.md#collected-metadata-hyper-v)。


## <a name="how-is-data-stored"></a>データをどのように格納するか

Azure Migrate アプライアンスによって収集されたデータは、Azure Migrate プロジェクトを作成した Azure の場所に格納されます。 

- データは Microsoft サブスクリプションに安全に格納され、Azure Migrate プロジェクトを削除すると削除されます。
- [依存関係の視覚化](concepts-dependency-visualization.md)を使用する場合、収集されたデータは、Azure サブスクリプションで作成された米国の Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。

## <a name="how-much-data-is-uploaded-in-continuous-profiling"></a>継続的プロファイリングでアップロードされるデータの量はどのくらいですか。

Azure Migrate に送信されるデータの量は、いくつかのパラメーターによって変わります。 概要を説明すると、10 台のマシン (それぞれ 1 台のディスクと 1 つの NIC を搭載) の Azure Migrate プロジェクトからは、1 日あたり約 50 MB が送信されます。 この値は概算で、NIC とディスクのデータ ポイント数に基づいて変化します。 マシン、NIC、またはディスクの数が増加した場合、送信されるデータの増加は非線形です。

## <a name="is-data-encrypted-at-restin-transit"></a>保存時/転送中のデータは暗号化されますか。

どちらも「はい」です。

- メタデータは HTTPS を介してインターネット上で安全に Azure Migrate サービスに送信されます。
- メタデータは、Microsoft サブスクリプションの [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) データベースと [Azure Blob ストレージ](../storage/common/storage-service-encryption.md)に格納されます。 メタデータは保存時に暗号化されます。
- また、依存関係の分析用データも転送中に暗号化されます (セキュア HTTPS)。 これは、サブスクリプションの Log Analytics ワークスペースに格納されます。 また、保存時にも暗号化されます。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>アプライアンスはどのように vCenter Server に接続しますか。

1. アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。
2. アプライアンスは VMware PowerCLI を使用して vCenter Server に照会し、vCenter Server が管理する VM に関するメタデータを収集します。
3. アプライアンスは、VM に関する構成データ (コア、メモリ、ディスク、NIC) と、過去 1 か月間の各 VM のパフォーマンス履歴を収集します。
4. 収集されたメタデータは、Azure Migrate:Server Assessment に送信され (インターネット経由、HTTPS)、評価されます。

## <a name="can-i-connect-the-appliance-to-multiple-vcenter-servers"></a>1 つのアプライアンスを複数の vCenter Server に接続できますか。

いいえ。 アプライアンスと vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server インスタンス上の VM を検出する場合は、複数のアプライアンスをデプロイする必要があります。

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>1 つのアプライアンスで検出できる VM またはサーバーの数を教えてください

1 つのアプライアンスで最大 10,000 までの VMware VM、最大 5,000 までの Hyper-V VM、および最大 250 までの物理サーバーを検出できます。 オンプレミス環境にさらに多くのマシンがある場合は、[Hyper-V](scale-hyper-v-assessment.md) 評価、[VMware](scale-vmware-assessment.md) 評価、および[物理](scale-physical-assessment.md)評価のスケーリングに関する記事を参照してください。

## <a name="can-i-delete-an-appliance"></a>アプライアンスを削除できますか。

現在、プロジェクトからアプライアンスを削除することはサポートされていません。

- アプライアンスを削除する唯一の方法は、アプライアンスに関連付けられた Azure Migrate プロジェクトを含むリソース グループを削除することです。
- ただし、リソース グループを削除すると、リソース グループ内のプロジェクトに関連付けられている他の登録済みアプライアンス、検出されたインベントリ、評価、およびその他すべての Azure コンポーネントも削除されます。


## <a name="can-i-use-the-appliance-with-a-different-subscriptionproject"></a>別のサブスクリプションまたはプロジェクトでアプライアンスを使用できますか。

アプライアンスを使用して検出を開始した後、別の Azure サブスクリプションでアプライアンスを再構成したり、別の Azure Migrate プロジェクトでアプライアンスを使用したりすることはできません。 また、別の vCenter Server 上の VM を検出することもできません。 これらのタスクには新しいアプライアンスを設定してください。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM 上にアプライアンスを設定できますか。
いいえ。 これは現在サポートされていません。 

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi ホスト上で検出できますか。
いいえ。 VMware VM を検出するには vCenter Server が必要です。

## <a name="how-do-i-update-the-appliance"></a>アプライアンスを更新するにはどうすればよいですか。

既定では、アプライアンスとそのインストール済みエージェントは自動的に更新されます。 アプライアンスでは、24 時間ごとに 1 回、更新プログラムがチェックされます。 更新プロセスが失敗した場合は、再試行されます。 自動更新では、アプライアンスとアプライアンス エージェントのみが更新されます。 オペレーティング システムは更新されません。 オペレーティング システムを最新の状態に保つには、Microsoft Update を使用してください。

## <a name="can-i-check-agent-health"></a>エージェントの正常性を確認できますか。

ポータルで、Server Assessment または Server Migration ツールの **[エージェントの正常性]** ページに移動します。 そこで、アプライアンスと Azure 上の検出エージェントと評価エージェント間の接続状態を確認することができます。

## <a name="next-steps"></a>次のステップ
[Azure Migrate の概要](migrate-services-overview.md)を確認します。
