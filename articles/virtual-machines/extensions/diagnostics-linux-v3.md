---
title: Azure Compute - Linux Diagnostic Extension 3.0
description: Azure Linux Diagnostic Extension (LAD) 3.0 を構成して、Azure で実行中の Linux VM からメトリックとログ イベントを収集する方法。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04264276b3419e32aa420792027502ce3d1acbb2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669684"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Linux Diagnostic Extension 3.0 を使用して、メトリックとログを監視する

このドキュメントでは、Linux Diagnostic Extension (LAD) のバージョン 3.0 以降について説明します。

> [!IMPORTANT]
> バージョン 2.3 以前の詳細については、[Linux VM のパフォーマンスと診断データの監視](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)に関する記事を参照してください。

## <a name="introduction"></a>はじめに

Linux Diagnostic Extension は、Microsoft Azure で実行される Linux VM の正常性を監視するために役立ちます。 次の機能が用意されています。

* VM からシステム パフォーマンス メトリックを収集し、指定されたストレージ アカウント内の特定のテーブルに格納します。
* Syslog からログ イベントを取得し、指定されたストレージ アカウント内の特定のテーブルに格納します。
* ユーザーは収集およびアップロードされたデータ メトリックをカスタマイズできます。
* ユーザーは収集およびアップロードされたイベントの syslog ファシリティと重大度レベルをカスタマイズできます。
* ユーザーは指定したログ ファイルを指定したストレージ テーブルにアップロードできます。
* 指定されたストレージ アカウント内の任意の Azure Event Hubs エンドポイントおよび JSON 形式 の BLOB にメトリックとログ イベントを送信することがサポートされています。

この拡張機能は、両方の Azure デプロイメント モデルで動作します。

## <a name="install-the-extension-on-a-vm"></a>VM に拡張機能をインストールする

この拡張機能は、Azure PowerShell コマンドレット、Azure CLI スクリプト、Azure Resource Monitor テンプレート (ARM テンプレート)、Azure portal のいずれかを使用して有効にできます。 詳細については、[拡張機能](features-linux.md)に関するページを参照してください。

>[!NOTE]
>LAD VM 拡張機能の一部のコンポーネントは、[Log Analytics VM 拡張機能](./oms-linux.md)にも付属しています。 このアーキテクチャが原因で、両方の拡張機能が同じ ARM テンプレートでインスタンス化されると、競合が発生する可能性があります。 
>
>インストール時の競合を回避するには、[`dependsOn` ディレクティブ](../../azure-resource-manager/templates/resource-dependency.md#dependson)を使用して、拡張機能が順番にインストールされるようにします。 拡張機能は、どちらの順序でもインストールできます。

これらのインストール手順と[ダウンロード可能な構成例](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)により、LAD 3.0 は次のように構成されます。

* LAD 2.3 と同じメトリックをキャプチャして保存します。
* 役に立つ一連のファイル システム メトリックをキャプチャします。 この機能は LAD 3.0 での新機能です。
* LAD 2.3 が有効になっている既定の syslog コレクションをキャプチャします。
* Azure portal エクスペリエンスを使用して、VM メトリックのチャート作成とアラート通知を行います。

ダウンロード可能な構成は単なる例です。 ニーズに合うように変更してください。

### <a name="supported-linux-distributions"></a>サポートされている Linux ディストリビューション

LAD では、次のディストリビューションとバージョンがサポートされます。 ディストリビューションとバージョンの一覧は、Azure で動作保証済みの Linux ベンダーのイメージにのみ適用されます。 一般に、この拡張機能では、アプライアンスなどのサードパーティの BYOL および BYOS イメージはサポートされていません。

Debian 7 などのメジャー バージョンのみを示しているディストリビューションは、すべてのマイナー バージョンでもサポートされています。 マイナー バージョンが指定されている場合は、そのバージョンのみがサポートされます。 プラス記号 (+) が追加されている場合は、指定されているバージョンと同じかそれより後のマイナー バージョンがサポートされます。

サポートされているディストリビューションとバージョン:

- Ubuntu 20.04、18.04、16.04、14.04
- CentOS 7、6.5+
- Oracle Linux 7、6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9、8、7
- Red Hat Enterprise Linux (RHEL) 7、6.7+

### <a name="prerequisites"></a>前提条件

* **Azure Linux エージェント バージョン 2.2.0 以降**。 ほとんどの Azure VM Linux ギャラリー イメージには、バージョン 2.2.7 以降が含まれています。 `/usr/sbin/waagent -version` を実行して、VM にインストールされているバージョンを確認します。 VM で以前のバージョンが実行されている場合は、[ゲスト エージェントを更新します](./update-linux-agent.md)。
* **Azure CLI**。 必要に応じて、ご使用のマシンに [Azure CLI 環境をセットアップします](/cli/azure/install-azure-cli)。
* **Wget コマンド**。 まだない場合は、`sudo apt-get install wget` を実行します。
* 既存の **Azure サブスクリプション**。 
* データを格納するための既存の **汎用ストレージ アカウント**。 汎用ストレージ アカウントでは、テーブル ストレージがサポートされる必要があります。 BLOB ストレージ アカウントは機能しません。
* **Python 2**。

### <a name="python-requirement"></a>Python の要件

Linux Diagnostic Extension には、Python 2 が必要です。 お使いの仮想マシンで使用されているディストリビューションに Python 2 が既定で含まれない場合は、それをインストールする必要があります。 次のサンプル コマンドでは、さまざまなディストリビューションに Python 2 がインストールされます。   

- Red Hat、CentOS、Oracle: `yum install -y python2`
- Ubuntu、Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

`python2` 実行可能ファイルに *python* という別名を付ける必要があります。 このエイリアスを設定する 1 つの方法を次に示します。

1. 次のコマンドを実行して、既存の別名を削除します。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 次のコマンドを実行して、別名を作成します。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>サンプル インストール

以降のサンプルでダウンロードされる構成例では、一連の標準データが収集され、それらがテーブル ストレージに送信されます。 構成例の URL と内容は変更される可能性があります。 

ほとんどの場合、ポータル設定の JSON ファイルのコピーをダウンロードし、必要に応じてカスタマイズする必要があります。 その後は、URL から毎回ダウンロードするのではなく、テンプレートまたは独自の自動化を使用して、カスタマイズされたバージョンの構成ファイルを使用します。

> [!NOTE]
> 以降のサンプルでは、コードを実行する前に、最初のセクションの変数に正しい値を入力します。 
#### <a name="azure-cli-sample"></a>Azure CLI のサンプル

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>LAD 3.0 を仮想マシン スケール セット インスタンスにインストールするための Azure CLI のサンプル

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell のサンプル

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>拡張機能の設定を更新する

保護された設定または公開設定を変更した後、同じコマンドを実行して、VM にそれらをデプロイします。 設定が変更されると、更新が拡張機能に送信されます。 LAD は構成を再読み込みして、再起動します。

### <a name="migrate-from-previous-versions-of-the-extension"></a>以前のバージョンの拡張機能から移行する

拡張機能の最新バージョンは *4.0* です。 

> [!IMPORTANT]
> この拡張機能では、構成の面で大幅な変更点が導入されます。 このような変更によって拡張機能のセキュリティが強化されたため、2.x との下位互換性を維持できませんでした。 また、この拡張機能の拡張機能パブリッシャーは、2.x バージョンのパブリッシャーとは異なります。
>
> 2\.x から最新バージョンに移行するには、(以前のパブリッシャー名の下の) 以前の拡張機能をアンインストールします。 その後、バージョン 3 をインストールします。

推奨事項:

* 自動マイナー バージョン アップグレードを有効にして、拡張機能をインストールします。
  * Azure XPLAT CLI または PowerShell を使用して拡張機能をインストールする場合、クラシック デプロイ モデルの VM では、バージョン `3.*` を指定します。
  * Azure Resource Manager デプロイ モデルの VM では、VM 展開テンプレートに「`"autoUpgradeMinorVersion": true`」を含めます。
* LAD 3.0 では、新規または別のストレージ アカウントを使用します。 LAD 2.3 と LAD 3.0 には小さな非互換性がいくつかあるため、アカウントの共有には手間がかかります。
  * LAD 3.0 では、syslog イベントが別の名前のテーブルに格納されます。
  * LAD 3.0 では、`builtin` メトリックの `counterSpecifier` 文字列が異なります。

## <a name="protected-settings"></a>保護された設定

この一連の構成情報には、人目に付かないように保護する必要がある機密情報が含まれています。 ストレージ資格情報などが含まれます。 これらの設定は、暗号化された形式で拡張機能に転送され、格納されます。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

名前 | 値
---- | -----
storageAccountName | 拡張機能によってデータが書き込まれるストレージ アカウントの名前。
storageAccountEndPoint | (省略可能) ストレージ アカウントが存在するクラウドを示すエンドポイント。 この設定がない場合、LAD の既定値では Azure パブリック クラウド `https://core.windows.net` になります。 Azure Germany、Azure Government、Azure China 21Vianet でストレージ アカウントを使用するには、この値を必要に応じて設定します。
storageAccountSasToken | BLOB およびテーブル サービス (`ss='bt'`) の[アカウント SAS トークン](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)。 このトークンは、コンテナーとオブジェクト (`srt='co'`) に適用されます。 これにより、追加、作成、一覧表示、更新、書き込みのアクセス許可が付与されます (`sp='acluw'`)。 先頭の疑問符 (?) を *含めないで* ください。
mdsdHttpProxy | (省略可能) 指定されたストレージ アカウントとエンドポイントに拡張機能が接続するために必要な HTTP プロキシ情報。
sinksConfig | (省略可能) メトリックとイベントの配信が可能な代替宛先の詳細。 以降のセクションでは、拡張機能でサポートされる各データ シンクについて詳しく説明します。

ARM テンプレート内の SAS トークンを取得するには、`listAccountSas` 関数を使用します。 テンプレートの例については、[List 関数の例](../../azure-resource-manager/templates/template-functions-resource.md#list-example)を参照してください。

必要な SAS トークンを Azure portal で作成できます。

1. 拡張機能の書き込み先となる汎用ストレージ アカウントを選択します。
1. 左側のメニューの **[設定]** で、 **[Shared Access Signature]** を選択します。
1. 前に説明したように、選択を行います。
1. **[SAS の生成]** を選択します。

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="[SAS の生成] ボタンが示された [Shared access signature] ページが表示されているスクリーンショット。":::

生成された SAS を `storageAccountSasToken` フィールドにコピーします。 先頭の疑問符 (?) を削除します。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

省略可能な `sinksConfig` セクションでは、拡張機能で収集された情報を送信する宛先をさらに定義します。 `sink` 配列には、追加のデータ シンクごとのオブジェクトが含まれています。 `type` 属性によって、オブジェクトの他の属性が決まります。

要素 | 値
------- | -----
name | このシンクを拡張機能構成の他の場所で参照するための文字列。
type | 定義されているシンクの型。 この型のインスタンス内のその他の値 (存在する場合) を決定します。

LAD バージョン 3.0 では、`EventHub` と `JsonBlob` の 2 つのシンクの型がサポートされています。

#### <a name="eventhub-sink"></a>EventHub シンク

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

`"sasURL"` エントリには、データの発行先となるイベント ハブの完全な URL (SAS トークンを含む) が含まれています。 LAD には、送信要求を有効にするポリシーに名前を付ける SAS が必要です。 

次に例を示します。

* `contosohub` という名前の Azure Event Hubs 名前空間を作成します。
* `syslogmsgs` という名前のイベント ハブを名前空間に作成します。
* 送信要求を有効にする共有アクセス ポリシーをイベント ハブに作成します。 ポリシーに `writer` という名前を付けます。

2018 年 1 月 1 日の午前 0 時 (UTC) まで SAS が有効な場合、sasURL の値は次の例のようになります。

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs 用の SAS トークンでの情報の生成と取得の詳細については、「[SAS トークンを生成する](/rest/api/eventhub/generate-sas-token#powershell)」を参照してください。

#### <a name="jsonblob-sink"></a>JsonBlob シンク

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

`JsonBlob` シンクに転送されたデータは、Azure Storage 内の BLOB に格納されます。 LAD の各インスタンスは、各シンク名に対して 1 時間ごとに BLOB を作成します。 各 BLOB には、オブジェクトの構文的に有効な JSON 配列が必ず含まれています。 新しいエントリは、配列にアトミックに追加されます。 

BLOB はシンクと同じ名前のコンテナーに格納されます。 BLOB コンテナー名に対する Azure Storage のルールが、`JsonBlob` シンクの名前に適用されます。 名前は 3 から 63 文字で、小文字の英数の ASCII 文字またはダッシュを使用します。

## <a name="public-settings"></a>Public 設定

公開設定構造体には、拡張機能によって収集された情報を制御するさまざまな設定ブロックが含まれています。 各設定は省略可能です。 `ladCfg` を指定した場合、`StorageAccount` も指定する必要があります。

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

要素 | 値
------- | -----
StorageAccount | 拡張機能によってデータが書き込まれるストレージ アカウントの名前。 [保護された設定](#protected-settings)で指定されている名前にする必要があります。
mdsdHttpProxy | (省略可能) [保護された設定](#protected-settings)の場合と同じです。 パブリック値はプライベート値 (設定されている場合) によってオーバーライドされます。 [保護された設定](#protected-settings)に、パスワードなどの秘密を含むプロキシ設定を配置します。

以降のセクションでは、残りの要素に関する詳細を説明します。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

`ladCfg` 構造体は省略可能です。 これにより、Azure Monitor メトリック サービスおよび他のデータ シンクに配信されるメトリックとログの収集が制御されます。 ユーザーは次のものを指定する必要があります。

* `performanceCounters` または `syslogEvents` のいずれか、あるいは両方。 
* `metrics` 構造体。

要素 | 値
------- | -----
eventVolume | (省略可能) ストレージ テーブル内に作成されるパーティションの数を制御します。 `"Large"`、`"Medium"`、または `"Small"` である必要があります。 値を指定しない場合の既定値は `"Medium"` です。
sampleRateInSeconds | (省略可能) 生の (未集計) メトリックを収集する既定の間隔。 サポートされている最小サンプル レートは 15 秒です。 値を指定しない場合の既定値は `15` です。

#### <a name="metrics"></a>metrics

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

要素 | 値
------- | -----
resourceId | VM または VM が属するスケール セットの Azure Resource Manager リソース ID。 この設定は、構成で `JsonBlob` シンクが使用されている場合でも、指定する必要があります。
scheduledTransferPeriod | 集計メトリックが計算され、Azure Monitor メトリックに転送される頻度。 頻度は、IS 8601 の時間間隔として表されます。 最小の転送間隔は 60 秒、つまり、PT1M です。 少なくとも 1 つの `scheduledTransferPeriod` を指定します。

`performanceCounters` セクションで指定されたメトリックのサンプルは、15 秒ごと、またはカウンターに明示的に定義されたサンプル レートで収集されます。 この例のように複数の `scheduledTransferPeriod` 頻度が指定されている場合、各集計は独立して計算されます。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

省略可能な `performanceCounters` セクションでは、メトリックの収集が制御されます。 生のサンプルは、[`scheduledTransferPeriod`](#metrics) ごとに集計され、次の値が生成されます。

* 平均
* 最小値
* 最大値
* 最後に収集された値
* 集計を計算するために使用された生のサンプルの数

要素 | 値
------- | -----
sinks | (省略可能) LAD で集計したメトリックの結果を送信するシンクの名前をコンマで区切ったリスト。 集計されたすべてのメトリックは、一覧表示された各シンクに発行されます。 例: `"EHsink1, myjsonsink"`. 詳細については、「[`sinksConfig`](#sinksconfig)」を参照してください。
type | メトリックの実際のプロバイダーを識別します。
class | `"counter"` とともに、プロバイダーの名前空間内の特定のメトリックを識別します。
counter | `"class"` とともに、プロバイダーの名前空間内の特定のメトリックを識別します。
counterSpecifier | Azure Monitor メトリック名前空間内の特定のメトリックを識別します。
condition | (省略可能) メトリックが適用されるオブジェクトのインスタンスを選択します。 または、そのオブジェクトのすべてのインスタンスの集計を選択します。 
sampleRate | このメトリックの生のサンプルが収集されるレートを設定する IS 8601 の間隔。 値が設定されていない場合、収集間隔は [`sampleRateInSeconds`](#ladcfg) の値によって設定されます。 サポートされている最小サンプル レートは 15 秒 (PT15S) です。
unit | メトリックの単位を定義します。 次のいずれかの文字列にする必要があります: `"Count"`、`"Bytes"`、`"Seconds"`、`"Percent"`、`"CountPerSecond"`、`"BytesPerSecond"`、`"Millisecond"`。 収集されたデータのコンシューマーは、収集されたデータ値がこの単位に一致することを期待しています。 LAD はこのフィールドを無視します。
displayName | Azure Monitor メトリックのデータに関連付けられるラベル。 このラベルは、関連付けられているロケール設定によって指定された言語で設定します。 LAD はこのフィールドを無視します。

`counterSpecifier` は任意の識別子です。 Azure portal のグラフ作成およびアラート通知機能のようなメトリックのコンシューマーでは、メトリックまたはメトリックのインスタンスを識別する "キー" として `counterSpecifier` が使用されます。 

`builtin` メトリックの場合、`/builtin/` で始まる `counterSpecifier` 値をお勧めします。 メトリックの特定のインスタンスを収集する場合は、`counterSpecifier` 値にインスタンスの識別子を関連付けることをお勧めします。 

次に例をいくつか示します。

* `/builtin/Processor/PercentIdleTime` - すべての vCPU の平均アイドル時間
* `/builtin/Disk/FreeSpace(/mnt)` - `/mnt` ファイル システム用の空き領域
* `/builtin/Disk/FreeSpace` - マウントされたすべてのファイル システムの平均空き領域

LAD でも Azure portal でも、`counterSpecifier` の値がいずれかのパターンに一致することは期待されていません。 `counterSpecifier` 値の構成は一貫した方法で行ってください。

`performanceCounters` を指定すると、LAD では常に Azure Storage のテーブルにデータが書き込まれます。 同じデータを JSON BLOB と Event Hubs のどちらか一方または両方に書き込むことができます。 ただし、テーブルへのデータの格納を無効にすることはできません。 

同じストレージ アカウント名とエンドポイントが使用される LAD のすべてのインスタンスでは、メトリックとログが同じテーブルに追加されます。 同じテーブル パーティションに書き込みを行う VM が多すぎる場合、Azure によってそのパーティションへの書き込みが抑えられることがあります。 

`eventVolume` の設定によって、エントリは 1 (小)、10 (中)、100 (大) のパーティションに分散されます。 通常、トラフィックの調整を回避するには、中パーティションで十分です。 

Azure portal の Azure Monitor メトリック機能では、このテーブルのデータを使用して、グラフを生成したり、アラートをトリガーしたりします。 テーブル名は、次の文字列を連結したものです。

* `WADMetrics`
* テーブルに格納されている集計値の `"scheduledTransferPeriod"`
* `P10DV2S`
* 10 日ごとに変更される "YYYYMMDD" 形式の日付

例は、`WADMetricsPT1HP10DV2S20170410` や `WADMetricsPT1MP10DV2S20170609` のようになります。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

省略可能な `syslogEvents` セクションでは、syslog からのログ イベントの収集を制御します。 このセクションを省略すると、syslog イベントはまったくキャプチャされません。

`syslogEventConfiguration` コレクションには、対象の syslog ファシリティごとに 1 つのエントリがあります。 特定のファシリティで `minSeverity` が `"NONE"` の場合、またはそのファシリティが要素にまったく表示されない場合、そのファシリティからのイベントはキャプチャされません。

要素 | 値
------- | -----
sinks | 個々のログ イベントの発行先となるシンクの名前をコンマで区切ったリスト。 `syslogEventConfiguration` の制限に一致するすべてのログ イベントが、リストされている各シンクに発行されます。 例: `"EHforsyslog"`
facilityName | syslog ファシリティの名前 (`"LOG_USER"` や `"LOG\LOCAL0"` など)。 詳細については、[syslog の man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)で "ファシリティ" に関するセクションを参照してください。
minSeverity | syslog の重大度レベル (`"LOG_ERR"` や `"LOG_INFO"` など)。 詳細については、[syslog の man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)で "レベル" に関するセクションを参照してください。 拡張機能は、指定されたレベル以上のファシリティに送信されるイベントをキャプチャします。

`syslogEvents` を指定すると、LAD では常に Azure Storage のテーブルにデータが書き込まれます。 同じデータを JSON BLOB と Event Hubs のどちらか一方または両方に書き込むことができます。 ただし、テーブルへのデータの格納を無効にすることはできません。 

このテーブルのパーティション分割動作は、`performanceCounters` についての説明と同じです。 テーブル名は、次の文字列を連結したものです。

* `LinuxSyslog`
* 10 日ごとに変更される "YYYYMMDD" 形式の日付

例は、`LinuxSyslog20170410` や `LinuxSyslog20170609` のようになります。

### <a name="perfcfg"></a>perfCfg

`perfCfg` セクションは省略可能です。 任意の [Open Management Infrastructure (OMI)](https://github.com/Microsoft/omi) クエリの実行を制御します。

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

要素 | 値
------- | -----
namespace | (省略可能) クエリが実行される OMI 名前空間。 指定しない場合、既定値は `"root/scx"` です。 これは、[System Center クロスプラットフォーム プロバイダー](https://github.com/Microsoft/SCXcore)によって実装されます。
query | 実行する OMI クエリ。
table | (省略可能) 指定されたストレージ アカウントの Azure Storage テーブル。 詳細については、「[保護された設定](#protected-settings)」を参照してください。
frequency | (省略可能) クエリの実行間隔 (秒)。 既定値は 300 (5 分) です。 最小値は 15 秒です。
sinks | (省略可能) メトリック結果の生のサンプルが発行される追加のシンクの名前をコンマで区切ったリスト。 これらの生のサンプルの集計は、拡張機能または Azure Monitor メトリックスによって計算されません。

`"table"` と `"sinks"` のいずれか一方または両方を指定する必要があります。

### <a name="filelogs"></a>fileLogs

`fileLogs` セクションでは、ログ ファイルのキャプチャを制御します。 LAD では、このファイルに書き込まれる新しいテキスト行がキャプチャされます。 テーブル行や、指定されたシンク (`JsonBlob` や `EventHub` など) にそれらが書き込まれます。

> [!NOTE]
> `fileLogs` は `omsagent` という名前の LAD のサブコンポーネントによってキャプチャされます。 `fileLogs` を収集するには、`omsagent` ユーザーが指定したファイルに対する読み取りアクセス許可を持っていることを確認します。 このユーザーには、そのファイルへのパス内のすべてのディレクトリに対する実行アクセス許可も必要となります。 LAD がインストールされたら、`sudo su omsagent -c 'cat /path/to/file'` を実行してアクセス許可を確認できます。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

要素 | 値
------- | -----
file | 監視され、キャプチャされるログ ファイルの完全なパス名。 パス名では、1 つのファイルを指定する必要があります。 ディレクトリの名前を指定したり、ワイルドカード文字を含めたりすることはできません。 `omsagent` ユーザー アカウントには、ファイル パスの読み取りアクセス権が必要です。
table | (省略可能) file の "末尾" から新しい行が書き込まれる Azure Storage テーブル。 テーブルは、保護された構成で指定されている、指定されたストレージ アカウント内に存在する必要があります。 
sinks | (省略可能) ログ行が送信される追加のシンクの名前をコンマで区切ったリスト。

`"table"` と `"sinks"` のいずれか一方または両方を指定する必要があります。

## <a name="metrics-supported-by-the-builtin-provider"></a>組み込みプロバイダーでサポートされているメトリック

`builtin` メトリック プロバイダーは、幅広いユーザーにとって最も興味深いメトリックのソースです。 これらのメトリックは、大きく 5 つのクラスに分類されます。

* プロセッサ
* メモリ
* ネットワーク
* ファイル システム
* ディスク

### <a name="builtin-metrics-for-the-processor-class"></a>"プロセッサ" クラスの組み込みのメトリック

"プロセッサ" クラスのメトリックは、VM のプロセッサ使用率に関する情報を提供します。 パーセンテージが集計されると、結果はすべての CPU の平均になります。 

2 vCPU の VM では、1 つの vCPU が 100% ビジーで、もう一方が 100% アイドル状態の場合、報告される `PercentIdleTime` は 50 です。 各 vCPU が同じ期間に 50% ビジーだった場合、報告される結果はやはり 50 になります。 4 vCPU の VM では、1 つの vCPU が 100% ビジー状態で、その他がアイドル状態になると、報告される `PercentIdleTime` は 75 になります。

カウンタ | 意味
------- | -------
PercentIdleTime | 集計ウィンドウ中にプロセッサでカーネル アイドル ループが実行されていた時間の割合
PercentProcessorTime | 非アイドルのスレッドを実行している時間の割合
PercentIOWaitTime | IO 操作の完了を待機した時間の割合
PercentInterruptTime | ハードウェアまたはソフトウェア割り込みと DPC (遅延プロシージャ呼び出し) を実行する時間の割合
PercentUserTime | 集計ウィンドウ中の非アイドル時間のうち、通常の優先度のユーザー モードで消費された時間の割合
PercentNiceTime | 非アイドル時間のうち、低下した (適した) 優先度で消費された割合
PercentPrivilegedTime | 非アイドル時間のうち、特権 (カーネル) モードで消費された割合

最初の 4 つのカウンターの合計は 100% となる必要があります。 最後の 3 つのカウンターの合計も 100% となります。 これら 3 つのカウンターで、`PercentProcessorTime`、`PercentIOWaitTime`、`PercentInterruptTime` の合計が分割されます。

すべてのプロセッサで単一のメトリックを集計するには、`"condition": "IsAggregate=TRUE"` を設定します。 4 つの vCPU の VM の 2 番目の論理プロセッサなど、特定のプロセッサのメトリックを取得するには、`"condition": "Name=\\"1\\""` のように設定します。 論理プロセッサ番号は、 `[0..n-1]` の範囲内です。

### <a name="builtin-metrics-for-the-memory-class"></a>"メモリ" クラスの組み込みのメトリック

"メモリ" クラスのメトリックでは、メモリの使用率、ページング、スワップに関する情報が提供されます。

カウンタ | 意味
------- | -------
AvailableMemory | 使用可能な物理メモリ (MiB)
PercentAvailableMemory | 合計メモリに対する使用可能な物理メモリの割合
UsedMemory | 使用中の物理メモリ (MiB)
PercentUsedMemory | 合計メモリに対する使用中の物理メモリの割合
PagesPerSec | 合計ページング (読み取り/書き込み)
PagesReadPerSec | バッキング ストアから読み取られたページ数 (スワップ ファイル、プログラム ファイル、マップされたファイルなど)
PagesWrittenPerSec | バッキング ストアに書き込まれたページ数 (スワップ ファイル、マップされたファイルなど)
AvailableSwap | 未使用のスワップ領域 (MiB)
PercentAvailableSwap | 合計スワップに対する未使用のスワップ領域の割合
UsedSwap | 使用中のスワップ領域 (MiB)
PercentUsedSwap | 合計スワップに対する使用中のスワップ領域の割合

このメトリックのクラスには、1 つのインスタンスしかありません。 `"condition"` 属性は有用な設定を持たず、省略する必要があります。

### <a name="builtin-metrics-for-the-network-class"></a>"ネットワーク" クラスの組み込みのメトリック

"ネットワーク" クラスのメトリックでは、起動後の個々のネットワーク インターフェイスでのネットワーク アクティビティに関する情報が提供されます。 

LAD では帯域幅メトリックは公開されません。 これらのメトリックは、ホスト メトリックから取得できます。

カウンタ | 意味
------- | -------
BytesTransmitted | 起動後に送信された合計バイト数
BytesReceived | 起動後に受信した合計バイト数
BytesTotal | 起動後に送受信された合計バイト数
PacketsTransmitted | 起動後に送信された合計パケット数
PacketsReceived | 起動後に受信した合計パケット数
TotalRxErrors | 起動後の受信エラー数
TotalTxErrors | 起動後の送信エラー数
TotalCollisions | 起動後にネットワーク ポートによって報告された競合の数

"ネットワーク" クラスはインスタンス化されますが、LAD では、すべてのネットワーク デバイスから集計されたネットワーク メトリックのキャプチャをサポートしていません。 eth0 などの特定のインターフェイスのメトリックを取得するには、`"condition": "InstanceID=\\"eth0\\""` のように設定します。

### <a name="builtin-metrics-for-the-file-system-class"></a>"ファイル システム" クラスの組み込みメトリック

"ファイル システム" クラスのメトリックでは、ファイル システムの使用状況に関する情報が提供されます。 絶対値と割合の値は、(root ではなく) 通常のユーザーに表示されるように報告されます。

カウンタ | 意味
------- | -------
FreeSpace | 使用可能なディスク領域 (バイト単位)
UsedSpace | 使用済みディスク領域 (バイト単位)
PercentFreeSpace | 空き領域の割合
PercentUsedSpace | 使用領域の割合
PercentFreeInodes | 未使用のインデックス ノード (inode) の割合
PercentUsedInodes | すべてのファイル システム全体で合計された割り当て済み (使用中) inode の割合
BytesReadPerSecond | 1 秒あたりの読み取りバイト数
BytesWrittenPerSecond | 1 秒あたりの書き込みバイト数
BytesPerSecond | 1 秒あたりの読み取りまたは書き込みバイト数
ReadsPerSecond | 1 秒あたりの読み取り操作
WritesPerSecond | 1 秒あたりの書き込み操作
TransfersPerSecond | 1 秒あたりの読み取りまたは書き込み操作

すべてのファイル システムの集計値を取得するには、`"condition": "IsAggregate=True"` を設定します。 `"/mnt"` などの特定のマウントされたファイル システムの値を取得するには、`"condition": 'Name="/mnt"'` を設定します。 

> [!NOTE]
> JSON ではなく Azure portal を使用している場合、条件フィールドの形式は `Name='/mnt'` になります。

### <a name="builtin-metrics-for-the-disk-class"></a>"ディスク" クラスの組み込みメトリック

"ディスク" クラスのメトリックは、ディスク デバイスの使用状況に関する情報を提供します。 これらの統計情報は、ドライブ全体に適用されます。 

デバイスに複数のファイル システムが存在する場合、そのデバイスのカウンターは、実際にはすべてのファイル システムについて集計されます。

カウンタ | 意味
------- | -------
ReadsPerSecond | 1 秒あたりの読み取り操作
WritesPerSecond | 1 秒あたりの書き込み操作
TransfersPerSecond | 1 秒あたりの合計操作数
AverageReadTime | 読み取り操作あたりの平均秒数
AverageWriteTime | 書き込み操作あたりの平均秒数
AverageTransferTime | 操作あたりの平均秒数
AverageDiskQueueLength | キューに登録済みのディスク操作数の平均値
ReadBytesPerSecond | 1 秒あたりの読み取りバイト数
WriteBytesPerSecond | 1 秒あたりの書き込みバイト数
BytesPerSecond | 1 秒あたりの読み取りまたは書き込みバイト数

すべてのディスクの集計値を取得するには、`"condition": "IsAggregate=True"` を設定します。 特定のデバイス (`/dev/sdf1` など) の情報を取得するには、`"condition": "Name=\\"/dev/sdf1\\""` のように設定します。

## <a name="install-and-configure-lad-30"></a>LAD 3.0 をインストールして構成する

### <a name="azure-cli"></a>Azure CLI

保護された設定が *ProtectedSettings.json* ファイルにあり、パブリック構成情報が *PublicSettings.json* にある場合は、次のコマンドを実行します。

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

このコマンドは、Azure CLI の Azure Resource Manager モードを使用していることを前提としています。 クラシック デプロイ モデルの VM 用に LAD を構成するには、"asm" モード (`azure config mode asm`) に切り替え、コマンド内のリソース グループ名を省略します。 

詳細については、[クロスプラットフォーム CLI ドキュメント](/cli/azure/authenticate-azure-cli)をご覧ください。

### <a name="powershell"></a>PowerShell

保護された設定が `$protectedSettings` 変数にあり、パブリック構成情報が `$publicSettings` 変数にある場合、このコマンドを実行します。

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>LAD 3.0 の構成例

前述の定義に基づき、このセクションでは LAD 3.0 拡張機能の構成例を示して説明します。 この例を特定のケースに適用するには、独自のストレージ アカウント名、アカウント SAS トークン、Event Hubs SAS トークンを使用します。

> [!NOTE]
> 公開設定と保護された設定を提供する方法は、LAD のインストールに Azure CLI または PowerShell のいずれを使用するかによって異なります。 
>
> * Azure CLI を使用する場合、前のサンプル コマンドを使用するには、次の設定を *ProtectedSettings.json* と *PublicSettings.json* に保存します。 
> * PowerShell を使用する場合、`$protectedSettings = '{ ... }'` を実行して `$protectedSettings` と `$publicSettings` に次の設定を保存します。

### <a name="protected-settings"></a>保護された設定

保護された設定では次が構成されます。

* ストレージ アカウントです。
* 一致するアカウント SAS トークン。
* 複数のシンク (`JsonBlob` または `EventHub` と SAS トークン)。

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Public 設定

公開設定により、LAD で次の操作が行われます。

* percent-processor-time メトリックと used-disk-space メトリックを `WADMetrics*` テーブルにアップロードする。
* syslog ファシリティ `"user"` と重大度 `"info"` からのメッセージを `LinuxSyslog*` テーブルにアップロードする。
* 生の OMI クエリの結果 (`PercentProcessorTime` と `PercentIdleTime`) を指定された `LinuxCPU` テーブルにアップロードする。
* ファイル `/var/log/myladtestlog` の追加行を `MyLadTestLog` テーブルにアップロードする。

いずれの場合も、データは次の場所にもアップロードされます。

* Azure Blob Storage です。 コンテナー名は、`JsonBlob` シンクで定義されているとおりです。
* `EventHub` シンクで指定されている Event Hubs エンドポイント。

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

構成の `resourceId` は、VM または仮想マシン スケール セットのものと一致する必要があります。

* Azure プラットフォームのメトリックによるチャート作成およびアラート通知では、作業中の VM の `resourceId` が認識されています。 `resourceId` を検索キーとして使用して、VM のデータが検索されることが想定されます。
* Azure 自動スケーリングを使用する場合、自動スケーリング構成の `resourceId` は LAD で使用する `resourceId` と一致する必要があります。
* `resourceId` は、LAD によって書き込まれる JSON BLOB の名前に組み込まれています。

## <a name="view-your-data"></a>データを表示する

パフォーマンス データの表示やアラートの設定には、Azure portal を使用します。

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Azure portal が表示されているスクリーンショット。メトリックの [使用済みディスク領域] が選択され、結果のグラフが表示されています。":::

`performanceCounters` データは常に Azure Storage テーブルに格納されます。 Azure Storage API は、さまざまな言語とプラットフォームで利用できます。

`JsonBlob` シンクに送信されるデータは、[保護された設定](#protected-settings)で指定されたストレージ アカウント内の BLOB に格納されます。 Azure Blob Storage API を使用すると、BLOB データを使用できます。

次の UI ツールを使用して、Azure Storage のデータにアクセスすることもできます。

<<<<<<< HEAD
* Visual Studio のサーバー エクスプローラー。
* [スクリーンショットには、Azure Storage Exploref のコンテナーとテーブルが示されています](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer")。
=======
* Visual Studio のサーバー エクスプローラー
* [Azure 記憶域エクスプローラー](https://azurestorageexplorer.codeplex.com/)
>>>>>>> repo_sync_working_branch

Azure Storage Explorer セッションの次のスクリーンショットには、テスト VM の正しく構成された LAD 3.0 拡張機能から生成された Azure Storage テーブルとコンテナーが示されています。 この画像は、「[LAD 3.0 の構成例](#example-lad-30-configuration)」と正確には一致していません。

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Azure Storage Explorer が表示されているスクリーンショット。":::


Event Hubs エンドポイントに発行されたメッセージを使用する方法の詳細については、関連する [Event Hubs のドキュメント](../../event-hubs/event-hubs-about.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) で、収集するメトリック用のアラートを作成します。
* メトリックの[監視グラフ](../../azure-monitor/data-platform.md)を作成します。
<<<<<<< HEAD
* メトリックを使用して[仮想マシン スケール セットを作成](../linux/tutorial-create-vmss.md)し、自動スケールを制御する方法について説明します。
=======
* メトリックを使用して[仮想マシン スケール セットを作成](../linux/tutorial-create-vmss.md)し、自動スケーリングを制御します。
>>>>>>> repo_sync_working_branch
