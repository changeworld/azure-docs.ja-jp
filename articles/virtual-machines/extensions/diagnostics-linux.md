---
title: Azure Compute - Linux Diagnostic Extension 4.0
description: Azure Linux Diagnostic Extension (LAD) 4.0 を構成して、Azure で実行中の Linux VM からメトリックとログ イベントを収集する方法。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aae7cc0d9289e4121f0cb58b719ff791e4880bb4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111950021"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>Linux Diagnostic Extension 4.0 を使用して、メトリックとログを監視する

このドキュメントでは、Linux Diagnostic Extension (LAD) の最新バージョンについて説明します。

> [!IMPORTANT]
> バージョン 3.x の詳細については、「[Linux Diagnostic Extension 3.0 を使用して、メトリックとログを監視する](./diagnostics-linux-v3.md)」を参照してください。
> バージョン 2.3 以前の詳細については、[Linux VM のパフォーマンスと診断データの監視](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)に関する記事を参照してください。

## <a name="introduction"></a>はじめに

Linux Diagnostic Extension は、Microsoft Azure で実行中の Linux VM の正常性を監視するのに役立ちます。 次のコレクションと機能があります。

| データ ソース | カスタマイズ オプション | 必須の宛先 | 省略可能な宛先 |
| ----------- | --------------------- | -------------------- | --------------------- |
| メトリック     | [カウンター、集計、サンプル レート、指定子](#performancecounters) | Azure Table Storage | EventHub、Azure Blob Storage (JSON 形式)、Azure Monitor<sup>1</sup> |
| syslog      | [ファシリティ、重大度レベル](#syslogevents) | Azure Table Storage | EventHub、Azure Blob Storage (JSON 形式)
| ファイル       | [ログ パス、宛先テーブル](#filelogs) | Azure Table Storage | EventHub、Azure Blob Storage (JSON 形式)

<sup>1</sup> LAD 4.0 の新機能

この拡張機能は、両方の Azure デプロイ モデル (Azure Resource Manager とクラシック) で動作します。

## <a name="install-the-extension"></a>拡張機能をインストールする

VM および仮想マシン スケール セット用のこの拡張機能は、Azure PowerShell コマンドレット、Azure CLI スクリプト、Azure Resource Manager テンプレート (ARM テンプレート)、Azure portal のいずれかを使用して有効にできます。 詳細については、[拡張機能](features-linux.md)に関するページをご覧ください。

>[!NOTE]
>Linux Diagnostic VM 拡張機能の一部のコンポーネントは、[Log Analytics VM 拡張機能](./oms-linux.md)にも付属しています。 このアーキテクチャが原因で、両方の拡張機能が同じ ARM テンプレートでインスタンス化されると、競合が発生する場合があります。
>
>インストール時の競合を回避するには、[`dependsOn` ディレクティブ](../../azure-resource-manager/templates/resource-dependency.md#dependson)を使用して、拡張機能を順番にインストールします。 拡張機能は、どちらの順序でもインストールできます。

インストール手順と[ダウンロード可能なサンプル構成](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)を使用して、LAD 4.0 を次のように構成します。

* LAD バージョン 2.3 および 3.x で提供されていたのと同じメトリックをキャプチャして保存します。
* Azure Storage への通常のシンクと共に Azure Monitor シンクにメトリックを送信します。 この機能は LAD 4.0 での新機能です。
* LAD 3.0 と同じように、便利な一連のファイル システム メトリックをキャプチャします。
* LAD 2.3 で有効になっている既定の syslog コレクションをキャプチャします。
* Azure portal エクスペリエンスを使用して、VM メトリックのチャート作成とアラート通知を行います。

ダウンロード可能な構成は単なる例です。 ニーズに合うように変更してください。

### <a name="supported-linux-distributions"></a>サポートされている Linux ディストリビューション

Linux Diagnostic Extension では、多くのディストリビューションとバージョンがサポートされています。 ディストリビューションとバージョンの以下の一覧は、Azure で動作保証済みの Linux ベンダーのイメージにのみ適用されます。 一般に、この拡張機能では、アプライアンスなどのサードパーティの BYOL および BYOS イメージはサポートされていません。

Debian 7 などのメジャー バージョンのみを示しているディストリビューションは、すべてのマイナー バージョンでもサポートされています。 特定のマイナー バージョンが指定されている場合は、そのバージョンのみがサポートされます。 プラス記号 (+) が追加されている場合は、指定されているバージョンと同じかそれより後のマイナー バージョンがサポートされます。

サポートされているディストリビューションとバージョン:

- Ubuntu 18.04、16.04、14.04
- CentOS 7、6.5+
- Oracle Linux 7、6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9、8、7
- Red Hat Enterprise Linux (RHEL) 7、6.7+

### <a name="prerequisites"></a>前提条件

* **Azure Linux エージェント バージョン 2.2.0 以降**。 ほとんどの Azure VM Linux ギャラリー イメージには、バージョン 2.2.7 以降が含まれています。 `/usr/sbin/waagent -version` を実行して、VM にインストールされているバージョンを確認します。 VM で古いバージョンのゲスト エージェントが実行されている場合は、[Linux エージェントを更新](./update-linux-agent.md)します。
* **Azure CLI**。 ご使用のマシンに [Azure CLI 環境をセットアップ](/cli/azure/install-azure-cli)します。
* **`wget` コマンド**。 まだない場合は、`sudo apt-get install wget` を実行します。
* データを格納するための **Azure サブスクリプションと汎用ストレージ アカウント**。  汎用ストレージ アカウントでは、必須のテーブル ストレージがサポートされます。  BLOB ストレージ アカウントは機能しません。
* **Python 2**。

### <a name="python-requirement"></a>Python の要件

Linux Diagnostic Extension には、Python 2 が必要です。 お使いの仮想マシンで使用されているディストリビューションに Python 2 が既定で含まれない場合は、それをインストールします。

次のサンプル コマンドでは、さまざまなディストリビューションに Python 2 がインストールされます。

- Red Hat、CentOS、Oracle: `yum install -y python2`
- Ubuntu、Debian: `apt-get install -y python2`
- SUSE: `zypper install -y python2`

`python2` 実行可能ファイルに *python* という別名を付ける必要があります。 これを実現する方法は次のとおりです。

1. 次のコマンドを実行して、既存の別名を削除します。

    ```
    sudo update-alternatives --remove-all python
    ```

2. 次のコマンドを実行して、新しい別名を作成します。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="installation"></a>インストール

LAD 4.0 は、Azure CLI または PowerShell でインストールおよび構成できます。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

保護された設定が *ProtectedSettings.json* ファイルにあり、パブリック構成情報が *PublicSettings.json* にある場合、このコマンドを実行します。

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

このコマンドは、Azure CLI の Azure Resource Management モードを使用していることを前提としています。 クラシック デプロイ モデルの VM 用に LAD を構成するには、サービス管理モード (`azure config mode asm`) に切り替え、コマンド内のリソース グループ名を省略します。

詳細については、[クロスプラットフォーム CLI ドキュメント](/cli/azure/authenticate-azure-cli)をご覧ください。

# <a name="powershell"></a>[PowerShell](#tab/powershell)

保護された設定が `$protectedSettings` 変数にあり、パブリック構成情報が `$publicSettings` 変数にある場合、このコマンドを実行します。

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

---

### <a name="sample-installation"></a>サンプル インストール

> [!NOTE]
> 以降のサンプルでは、コードを実行する前に、最初のセクションの変数に適切な値を入力します。

これらの例では、サンプル構成により標準データのセットが収集され、それらがテーブル ストレージに送信されます。 サンプル構成の URL と内容は変更される場合があります。

ほとんどの場合、ポータル設定の JSON ファイルのコピーをダウンロードし、必要に応じてカスタマイズする必要があります。 次に、URL から毎回ダウンロードするのではなく、テンプレートまたは独自の自動化を使用して、カスタマイズされたバージョンの構成ファイルを使用します。

> [!NOTE]
> 新しい Azure Monitor シンクを有効にする場合は、管理サービス ID (MSI) 認証トークンを生成するため、VM でシステム割り当ての ID を有効にする必要があります。 これらの設定は、VM の作成中または作成後に追加できます。
>
> Azure portal、Azure CLI、PowerShell、Azure Resource Manager の手順については、[マネージド ID の構成](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)に関するページを参照してください。

# <a name="azure-cli"></a>[Azure CLI](#tab/azcli)

#### <a name="installation-sample---azure-cli"></a>インストールのサンプル - Azure CLI

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

#### <a name="installation-sample---powershell"></a>インストールのサンプル - PowerShell

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

---

#### <a name="installation-sample-for-virtual-machine-scale-sets---azure-cli"></a>仮想マシン スケール セットのインストールのサンプル - Azure CLI

```azurecli
# Set your Azure virtual machine scale set diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

### <a name="update-the-extension-settings"></a>拡張機能の設定を更新する

保護された設定またはパブリック設定を変更した後、同じコマンドを実行して、VM にそれらを展開します。 いずれかの設定が変更されると、更新が拡張機能に送信されます。 LAD は構成を再読み込みして、再起動します。

### <a name="migrate-from-previous-versions-of-the-extension"></a>以前のバージョンの拡張機能から移行する

拡張機能の最新バージョンは、"*現在パブリック プレビュー段階の 4.0*" です。 以前のバージョン 3.x は引き続きサポートされています。 しかし、2.x バージョンは、2018 年 7 月 31 日以降、非推奨となりました。

> [!IMPORTANT]
> 3\.x から最新バージョンの拡張機能に移行するには、古い拡張機能をアンインストールします。 次に、バージョン 4 をインストールします。これには、Azure Monitor シンクにメトリックを送信するための、システム割り当て ID とシンクの更新された構成が含まれます。

新しい拡張機能をインストールするときに、マイナー バージョンの自動アップグレードを有効にします。
* Azure Resource Manager デプロイ モデルの VM では、VM デプロイ テンプレートに「`"autoUpgradeMinorVersion": true`」を含めます。
* Azure CLI または PowerShell を使用して拡張機能をインストールする場合、クラシック デプロイ モデルの VM では、バージョン `4.*` を指定します。

LAD 3.x に使用したのと同じストレージ アカウントを使用できます。

## <a name="protected-settings"></a>保護された設定

この一連の構成情報には、人目に付かないように保護する必要がある機密情報が含まれています。 ストレージ資格情報などが含まれます。 この設定は、暗号化された形式で拡張機能に転送され、格納されます。

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
storageAccountEndPoint | (省略可能) ストレージ アカウントが存在するクラウドを示すエンドポイント。 この設定がない場合、既定では、LAD は Azure パブリック クラウド `https://core.windows.net` を使用します。 Azure Germany、Azure Government、Azure China 21Vianet でストレージ アカウントを使用するには、この値を必要に応じて設定します。
storageAccountSasToken | BLOB およびテーブル サービス (`ss='bt'`) の[アカウント SAS トークン](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)。 このトークンは、コンテナーとオブジェクト (`srt='co'`) に適用されます。 これにより、追加、作成、一覧表示、更新、書き込みのアクセス許可が付与されます (`sp='acluw'`)。 先頭の疑問符 (?) を *含めないで* ください。
mdsdHttpProxy | (省略可能) 指定されたストレージ アカウントとエンドポイントに拡張機能が接続するために必要な HTTP プロキシ情報。
sinksConfig | (省略可能) メトリックとイベントの配信が可能な代替宛先の詳細。 以下のセクションでは、拡張機能によってサポートされる各データ シンクについて詳しく説明します。

ARM テンプレート内の SAS トークンを取得するには、`listAccountSas` 関数を使用します。 テンプレートの例については、[List 関数の例](../../azure-resource-manager/templates/template-functions-resource.md#list-example)を参照してください。

必要な SAS トークンを Azure portal で作成できます。

1. 拡張機能の書き込み先となる汎用ストレージ アカウントを選択します。
1. 左側のメニューの **[設定]** で、 **[Shared Access Signature]** を選択します。
1. 前に説明したように、選択を行います。
1. **[SAS の生成]** を選択します。

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="[SAS の生成] ボタンが示された [Shared access signature] ページが表示されているスクリーンショット。":::

生成された SAS を `storageAccountSasToken` フィールドにコピーします。 先頭の疑問符 (?) を削除します。

### <a name="sinksconfig"></a>sinksConfig

> [!NOTE]
> パブリックと保護の両方の設定には、省略可能な `sinksConfig` セクションがあります。 "*保護された*" 設定の `sinksConfig` セクションでは、`sasURL` のようなシークレットが含まれているため、`EventHub` および `JsonBlob` シンク構成のみが保持されます。 `AzMonSink` シンク構成を保護された設定に "**含めることはできません**"。

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

省略可能な `sinksConfig` セクションでは、収集された情報が拡張機能によって送信される送信先をさらに定義します。 `"sink"` 配列には、追加のデータ シンクごとのオブジェクトが含まれています。 `"type"` 属性によって、オブジェクトの他の属性が決まります。

要素 | 値
------- | -----
name | このシンクを拡張機能構成の他の場所で参照するために使用される文字列。
type | 定義されているシンクの型。 この型のインスタンス内のその他の値 (存在する場合) を決定します。

Linux Diagnostic Extension 4.0 では、`EventHub` と `JsonBlob` という 2 つの保護されたシンクの種類がサポートされています。

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

`"sasURL"` エントリには、データの発行先となるイベント ハブの完全な URL (SAS トークンを含む) が含まれています。 LAD には、送信要求を有効にするポリシーに名前を付ける SAS が必要です。 次に例を示します。

* `contosohub` という名前の Event Hubs 名前空間を作成します。
* `syslogmsgs` という名前のイベント ハブを名前空間に作成します。
* 送信要求を有効にする `writer` という名前の共有アクセス ポリシーをイベント ハブに作成します。

2018 年 1 月 1 日午前 0 時 UTC まで有効な SAS を作成した場合、`sasURL` の値は次の例のようになります。

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Event Hubs 用の SAS トークンでの情報の生成と取得の詳細については、「[SAS トークンの生成](/rest/api/eventhub/generate-sas-token#powershell)」を参照してください。

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

BLOB はシンクと同じ名前のコンテナーに格納されます。 BLOB コンテナー名に対する Azure Storage のルールが、`JsonBlob` シンクの名前に適用されます。 つまり、名前は 3 から 63 文字で、小文字の英数の ASCII 文字またはダッシュを使用します。

## <a name="public-settings"></a>Public 設定

パブリック設定構造体には、拡張機能によって収集された情報を制御するさまざまな設定ブロックが含まれています。 各設定 (`ladCfg` を除く) は省略可能です。 `ladCfg` にメトリックまたは syslog の収集を指定する場合、`StorageAccount` も指定する必要があります。 LAD 4.0 からのメトリック用に Azure Monitor シンクを有効にするには、`sinksConfig` 要素を指定する必要があります。

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

要素 | 値
------- | -----
StorageAccount | 拡張機能によってデータが書き込まれるストレージ アカウントの名前。 [保護された設定](#protected-settings)で指定されている名前にする必要があります。
mdsdHttpProxy | (省略可能) [保護された設定](#protected-settings)で指定されているプロキシ。 プライベートの値が設定されている場合は、パブリックの値がオーバーライドされます。 [保護された設定](#protected-settings)に、パスワードなどの秘密を含むプロキシ設定を格納します。

以下のセクションでは、残りの要素に関する詳細を説明します。

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

`ladCfg` 構造体は、Azure Monitor メトリック サービスおよび他のデータ シンクに配信するためのメトリックとログの収集を制御します。 `performanceCounters` または `syslogEvents` のどちらか、またはその両方を指定します。 また、`metrics` 構造体を指定します。

syslog またはメトリックの収集を有効にしない場合は、次のように、`ladCfg` 要素に空の構造体を指定します。

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
}
```

要素 | 値
------- | -----
eventVolume | (省略可能) ストレージ テーブル内で作成されるパーティションの数を制御します。 値は `"Large"`、`"Medium"`、または `"Small"` である必要があります。 値が指定されていない場合、既定値は `"Medium"` になります。
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
resourceId | VM または VM が所属する仮想マシン スケール セットの Azure Resource Manager リソース ID。 また、構成でいずれかの `JsonBlob` シンクを使用する場合は、この設定も指定します。
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
sinks | (省略可能) LAD で集計したメトリックの結果を送信するシンクの名前をコンマで区切ったリスト。 集計されたすべてのメトリックは、一覧表示された各シンクに発行されます。 例: `"MyEventHubSink, MyJsonSink, MyAzMonSink"`. 詳細については、[`sinksConfig` (保護された設定)](#sinksconfig) および [`sinksConfig` (パブリック設定)](#sinksconfig-1) を参照してください。
type | メトリックの実際のプロバイダーを識別します。
class | `"counter"` とともに、プロバイダーの名前空間内の特定のメトリックを識別します。
counter | `"class"` とともに、プロバイダーの名前空間内の特定のメトリックを識別します。 [以下](#metrics-supported-by-the-builtin-provider)の使用可能なカウンターの一覧を参照してください。
counterSpecifier | Azure Monitor メトリック名前空間内の特定のメトリックを識別します。
condition | (省略可能) メトリックが適用されるオブジェクトのインスタンスを選択します。 または、そのオブジェクトのすべてのインスタンスの集計を選択します。
sampleRate | このメトリックの生のサンプルが収集されるレートを設定する IS 8601 の間隔。 値が設定されていない場合、収集間隔は [`sampleRateInSeconds`](#ladcfg) の値によって設定されます。 サポートされている最小サンプル レートは 15 秒 (PT15S) です。
unit | メトリックの単位を定義します。 次のいずれかの文字列にする必要があります: `"Count"`、`"Bytes"`、`"Seconds"`、`"Percent"`、`"CountPerSecond"`、`"BytesPerSecond"`、`"Millisecond"`。 収集されたデータのコンシューマーは、収集されたデータ値がこの単位に一致することを期待しています。 LAD はこのフィールドを無視します。
displayName | `Guest (classic)` メトリックの名前空間で表示するときに Azure Monitor メトリック内のデータに関連付けられるラベル。 このラベルは、関連付けられているロケール設定によって指定された言語で設定します。 LAD はこのフィールドを無視します。<br/>**注**: `azure.vm.linux.guestmetrics` メトリックの名前空間で同じメトリックを表示する場合 (`AzMonSink` が構成されている場合に使用可能)、表示名は完全にカウンターに依存します。 カウンターと名前の間のマッピングについては、[以下の表](#metrics-supported-by-the-builtin-provider)を参照してください。

`counterSpecifier` は任意の識別子です。 Azure portal のグラフ作成およびアラート通知機能のようなメトリックのコンシューマーでは、メトリックまたはメトリックのインスタンスを識別する "キー" として `counterSpecifier` が使用されます。

`builtin` メトリックの場合、`/builtin/` で始まる `counterSpecifier` 値をお勧めします。 メトリックの特定のインスタンスを収集する場合は、`counterSpecifier` 値にインスタンスの識別子を添付します。 次に例をいくつか示します。

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
facilityName | syslog ファシリティの名前 (`"LOG\_USER"` や `"LOG\_LOCAL0"` など)。 詳細については、[syslog の man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)で "ファシリティ" に関するセクションを参照してください。
minSeverity | syslog の重大度レベル (`"LOG\_ERR"` や `"LOG\_INFO"` など)。 詳細については、[syslog の man ページ](http://man7.org/linux/man-pages/man3/syslog.3.html)で "レベル" に関するセクションを参照してください。 拡張機能は、指定されたレベル以上のファシリティに送信されるイベントをキャプチャします。

`syslogEvents` を指定すると、LAD では常に Azure Storage のテーブルにデータが書き込まれます。 同じデータを JSON BLOB と Event Hubs のどちらか一方または両方に書き込むことができます。 ただし、テーブルへのデータの格納を無効にすることはできません。

このテーブルのパーティション分割動作は、`performanceCounters` についての説明と同じです。 テーブル名は、次の文字列を連結したものです。

* `LinuxSyslog`
* 10 日ごとに変更される "YYYYMMDD" 形式の日付

例は、`LinuxSyslog20170410` や `LinuxSyslog20170609` のようになります。

### <a name="sinksconfig"></a>sinksConfig

省略可能なパブリック `sinksConfig` セクションでは、ストレージ アカウントと既定のゲスト メトリック ブレードに加えて、Azure Monitor シンクへのメトリックの送信を有効にします。

> [!NOTE]
> パブリックと保護の両方の設定には、省略可能な `sinksConfig` セクションがあります。 "*パブリック*" 設定の `sinksConfig` セクションには、`AzMonSink` シンク構成のみが保持され ます。 `EventHub` および `JsonBlob` シンク構成をパブリック設定に "**含めることはできません**"。

> [!NOTE]
> `sinksConfig` セクションでは、VM または仮想マシンス ケール セットでシステム割り当て ID を有効にする必要があります。
> Azure portal、CLI、PowerShell、または Azure Resource Manager を使用して、システムによってシステム割り当て ID を有効にすることができます。 [詳細な手順](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)に従うか、この記事の前のインストールのサンプルを参照してください。

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```

### <a name="filelogs"></a>fileLogs

`fileLogs` セクションでは、ログ ファイルのキャプチャを制御します。 LAD では、ファイルに書き込まれた新しいテキスト行がキャプチャされます。 テーブル行や、指定されたシンク (`JsonBlob` や `EventHub` など) にそれらを書き込みます。

> [!NOTE]
> `fileLogs` は `omsagent` という名前の LAD のサブコンポーネントによってキャプチャされます。 `fileLogs` を収集するには、`omsagent` ユーザーが指定したファイルに対する読み取りアクセス許可を持っていることを確認します。 また、そのファイルへのパス内のすべてのディレクトリに対する実行アクセス許可も必要です。 LAD がインストールされたら、`sudo su omsagent -c 'cat /path/to/file'` を実行してアクセス許可を確認できます。

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
file | 監視され、キャプチャされるログ ファイルの完全なパス名。 パス名は、1 つのファイルに対するものです。 ディレクトリの名前を指定したり、ワイルドカード文字を含めたりすることはできません。 `omsagent` ユーザー アカウントには、ファイル パスの読み取りアクセス権が必要です。
table | (省略可能) file の "末尾" から新しい行が書き込まれる Azure Storage テーブル。 テーブルは、保護された構成で指定されている、指定されたストレージ アカウント内に存在する必要があります。
sinks | (省略可能) ログ行が送信される追加のシンクの名前をコンマで区切ったリスト。

`"table"` と `"sinks"` のいずれか一方または両方を指定する必要があります。

## <a name="metrics-supported-by-the-builtin-provider"></a>組み込みプロバイダーでサポートされているメトリック

> [!NOTE]
> LAD でサポートされる既定のメトリックは、すべてのファイルシステム、ディスク、または名前にわたって集計されます。 集計されないメトリックについては、新しい Azure Monitor シンク メトリックのサポートを参照してください。

> [!NOTE]
> 各メトリックの表示名は、それが属しているメトリックの名前空間によって異なります。
> * `Guest (classic)` (ストレージアカウントから設定): `performanceCounters` セクションに指定された `displayName`、または Azure portal に表示される既定の表示名 ([VM] > [診断設定] > [メトリック] > [カスタム])。
> * `azure.vm.linux.guestmetrics` (構成されている場合は `AzMonSink` から設定): 下の表で指定されている "`azure.vm.linux.guestmetrics`表示名"。
>
> 実装の詳細により、`Guest (classic)` と `azure.vm.linux.guestmetrics` のバージョンの間のメトリック値は異なります。 クラシック メトリックにはエージェントに特定の集計が適用されていますが、新しいメトリックは集計されていないカウンターであるため、お客様は表示/アラート時に必要に応じて柔軟に集計を行うことができます。

`builtin` メトリック プロバイダーは、幅広いユーザーにとって最も興味深いメトリックのソースです。 これらのメトリックは、大きく 5 つのクラスに分類されます。

* プロセッサ
* メモリ
* ネットワーク
* ファイル システム
* ディスク

### <a name="builtin-metrics-for-the-processor-class"></a>"プロセッサ" クラスの組み込みのメトリック

"プロセッサ" クラスのメトリックは、VM のプロセッサ使用率に関する情報を提供します。 パーセンテージが集計されると、結果はすべての CPU の平均になります。

2 vCPU の VM では、1 つの vCPU が 100% ビジーで、もう一方が 100% アイドル状態の場合、報告される `PercentIdleTime` は 50 です。 各 vCPU が同じ期間に 50% ビジーだった場合、報告される結果はやはり 50 になります。 4 vCPU の VM では、1 つの vCPU が 100% ビジー状態で、その他がアイドル状態になると、報告される `PercentIdleTime` は 75 になります。

カウンタ | `azure.vm.linux.guestmetrics` 表示名 | 説明
--------- | ---------------------------------- | -------
`PercentIdleTime` | `cpu/usage_idle` | 集計ウィンドウ中にプロセッサでカーネル アイドル ループが実行されていた時間の割合
`PercentProcessorTime` |  `cpu/usage_active` | 非アイドルのスレッドを実行している時間の割合
`PercentIOWaitTime` |  `cpu/usage_iowait` | IO 操作の完了を待機した時間の割合
`PercentInterruptTime` |  `cpu/usage_irq` | ハードウェアまたはソフトウェア割り込みと DPC (遅延プロシージャ呼び出し) を実行する時間の割合
`PercentUserTime` |  `cpu/usage_user` | 集計ウィンドウ中の非アイドル時間のうち、通常の優先度のユーザー モードで消費された時間の割合
`PercentNiceTime` |  `cpu/usage_nice` | 非アイドル時間のうち、低下した (適した) 優先度で消費された割合
`PercentPrivilegedTime` |  `cpu/usage_system` | 非アイドル時間のうち、特権 (カーネル) モードで消費された割合

最初の 4 つのカウンターの合計は 100% となる必要があります。 最後の 3 つのカウンターの合計も 100% となります。 これら 3 つのカウンターで、`PercentProcessorTime`、`PercentIOWaitTime`、`PercentInterruptTime` の合計が分割されます。

### <a name="builtin-metrics-for-the-memory-class"></a>"メモリ" クラスの組み込みのメトリック

"メモリ" クラスのメトリックでは、メモリの使用率、ページング、スワップに関する情報が提供されます。

カウンタ | `azure.vm.linux.guestmetrics` 表示名 | 説明
--------- | ---------------------------------- | -------
`AvailableMemory` | `mem/available` | 使用可能な物理メモリ (MiB)
`PercentAvailableMemory` | `mem/available_percent` | 合計メモリに対する使用可能な物理メモリの割合
`UsedMemory` | `mem/used` | 使用中の物理メモリ (MiB)
`PercentUsedMemory` | `mem/used_percent` | 合計メモリに対する使用中の物理メモリの割合
`PagesPerSec` | `kernel_vmstat/total_pages` | 合計ページング (読み取り/書き込み)
`PagesReadPerSec` | `kernel_vmstat/pgpgin` | バッキング ストアから読み取られたページ数 (スワップ ファイル、プログラム ファイル、マップされたファイルなど)
`PagesWrittenPerSec` | `kernel_vmstat/pgpgout` | バッキング ストアに書き込まれたページ数 (スワップ ファイル、マップされたファイルなど)
`AvailableSwap` | `swap/free` | 未使用のスワップ領域 (MiB)
`PercentAvailableSwap` | `swap/free_percent` | 合計スワップに対する未使用のスワップ領域の割合
`UsedSwap` | `swap/used` | 使用中のスワップ領域 (MiB)
`PercentUsedSwap` | `swap/used_percent` | 合計スワップに対する使用中のスワップ領域の割合

このメトリックのクラスには、1 つのインスタンスしかありません。 `"condition"` 属性は有用な設定を持たず、省略する必要があります。

### <a name="builtin-metrics-for-the-network-class"></a>"ネットワーク" クラスの組み込みのメトリック

"ネットワーク" クラスのメトリックでは、起動後の個々のネットワーク インターフェイスでのネットワーク アクティビティに関する情報が提供されます。

LAD では帯域幅メトリックは公開されません。 これらのメトリックは、ホスト メトリックから取得できます。

カウンタ | `azure.vm.linux.guestmetrics` 表示名 | 説明
--------- | ---------------------------------- | -------
`BytesTransmitted` | `net/bytes_sent` | 起動後に送信された合計バイト数
`BytesReceived` | `net/bytes_recv` | 起動後に受信した合計バイト数
`BytesTotal` | `net/bytes_total` | 起動後に送受信された合計バイト数
`PacketsTransmitted` | `net/packets_sent` | 起動後に送信された合計パケット数
`PacketsReceived` | `net/packets_recv` | 起動後に受信した合計パケット数
`TotalRxErrors` | `net/err_in` | 起動後の受信エラー数
`TotalTxErrors` | `net/err_out` | 起動後の送信エラー数
`TotalCollisions` | `net/drop_total` | 起動後にネットワーク ポートによって報告された競合の数

### <a name="builtin-metrics-for-the-file-system-class"></a>ファイル システム クラスの組み込みメトリック

"ファイル システム" クラスのメトリックでは、ファイル システムの使用状況に関する情報が提供されます。 絶対値と割合の値は、(root ではなく) 通常のユーザーに表示されるように報告されます。

カウンタ | `azure.vm.linux.guestmetrics` 表示名 | 説明
--------- | ---------------------------------- | -------
`FreeSpace` | `disk/free` | 使用可能なディスク領域 (バイト単位)
`UsedSpace` | `disk/used` | 使用済みディスク領域 (バイト単位)
`PercentFreeSpace` | `disk/free_percent` | 空き領域の割合
`PercentUsedSpace` | `disk/used_percent` | 使用領域の割合
`PercentFreeInodes` | `disk/inodes_free_percent` | 未使用のインデックス ノード (inode) の割合
`PercentUsedInodes` | `disk/inodes_used_percent` | すべてのファイル システム全体で合計された割り当て済み (使用中) inode の割合
`BytesReadPerSecond` | `diskio/read_bytes_filesystem` | 1 秒あたりの読み取りバイト数
`BytesWrittenPerSecond` | `diskio/write_bytes_filesystem` | 1 秒あたりの書き込みバイト数
`BytesPerSecond` | `diskio/total_bytes_filesystem` | 1 秒あたりの読み取りまたは書き込みバイト数
`ReadsPerSecond` | `diskio/reads_filesystem` | 1 秒あたりの読み取り操作
`WritesPerSecond` | `diskio/writes_filesystem` | 1 秒あたりの書き込み操作
`TransfersPerSecond` | `diskio/total_transfers_filesystem` | 1 秒あたりの読み取りまたは書き込み操作

### <a name="builtin-metrics-for-the-disk-class"></a>"ディスク" クラスの組み込みメトリック

"ディスク" クラスのメトリックは、ディスク デバイスの使用状況に関する情報を提供します。 これらの統計情報は、ドライブ全体に適用されます。

デバイスに複数のファイル システムが存在する場合、そのデバイスのカウンターは、実際にはすべてのファイル システムについて集計されます。

カウンタ | `azure.vm.linux.guestmetrics` 表示名 | 説明
--------- | ---------------------------------- | -------
`ReadsPerSecond` | `diskio/reads` | 1 秒あたりの読み取り操作
`WritesPerSecond` | `diskio/writes` | 1 秒あたりの書き込み操作
`TransfersPerSecond` | `diskio/total_transfers` | 1 秒あたりの合計操作数
`AverageReadTime` | `diskio/read_time` | 読み取り操作あたりの平均秒数
`AverageWriteTime` | `diskio/write_time` | 書き込み操作あたりの平均秒数
`AverageTransferTime` | `diskio/io_time` | 操作あたりの平均秒数
`AverageDiskQueueLength` | `diskio/iops_in_progress` | キューに登録済みのディスク操作数の平均値
`ReadBytesPerSecond` | `diskio/read_bytes` | 1 秒あたりの読み取りバイト数
`WriteBytesPerSecond` | `diskio/write_bytes` | 1 秒あたりの書き込みバイト数
`BytesPerSecond` | `diskio/total_bytes` | 1 秒あたりの読み取りまたは書き込みバイト数

## <a name="example-lad-40-configuration"></a>LAD 4.0 の構成例

前述の定義に基づき、このセクションでは LAD 4.0 拡張機能の構成例を示して説明します。 このサンプルを特定のケースに適用するには、独自のストレージ アカウント名、アカウント SAS トークン、Event Hubs SAS トークンを使用します。

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

Public 設定により、LAD は次の操作を行います。

* percent-processor-time メトリックと used-disk-space メトリックを `WADMetrics*` テーブルにアップロードする。
* Syslog ファシリティ `"user"` と重大度 `"info"` からのメッセージを `LinuxSyslog*` テーブルにアップロードする。
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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

## <a name="view-your-data"></a>データの表示

Azure ポータルを使用して、パフォーマンス データを表示したり、アラートを設定したりします。

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="スクリーンショットには Azure portal が表示されています。メトリックの [使用済みディスク領域] が選択されています。結果のグラフが表示されています。":::

`performanceCounters` データは常に Azure Storage テーブルに格納されます。 Azure Storage API は、さまざまな言語とプラットフォームで利用できます。

`JsonBlob` シンクに送信されるデータは、[保護された設定](#protected-settings)で指定されたストレージ アカウント内の BLOB に格納されます。 すべての Azure Blob Storage API で BLOB データを使用できます。

次の UI ツールを使用して、Azure Storage のデータにアクセスすることもできます。

* Visual Studio のサーバー エクスプローラー
* [Azure 記憶域エクスプローラー](https://azurestorageexplorer.codeplex.com/)

Azure Storage Explorer セッションの次のスクリーンショットでは、テスト VM 上で正しく構成された LAD 4.0 拡張機能から生成された Azure Storage テーブルとコンテナーが示されています。 画像は [サンプル LAD 4.0 構成](#example-lad-40-configuration)と正確には一致しません。

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="スクリーンショットに Azure Storage Explorer が示されています。":::

Event Hubs エンドポイントに発行されたメッセージを使用する方法の詳細については、関連する [Event Hubs のドキュメント](../../event-hubs/event-hubs-about.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) で、収集するメトリック用のアラートを作成します。
* メトリックの[監視グラフを作成します](../../azure-monitor/data-platform.md)。
* 自動スケーリングを制御するためにメトリックを使用して[仮想マシン スケール セットを作成](../linux/tutorial-create-vmss.md)します。