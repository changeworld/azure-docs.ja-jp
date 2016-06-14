<properties
	pageTitle="Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する | Microsoft Azure"
	description="この記事では、Azure CLI を使用した、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について説明します。"
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

以下の手順では、Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法を説明します。この記事では、[Azure CLI](../xplat-cli-install.md) サインイン (`azure login`) が必要です。

## 手順 1. 移行を準備する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- [サポートされていない構成または機能の一覧](virtual-machines-windows-migration-classic-resource-manager.md)をご確認ください。サポートされていない構成または機能を使用する仮想マシンがある場合、機能または構成のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
-	インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。
- このサービスはパブリック プレビュー段階なので、移行用のテスト環境は、必ず運用環境から分離してください。また、テスト環境と運用環境で、ストレージ アカウント、仮想ネットワークなどのリソースが混在しないようにしてください。

## 手順 2. サブスクリプションを設定し、移行のパブリック プレビューにサインアップする

移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。[Azure CLI をインストール](../xplat-cli-install.md)し、[サブスクリプションを選択](../xplat-cli-connect.md)します。

次のコマンドを使用して Azure サブスクリプションを選択します

	azure account set "azure-subscription-name"

次のコマンドを使用して、パブリック プレビューにサインアップします。次のコマンドは、場合によってはタイムアウトになるため注意してください。ただし、登録は成功します。

	azure provider register Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分間お待ちください。承認の状態を確認するには、次のコマンドを使用します。RegistrationState が `Registered` であることを確認してから続行してください。

	azure provider show Microsoft.ClassicInfrastructureMigrate

次に、CLI を `asm` モードに切り替えます。

	azure config mode asm

## 手順 3. IaaS リソースを移行するコマンドを実行する

>[AZURE.NOTE] ここで説明するすべての操作がべき等です。サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。これによりプラットフォームでアクションが再試行されます。

### クラウド サービス内 (仮想ネットワーク外) で仮想マシンを移行する

次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。クラウド サービスの VM が仮想ネットワーク内にある場合、または Web/worker ロールを持つ場合は、エラー メッセージが返されます。

	azure service list

次のコマンドを実行して、詳細出力からクラウド サービスのデプロイ名を取得します。ほとんどの場合、デプロイ名はクラウド サービス名と同じです。

	azure service show servicename -vv

移行に合わせてクラウド サービスの仮想マシンを準備します。2 つのオプションから選択できます。

プラットフォームで作成した仮想ネットワークに VM を移行する場合は、次のコマンドを使用します。

	azure service deployment prepare-migration servicename deploymentname new "" "" ""

Resource Manager デプロイメント モデルの既存の仮想ネットワークに移行する場合は、次のコマンドを使用します。

	azure service deployment prepare-migration serviceName deploymentName existing destinationVNETResourceGroupName subnetName vnetName

準備操作が成功したら、詳細出力で VM の移行状態を検索し、VM が `Prepared` 状態であることを確認します。

	azure vm show vmName -vv

CLI または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	azure service deployment abort-migration serviceName deploymentName

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure service deployment commit-migration serviceName deploymentName

### 仮想ネットワークの仮想マシンを移行する

移行する仮想ネットワークを選択します。仮想ネットワークに Web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

次のコマンドを使用して、サブスクリプションのすべての仮想ネットワークを取得します。

	azure network vnet list

次のコマンドを使用して、移行対象の仮想ネットワークを準備します。

	azure network vnet prepare-migration virtualnetworkname

CLI または Azure ポータルを使用して、準備した仮想マシンの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	azure network vnet abort-migration virtualnetworkname

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure network vnet commit-migration virtualnetworkname

## 次のステップ

- [プラットフォームでサポートされているクラシックから Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0601_2016-->