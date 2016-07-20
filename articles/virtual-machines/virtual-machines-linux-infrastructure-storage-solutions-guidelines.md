<properties
	pageTitle="ストレージ ソリューションのガイドライン |Microsoft Azure"
	description="Azure インフラストラクチャ サービスでのストレージ ソリューションのデプロイに関する主要な設計と実装のガイドラインについて説明します。"
	documentationCenter=""
	services="virtual-machines-linux"
	authors="iainfoulds"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/22/2016"
	ms.author="iainfou"/>

# ストレージ インフラストラクチャのガイドライン

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)]

この記事は、最適な仮想マシン (VM) のパフォーマンスを実現するための、ストレージのニーズと設計に関する考慮事項について説明します。


## ストレージに関する実装ガイドライン

決めること:

- ワークロードのために Standard Storage または Premium Storage のうちのいずれを使用する必要があるか
- 1023 GB を超えるディスクを作成するためにディスクのストライピングが必要か
- ワークロードに最適な I/O パフォーマンスを実現するためにディスクのストライピングが必要か
- IT ワークロードやインフラストラクチャをホストするために必要なストレージ アカウントのセット

タスク:

- デプロイするアプリケーションの I/O 要求を確認し、適切なストレージ アカウントの数と種類を計画します。
- 名前付け規則を使用してストレージ アカウントのセットを作成します。Azure CLI またはポータルを使用することができます。


## Storage

Azure Storage は仮想マシン (VM) とアプリケーションをデプロイし、管理するための重要な要素です。Azure Storage はファイル データ、構造化されていないデータ、メッセージを保存するためのサービスを提供します。VM をサポートするインフラストラクチャの一部でもあります。

VM をサポートするために 2 種類のストレージ アカウントを使用できます。

- Standard Storage アカウントでは、BLOB ストレージ (Azure VM ディスクの保存に利用)、テーブル ストレージ、キュー ストレージ、ファイル ストレージにアクセスできます。
- [Premium Storage](../storage/storage-premium-storage.md) は、MongoDB シャード クラスターなどの高負荷の I/O ワークロードのための、高パフォーマンスで待ち時間の少ないディスク サポートを提供し、現在は Azure VM ディスクのみをサポートします。

Azure で作成される VM には、オペレーティング システム ディスク、一時ディスク、および 0 個以上のオプションのデータ ディスクが含まれます。オペレーティング システム ディスクとデータ ディスクは Azure ページ BLOB です。一時ディスクは、コンピューターが存在するノードにローカル保存されます。メンテナンス イベント中 VM をホスト間に移行する場合があるため、一時ディスクを非永続的データに使用するためだけにアプリケーションを設計する場合は注意が必要です。一時ディスクに格納されているデータが失われます。

持続性と高可用性が、基盤となる Azure Storage 環境に備わっており、計画外メンテナンスやハードウェアの故障からデータを保護します。Azure Storage 環境を設計する際、VM ストレージのレプリケートを、対象の Azure データセンター内にローカルで行うか、対象のリージョン内の Azure データセンター間で行うか、さらには異なるリージョン間の Azure データセンター間で行うかを選択できます。高可用性のためのレプリケーション オプションの詳細については、[こちら](../storage/storage-introduction.md#replication-for-durability-and-high-availability)をご覧ください。

BLOB の最大サイズは 1024 ギガバイト (GB) で、それには VHD ファイルのメタデータ (フッター) が含まれるため、オペレーティング システム ディスクとデータ ディスクの最大サイズは 1023 GB です (1 GB は 1024<sup>3</sup> バイト)。論理ボリューム マネージャー (LVM) を使用してデータ ディスクをプールし、1023 GB を超える論理ボリュームを VM に割り当てることで、この制限を超えることができます。

Azure Storage のデプロイを設計する際に、いくつかのスケーラビリティ限界があります。詳細については、[Microsoft Azure サブスクリプションとサービスの制限、クォータ、制約](azure-subscription-service-limits.md#storage-limits)に関するページを参照してください。また、「[Azure ストレージのスケーラビリティおよびパフォーマンスのターゲット](../storage/storage-scalability-targets.md)」もご覧ください。

アプリケーション ストレージに関しては、BLOB ストレージを使用して、ドキュメント、イメージ、バックアップ、構成データ、ログなどの非構造化データを保存できます。アプリケーションが VM に接続されている仮想ディスクに書き込むのではなく、アプリケーションが Azure BLOB ストレージに直接書き込むことができます。BLOB ストレージには、可用性ニーズとコスト面の制約に応じて、[ホット ストレージ層とクール ストレージ層](../storage/storage-blob-storage-tiers.md)のオプションも用意されています。


## ストライピングされたディスク
データ ディスクにストライピングを使用すると、1023 GB より大きいディスクを作成できるだけでなく、多くの場合に複数の BLOB で単一ボリュームのストレージをバックアップできるので、パフォーマンスが向上します。ストライピングにより、単一の論理ディスクのデータを読み書きするのに必要な I/O が並列化されます。

Azure では、VM のサイズにより、使用できるデータ ディスクの量と帯域幅が制限されます。詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」を参照してください。

Azure データ ディスクにディスク ストライピングを使用する場合は、次のガイドラインを考慮してください。

- データ ディスクは常に最大サイズ (1023 GB) にする必要があります
- VM のサイズで許可されている最大数のデータ ディスクをアタッチします
- LVM を使用します
- Azure データ ディスクのキャッシュ オプションを使わないようにします (キャッシュ ポリシー = なし)

詳細については、[Linux VM での LVM の構成](virtual-machines-linux-configure-lvm.md)に関するページをご覧ください。


## 複数のストレージ アカウント

Azure Storage 環境を設計するとき、デプロイする VM の増加に伴って複数のストレージ アカウントを使用できます。これにより I/O を基盤となる Azure Storage インフラストラクチャ間に分散させ、VM とアプリケーションの最適なパフォーマンスを維持することができます。デプロイされるアプリケーションを設計する際に、各 VM の I/O 要件を考慮し、Azure Storage アカウント間に VM を分散してください。I/O 要求の高い VM をすべて 1 つまたは 2 つのアカウントだけにグループ化することは避けてください。

さまざまな Azure Storage オプションの I/O 機能と推奨する最大値の詳細については、「[Azure Storage のスケーラビリティおよびパフォーマンスのターゲット](../storage/storage-scalability-targets.md)」を参照してください。


## 次のステップ

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)]

<!---HONumber=AcomDC_0706_2016-->