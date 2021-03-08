---
title: Azure Migrate アプライアンスの FAQ
description: Azure Migrate アプライアンスに関する一般的な質問の回答を示します。
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 09/15/2020
ms.openlocfilehash: 9badbfe6cfe12d67e07f0889d175ed32bc455321
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753877"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate アプライアンス:一般的な質問

この記事では、Azure Migrate アプライアンスに関する一般的な質問の回答を示します。 その他の質問については、次のリソースを確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md) に関する質問
- [サーバー移行](common-questions-server-migration.md) に関する質問
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問の回答を示します。

## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスとは何ですか。

Azure Migrate アプライアンスは、Azure Migrate が次のことを行う、軽量アプライアンスです。サーバー評価ツールが使用して、オンプレミスまたは任意のクラウドの物理サーバーまたは仮想サーバーを検出して評価する、軽量アプライアンスです。 Azure Migrate: オンプレミス VMware VM のエージェントレス移行を行うためにアプライアンスも使用するサーバー移行ツールです。

Azure Migrate アプライアンスに関する詳細を示します。

- アプライアンスは、VM または物理マシンとしてオンプレミスにデプロイされます。
- アプライアンスはオンプレミスのマシンを検出し、マシンのメタデータとパフォーマンス データを Azure Migrate に継続的に送信します。
- アプライアンスによる検出はエージェントレスです。 検出されたマシンには何もインストールされません。

アプライアンスの[詳細をご覧ください](migrate-appliance.md)。

## <a name="how-can-i-deploy-the-appliance"></a>アプライアンスをデプロイするにはどうすればよいですか。

アプライアンスは次のようにデプロイできます。

- VMware VM (.OVA ファイル) および Hyper-V VM (.VHD ファイル) を検出するためのテンプレートを使用して、アプライアンスをホストする新しい VM を作成します。
- テンプレートを使用しない場合は、ポータルから zip ファイルでダウンロードできる PowerShell インストーラー スクリプトを使用して、VMware VM または Hyper-V VM を検出するための既存の物理マシンまたは仮想マシンにアプライアンスをデプロイすることができます。
- オンプレミスまたは任意のクラウドの物理サーバーまたは仮想サーバーの場合は、既存のサーバー上のスクリプトを使用して、常にアプライアンスをデプロイします。
- Azure Government では、3 つのアプライアンスすべてを、PowerShell インストーラー スクリプトのみを使用してデプロイできます。

## <a name="how-does-the-appliance-connect-to-azure"></a>アプライアンスはどのように Azure に接続しますか。

アプライアンスはインターネット経由、または Azure ExpressRoute を使用して接続できます。 

- アプライアンスがそれらの [Azure URL](./migrate-appliance.md#url-access) に接続できることを確認してください。 
- ExpressRoute と Microsoft ピアリングを使用できます。  パブリック ピアリングは非推奨となり、新しい ExpressRoute 回線には使用できません。
- プライベート ピアリングのみはサポートされていません。



## <a name="does-appliance-analysis-affect-performance"></a>アプライアンス分析はパフォーマンスに影響しますか。

Azure Migrate アプライアンスでは、パフォーマンス データを測定するために、オンプレミスのマシンが継続的にプロファイルされます。 このプロファイル作成は、プロファイル対象のマシンのパフォーマンスにほとんど影響を与えません。

## <a name="can-i-harden-the-appliance-vm"></a>アプライアンス VM を強化することはできますか。

ダウンロードしたテンプレートを使用してアプライアンス VM を作成する場合、Azure Migrate アプライアンスに必要な通信規則およびファイアウォール規則を設定したままにしておくと、コンポーネント (ウイルス対策など) をテンプレートに追加できます。

## <a name="what-network-connectivity-is-required"></a>どのようなネットワーク接続が必要ですか。

アプライアンスは Azure の URL にアクセスできる必要があります。 URL リストを[確認してください](migrate-appliance.md#url-access)。

## <a name="what-data-does-the-appliance-collect"></a>アプライアンスはどのようなデータを収集しますか。

Azure Migrate アプライアンスが VM 上で収集するデータの詳細については、次の記事を参照してください。

- **VMware VM**:収集データを [レビュー](migrate-appliance.md#collected-data---vmware)します。
- **Hyper-V VM**:収集データを [レビュー](migrate-appliance.md#collected-data---hyper-v)します。
- **物理サーバーまたは仮想サーバー**: 収集データを [レビュー](migrate-appliance.md#collected-data---physical)します。

## <a name="how-is-data-stored"></a>データをどのように格納するか

Azure Migrate アプライアンスによって収集されたデータは、Azure Migrate プロジェクトを作成した Azure の場所に格納されます。

データの格納方法の詳細については、こちらをご覧ください。

- 収集したデータは、Microsoft サブスクリプションの CosmosDB に安全に格納されます。 このデータは、Azure Migrate プロジェクトを削除すると削除されます。 ストレージは Azure Migrate によって処理されます。 収集したデータに対してストレージ アカウントを具体的に選択することはできません。
- [依存関係の視覚化](concepts-dependency-visualization.md)を使用する場合、収集されたデータは、Azure サブスクリプションで作成された Azure Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。 

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>継続的プロファイリングでアップロードされるデータの量はどのくらいですか。

Azure Migrate に送信されるデータの量は、複数のパラメーターによって変わります。 例として、10 台のマシン (それぞれ 1 つのディスクと 1 つの NIC) を持つ Azure Migrate プロジェクトは、1 日あたり約 50 MB のデータを送信します。 この値は概数です。実際の値は、ディスクと NIC のデータポイントの数によって異なります。 マシン、ディスク、または NIC の数が増える場合、送信されるデータの増加は非線形です。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>保存時と転送中のデータは暗号化されますか。

どちらも「はい」です。

- メタデータは HTTPS を介してインターネット上で安全に Azure Migrate サービスに送信されます。
- メタデータは、Microsoft サブスクリプションの [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) データベースと [Azure Blob ストレージ](../storage/common/storage-service-encryption.md)に格納されます。 メタデータは保存時に暗号化されます。
- また、依存関係の分析用データも転送中に暗号化されます (セキュア HTTPS)。 これは、サブスクリプションの Log Analytics ワークスペースに格納されます。 データは、依存関係分析のために保存時に暗号化されます。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>アプライアンスはどのように vCenter Server に接続しますか。

次の手順では、アプライアンスが VMware vCenter Server に接続する方法について説明します。

1. アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。
2. アプライアンスは VMware PowerCLI を使用して vCenter Server に照会し、vCenter Server が管理する VM に関するメタデータを収集します。
3. アプライアンスは、VM に関する構成データ (コア、メモリ、ディスク、NIC) と、過去 1 か月間の各 VM のパフォーマンス履歴を収集します。
4. 収集されたメタデータは、Azure Migrate に送信されます。評価用 Server Assessment ツール (インターネット経由、HTTPS)。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate アプライアンスは複数の vCenter サーバーに接続できますか。

いいえ。 [Azure Migrate アプライアンス](migrate-appliance.md) と vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server インスタンス上の VM を検出する場合は、複数のアプライアンスをデプロイする必要があります。 

## <a name="can-an-azure-migrate-project-have-multiple-appliances"></a>Azure Migrate のプロジェクトは複数のアプライアンスを持つことができますか。

1 つのプロジェクトに複数のアプライアンスをアタッチすることができます。 ただし、1 つのアプライアンスは、1 つの Azure Migrate リソースにのみ関連付けることができます。 

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate アプライアンス/レプリケーション アプライアンスは同じ vCenter に接続できますか。

はい。 Azure Migrate アプライアンス (評価とエージェントレスの VMware 移行に使用) とレプリケーション アプライアンス (VMware VM のエージェントベースの移行に使用) の両方を、同じ vCenter サーバーに追加できます。 ただし、同じ VM 上に両方のアプライアンスを設定していないことと、現在サポートされていないことを確認してください。

## <a name="how-many-vms-or-servers-can-i-discover-with-an-appliance"></a>1 つのアプライアンスで検出できる VM またはサーバーの数を教えてください

1 つのアプライアンスで最大 10,000 台の VMware VM、最大 5,000 台の Hyper-V VM、および最大 1,000 台の物理サーバーを検出できます。 オンプレミスの環境に多くのマシンがある場合は、「[Hyper-v の評価のスケーリング](scale-hyper-v-assessment.md)」、「[VMware の評価のスケーリング](scale-vmware-assessment.md)」、「[物理サーバーの評価のスケーリング](scale-physical-assessment.md)」を参照してください。

## <a name="can-i-delete-an-appliance"></a>アプライアンスを削除できますか。

現在、プロジェクトからアプライアンスを削除することはサポートされていません。

アプライアンスを削除する唯一の方法は、アプライアンスに関連付けられた Azure Migrate プロジェクトを含むリソース グループを削除することです。

ただし、リソース グループを削除すると、リソース グループ内のプロジェクトに関連付けられている他の登録済みアプライアンス、検出されたインベントリ、評価、およびその他すべての Azure コンポーネントも削除されます。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>別のサブスクリプションまたはプロジェクトでアプライアンスを使用できますか。

別のサブスクリプションまたはプロジェクトでアプライアンスを使用するには、アプライアンス マシンで特定のシナリオ (VMware/Hyper-V/物理) の PowerShell インストーラー スクリプトを実行して、既存のアプライアンスを再構成する必要があります。 このスクリプトでは、既存のアプライアンス コンポーネントと設定がクリーンアップされ、新しいアプライアンスがデプロイされます。 新たにデプロイされたアプライアンス構成マネージャーの使用を開始する前に、ブラウザーのキャッシュを必ず消去してください。

また、再構成されたアプライアンスでは、既存の Azure Migrate プロジェクト キーを再利用することができません。 アプライアンスの登録を完了するには、目的のサブスクリプションまたはプロジェクトから、必ず新しいキーを生成してください。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM 上にアプライアンスを設定できますか。

いいえ。 現在、このオプションはサポートされていません。 

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi ホスト上で検出できますか。

いいえ。 VMware VM を検出するには vCenter Server が必要です。

## <a name="how-do-i-update-the-appliance"></a>アプライアンスを更新するにはどうすればよいですか。

既定では、アプライアンスとそのインストール済みエージェントは自動的に更新されます。 アプライアンスでは、24 時間ごとに更新プログラムがチェックされます。 失敗した更新は再試行されます。 

これらの自動更新によって更新されるのは、アプライアンスとアプライアンス エージェントだけです。 オペレーティング システムは、Azure Migrate の自動更新では更新されません。 オペレーティング システムを最新の状態に保つには、Windows Update を使用してください。

## <a name="can-i-check-agent-health"></a>エージェントの正常性を確認できますか。

はい。 ポータルで、Azure Migrate の **エージェントの正常性** ページにアクセスします。Server Assessment または Azure Migrate:サーバー移行ツール。 そこで、Azure とアプライアンス上の検出エージェントと評価エージェント間の接続状態を確認することができます。

## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。