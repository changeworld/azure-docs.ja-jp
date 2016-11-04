この記事では、スケーラビリティ、可用性、管理容易性、およびセキュリティに注目しながら、Azure で Linux 仮想マシン (VM) を実行するための実績のある一連の手法について説明します。Azure では、CentOS、Debian、Red Hat Enterprise、Ubuntu、FreeBSD など、現在普及しているさまざまな Linux ディストリビューションに対応します。詳細については、「[Azure と Linux][azure-linux]」を参照してください。

> [!NOTE]
> Azure には、[Resource Manager][resource-manager-overview] とクラシックという 2 種類のデプロイ モデルがあります。この記事では、新しいデプロイ用に Microsoft から推奨されている Resource Manager を使います。
> 
> 

Azure 上の単一の VM については、稼働時間のサービス レベル アグリーメント (SLA) がないため、実稼働ワークロードで単一の VM を使用することはお勧めしません。SLA を取得するには、複数の VM を[可用性セット][availability-set]にデプロイする必要があります。詳細については、[Azure での複数の VM の実行][multi-vm]に関するページをご覧ください。

## アーキテクチャ ダイアグラム
Azure で VM をプロビジョニングする際は、VM 自体のみよりも、多くの変動的な部分があります。コンピューティング、ネットワーク、およびストレージの要素があります。

![[0]][0]

* **リソース グループ。** [*リソース グループ*][resource-manager-overview] は、関連リソースを保持するコンテナーです。この VM のリソースを保持するリソース グループを作成します。
* **VM**。VM は、発行されたイメージのリスト、または Azure BLOB ストレージにアップロードした仮想ハード ディスク (VHD) ファイルからプロビジョニングできます。
* **OS ディスク。** OS ディスクは、[Azure Storage][azure-storage] に格納されている VHD です。これは、ホスト コンピューターがダウンした場合でも VM が保持されることを意味します。OS ディスクは `/dev/sda1` です。
* **一時ディスク。** VM は一時ディスクを使用して作成されます。このディスクは、ホスト コンピューターの物理ドライブ上に格納されます。このディスクは、Azure Storage には保存されないため、再起動中やその他の VM ライフサイクル イベント時に失われる可能性があります。ページ ファイルやスワップ ファイルなどの一時的なデータにのみ、このディスクを使用してください。一時ディスクは `/dev/sdb1` で、`/mnt/resource` または `/mnt` でマウントされます。
* **データ ディスク。** [データ ディスク][data-disk]は、アプリケーション データに使用される永続的な VHD です。データ ディスクは、OS ディスクと同様に、Azure Storage に格納されます。
* **仮想ネットワーク (VNet) とサブネット。** Azure 上のすべての VM は、(VNet) にデプロイされます。仮想ネットワークは、さらに複数のサブネットに分かれています。
* **パブリック IP アドレス。** パブリック IP アドレスは、SSH 経由など、VM と通信するために必要です。
* **ネットワーク インターフェイス (NIC)**。NIC を使用すると、VM は仮想ネットワークと通信できます。
* **ネットワーク セキュリティ グループ (NSG)**。[NSG][nsg] は、サブネットへのネットワーク トラフィックを許可または拒否するために使用します。NSG は、個々 の NIC またはサブネットに関連付けることができます。NSG をサブネットに関連付けると、そのサブネット内のすべての VM に NSG ルールが適用されます。
* **[診断]。** 診断ログは、VM の管理とトラブルシューティングにとって非常に重要です。

## 推奨事項
### VM の推奨事項
* ハイ パフォーマンス コンピューティングなどの特殊なワークロードがない限り、DS と GS のシリーズをお勧めします。詳細については、[仮想マシンのサイズ][virtual-machine-sizes]に関するページをご覧ください。既存のワークロードを Azure に移動する場合は、オンプレミスのサーバーに最も適合性が高い VM サイズから開始します。次に、CPU、メモリ、およびディスクの 1 秒あたりの入力/出力操作 (IOPS) について、実際のワークロードのパフォーマンスを測定し、必要に応じてサイズを調整します。また、複数の NIC が必要な場合は、各サイズの NIC の制限に注意してください。
* VM および他のリソースをプロビジョニングする際は、場所を指定する必要があります。一般的に、内部ユーザーや顧客に最も近い場所を選択します。ただし、すべての場所ですべての VM サイズを利用できるとは限りません。詳細については、「[リージョン別のサービス][services-by-region]」を参照してください。指定した場所で利用できる VM サイズを一覧表示するには、次の Azure コマンド ライン インターフェイス (CLI) コマンドを実行します。
  
    ```
    azure vm sizes --location <location>
    ```
* 発行された VM イメージの選択については、[Azure 仮想マシン イメージへの移動と選択][select-vm-image]に関するページをご覧ください。

### ディスクとストレージの推奨事項
* 最適なディスク I/O パフォーマンスを得るには、[Premium Storage][premium-storage] をお勧めします。Premium Storage では、データがソリッド ステート ドライブ (SSD) に格納されます。コストは、プロビジョニングされたディスクのサイズに基づいて決まります。また、IOPS とスループット (つまり、データ転送速度) もディスク サイズによって異なるため、ディスクをプロビジョニングする場合は、3 つの要素 (容量、IOPS、スループット) すべてを考慮してください。
* 1 つのストレージ アカウントで、1 ～ 20 の VM をサポートできます。
* 1 つ以上のデータ ディスクを追加します。新しく作成した VHD は、フォーマットされていません。その VM にログインしてディスクをフォーマットしてください。データ ディスクは、`/dev/sdc`、`/dev/sdd` などのように表示されます。`lsblk` を実行すると、ディスクなどのブロック デバイスの一覧を表示できます。データ ディスクを使用するには、新しいパーティションとファイル システムを作成し、ディスクをマウントします。次に例を示します。
  
    ```bat
    # Create a partition.
    sudo fdisk /dev/sdc     # Enter 'n' to partition, 'w' to write the change.     
  
    # Create a file system.
    sudo mkfs -t ext3 /dev/sdc1
  
    # Mount the drive.
    sudo mkdir /data1
    sudo mount /dev/sdc1 /data1
    ```
* データ ディスクの数が多い場合は、ストレージ アカウントの合計 I/O 制限に注意してください。詳細については、「[仮想マシン ディスクの制限][vm-disk-limits]」を参照してください。
* データ ディスクを追加すると、ディスクに論理ユニット番号 (LUN) の ID が割り当てられます。LUN ID は必要に応じて指定できます。たとえば、ディスクを交換する際に同じ LUN ID を保持したい場合や、特定の LUN ID を検索するアプリがある場合などに指定します。ただし、ディスクごとに一意な LUN ID である必要があります。
* I/O スケジューラを変更して、(Premium Storage で使用される) ソリッド ステート ドライブ (SSD) のパフォーマンスを最適化できます。一般的な推奨事項は、SSD に対して NOOP スケジューラを使用することですが、[iostat] などのツールを使用して、特定のワークロードのディスク I/O パフォーマンスを監視する必要があります。
* 最適なパフォーマンスを得るには、診断ログを保持するためのストレージ アカウントを別途作成します。診断ログには、標準的なローカル冗長ストレージ (LRS) アカウントがあれば十分です。

### ネットワークの推奨事項
* パブリック IP アドレスは、動的でも静的でもかまいません。既定では、動的アドレスになっています。
  
  * 変化しない固定 IP アドレスが必要な場合 (たとえば、DNS に A レコードを作成する必要がある場合や IP アドレスをホワイトリストに登録する必要がある場合) は、[静的 IP アドレス][static-ip]を予約します。
  * IP アドレスの完全修飾ドメイン名 (FQDN) を作成することもできます。これにより、その FQDN を参照する DNS で [CNAME レコード][cname-record]を登録できます。詳細については、「[Azure ポータルでの完全修飾ドメイン名の作成][fqdn]」を参照してください。
* すべての NSG に[既定の規則][nsg-default-rules] \(すべての受信インターネット トラフィックをブロックする規則など) のセットが含まれています。既定のルールを削除することはできませんが、他の規則でオーバーライドすることはできます。インターネット トラフィックを有効にするには、特定のポート (HTTP のポート 80 など) への着信トラフィックを許可するルールを作成します。
* SSH を有効にするには、TCP ポート 22 への着信トラフィックを許可する規則を NSG に追加します。

## 拡張性に関する考慮事項
* [VM サイズを変更する][vm-resize]ことで VM をスケールアップまたはスケールダウンできます。
* 水平方向にスケール アウトするには、ロード バランサーの内側の可用性セットに 2 つ以上の VM を配置します。詳細については、[Azure での複数の VM の実行][multi-vm]に関するページをご覧ください。

## 可用性に関する考慮事項
* 前述のように、単一の VM には SLA がありません。SLA を取得するには、複数の VM を可用性セットにデプロイする必要があります。
* VM は、[計画的メンテナンス][planned-maintenance]または[計画外メンテナンス][manage-vm-availability]の影響を受ける可能性があります。[VM の再起動ログ][reboot-logs]を使用すると、VM の再起動が計画的なメンテナンスによるものかどうかを確認できます。
* VHD は、レプリケートによって持続性と可用性が確保される [Azure Storage][azure-storage] によってサポートされます。
* 通常の操作中に (ユーザー エラーなどによる) 偶発的なデータの損失から保護するために、[BLOB スナップショット][blob-snapshot]や他のツールを使用してポイントインタイム バックアップを実装することも必要です。

## 管理容易性に関する考慮事項
* **リソース グループ。** 同じライフ サイクルを共有する密結合のリソースを同じ[リソース グループ][resource-manager-overview]に配置します。リソース グループを使用すると、グループとしてリソースをデプロイおよび監視し、リソース グループ別に請求コストをまとめることができます。セットとしてリソースを削除することもできます。これはテスト デプロイの場合に便利です。リソースにはわかりやすい名前を付けます。これにより、特定のリソースを見つけて、その役割を理解することが簡単になります。「[Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)][naming conventions]」を参照してください。
* **SSH**。Linux VM を作成する前に、2048 ビット RSA 公開/秘密キー ペアを生成します。VM を作成する場合は、公開キー ファイルを使用します。詳細については、「[Azure 上の Linux または Mac における SSH の使用方法][ssh-linux]」を参照してください。
* **VM 診断。** 基本的な正常性メトリック、診断インフラストラクチャ ログ、[ブート診断][boot-diagnostics]などの監視と診断を有効にします。VM が起動不可能な状態になった場合は、起動エラーを診断するのにブート診断が役立ちます。詳細については、「[監視と診断の有効化][enable-monitoring]」を参照してください。
  
    次の CLI コマンドを実行して、診断を有効にします。
  
    ```text
    azure vm enable-diag <resource-group> <vm-name>
    ```
* **VM の停止。** Azure では、"停止" 状態と "割り当て解除済み" 状態が区別されます。VM が "停止" 状態のときは課金されます。VM が "割り当て解除済み" 状態のときは課金されません。
  
    次の CLI コマンドを使用して、VM の割り当てを解除します。
  
    ```text
    azure vm deallocate <resource-group> <vm-name>
    ```
  
    Azure ポータルの **[停止]** ボタンを使用した場合も VM の割り当てが解除されます。ただし、ログイン中に OS からシャットダウンした場合、VM は停止しますが、割り当て解除されないため、引き続き課金されます。
* **VM の削除。** VM を削除しても VHD は削除されません。つまり、データを失うことなく安全に VM を削除できます。ただし、Storage に対して引き続き課金されます。VHD を削除するには、[BLOB ストレージ][blob-storage]からファイルを削除します。
  
  誤って削除されないように、[リソース ロック][resource-lock]を使用してリソース グループ全体をロックするか、VM などの個々のリソースをロックします。

## セキュリティに関する考慮事項
* [OSPatching] VM 拡張機能を使用して、OS の更新を自動化します。VM をプロビジョニングする場合は、この拡張機能をインストールしてください。パッチをインストールする頻度と、パッチの適用後に再起動するかどうかを指定することができます。
* [ロールベースのアクセス制御][rbac] \(RBAC) を使用して、デプロイする Azure リソースへのアクセスを制御します。RBAC を使用すると、DevOps チームのメンバーに承認の役割を割り当てることができます。たとえば、閲覧者の役割では、Azure リソースを表示することはできますが、作成、削除、または管理することはできません。一部の役割は、特定の Azure リソースの種類に固有です。たとえば、仮想マシン作成協力者ロールでは、VM の再起動または割り当て解除、管理者パスワードのリセット、新しい VM の作成などができます。このリファレンス アーキテクチャで役立つ他の[組み込みの RBAC の役割][rbac-roles]には、[DevTest Labs ユーザー][rbac-devtest]や[ネットワーク共同作成者][rbac-network]などがあります。ユーザーを複数の役割に割り当てることができ、よりきめ細かいアクセス許可のカスタム ロールを作成することができます。
  
  > [!NOTE]
  > RBAC では、VM にログインしているユーザーが実行できる操作は制限されません。これらのアクセス許可は、ゲスト OS のアカウントの種類によって決まります。
  > 
  > 
* プロビジョニング操作や他の VM イベントを確認するには、[監査ログ][audit-logs]を使用します。
* OS ディスクとデータ ディスクを暗号化する必要がある場合は、[Azure Disk Encryption][disk-encryption] を検討します。

## ソリューションのコンポーネント
ソリューションのサンプル スクリプト ([Deploy-ReferenceArchitecture.ps1][solution-script]) が用意されています。このスクリプトを使用すると、この記事で説明されている推奨事項に従ったアーキテクチャを実装できます。このスクリプトは、[Azure Resource Manager][arm-templates] のテンプレートを使用します。テンプレートは、特定のアクション (VNet の作成や NSG の構成など) を実行する基本的な構成要素のセットとして利用できます。スクリプトの目的は、テンプレート デプロイを調整することです。

別の JSON ファイル内で保持されているパラメーターを使用して、テンプレートがパラメーター化されます。これらのファイルにあるパラメーターを変更して、独自の要件を満たすようにデプロイを構成できます。テンプレート自体を変更する必要はありません。パラメーター ファイル内のオブジェクトのスキーマを変更してはならないことに注意してください。

テンプレートを編集する場合は、「[Recommended Naming Conventions for Azure Resources (Azure リソースの推奨される名前付け規則)][naming conventions]」で説明されている名前付け規則に従って、オブジェクトを作成します。

スクリプトでは、以下のパラメーター ファイルを参照して VM および関連するインフラストラクチャを構築します。

* **[virtualNetwork.parameters.json][vnet-parameters]**。このファイルは、名前、アドレス空間、サブネット、および必要となるすべての DNS サーバーのアドレスなど、VNet 設定を定義します。サブネット アドレスは VNet のアドレス空間に含まれている必要があることに注意してください。
  
    <!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/virtualNetwork.parameters.json#L4-L21 -->
  
    ```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg",
        "addressPrefixes": [
          "172.17.0.0/16"
        ],
        "subnets": [
          {
            "name": "ra-single-vm-sn",
            "addressPrefix": "172.17.0.0/24"
          }
        ],
        "dnsServers": [ ]
      }
    }
  }
    ```
* **[networkSecurityGroup.parameters.json][nsg-parameters]**。このファイルには、NSG と NSG 規則の定義が含まれています。`virtualNetworkSettings` ブロック内の `name` パラメーターは、NSG が接続される VNet を指定します。`networkSecurityGroupSettings` ブロック内の `subnets` パラメーターは、VNet で NSG 規則を適用するすべてのサブネットを指定します。これらは、**virtualNetwork.parameters.json** ファイルで定義された項目である必要があります。
  
    この例に示すようにセキュリティ ルールには、SSH 接続を介してユーザーが VM に接続できます。`securityRules` 配列に項目をさらに追加することで、追加のポートを開くことができます (または特定のポート経由のアクセスを拒否できます)。
    <!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/networkSecurityGroups.parameters.json#L4-L36 -->
  
    ```json
  "parameters": {
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "networkSecurityGroupsSettings": {
      "value": [
        {
          "name": "ra-single-vm-nsg",
          "subnets": [
            "ra-single-vm-sn"
          ],
          "networkInterfaces": [
          ],
          "securityRules": [
            {
              "name": "default-allow-ssh",
              "direction": "Inbound",
              "priority": 1000,
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationPortRange": "22",
              "access": "Allow",
              "protocol": "Tcp"
            }
          ]
        }
      ]
    }
  }
    ```
* **[virtualMachineParameters.json][vm-parameters]**。このファイルでは、VM の名前とサイズ、管理者ユーザーのセキュリティ資格情報、作成するディスク、作成したディスクを保持するストレージ アカウントなど、VM 自体の設定を定義します。
  
    `linux` に `osType` パラメーターを設定することを確認します。`imageReference` セクション内にあるイメージの指定も行わなければなりません。以下の値は、RedHat Linux 7.2 の最新のビルドで、VM を作成します。次の Azure CLI コマンドを使用すると、リージョン (westus 領域を使用する例) 内のすべての利用可能な RedHat イメージの一覧を取得できます。
  
    ```powershell
    azure vm image list westus redhat rhel
    ```
  
    `nics` セクション内の `subnetName` パラメーターは、VM のサブネットを指定します。同様に、`virtualNetworkSettings` 内の `name` パラメーターは使用する VNet を識別します。これらの値は、**virtualNetwork.parameters.json** ファイルで定義されたサブネットおよび VNet の名前である必要があります。
  
    1 つのストレージ アカウントを共有する、または独自のストレージ アカウントを持つ複数の VM を作成するには、`buildingBlockSettings` セクションで設定を変更します。複数の VM を作成する場合は、`availabilitySet` セクションで、使用または作成する可用性セットの名前を指定する必要もあります。
  
    <!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/virtualMachine.parameters.json#L4-L63 -->
  
    ```json
  "parameters": {
    "virtualMachinesSettings": {
      "value": {
        "namePrefix": "ra-single-vm",
        "computerNamePrefix": "cn",
        "size": "Standard_DS1_v2",
        "osType": "linux",
        "adminUsername": "testuser",
        "adminPassword": "AweS0me@PW",
        "osAuthenticationType": "password",
        "nics": [
          {
            "isPublic": "true",
            "subnetName": "ra-single-vm-sn",
            "privateIPAllocationMethod": "dynamic",
            "publicIPAllocationMethod": "dynamic",
            "enableIPForwarding": false,
            "dnsServers": [
            ],
            "isPrimary": "true"
          }
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.5-LTS",
          "version": "latest"
        },
        "dataDisks": {
          "count": 2,
          "properties": {
            "diskSizeGB": 128,
            "caching": "None",
            "createOption": "Empty"
          }
        },
        "osDisk": {
          "caching": "ReadWrite"
        },
        "extensions": [ ],
        "availabilitySet": {
          "useExistingAvailabilitySet": "No",
          "name": ""
        }
      }
    },
    "virtualNetworkSettings": {
      "value": {
        "name": "ra-single-vm-vnet",
        "resourceGroup": "ra-single-vm-rg"
      }
    },
    "buildingBlockSettings": {
      "value": {
        "storageAccountsCount": 1,
        "vmCount": 1,
        "vmStartIndex": 0
      }
    }
  }
    ```

## ソリューションのデプロイ
ソリューションは、次の条件を前提とします。

* リソース グループを作成できる既存の Azure サブスクリプションがある。
* Azure Powershell の最新のビルドをダウンロードしてインストールしている。手順については、[こちら][azure-powershell-download]を参照してください。

ソリューションを展開するスクリプトを実行するには、次の手順を実行します。

1. `Scripts` および `Templates` という名前のサブフォルダーを含むフォルダーを作成します。
2. テンプレート フォルダーでは、Linux という名前の別のサブフォルダーを作成します。
3. [Deploy-ReferenceArchitecture.ps1][solution-script] ファイルを Scripts フォルダーにダウンロードします。
4. テンプレート / Linux のフォルダーに、次のファイルをダウンロードします。
   
   * [virtualNetwork.parameters.json][vnet-parameters]
   * [networkSecurityGroup.parameters.json][nsg-parameters]
   * [virtualMachineParameters.json][vm-parameters]
5. Scripts フォルダーの Deploy-ReferenceArchitecture.ps1 ファイルを編集して次の行を変更し、スクリプトによって作成される VM とリソースを保持するために作成または使用するリソース グループを指定します。
   
    <!-- source: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/Deploy-ReferenceArchitecture.ps1#L37 -->
   
    ```powershell
    $resourceGroupName = "ra-single-vm-rg"
    ```
6. ソリューションのコンポーネントのセクションで説明したように、仮想ネットワーク、NSG、VM のパラメーターを設定する Templates/Linux フォルダー内の JSON ファイルをそれぞれ編集します。
   
   > [!NOTE]
   > virtualMachineParameters.json ファイルの `virtualNetworkSettings` セクションの `resourceGroup` パラメーターは、Deploy-ReferenceArchitecture.ps1 スクリプト ファイルで指定したのと同じものに設定してください。
   > 
   > 
7. Azure PowerShell ウィンドウを開き、Scripts フォルダーに移動して、次のコマンドを実行します。
   
    ```powershell
    .\Deploy-ReferenceArchitecture.ps1 <subscription id> <location> Linux
    ```
   
    `<subscription id>` は、Azure サブスクリプション ID に置き換えてください。
   
    `<location>` には、`eastus`、`westus` などの Azure リージョンを指定します。
8. スクリプトの実行が完了したら、Azure ポータルを使用してネットワーク、NSG、VM が正常に作成されたことを確認ます。

## 次のステップ
[Virtual Machines の SLA][vm-sla] を適用するには、可用性セットに 2 つ以上のインスタンスをデプロイする必要があります。詳細については、[Azure での複数の VM の実行][multi-vm]に関するページをご覧ください。

<!-- links -->

[audit-logs]: https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/
[availability-set]: ../articles/virtual-machines/virtual-machines-windows-create-availability-set.md
[azure-cli]: ../articles/virtual-machines-command-line-tools.md
[azure-linux]: ../articles/virtual-machines/virtual-machines-linux-azure-overview.md
[azure-storage]: ../articles/storage/storage-introduction.md
[blob-snapshot]: ../articles/storage/storage-blob-snapshots.md
[blob-storage]: ../articles/storage/storage-introduction.md
[boot-diagnostics]: https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/
[cname-record]: https://en.wikipedia.org/wiki/CNAME_record
[data-disk]: ../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md
[disk-encryption]: ../articles/security/azure-security-disk-encryption.md
[enable-monitoring]: ../articles/azure-portal/insights-how-to-use-diagnostics.md
[fqdn]: ../articles/virtual-machines/virtual-machines-linux-portal-create-fqdn.md
[iostat]: https://en.wikipedia.org/wiki/Iostat
[manage-vm-availability]: ../articles/virtual-machines/virtual-machines-linux-manage-availability.md
[multi-vm]: ../articles/guidance/guidance-compute-multi-vm.md
[naming conventions]: ../articles/guidance/guidance-naming-conventions.md
[nsg]: ../articles/virtual-network/virtual-networks-nsg.md
[nsg-default-rules]: ../articles/virtual-network/virtual-networks-nsg.md#default-rules
[OSPatching]: https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching
[planned-maintenance]: ../articles/virtual-machines/virtual-machines-linux-planned-maintenance.md
[premium-storage]: ../articles/storage/storage-premium-storage.md
[rbac]: ../articles/active-directory/role-based-access-control-what-is.md
[rbac-roles]: ../articles/active-directory/role-based-access-built-in-roles.md
[rbac-devtest]: ../articles/active-directory/role-based-access-built-in-roles.md#devtest-lab-user
[rbac-network]: ../articles/active-directory/role-based-access-built-in-roles.md#network-contributor
[reboot-logs]: https://azure.microsoft.com/blog/viewing-vm-reboot-logs/
[Resize-VHD]: https://technet.microsoft.com/ja-JP/library/hh848535.aspx
[Resize virtual machines]: https://azure.microsoft.com/blog/resize-virtual-machines/
[resource-lock]: ../articles/resource-group-lock-resources.md
[resource-manager-overview]: ../articles/resource-group-overview.md
[select-vm-image]: ../articles/virtual-machines/virtual-machines-linux-cli-ps-findimage.md
[services-by-region]: https://azure.microsoft.com/regions/#services
[ssh-linux]: ../articles/virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md
[static-ip]: ../articles/virtual-network/virtual-networks-reserved-public-ip.md
[storage-price]: https://azure.microsoft.com/pricing/details/storage/
[virtual-machine-sizes]: ../articles/virtual-machines/virtual-machines-linux-sizes.md
[vm-disk-limits]: ../articles/azure-subscription-service-limits.md#virtual-machine-disk-limits
[vm-resize]: ../articles/virtual-machines/virtual-machines-linux-change-vm-size.md
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_0/
[arm-templates]: https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/
[solution-script]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/deploy-reference-architecture.sh
[vnet-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/linux/virtualNetwork.parameters.json
[nsg-parameters]: https://github.com/mspnp/reference-architectures/blob/master/guidance-compute-single-vm/parameters/linux/networkSecurityGroups.parameters.json
[vm-parameters]: https://github.com/mspnp/reference-architectures/tree/master/guidance-compute-single-vm/parameters/linux/virtualMachine.parameters.json
[azure-powershell-download]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/
[0]: ./media/guidance-blueprints/compute-single-vm.png "Azure における単一の Linux VM アーキテクチャ"

<!---HONumber=AcomDC_0831_2016-->