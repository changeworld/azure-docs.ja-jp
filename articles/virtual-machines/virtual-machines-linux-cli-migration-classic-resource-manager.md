<properties
	pageTitle="Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する | Microsoft Azure"
	description="この記事では、Azure CLI を使用した、プラットフォームでサポートされているクラシックから Azure Resource Manager へのリソースの移行について説明します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/19/2016"
	ms.author="cynthn"/>

# Azure CLI を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する

以下の手順では、Azure コマンド ライン インターフェイス (CLI) コマンドを使用して、サービスとしてのインフラストラクチャ (IaaS) のリソースをクラシック デプロイメント モデルから Azure Resource Manager デプロイメント モデルに移行する方法を説明します。この記事では、[Azure CLI](../xplat-cli-install.md) が必要です。

>[AZURE.NOTE] ここで説明するすべての操作がべき等です。サポートされていない機能や構成エラー以外の問題が発生した場合は、準備、中止、またはコミット操作を再試行することをお勧めします。これによりプラットフォームでアクションが再試行されます。

## 手順 1. 移行を準備する

ここでは、クラシックから Resource Manager への IaaS リソースの移行を評価するときに推奨できるベスト プラクティスをいくつか紹介します。

- [サポートされていない構成または機能の一覧](virtual-machines-windows-migration-classic-resource-manager.md)をご確認ください。サポートされていない構成または機能を使用する仮想マシンがある場合、機能または構成のサポートが発表されるまで待つことをお勧めします。または、必要に応じて、その機能を削除するか、その構成を外して、移行を可能にすることもできます。
-	インフラストラクチャとアプリケーションをデプロイする自動化スクリプトを今お持ちの場合は、これらのスクリプトを移行に使用して、同様のテスト設定を作成してみてください。または、Azure ポータルを使用してサンプル環境をセットアップすることもできます。

## 手順 2. サブスクリプションを設定し、プロバイダーを登録する

移行のシナリオの場合、クラシックと Resource Manager の両方に合わせて環境をセットアップする必要があります。[Azure CLI をインストール](../xplat-cli-install.md)し、[サブスクリプションを選択](../xplat-cli-connect.md)します。

アカウントにサインインします。
	
	azure login

次のコマンドを使用して Azure サブスクリプションを選択します。

	azure account set "<azure-subscription-name>"

>[AZURE.NOTE] 登録は 1 回限りの手順ですが、移行を試みる前に実行する必要があります。登録を行わないと、次のエラー メッセージが表示されます

>	*BadRequest : Subscription is not registered for migration.* 

移行リソース プロバイダーに登録するには、次のコマンドを使用します。次のコマンドは、場合によってはタイムアウトになるため注意してください。ただし、登録は成功します。

	azure provider register Microsoft.ClassicInfrastructureMigrate

登録が完了するまで 5 分間お待ちください。承認の状態を確認するには、次のコマンドを使用します。RegistrationState が `Registered` であることを確認してから続行してください。

	azure provider show Microsoft.ClassicInfrastructureMigrate

次に、CLI を `asm` モードに切り替えます。

	azure config mode asm

## 手順 3. 現在のデプロイメントまたは VNET の Azure リージョンで Azure Resource Manager 仮想マシンのコア数が十分にあることを確認する

この手順では、`arm` モードに切り替える必要があります。これを行うには、次のコマンドを使用します。

```
azure config mode arm
```

次の CLI コマンドを使用すると、Azure Resource Manager での現在のコア数を確認できます。コア クォータの詳細については、「[制限と Azure Resource Manager](../articles/azure-subscription-service-limits.md#limits-and-the-azure-resource-manager)」をご覧ください。

```
azure vm list-usage -l "<Your VNET or Deployment's Azure region"
```

確認が終わったら、`asm` モードに切り替えることができます。

	azure config mode asm


## 手順 4. オプション 1 - クラウド サービスの仮想マシンを移行する 

次のコマンドを使用してクラウド サービスの一覧を取得し、移行するクラウド サービスを選択します。クラウド サービスの VM が仮想ネットワーク内にある場合、または Web/worker ロールを持つ場合は、エラー メッセージが返されます。

	azure service list

次のコマンドを実行して、詳細出力からクラウド サービスのデプロイ名を取得します。ほとんどの場合、デプロイ名はクラウド サービス名と同じです。

	azure service show <serviceName> -vv

移行に合わせてクラウド サービスの仮想マシンを準備します。2 つのオプションから選択できます。

プラットフォームで作成した仮想ネットワークに VM を移行する場合は、次のコマンドを使用します。

	azure service deployment prepare-migration <serviceName> <deploymentName> new "" "" ""

Resource Manager デプロイメント モデルの既存の仮想ネットワークに移行する場合は、次のコマンドを使用します。

	azure service deployment prepare-migration <serviceName> <deploymentName> existing <destinationVNETResourceGroupName> subnetName <vnetName>

準備操作が正常に完了したら、詳細出力で VM の移行状態を調べ、VM が `Prepared` 状態であることを確認します。

	azure vm show <vmName> -vv

CLI または Azure ポータルを使用して、準備したリソースの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	azure service deployment abort-migration <serviceName> <deploymentName>

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure service deployment commit-migration <serviceName> <deploymentName>


	
## 手順 4. オプション 2 - 仮想ネットワーク内の仮想マシンを移行する

移行する仮想ネットワークを選択します。仮想ネットワークに Web/worker ロールが含まれる場合、またはサポートされない構成の VM が含まれる場合は、検証エラー メッセージが返されます。

次のコマンドを使用して、サブスクリプションのすべての仮想ネットワークを取得します。

	azure network vnet list
	
出力は次のようになります。

![仮想ネットワークの名前全体が強調表示されているコマンド ラインのスクリーン ショット。](./media/virtual-machines-linux-cli-migration-classic-resource-manager/vnet.png)

上の例では、**"Group classicubuntu16 classicubuntu16"** 全体が **virtualNetworkName** です。

次のコマンドを使用して、移行対象の仮想ネットワークを準備します。

	azure network vnet prepare-migration <virtualNetworkName>

CLI または Azure ポータルを使用して、準備した仮想マシンの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	azure network vnet abort-migration <virtualNetworkName>

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure network vnet commit-migration <virtualNetworkName>

## 手順 5. ストレージ アカウントを移行する

仮想マシンの移行が完了したら、ストレージ アカウントを移行することをお勧めします。

次のコマンドを使用して、移行対象のストレージ アカウントを準備します

	azure storage account prepare-migration <storageAccountName>

CLI または Azure ポータルを使用して、準備したストレージ アカウントの構成を確認します。移行の準備ができていない場合に以前の状態に戻すには、次のコマンドを使用します。

	azure storage account abort-migration <storageAccountName>

準備した構成が正しい場合は、次に進み、次のコマンドを使用してリソースをコミットできます。

	azure storage account commit-migration <storageAccountName>

## 次のステップ

- [プラットフォームでサポートされているクラシックから Resource Manager への IaaS リソースの移行](virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Resource Manager への移行に関する技術的な詳細](virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)

<!---HONumber=AcomDC_0907_2016-->