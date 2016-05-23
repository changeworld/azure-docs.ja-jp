<properties
	pageTitle="Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する"
	description="この記事では、Azure CLI を使用した、プラットフォームでサポートされるサービス管理から Azure Resource Manager への移行サービス機能について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="mahthi"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="mahthi"/>

# Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する

ここでは、Azure CLI コマンドを使用して IaaS リソースをクラシックから Resource Manager に移行する手順について説明します。この記事では、[Azure CLI](../xplat-cli-install.md) のログインが必要です (`azure login`)。

## 手順 1: 移行を準備する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- サポートされていない構成または機能の一覧については、[こちら](virtual-machines-windows-migration-classic-resource-manager.md)を参照してください。サポートされていない構成または機能を使用する Virtual Machines がある場合、機能や構成のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
-	現時点で、インフラストラクチャとアプリケーションをデプロイする自動スクリプトがある場合は、そのスクリプトを移行に使用して、同様のテスト セットアップを作成して見てください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。
- このサービスはパブリック プレビュー段階なので、移行用のテスト環境は、必ず運用環境から分離してください。また、テスト環境と運用環境で、ストレージ アカウント、VNet などのリソースが混在しないようにしてください。

## 手順 2: サブスクリプションを設定し、移行のパブリック プレビューにサインアップする

移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。[Azure CLI をインストール](../xplat-cli-install.md)し、[サブスクリプションを選択](../xplat-cli-connect.md)します。

次のコマンドを使用して Azure サブスクリプションを選択します

	azure account set "azure-subscription-name"

次のコマンドを使用して、パブリック プレビューにサインアップします。場合によっては、このコマンドがタイムアウトすることもありますが、登録は成功します。次の手順を実行して、登録の状態を確認してください。

	azure provider register Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分待ってください。次のコマンドを使用して、承認の状態を確認できます。次に進む前に、RegistrationState が 'Registered' であることを確認します。

	azure provider show Microsoft.ClassicInfrastructureMigrate

次に、CLI を asm モードに切り替えます

	azure config mode asm

## 手順 3: IaaS リソースを移行するコマンド

>[AZURE.NOTE] 次に示すすべての操作はべき等であることに注意してください。サポートされていない機能や構成エラー以外の場合は、準備、中止またはコミット操作を再試行し、プラットフォームでアクションを再試行することをお勧めします。

### (Virtual Network ではなく) クラウド サービス内の Virtual Machines を移行する

次のコマンドを使用して Cloud Services の一覧を取得し、移行するクラウド サービスを選択します。Cloud Services の VM が VNet 内にある場合、または web/worker ロールがある場合、エラー メッセージが返されます。

	azure service list

次のコマンドを実行して、詳細出力からクラウド サービスのデプロイ名を取得します。ほとんどの場合、デプロイ名はクラウド サービス名と同じです。

	azure service show servicename -vv

移行に合わせてクラウド サービスの Virtual Machines を準備します。2 つのオプションから選択できます。

Virtual Network を作成したプラットフォームに VM を移行する場合、次のコマンドを使用します。

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Resource Manager デプロイメント モデルで既存の Virtual Network に移行する場合、次のコマンドを使用します。

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

準備操作が成功したら、詳細出力で VM の移行状態を検索し、VM が 'Prepared' 状態であることを確認します。

	azure vm show vmName -vv

CLI または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができておらず、古い状態に戻すには、次のコマンドを使用します。

	azure service deployment abort-migration serviceName deploymentName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure service deployment commit-migration serviceName deploymentName

### Virtual Network の Virtual Machines を移行する

移行する Virtual Network を選択します。Virtual Network に web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合、検証エラー メッセージが返されます。

次のコマンドを使用して、サブスクリプションのすべての Virtual Network を取得します。

	azure network vnet list

次のコマンドを使用して、移行対象の Virtual Network を準備します。

	azure network vnet prepare-migration virtualnetworkname

CLI または Azure ポータルを使用して、準備した Virtual Machines の構成を確認します。移行の準備ができておらず、古い状態に戻すには、次のコマンドを使用します。

	azure network vnet abort-migration virtualnetworkname

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure network vnet commit-migration virtualnetworkname

## 参照

- [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0511_2016-->