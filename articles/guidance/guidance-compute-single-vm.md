<properties
   pageTitle="仮想マシンの実行 (Windows) | 青写真 | Microsoft Azure"
   description="拡張性、回復性、管理容易性、およびセキュリティに注目しながら、Azure で単一の VM を実行する方法。"
   services=""
   documentationCenter="na"
   authors="mikewasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="mikewasson"/>

# Azure で単一の Windows VM を実行する

この記事では、拡張性、回復性、管理容易性、およびセキュリティに注目しながら、Azure で単一の Windows VM を実行するための実績のある一連の手法について概説します。

> [AZURE.WARNING] Azure 上の単一の VM にはアップタイムの SLA がありません。この構成は、運用環境のデプロイではなく、開発用やテスト用に使用してください。

Azure には、[Resource Manager][resource-manager-overview] とクラシックという 2 種類のデプロイ モデルがあります。この記事では、新しいデプロイ用に Microsoft から推奨されている Resource Manager を使います。Resource Manager は、[Azure ポータル][azure-portal]、[Azure PowerShell][azure-powershell]、[Azure CLI][azure-cli] コマンド、[Resource Manager テンプレート][arm-templates]など、複数の方法で使うことができます。この記事では、Azure CLI を使った例を紹介します。

![IaaS: single VM](media/guidance-compute-single-vm.png)

Azure で単一の VM をプロビジョニングする際は、コア VM 自体より多くの変動的な部分が関与してきます。コンピューティング、ネットワーク、およびストレージの要素があります。

- **リソース グループ**。 この VM のリソースを保持する[リソース グループ][resource-manager-overview]を作成します。_リソース グループ_は、関連リソースを保持するコンテナーです。

- **VM**。VM は、発行されたイメージのリスト、または Azure Blob Storage にアップロードした VHD ファイルからプロビジョニングできます。

- **OS ディスク**。 OS ディスクは、[Azure Storage][azure-storage] に格納されている VHD です。これは、ホスト コンピューターがダウンした場合でも VM が保持されることを意味します。

- **一時ディスク**。 VM は一時ディスク (Windows の `D:`ドライブ) を使用して作成されます。このディスクは、ホスト コンピューターの物理ドライブ上に格納されます。このディスクは、Azure Storage には保存_されない_ため、再起動中やその他の VM ライフサイクル イベント時に失われる可能性があります。ページ ファイルやスワップ ファイルなどの一時的なデータにのみ、このディスクを使用してください。

- **データ ディスク**。 [データ ディスク][data-disk]は、アプリケーション データに使用される永続的な VHD です。データ ディスクは、OS ディスクと同様に、Azure Storage に格納されます。

- **仮想ネットワーク (VNet) とサブネット**。 Azure 上のすべての VM は、仮想ネットワーク (VNet) にデプロイされます。仮想ネットワークは、さらに複数のサブネットに分かれています。

- **パブリック IP アドレス**。 パブリック IP アドレスは、リモート デスクトップ (RDP) 経由など、VM と通信するために必要です。

- **ネットワーク セキュリティ グループ (NSG)**。 [NSG][nsg] は、VM へのネットワーク トラフィックを許可または拒否するために使用します。既定の NSG 規則では、すべての着信インターネット トラフィックが拒否されます。

- **ネットワーク インターフェイス カード (NIC)**。NIC を使用すると、VM は仮想ネットワークと通信できます。

- **[診断]。** 診断ログは、VM の管理とトラブルシューティングにとって非常に重要です。

## VM の推奨事項

- 既存のワークロードを Azure に移動する場合は、オンプレミスのサーバーに最も適合性が高い [VM サイズ][virtual-machine-sizes]を選択します。I/O 集中型ワークロードに Premium Storage を使用できる DS シリーズと GS シリーズをお勧めします。

    - パフォーマンスが高く、待機時間の短いディスク アクセスを必要としないワークロードでは、A シリーズや D シリーズなど、その他の Standard レベルの VM サイズを検討してください。

- VM および他のリソースをプロビジョニングする際は、場所を指定する必要があります。一般的に、内部ユーザーや顧客に最も近い場所を選択します。ただし、すべての場所ですべての VM SKU を利用できるとは限りません。詳細については、「[リージョン別のサービス][services-by-region]」を参照してください。

- 発行された VM イメージの選択については、「[Windows PowerShell と Azure CLI による Azure 仮想マシン イメージのナビゲーションと選択][select-vm-image]」を参照してください。

## ディスクとストレージの推奨事項

最適なディスク I/O パフォーマンスを実現するには、[Premium Storage][premium-storage] をお勧めします。ただし、Premium Storage では、DS または GS シリーズの VM が必要です。

- Premium Storage の場合、コストは、プロビジョニングされたディスクのサイズに基づいて決まります。また、IOPS とスループット (つまり、データ転送速度) もディスク サイズによって異なるため、ディスクをプロビジョニングする場合は、3 つの要素 (容量、IOPS、スループット) すべてを考慮してください。

- Standard Storage の場合、コストは、ディスクに書き込まれたデータの量に基づいて決まります。そのため、最大サイズ (1,023 GB) をプロビジョニングすることをお勧めします。ただし、ディスクをフォーマットするときに必ずクイック フォーマットを使用してください。完全なディスク フォーマットではディスクにゼロが書き込まれるため、結果として、実際のストレージが使用されます。「[Azure Storage 料金][storage-price]」を参照してください。

- Standard Storage を選択する場合は、geo 冗長ストレージ (GRS) をお勧めします。これは、地域的な停電が発生した場合やプライマリ リージョンが回復不可能な障害が発生した場合でもデータが保持されるためです。

- 1 つ以上のデータ ディスクを追加します。新しく作成した VHD は、フォーマットされていません。その VM にログインしてディスクをフォーマットしてください。

- データ ディスクの数が多い場合は、ストレージ アカウントの合計 I/O 制限に注意してください。詳細については、「[仮想マシン ディスクの制限][vm-disk-limits]」を参照してください。

- 最適なパフォーマンスを得るには、診断ログを保持するためのストレージ アカウントを別途作成します。診断ログには、標準的なローカル冗長ストレージ (LRS) アカウントがあれば十分です。

## ネットワークの推奨事項

- 単一の VM の場合、1 つのサブネットを含む 1 つの VNet を作成します。さらに、NSG とパブリック IP アドレスも作成します。

- パブリック IP アドレスは、動的でも静的でもかまいません。既定では、動的アドレスになっています。

    - 変化しない固定 IP アドレスが必要な場合 (たとえば、DNS に A レコードを作成する必要がある場合や IP アドレスをホワイトリストに登録する必要がある場合) は、[静的 IP アドレス][static-ip]を予約します。

    - 既定では、IP アドレスに完全修飾ドメイン名 (FQDN) は含まれません。詳細については、「[Azure ポータルでの完全修飾ドメイン名の作成][fqdn]」を参照してください。

- NIC を割り当てて、IP アドレス、サブネット、および NSG に関連付けます。

- 既定の NSG 規則では、RDP が許可されていません。RDP を有効にするには、TCP ポート 3389 への着信トラフィックを許可する規則を NSG に追加します。

## 拡張性

VM の規模は、VM サイズを変更することでスケールアップまたはスケールダウンできます。VM のサイズを変更するには、次の Azure CLI コマンドを使用します。

```text
azure vm set -g <<resource-group>> --vm-size <<new-vm-size>
    --boot-diagnostics-storage-uri <<storage-account-uri>> <<vm-name>>
```

VM のサイズを変更すると、システムの再起動がトリガーされます。再起動後、既存の OS とデータ ディスクが再マップされます。一時ディスク上に格納されているデータはすべて失われます。`--boot-diagnostics-storage-uri` オプションを使用すると、[ブート診断][boot-diagnostics]で、起動に関連するすべてのエラーをログに記録できます。

SKU ファミリ間での規模の変更を行うことができない場合があります (たとえば、A シリーズから G シリーズ)。既存の VM に使用できるサイズの一覧を取得するには、次の CLI コマンドを使用します。

```text
azure vm sizes -g <<resource-group>> --vm-name <<vm-name>>
```

この一覧に含まれていないサイズに変更するには、VM インスタンスを削除してから新しい VM を作成する必要があります。VM を削除しても VHD は削除されません。

## 可用性

- 単一の VM には可用性の SLA がありません。可用性に関する SLA を実現するには、複数の VM を 1 つの可用性セットにデプロイする必要があります。

- VM は、[計画的メンテナンス][planned-maintenance]または[計画外メンテナンス][manage-vm-availability]の影響を受ける可能性があります。[VM の再起動ログ][reboot-logs]を使用すると、VM の再起動が計画的なメンテナンスによるものかどうかを確認できます。

- 単一の VM を可用性セットに含めないでください。VM を可用性セットに含めることは、複数インスタンス セットの一部として VM を扱うように Azure に指示することであるため、計画済みメンテナンスに伴う再起動に関して事前の警告または通知が送付されなくなります。

- VHD は、レプリケートによって持続性と可用性が確保される [Azure Storage][azure-storage] によってサポートされます。

- 通常の操作中に (ユーザー エラーなどによる) 偶発的なデータの損失から保護するために、[BLOB スナップショット][blob-snapshot]または他のツールを使用してポイントインタイム バックアップを実装する必要もあります。

## 管理容易性

- 次の CLI コマンドを実行して、VM 診断を有効にします。
    
    ```text
    azure vm enable-diag <<resource-group>> <<vm-name>>
    ```
    
    このコマンドでは、基本的な正常性メトリック、診断インフラストラクチャ ログ、およびブート診断を有効にします。詳細については、「[監視と診断の有効化][enable-monitoring]」を参照してください。

- [Azure ログ収集][log-collector]拡張機能を使用してログを収集し、Azure Storage にアップロードします。

- Azure では、"停止" 状態と "割り当て解除済み" 状態が区別されます。VM が "停止" 状態のときは課金されます。VM が "割り当て解除済み" 状態のときは課金されません ([Azure VM に関する FAQ][vm-faq] を参照してください)。

    次の CLI コマンドを使用して、VM の割り当てを解除します。
    
    ```text
    azure vm deallocate <<resource-group>> <<vm-name>>
    ```
    
    注: Azure ポータルの **[停止]** ボタンを使用した場合も VM の割り当てが解除されます。ただし、(RDP 経由で) Windows 内からシャットダウンした場合、VM は停止しますが、割り当て解除_されない_ため、引き続き課金されます。

- VM を削除しても VHD は削除されません。つまり、データを失うことなく安全に VM を削除できます。ただし、Storage に対して引き続き課金されます。VHD を削除するには、[Blob Storage][blog-storage] からファイルを削除してください。

- OS ディスクのサイズを変更するには、.vhd ファイルをダウンロードし、[Resize-VHD][Resize-VHD] などのツールを使用して VHD のサイズを変更します。サイズを変更した VHD を Blob Storage にアップロードした後、VM インスタンスを削除し、サイズを変更した VHD を使用する新しいインスタンスをプロビジョニングします。


## セキュリティ

- [Azure Security Center][security-center] を使用すると、Azure リソースのセキュリティの状態を一元的に表示して把握できます。Security Center は、潜在的なセキュリティ上の問題 (システムの更新プログラム、マルウェア対策、エンドポイント ACL など) を監視し、デプロイのセキュリティの正常性を包括的に示します。**注:** この記事の執筆時点では、Security Center はまだプレビュー段階です。

- [ロールベースのアクセス制御][rbac] (RBAC) を使用して、デプロイする Azure リソース (VM、ネットワークなど) を管理できる DevOps チーム メンバーを定義します。

- [セキュリティ拡張機能][security-extensions]をインストールすることを検討します。

- [Azure Disk Encryption][disk-encryption] を使用して、OS ディスクとデータ ディスクを暗号化します。**注:** この記事の執筆時点では、Azure Disk Encryption はまだプレビュー段階です。

## トラブルシューティング

- ローカル管理者のパスワードをリセットするには、`vm reset-access` Azure CLI コマンドを実行します。
    
    ```text
    azure vm reset-access -u <<user>> -p <<new-password>> <<resource-group>> <<vm-name>>
    ```
    
- VM が起動不可能な状態になった場合は、[ブート診断][boot-diagnostics]を使用して起動エラーを診断します。

- プロビジョニング操作やその他の VM イベントを確認するには、[監査ログ][audit-logs]を参照します。

## Azure CLI コマンド (例)

次の Windows バッチ スクリプトは、[Azure CLI][azure-cli] コマンドを実行し、前の図に示されているように、単一の VM インスタンスとそれに関連するネットワーク リソースやストレージ リソースをデプロイします。

```bat
ECHO OFF
SETLOCAL

IF "%~1"=="" (
    ECHO Usage: %0 subscription-id
    EXIT /B
    )

:: Set up variables to build out the naming conventions for deploying
:: the cluster

SET LOCATION=eastus2
SET APP_NAME=app1
SET ENVIRONMENT=dev
SET USERNAME=testuser
SET PASSWORD=AweS0me@PW

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1

:: Set up the names of things using recommended conventions
SET RESOURCE_GROUP=%APP_NAME%-%ENVIRONMENT%-rg
SET VM_NAME=%APP_NAME%-vm0

SET IP_NAME=%APP_NAME%-pip
SET NIC_NAME=%VM_NAME%-0nic
SET NSG_NAME=%APP_NAME%-nsg
SET SUBNET_NAME=%APP_NAME%-subnet
SET VNET_NAME=%APP_NAME%-vnet
SET VHD_STORAGE=%VM_NAME:-=%st0
SET DIAGNOSTICS_STORAGE=%VM_NAME:-=%diag

:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see...
SET VM_SIZE=Standard_DS1

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --resource-group %RESOURCE_GROUP% ^
  --subscription %SUBSCRIPTION%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% %POSTFIX%

:: Create the NIC
CALL azure network nic create --network-security-group-name %NSG_NAME% ^
  --public-ip-name %IP_NAME% --subnet-name %SUBNET_NAME% --subnet-vnet-name ^
  %VNET_NAME%  --name %NIC_NAME% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS %POSTFIX% %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS %POSTFIX% %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --nic-name %NIC_NAME% --vnet-name %VNET_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new -g %RESOURCE_GROUP% --vm-name %VM_NAME% ^
  --size-in-gb 128 --vhd-name data1.vhd --storage-account-name %VHD_STORAGE%

:: Allow RDP
CALL azure network nsg rule create -g %RESOURCE_GROUP% --nsg-name %NSG_NAME% ^
  --direction Inbound --protocol Tcp --destination-port-range 3389 ^
  --source-port-range * --priority 100 --access Allow RDPAllow
```

<!-- links -->

[arm-templates]: ../virtual-machines/virtual-machines-deploy-rmtemplates-azure-cli.md
[audit-logs]: https://azure.microsoft.com/ja-JP/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../virtual-machines/virtual-machines-command-line-tools.md
[azure-portal]: ../azure-portal/resource-group-portal.md
[azure-powershell]: ../powershell-azure-resource-manager.md
[azure-storage]: ../storage/storage-introduction.md
[blob-snapshot]: ../storage/storage-blob-snapshots.md
[blog-storage]: ../storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/ja-JP/blog/boot-diagnostics-for-virtual-machines-v2/
[data-disk]: ../virtual-machines/virtual-machines-disks-vhds.md
[disk-encryption]: ../azure-security-disk-encryption.md
[enable-monitoring]: ../azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../virtual-machines/virtual-machines-create-fqdn-on-portal.md
[log-collector]: https://azure.microsoft.com/ja-JP/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../virtual-machines/virtual-machines-manage-availability.md
[nsg]: ../virtual-network/virtual-networks-nsg.md
[password-reset]: ../virtual-machines/virtual-machines-windows-reset-password.md
[planned-maintenance]: ../virtual-machines/virtual-machines-planned-maintenance.md
[premium-storage]: ../storage/storage-premium-storage-preview-portal.md
[rbac]: ../active-directory/role-based-access-control-configure.md
[reboot-logs]: https://azure.microsoft.com/ja-JP/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/ja-JP/library/hh848535.aspx
[resource-manager-overview]: ../resource-group-overview.md
[security-center]: https://azure.microsoft.com/ja-JP/services/security-center/
[security-extensions]: ../virtual-machines/virtual-machines-extensions-features.md#security-and-protection
[select-vm-image]: ../virtual-machines/resource-groups-vm-searching.md
[services-by-region]: https://azure.microsoft.com/ja-JP/regions/#services
[static-ip]: ../virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../virtual-machines/virtual-machines-size-specs.md
[vm-disk-limits]: ../azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-faq]: ../virtual-machines/virtual-machines-questions.md

<!---HONumber=AcomDC_0302_2016-->