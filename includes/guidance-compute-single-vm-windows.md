この記事では、スケーラビリティ、可用性、管理容易性、およびセキュリティに注目しながら、Azure で Windows 仮想マシン (VM) を実行するための実績のある一連の手法について説明します。

> [AZURE.NOTE] Azure には、[Resource Manager][resource-manager-overview] とクラシックという 2 種類のデプロイ モデルがあります。この記事では、新しいデプロイ用に Microsoft から推奨されている Resource Manager を使います。

Azure 上の単一の VM にはアップタイムの SLA がないため、実稼働ワークロードで単一の VM を使用することは推奨しません。SLA を取得するには、複数の VM を可用性セットにデプロイする必要があります。詳細については、「[Running multiple Windows VMs on Azure (Azure で複数の Windows VM を実行する)][multi-vm]」を参照してください。

## アーキテクチャ ダイアグラム

Azure で VM をプロビジョニングする際は、VM 自体のみよりも、多くの変動的な部分があります。コンピューティング、ネットワーク、およびストレージの要素があります。

![IaaS: single VM](./media/guidance-blueprints/compute-single-vm.png)

- **リソース グループ。** [_リソース グループ_][resource-manager-overview]は、関連リソースを保持するコンテナーです。この VM のリソースを保持するリソース グループを作成します。

- **VM。**VM は、発行されたイメージのリスト、または Azure Blob Storage にアップロードした VHD ファイルからプロビジョニングできます。

- **OS ディスク。** OS ディスクは、[Azure Storage][azure-storage] に格納されている VHD です。これは、ホスト コンピューターがダウンした場合でも VM が保持されることを意味します。

- **一時ディスク。** VM は一時ディスク (Windows の `D:` ドライブ) を使用して作成されます。このディスクは、ホスト コンピューターの物理ドライブ上に格納されます。このディスクは、Azure Storage には保存_されない_ため、再起動中やその他の VM ライフサイクル イベント時に失われる可能性があります。ページ ファイルやスワップ ファイルなどの一時的なデータにのみ、このディスクを使用してください。

- **データ ディスク。** [データ ディスク][data-disk]は、アプリケーション データに使用される永続的な VHD です。データ ディスクは、OS ディスクと同様に、Azure Storage に格納されます。

- **仮想ネットワーク (VNet) とサブネット。** Azure 上のすべての VM は、仮想ネットワーク (VNet) にデプロイされます。仮想ネットワークは、さらに複数のサブネットに分かれています。

- **パブリック IP アドレス。** パブリック IP アドレスは、リモート デスクトップ (RDP) 経由など、VM と通信するために必要です。

- **ネットワーク インターフェイス (NIC)。**NIC を使用すると、VM は仮想ネットワークと通信できます。

- **ネットワーク セキュリティ グループ (NSG)。**[NSG][nsg] は、サブネットへのネットワーク トラフィックを許可または拒否するために使用します。NSG は、個々 の NIC またはサブネットに関連付けることができます。NSG をサブネットに関連付けると、そのサブネット内のすべての VM に NSG ルールが適用されます。
 
- **[診断]。** 診断ログは、VM の管理とトラブルシューティングにとって非常に重要です。

## 推奨事項

### VM の推奨事項

- ハイ パフォーマンス コンピューティングなどの特殊なワークロードがない限り、DS と GS のシリーズをお勧めします。詳細については、「[仮想マシンのサイズ][virtual-machine-sizes]」をご覧ください。既存のワークロードを Azure に移動する場合は、オンプレミスのサーバーに最も適合性が高い VM サイズから開始します。次に、CPU、メモリ、およびディスク IOPS について、実際のワークロードのパフォーマンスを測定し、必要に応じてサイズを調整します。また、複数の NIC が必要な場合は、各サイズの NIC の制限に注意してください。  

- VM および他のリソースをプロビジョニングする際は、場所を指定する必要があります。一般的に、内部ユーザーや顧客に最も近い場所を選択します。ただし、すべての場所ですべての VM サイズを利用できるとは限りません。詳細については、「[リージョン別のサービス][services-by-region]」をご覧ください。指定した場所で利用できる VM サイズを一覧表示するには、次の Azure CLI コマンドを実行します。

    ```
    azure vm sizes --location <location>
    ```

- 発行された VM イメージの選択については、[Azure 仮想マシン イメージへの移動と選択][select-vm-image]に関するページを参照してください。

### ディスクとストレージの推奨事項

- 最適なディスク I/O パフォーマンスを得るには、[Premium Storage][premium-storage] をお勧めします。ここでは、データがソリッド ステート ドライブ (SSD) に格納されます。コストは、プロビジョニングされたディスクのサイズに基づいて決まります。また、IOPS とスループット (つまり、データ転送速度) もディスク サイズによって異なるため、ディスクをプロビジョニングする場合は、3 つの要素 (容量、IOPS、スループット) すべてを考慮してください。 

- 1 つ以上のデータ ディスクを追加します。新しく作成した VHD は、フォーマットされていません。その VM にログインしてディスクをフォーマットしてください。

- データ ディスクの数が多い場合は、ストレージ アカウントの合計 I/O 制限に注意してください。詳細については、「[仮想マシン ディスクの制限][vm-disk-limits]」を参照してください。

- 最適なパフォーマンスを得るには、診断ログを保持するためのストレージ アカウントを別途作成します。診断ログには、標準的なローカル冗長ストレージ (LRS) アカウントがあれば十分です。

- 可能であれば、OS ディスクではなく、データ ディスクにアプリケーションをインストールします。ただし、一部のレガシ アプリケーションでは、C: ドライブにコンポーネントをインストールする必要があります。この場合は、PowerShell を使用して、[OS ディスクのサイズを変更][resize-os-disk]します。

### ネットワークの推奨事項

- パブリック IP アドレスは、動的でも静的でもかまいません。既定では、動的アドレスになっています。

    - 変化しない固定 IP アドレスが必要な場合 (たとえば、DNS に A レコードを作成する必要がある場合や IP アドレスをホワイトリストに登録する必要がある場合) は、[静的 IP アドレス][static-ip]を予約します。

    - IP アドレスの完全修飾ドメイン名 (FQDN) を作成することもできます。これにより、その FQDN を参照する DNS で [CNAME レコード][cname-record]を登録できます。詳細については、「[Azure ポータルでの完全修飾ドメイン名の作成][fqdn]」を参照してください。

- すべての NSG は、すべての着信インターネット トラフィックをブロックするルールなど、[既定のルール][nsg-default-rules]のセットを含みます。既定のルールを削除することはできませんが、他の規則でオーバーライドすることはできます。インターネット トラフィックを有効にするには、特定のポート (HTTP のポート 80 など) への着信トラフィックを許可するルールを作成します。

- RDP を有効にするには、TCP ポート 3389 への着信トラフィックを許可する NSG ルールを追加します。

## 拡張性に関する考慮事項

- [VM サイズを変更する][vm-resize]ことで VM をスケールアップまたはスケールダウンできます。 

- 水平方向にスケール アウトするには、ロード バランサーの内側の可用性セットに 2 つ以上の VM を配置します。詳細については、「[Running multiple Windows VMs on Azure (Azure で複数の Windows VM を実行する)][multi-vm]」をご覧ください。

## 可用性に関する考慮事項

- 前述のように、単一の VM には SLA がありません。SLA を取得するには、複数の VM を可用性セットにデプロイする必要があります。

- VM は、[計画的メンテナンス][planned-maintenance]または[計画外メンテナンス][manage-vm-availability]の影響を受ける可能性があります。[VM の再起動ログ][reboot-logs]を使用すると、VM の再起動が計画的なメンテナンスによるものかどうかを確認できます。

- VHD は、レプリケートによって持続性と可用性が確保される [Azure Storage][azure-storage] によってサポートされます。

- 通常の操作中に (ユーザー エラーなどによる) 偶発的なデータの損失から保護するために、[BLOB スナップショット][blob-snapshot]または他のツールを使用してポイントインタイム バックアップを実装する必要もあります。

## 管理容易性に関する考慮事項

- **リソース グループ。** 同じライフ サイクルを共有する密結合のリソースを同じ[リソース グループ][resource-manager-overview]に配置します。リソース グループを使用すると、グループとしてリソースをデプロイおよび監視し、リソース グループ別に請求コストをまとめることができます。セットとしてリソースを削除することもできます。これはテスト デプロイの場合に便利です。リソースにはわかりやすい名前を付けます。これにより、特定のリソースを見つけて、その役割を理解することが簡単になります。「[Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)][naming conventions]」をご覧ください。

- **VM 診断。** 基本的な正常性メトリック、診断インフラストラクチャ ログ、および[ブート診断][boot-diagnostics]などの監視と診断を有効にします。VM が起動不可能な状態になった場合は、起動エラーを診断するのにブート診断が役立ちます。詳細については、「[監視と診断の有効化][enable-monitoring]」を参照してください。[Azure ログ収集][log-collector]拡張機能を使用して Azure プラットフォームのログを収集し、Azure Storage にアップロードします。

    次の CLI コマンドを実行して、診断を有効にします。

    ```text
    azure vm enable-diag <resource-group> <vm-name>
     ```

- **VM の停止。** Azure では、"停止" 状態と "割り当て解除済み" 状態が区別されます。VM が "停止" 状態のときは課金されます。VM が "割り当て解除済み" 状態のときは課金されません

    次の CLI コマンドを使用して、VM の割り当てを解除します。

    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```

    Azure ポータルの **[停止]** ボタンを使用した場合も VM の割り当てが解除されます。ただし、ログイン中に OS からシャットダウンした場合、VM は停止しますが、割り当て解除_されない_ため、引き続き課金されます。

- **VM の削除。** VM を削除しても VHD は削除されません。つまり、データを失うことなく安全に VM を削除できます。ただし、Storage に対して引き続き課金されます。VHD を削除するには、[Blob Storage][blob-storage] からファイルを削除してください。

  誤って削除されないように、[リソース ロック][resource-lock]を使用してリソース グループ全体をロックするか、VM などの個々 のリソースをロックします。



## セキュリティに関する考慮事項

- [Azure Security Center][security-center] を使用すると、Azure リソースのセキュリティの状態を一元的に表示して把握できます。セキュリティ センターは、潜在的なセキュリティ上の問題 (システムの更新プログラム、マルウェア対策など) を監視し、デプロイのセキュリティの正常性を包括的に示します。 

    - セキュリティ センターは、Azure サブスクリプションごとに構成されます。[セキュリティ センターの使用]に関するページの説明に従って、セキュリティ データの収集を有効にします。
    - データ収集を有効にすると、セキュリティ センターは、そのサブスクリプションに作成されているすべての VM を自動的にスキャンします。

- **更新プログラムの管理。** 有効な場合、セキュリティ センターは、セキュリティと重要な更新プログラムが不足しているかどうかを確認します。VM で[グループ ポリシー設定][group-policy]を使用して、システムの自動更新を有効にします。

- **マルウェア対策。** 有効な場合、セキュリティ センターは、マルウェア対策ソフトウェアがインストールされているかどうかを確認します。セキュリティ センターを使用して、Azure ポータル内からマルウェア対策ソフトウェアをインストールすることもできます。

- [ロールベースのアクセス制御][rbac] (RBAC) を使用して、デプロイする Azure リソースへのアクセスを制御します。RBAC を使用すると、DevOps チームのメンバーに承認の役割を割り当てることができます。たとえば、閲覧者の役割では、Azure リソースを表示することはできますが、作成、削除、または管理することはできません。一部の役割は、特定の Azure リソースの種類に固有です。たとえば、仮想マシンの共同作業者の役割では、VM の再起動または割り当て解除、管理者パスワードのリセット、新しい VM の作成などができます。この参照アーキテクチャで役立つ可能性があるその他の[組み込みの RBAC の役割][rbac-roles]は、[DevTest ラボ ユーザー][rbac-devtest]および[ネットワークの共同作業者][rbac-network]に含まれています。ユーザーを複数の役割に割り当てることができ、よりきめ細かいアクセス許可のカスタム ロールを作成することができます。

    > [AZURE.NOTE] RBAC では、VM にログインしているユーザーが実行できる操作は制限されません。これらのアクセス許可は、ゲスト OS のアカウントの種類によって決まります。

- ローカル管理者のパスワードをリセットするには、`vm reset-access` Azure CLI コマンドを実行します。

    ```text
    azure vm reset-access -u <user> -p <new-password> <resource-group> <vm-name>
    ```

- プロビジョニング操作やその他の VM イベントを確認するには、[監査ログ][audit-logs]を使用します。

- OS ディスクとデータ ディスクを暗号化する必要がある場合は、[Azure Disk Encryption][disk-encryption] を検討します。

## デプロイ スクリプトの例

次の Windows バッチ スクリプトは、[Azure CLI][azure-cli] コマンドを実行し、前の図に示されているように、単一の VM インスタンスとそれに関連するネットワーク リソースやストレージ リソースをデプロイします。

このスクリプトでは、「[Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)][naming conventions]」で説明されている名前付け規則を使用します。

```bat
ECHO OFF
SETLOCAL

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Set up variables for deploying resources to Azure.
:: Change these variables for your own deployment.

:: The APP_NAME variable must not exceed 4 characters in size.
:: If it does the 15 character size limitation of the VM name may be exceeded.

SET APP_NAME=app1
SET LOCATION=eastus2
SET ENVIRONMENT=dev
SET USERNAME=testuser


:: For Windows, use the following command to get the list of URNs:
:: azure vm image list %LOCATION% MicrosoftWindowsServer WindowsServer 2012-R2-Datacenter
SET WINDOWS_BASE_IMAGE=MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20160126

:: For a list of VM sizes see:
::   https://azure.microsoft.com/documentation/articles/virtual-machines-size-specs/
:: To see the VM sizes available in a region:
:: 	azure vm sizes --location <location>
SET VM_SIZE=Standard_DS1

:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

IF "%2"=="" (
    ECHO Usage: %0 subscription-id admin-password
    EXIT /B
    )

:: Explicitly set the subscription to avoid confusion as to which subscription
:: is active/default
SET SUBSCRIPTION=%1
SET PASSWORD=%2

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

:: Set up the postfix variables attached to most CLI commands
SET POSTFIX=--resource-group %RESOURCE_GROUP% --subscription %SUBSCRIPTION%

CALL azure config mode arm

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
:: Create resources

:: Create the enclosing resource group
CALL azure group create --name %RESOURCE_GROUP% --location %LOCATION% ^
  --subscription %SUBSCRIPTION%

:: Create the VNet
CALL azure network vnet create --address-prefixes 172.17.0.0/16 ^
  --name %VNET_NAME% --location %LOCATION% %POSTFIX%

:: Create the network security group
CALL azure network nsg create --name %NSG_NAME% --location %LOCATION% %POSTFIX%

:: Create the subnet
CALL azure network vnet subnet create --vnet-name %VNET_NAME% --address-prefix ^
  172.17.0.0/24 --name %SUBNET_NAME% --network-security-group-name %NSG_NAME% ^
  %POSTFIX%

:: Create the public IP address (dynamic)
CALL azure network public-ip create --name %IP_NAME% --location %LOCATION% %POSTFIX%

:: Create the NIC
CALL azure network nic create --public-ip-name %IP_NAME% --subnet-name ^
  %SUBNET_NAME% --subnet-vnet-name %VNET_NAME%  --name %NIC_NAME% --location ^
  %LOCATION% %POSTFIX%

:: Create the storage account for the OS VHD
CALL azure storage account create --type PLRS --location %LOCATION% %POSTFIX% ^
  %VHD_STORAGE%

:: Create the storage account for diagnostics logs
CALL azure storage account create --type LRS --location %LOCATION% %POSTFIX% ^
  %DIAGNOSTICS_STORAGE%

:: Create the VM
CALL azure vm create --name %VM_NAME% --os-type Windows --image-urn ^
  %WINDOWS_BASE_IMAGE% --vm-size %VM_SIZE%   --vnet-subnet-name %SUBNET_NAME% ^
  --vnet-name %VNET_NAME% --nic-name %NIC_NAME% --storage-account-name ^
  %VHD_STORAGE% --os-disk-vhd "%VM_NAME%-osdisk.vhd" --admin-username ^
  "%USERNAME%" --admin-password "%PASSWORD%" --boot-diagnostics-storage-uri ^
  "https://%DIAGNOSTICS_STORAGE%.blob.core.windows.net/" --location %LOCATION% ^
  %POSTFIX%

:: Attach a data disk
CALL azure vm disk attach-new --vm-name %VM_NAME% --size-in-gb 128 --vhd-name ^
  data1.vhd --storage-account-name %VHD_STORAGE% %POSTFIX%

:: Allow RDP
CALL azure network nsg rule create --nsg-name %NSG_NAME% --direction Inbound ^
  --protocol Tcp --destination-port-range 3389 --source-port-range * ^
  --priority 100 --access Allow RDPAllow %POSTFIX%
```

## 次のステップ

[仮想マシンの SLA][vm-sla] を適用するには、可用性セットに 2 つ以上のインスタンスをデプロイする必要があります。詳細については、「[Running multiple Windows VMs on Azure (Azure で複数の Windows VM を実行する)][multi-vm]」を参照してください。

<!-- links -->

[arm-templates]: ../articles/virtual-machines/virtual-machines-windows-cli-deploy-templates.md
[audit-logs]: https://azure.microsoft.com/ja-JP/blog/analyze-azure-audit-logs-in-powerbi-more/
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/ja-JP/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-windows-about-disks-vhds.md
[disk-encryption]: ../articles/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-windows-portal-create-fqdn.md
[group-policy]: https://technet.microsoft.com/ja-JP/library/dn595129.aspx
[log-collector]: https://azure.microsoft.com/ja-JP/blog/simplifying-virtual-machine-troubleshooting-using-azure-log-collector/
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-windows-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-windows-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/ja-JP/blog/viewing-vm-reboot-logs/
[resize-os-disk]: ../articles/virtual-machines/virtual-machines-windows-expand-os-disk.md
[Resize-VHD]: https://technet.microsoft.com/ja-JP/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/ja-JP/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[security-center]: https://azure.microsoft.com/ja-JP/services/security-center/
[select-vm-image]: ../articles/virtual-machines/virtual-machines-windows-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/ja-JP/regions/#services
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[セキュリティ センターの使用]: ../articles/security-center/security-center-get-started.md#use-security-center
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-windows-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/ja-JP/support/legal/sla/virtual-machines/v1_0/

<!---HONumber=AcomDC_0622_2016-->