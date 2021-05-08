---
title: Azure Migrate アプライアンスの FAQ
description: Azure Migrate アプライアンスに関する一般的な質問の回答を示します。
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 059a1888b529487f2b0d17509370897222a20d83
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105563023"
---
# <a name="azure-migrate-appliance-common-questions"></a>Azure Migrate アプライアンス:一般的な質問

この記事では、Azure Migrate アプライアンスに関する一般的な質問の回答を示します。 その他の質問については、次のリソースを確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md) に関する質問
- [サーバー移行](common-questions-server-migration.md) に関する質問
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問の回答を示します。

## <a name="what-is-the-azure-migrate-appliance"></a>Azure Migrate アプライアンスとは何ですか。

Azure Migrate アプライアンスは、オンプレミスまたはクラウドの物理または仮想サーバーを検出および評価するために Azure Migrate: Discovery and assessment ツールによって使用される軽量アプライアンスです。 Azure Migrate: Server Migration ツールでも、VMware 環境で実行されているオンプレミス サーバーのエージェントレス移行を行うためにアプライアンスを使用します。

Azure Migrate アプライアンスに関する詳細を示します。

- アプライアンスは、物理サーバーまたは仮想化サーバーとしてオンプレミスにデプロイされます。
- アプライアンスはオンプレミスのサーバーを検出し、サーバーのメタデータとパフォーマンス データを Azure Migrate に継続的に送信します。
- アプライアンスによる検出はエージェントレスです。 検出されたサーバーには何もインストールされません。

アプライアンスの[詳細をご覧ください](migrate-appliance.md)。

## <a name="how-can-i-deploy-the-appliance"></a>アプライアンスをデプロイするにはどうすればよいですか。

このアプライアンスは、次のいくつかの方法を使用してデプロイできます。

- アプライアンスは、VMware または Hyper-V 環境で実行されているサーバー用のテンプレート ([VMware の場合は OVA テンプレート](how-to-set-up-appliance-vmware.md)、[Hyper-V の場合は VHD](how-to-set-up-appliance-hyper-v.md)) を使用してデプロイできます。
- テンプレートを使用しない場合は、[PowerShell インストーラー スクリプト](deploy-appliance-script.md)を使って VMware または Hyper-V 環境用のアプライアンスをデプロイできます。
- Azure Government では、PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイする必要があります。 デプロイの手順については、[こちら](deploy-appliance-script-government.md)を参照してください。
- オンプレミスまたはその他のクラウドの物理あるいは仮想化されたサーバーの場合は、常に PowerShell インストーラー スクリプトを使用してアプライアンスをデプロイします。デプロイの手順については、[こちら](how-to-set-up-appliance-physical.md)を参照してください。

## <a name="how-does-the-appliance-connect-to-azure"></a>アプライアンスはどのように Azure に接続しますか。

アプライアンスはインターネット経由、または Azure ExpressRoute を使用して接続できます。 

- アプライアンスがそれらの [Azure URL](./migrate-appliance.md#url-access) に接続できることを確認してください。 
- ExpressRoute と Microsoft ピアリングを使用できます。 パブリック ピアリングは非推奨となり、新しい ExpressRoute 回線には使用できません。
- プライベート ピアリングのみはサポートされていません。


## <a name="does-appliance-analysis-affect-performance"></a>アプライアンス分析はパフォーマンスに影響しますか。

Azure Migrate アプライアンスでは、パフォーマンス データを測定するために、オンプレミスのサーバーが継続的にプロファイルされます。 このプロファイル作成は、プロファイル対象のサーバーのパフォーマンスにほとんど影響を与えません。

## <a name="can-i-harden-the-appliance"></a>アプライアンスを強化することはできますか。

ダウンロードしたテンプレートを使用してアプライアンスを作成する場合、Azure Migrate アプライアンスに必要な通信規則およびファイアウォール規則を設定したままにしておくと、コンポーネント (ウイルス対策など) をテンプレートに追加できます。

## <a name="what-network-connectivity-is-required"></a>どのようなネットワーク接続が必要ですか。

アプライアンスは Azure の URL にアクセスできる必要があります。 URL リストを[確認してください](migrate-appliance.md#url-access)。

## <a name="what-data-does-the-appliance-collect"></a>アプライアンスはどのようなデータを収集しますか。

Azure Migrate アプライアンスがサーバー上で収集するデータの詳細については、次の記事を参照してください。

- **VMware 環境のサーバー**: 収集データを[レビュー](migrate-appliance.md#collected-data---vmware)します。
- **Hyper-V 環境のサーバー**: 収集データを[レビュー](migrate-appliance.md#collected-data---hyper-v)します。
- **物理サーバーまたは仮想サーバー**: 収集データを [レビュー](migrate-appliance.md#collected-data---physical)します。

## <a name="how-is-data-stored"></a>データをどのように格納するか

Azure Migrate アプライアンスによって収集されたデータは、プロジェクトを作成した Azure の場所に格納されます。

データの格納方法の詳細については、こちらをご覧ください。

- 収集したデータは、Microsoft サブスクリプションの CosmosDB に安全に格納されます。 このデータは、プロジェクトを削除すると削除されます。 ストレージは Azure Migrate によって処理されます。 収集したデータに対してストレージ アカウントを具体的に選択することはできません。
- [依存関係の視覚化](concepts-dependency-visualization.md)を使用する場合、収集されたデータは、Azure サブスクリプションで作成された Azure Log Analytics ワークスペースに格納されます。 サブスクリプションの Log Analytics ワークスペースを削除すると、このデータは削除されます。

## <a name="how-much-data-is-uploaded-during-continuous-profiling"></a>継続的プロファイリングでアップロードされるデータの量はどのくらいですか。

Azure Migrate に送信されるデータの量は、複数のパラメーターによって変わります。 例として、10 台のサーバー (それぞれ 1 つのディスクと 1 つの NIC) を持つプロジェクトは、1 日あたり約 50 MB のデータを送信します。 この値は概数です。実際の値は、ディスクと NIC のデータポイントの数によって異なります。 サーバー、ディスク、または NIC の数が増える場合、送信されるデータの増加は非線形です。

## <a name="is-data-encrypted-at-rest-and-in-transit"></a>保存時と転送中のデータは暗号化されますか。

どちらも「はい」です。

- メタデータは HTTPS を介してインターネット上で安全に Azure Migrate サービスに送信されます。
- メタデータは、Microsoft サブスクリプションの [Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) データベースと [Azure Blob ストレージ](../storage/common/storage-service-encryption.md)に格納されます。 メタデータは保存時に暗号化されます。
- また、依存関係の分析用データも転送中に暗号化されます (セキュア HTTPS)。 これは、サブスクリプションの Log Analytics ワークスペースに格納されます。 データは、依存関係分析のために保存時に暗号化されます。

## <a name="how-does-the-appliance-connect-to-vcenter-server"></a>アプライアンスはどのように vCenter Server に接続しますか。

次の手順では、アプライアンスが VMware vCenter Server に接続する方法について説明します。

1. アプライアンスは、アプライアンスの設定時に提供された資格情報を使って vCenter Server (ポート 443) に接続します。
2. アプライアンスは VMware PowerCLI を使用して vCenter Server に照会し、vCenter Server が管理するサーバーに関するメタデータを収集します。
3. アプライアンスは、サーバーに関する構成データ (コア、メモリ、ディスク、NIC) と、過去 1 か月間の各サーバーのパフォーマンス履歴を収集します。
4. 収集されたメタデータは、評価のために Azure Migrate: Discovery and assessment ツールへ (インターネットを介して HTTPS 経由で) 送信されます。

## <a name="can-the-azure-migrate-appliance-connect-to-multiple-vcenter-servers"></a>Azure Migrate アプライアンスは複数の vCenter サーバーに接続できますか。

いいえ。 [Azure Migrate アプライアンス](migrate-appliance.md) と vCenter Server との間には一対一のマッピングが存在します。 複数の vCenter Server インスタンス上のサーバーを検出する場合は、複数のアプライアンスをデプロイする必要があります。

## <a name="can-a-project-have-multiple-appliances"></a>プロジェクトに複数のアプライアンスを含めることはできますか。

1 つのプロジェクトに複数のアプライアンスを登録することができます。 ただし、1 つのアプライアンスに登録できるのは、1 つのプロジェクトだけです。

## <a name="can-the-azure-migrate-appliancereplication-appliance-connect-to-the-same-vcenter"></a>Azure Migrate アプライアンス/レプリケーション アプライアンスは同じ vCenter に接続できますか。

はい。 Azure Migrate アプライアンス (評価とエージェントレスの VMware 移行に使用) とレプリケーション アプライアンス (VMware 上で実行されているサーバーのエージェントベースの移行に使用) の両方を、同じ vCenter サーバーに追加できます。 ただし、同じサーバー上に両方のアプライアンスを設定していないことと、現在サポートされていないことを確認してください。

## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>1 つのアプライアンスで検出できるサーバーの数を教えてください

1 つのアプライアンスで最大 10,000 までの VMware 環境のサーバー、最大 5,000 までの Hyper-V 環境のサーバー、および最大 1000 までの物理サーバーを検出できます。 オンプレミスの環境に多くのサーバーがある場合は、「[Hyper-v の評価のスケーリング](scale-hyper-v-assessment.md)」、「[VMware の評価のスケーリング](scale-vmware-assessment.md)」、「[物理サーバーの評価のスケーリング](scale-physical-assessment.md)」を参照してください。

## <a name="can-i-delete-an-appliance"></a>アプライアンスを削除できますか。

現在、プロジェクトからアプライアンスを削除することはサポートされていません。

アプライアンスを削除する唯一の方法は、アプライアンスに関連付けられたプロジェクトを含むリソース グループを削除することです。

ただし、リソース グループを削除すると、リソース グループ内のプロジェクトに関連付けられている他の登録済みアプライアンス、検出されたインベントリ、評価、およびその他すべての Azure コンポーネントも削除されます。

## <a name="can-i-use-the-appliance-with-a-different-subscription-or-project"></a>別のサブスクリプションまたはプロジェクトでアプライアンスを使用できますか。

別のサブスクリプションまたはプロジェクトでアプライアンスを使用するには、アプライアンス サーバーで特定のシナリオ (VMware/Hyper-V/物理) の PowerShell インストーラー スクリプトを実行して、既存のアプライアンスを再構成する必要があります。 このスクリプトでは、既存のアプライアンス コンポーネントと設定がクリーンアップされ、新しいアプライアンスがデプロイされます。 新たにデプロイされたアプライアンス構成マネージャーの使用を開始する前に、ブラウザーのキャッシュを必ず消去してください。

また、再構成されたアプライアンスでは、既存のプロジェクト キーを再利用することができません。 アプライアンスの登録を完了するには、目的のサブスクリプションまたはプロジェクトから、必ず新しいキーを生成してください。

## <a name="can-i-set-up-the-appliance-on-an-azure-vm"></a>Azure VM 上にアプライアンスを設定できますか。

いいえ。 現在、このオプションはサポートされていません。

## <a name="can-i-discover-on-an-esxi-host"></a>ESXi ホスト上で検出できますか。

いいえ。 VMware 環境のサーバーを検出するには vCenter Server が必要です。

## <a name="how-do-i-update-the-appliance"></a>アプライアンスを更新するにはどうすればよいですか。

既定では、アプライアンスとそのインストール済みエージェントは自動的に更新されます。 アプライアンスでは、24 時間ごとに更新プログラムがチェックされます。 失敗した更新は再試行されます。 

これらの自動更新によって更新されるのは、アプライアンスとアプライアンス エージェントだけです。 オペレーティング システムは、Azure Migrate の自動更新では更新されません。 オペレーティング システムを最新の状態に保つには、Windows Update を使用してください。

## <a name="can-i-check-agent-health"></a>エージェントの正常性を確認できますか。

はい。 ポータルで、Azure Migrate: Discovery and assessment または Azure Migrate: Server Migration ツールの **[エージェントの正常性]** ページに移動します。 そこで、Azure とアプライアンス上の検出エージェントと評価エージェント間の接続状態を確認することができます。

## <a name="can-i-add-multiple-server-credentials-on-vmware-appliance"></a>VMware アプライアンスに複数のサーバー資格情報を追加できますか。

はい。ソフトウェア インベントリ (インストールされているアプリケーションの検出)、エージェントレスの依存関係の分析、SQL Server インスタンスとデータベースの検出を実行するために、複数のサーバー資格情報がサポートされるようになりました。 アプライアンス構成マネージャーで資格情報を指定する方法については、[こちら](tutorial-discover-vmware.md#provide-server-credentials)を参照してください。

## <a name="what-type-of-server-credentials-can-i-add-on-the-vmware-appliance"></a>VMware アプライアンスにはどのような種類のサーバー資格情報を追加できますか。
アプライアンス構成マネージャーでは、ドメイン、Windows (ドメイン以外)、Linux (ドメイン以外)、SQL Server の認証資格情報を指定できます。 資格情報の指定方法とそれらの処理方法の詳細については、[こちら](add-server-credentials.md)を参照してください。

## <a name="what-type-of-sql-server-connection-properties-are-supported-by-azure-migrate-for-sql-discovery"></a>SQL 検出用に Azure Migrate でサポートされている SQL Server 接続プロパティの種類は何ですか。
Azure Migrate により、Azure Migrate アプライアンスとソース SQL Server インスタンスの間の通信が暗号化されます (Encrypt 接続プロパティが TRUE に設定されている場合)。 これらの接続は、[TrustServerCertificate](/dotnet/api/system.data.sqlclient.sqlconnectionstringbuilder.trustservercertificate) で暗号化されます (TRUE に設定時)。トランスポート層は、SSL を使用してチャネルを暗号化し、証明書チェーンをバイパスして信頼を検証します。 アプライアンス サーバーは、[証明書のルート証明機関を信頼](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)するように設定する必要があります。

サーバーの起動時に証明書がサーバーでプロビジョニングされない場合、SQL Server ではログオン パケットの暗号化に使用する自己署名入りの証明書が生成されます。 [詳細については、こちらを参照してください](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)。


## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。