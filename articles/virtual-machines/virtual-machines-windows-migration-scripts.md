<properties
	pageTitle="PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを複製する"
	description="この記事では、PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを 1 つ複製する方法を紹介します。"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkays"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/03/2016"
	ms.author="singhkay"/>

# PowerShell スクリプトを使用して Azure Resource Manager にクラシック仮想マシンを複製する

この記事では、[Azure/classic-iaas-resourcemanager-migration](https://github.com/Azure/classic-iaas-resourcemanager-migration) にあるスクリプトを使用し、**1 つ**のクラシック仮想マシンを Azure Resource Manager デプロイメント モデルに複製する方法について説明します。

>[AZURE.IMPORTANT]これらのスクリプトによる複製でクラシック仮想マシンにダウンタイムが発生します。プラットフォームでサポートされている移行については、次を参照してください。
- [プラットフォームでサポートされているクラシックから Azure Resource Manager への IaaS リソースの移行](./virtual-machines-windows-migration-classic-resource-manager.md)
- [プラットフォームでサポートされているクラシックから Azure Resource Manager への移行に関する技術的な詳細](./virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
- [Azure PowerShell を使用してクラシックから Azure Resource Manager へ IaaS リソースを移行する](./virtual-machines-windows-ps-migration-classic-resource-manager.md)

## スクリプトの取得

>[AZURE.NOTE]Microsoft サポートは PowerShell スクリプトを正式にサポートしていません。そのため、PowerShell スクリプトは Github でオープンソースになっており、修正プログラムや追加シナリオの PR を歓迎しています。

スクリプトを取得するには、上記のリポジトリから zip ファイルをダウンロードするか、下のコマンドの**いずれか**を利用してリポジトリを複製します。

```
git clone https://github.com/Azure/classic-iaas-resourcemanager-migration.git
```

**OR**

```
git clone git@github.com:Azure/classic-iaas-resourcemanager-migration.git
```

## 移行モジュールのインポート

次の手順では、PowerShell セッションにモジュールをインポートします。これは下のコマンドで実行できます

```
Import-Module .\asm2arm.psd1
```

## クラシック デプロイメント モデルと Azure Resource Manager サブスクリプションでの認証

クラシック仮想マシンを複製する前に、クラシック デプロイメント モデルと Azure Resource Manager スタックの両方で PowerShell セッションを認証する必要があります。これは次のコマンドレットで実行できます

```
Add-AzureAccount
Login-AzureRmAccount
```

>[AZURE.IMPORTANT]クラシック デプロイメント モデルには `Select-AzureSubscription` を、Azure Resource Manager には Set-AzureRmContext を使用し、既定のサブスクリプションを選択します。

## スクリプトの使用

### コマンドレット

モジュールをインポートすると、下の 2 つのコマンドレットがセッションに追加されます。

```
Add-AzureSMVmToRM
New-AzureSmToRMDeployment
```

これらのコマンドレットにより以下の機能が実行されます。

#### Add-AzureSMVmToRM
- 仮想マシンを所与として、一連の Azure Resource Manager テンプレートと命令型 PowerShell スクリプト (ディスク BLOB をコピーするために、また、VM に VM エージェント拡張機能がある場合) を生成します

>[AZURE.IMPORTANT]`-Deploy` スイッチが指定されている場合、このコマンドレットは `New-AzureSmToRMDeployment` コマンドレットを呼び出し、上で生成された Azure Resource Manager テンプレートと命令型 PowerShell スクリプトをデプロイします。

#### New-AzureSmToRMDeployment
- `Add-AzureSMVmToRM` コマンドレットで生成されたテンプレートと命令型 PowerShell スクリプトをデプロイし、移行を開始します。

### 複製するクラシック仮想マシンを特定する

これは、クラシック仮想マシンの状態を変数に格納し、`Add-AzureSMVmToRM` コマンドレットに渡すか、`ServiceName` パラメーターと VM `Name` パラメーターを直接使用することで実行できます。クラシック仮想マシンを特定したら、`Add-AzureSMVmToRM` コマンドレットで複製できます。

```
$vm = Get-AzureVm -ServiceName acloudservice -Name atestvm
Add-AzureSMVmToRM -VM $vm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

**OR**

```
Add-AzureSMVmToRM -ServiceName acloudservice -Name atestvm -ResourceGroupName aresourcegroupname -DiskAction CopyDisks -OutputFileFolder D:\myarmtemplates -AppendTimeStampForFiles -Deploy
```

>[AZURE.IMPORTANT]上の例では、`-Deploy` スイッチを使用しているため、`New-AzureSmToRMDeployment` コマンドレットを使用する必要があります。

## これらのスクリプトで移行はどのように動作しますか。

コマンドレットは classVM の複製手順を通過し、ストレージ リソース プロバイダー、ネットワーク リソース プロバイダー、コンピューティング リソース プロバイダーのカスタム PowerShell ハッシュ テーブルとしてリソースを生成します。リソースを表すこれらのハッシュ テーブルが配列に追加され、その後、JSON にシリアル化されることでテンプレートに変わり、ファイルに書き込まれます。

クラシック仮想マシンのエージェント拡張機能や DiskAction オプション値の存在に基づき、テンプレートによりファイルが作成されます。これらはすべて OutputFileFolder パラメーターで指定されたディレクトリに置かれます。ファイルは次のとおりです。

1. `<ServiceName>-<VMName>-setup<optional timestamp>.json`: このファイルはクラシック仮想マシンの複製前に準備する必要があるリソースを表し、潜在的に後続のすべての VM で同じになります (後続実行間の状態は保持しませんが、強制的に行われる BLOB コピー操作の前にストレージ アカウントをプロビジョニングする必要があるため、論理的であるのは、同種のリソースを 1 つにグループ化することだけでした)

2.  `<ServiceName>-<VMName>-deploy<optional timestamp>.json`: リソース マネージャー仮想マシンのテンプレートが含まれます
3.  `<ServiceName>-<VMName>-parameters<optional timestamp>.json`: テンプレートに渡された実際のパラメーターが含まれます
4.  `<ServiceName>-<VMName>-setextensions<optional timestamp>.json`: 仮想マシンのエージェント拡張機能を設定するために実行する一連の PowerShell コマンドレット。
4.  `<ServiceName>-<VMName>-copydisks<optional timestamp>.json`: CopyDisks オプションが指定されている場合、ディスク BLOB を複製するために実行する一連の PowerShell コマンドレット。

-Deploy フラグが設定されている場合、ファイルの生成後、コマンドレットは <ServiceName>-<VMName>-setup.json テンプレートをデプロイし、DiskAction パラメーターが CopyDisks に設定されている場合、ソースの仮想マシン ディスク BLOB を複製し、パラメーターの <ServiceName>-<VMName>-parameters.json ファイルを使用して、<ServiceName>-<VMName>-deploy.json テンプレートをデプロイします。仮想マシンのデプロイが完了したとき、(仮想マシンのエージェント拡張機能の) 命令型スクリプトやディスク複製のスクリプトが存在する場合、それらが実行されます。

### ネットワークの詳細
コマンドレットの意図は、クラシック ネットワークの設定をリソース マネージャーに複製することではありません。仮想マシン自体の複製に最も便利な方法でネットワーキング機能を活用します。条件による動作の違いは次のようになります。

1.  ターゲット リソース グループに仮想ネットワークがない
    - ソース仮想マシンがサブネットにない: 10.0.0.0/16 の既定の仮想ネットワークがサブネットと共に作成されます。アドレス空間は 10.0.0.0/22 です。
    - ソース仮想マシンがサブネットにある: 仮想マシンがオンになっている仮想ネットワークが検出されます。仮想ネットワークの仕様とサブネットが複製されます。
2.  ターゲット リソース グループに `<VM virtual network>arm` という名前の仮想ネットワークがある ('arm' という文字列が付きます)
    - 仮想ネットワークに名前とアドレス空間が同じサブネットがある場合、それを使用します。
    - 適切なサブネットが見つからない場合、ビット マスクが 22 の既存のサブネットからアドレス ブロックを探し、それを使用します。

## 複製とプラットフォーム移行の違い

現行の複製手法とプラットフォームでサポートされている移行にはいくつかの違いがあります。

### 複製


| 長所 | 短所 |
|--------------------------------------------------------|:----------------------------------------------------------------:|
| クラウド サービス内で仮想マシンを複製する | VM をシャットダウンする必要があり、ダウンタイムが発生する |
| VNET 内または VNET 外で仮想マシンを複製する | ディスク BLOB の複製が必要なシナリオで長い遅延が発生する |
| | VM のネットワーク構成を変更する - 内部 IP、VIP |


### プラットフォーム移行


| 長所 | 短所 |
|----------------------------------|:-------------------------------------------:|
| 仮想ネットワーク内の VM 構成の大半はダウンタイムを招きません。 | クラウド サービスのすべての VM とすべてのリソースがデプロイされている VNET のいずれかをまとめて移行する必用があります。 |
 
 
## サポートされていないシナリオ

**次は複製スクリプトの範囲外です**

 1. 実行中の仮想マシンの停止
 2. データ/ディスクの変更
 3. 実行中の VM の複製
 4. 複雑なシナリオで自動的に複数の VM を複製する
 5. 全体的 ASM ネットワーク構成の複製
 6. 負荷分散された VM の作成Azure の専門家が明示的に処理する必要がある構成を想定しています
 
 
## 試験済みの構成

_Add-AzureSMVmToRM_ コマンドレットは次のテスト ケースを使用して検証されました。

| # | Description |
|:---|:---|
| 1 | Windows 仮想マシンと既存の OS ディスク |
| 2 | Linux 仮想マシンと既存の OS ディスク |
| 3 | Windows 仮想マシン、既存の OS ディスクとデータ ディスク |
| 4 | Linux 仮想マシン、既存の OS ディスクとデータ ディスク |
| 5 | Windows 仮想マシン、イメージ ギャラリーから検索した新しい OS ディスク |
| 6 | Linux 仮想マシン、イメージ ギャラリーから検索した新しい OS ディスク |
| 7 | Windows 仮想マシン、新しい OS ディスク、空のデータ ディスク |
| 8 | Linux 仮想マシン、新しい OS ディスク、空のデータ ディスク |
| 9 | Windows 仮想マシンとパブリック エンドポイント |
| 10 | Linux 仮想マシンとパブリック エンドポイント |
| 11 | Windows 仮想マシンと WinRM 証明書 |
| 12 | Vnet とサブネットの Windows 仮想マシン |
| 13 | Vnet とサブネットの Linux 仮想マシン |
| 14 | Windows 仮想マシンとカスタム拡張機能 |
| 15 | Windows 仮想マシン、可用性セット |
| 16 | Windows 仮想マシン、可用性セット、複数のデータ ディスク、パブリック エンドポイント、Vnet とサブネット、カスタム拡張機能 |
| 17 | Linux 仮想マシン、可用性セット、複数のデータ ディスク、パブリック エンドポイント、Vnet とサブネット、カスタム拡張機能 |

## メモ
1. 複数の VM を短い間隔で複製する場合、DNS キャッシュ更新時間に起因し、パブリック IP アドレスに DNS 名前競合が発生する可能性があります。

<!---HONumber=AcomDC_0817_2016-->