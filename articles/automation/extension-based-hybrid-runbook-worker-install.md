---
title: 拡張機能ベースの Windows または Linux ユーザー Hybrid Runbook Worker を Azure Automation にデプロイする (プレビュー)
description: この記事では、お使いのローカル データセンターまたはクラウド環境内の Windows ベースのマシン上で Runbook を実行するために使用できる拡張機能ベースの Windows または Linux ユーザー Hybrid Runbook Worker をデプロイする方法について説明します。
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 48f83cd92f682b6fb66a1d7b5bebd49cd5053b62
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400777"
---
# <a name="deploy-an-extension-based-windows-or-linux-user-hybrid-runbook-worker-in-automation-preview"></a>拡張機能ベースの Windows または Linux ユーザー Hybrid Runbook Worker を Automation にデプロイする (プレビュー)

拡張機能ベースのオンボードは、**ユーザー** Hybrid Runbook Worker 専用です。 **システム** Hybrid Runbook Worker のオンボードについては、「[エージェントベースの Windows Hybrid Runbook Worker を Automation にデプロイする](./automation-windows-hrw-install.md)」または「[エージェントベースの Linux Hybrid Runbook Worker を Automation にデプロイする](./automation-linux-hrw-install.md)」を参照してください。 

Azure Automation のユーザー Hybrid Runbook Worker 機能を使用すると、Azure または Azure 以外のマシン ([Azure Arc 対応サーバー](../azure-arc/servers/overview.md)に登録されているサーバーを含む) 上で Runbook を直接実行することができます。 ロールをホストしているマシンまたはサーバーで、環境内のリソースに対して Runbook を直接実行して、それらのローカル リソースを管理できます。

Azure Automation によって Runbook が格納および管理された後、1 つ以上の選択されたマシンに配信されます。 この記事では、Windows または Linux マシンにユーザー Hybrid Runbook Worker をデプロイする方法、worker を削除する方法、および Hybrid Runbook Worker グループを削除する方法について説明します。

Runbook Worker が正常にデプロイされたら、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」を参照して、オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を確認します。

## <a name="prerequisites"></a>前提条件

### <a name="machine-minimum-requirements"></a>マシンの最小要件
- 2 コア。
- 4 GB の RAM。
- システム割り当てマネージド ID は、Azure 仮想マシンまたは Arc 対応サーバーで有効にする必要があります。  システム割り当てマネージド ID が有効になっていない場合は、追加プロセスの一環として有効になります。
- Azure 以外のマシンには、Azure Arc 対応サーバー エージェント (接続されているマシン エージェント) がインストールされている必要があります。 `AzureConnectedMachineAgent` のインストールの手順については、「[Azure portal からハイブリッド マシンを Azure に接続する](../azure-arc/servers/onboard-portal.md)」を参照してください。

### <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
| Windows | Linux (x64)|
|---|---|
| &#9679; Windows Server 2019 (Server Core を含む) <br> &#9679; Windows Server 2016、バージョン 1709 および 1803 (Server Core を除く) <br> &#9679; Windows Server 2012、2012 R2 <br><br> | &#9679; Debian GNU/Linux 7 および 8 <br> &#9679; Ubuntu 18.04 および 20.04 LTS <br> &#9679; SUSE Linux Enterprise Server 15 および 15.1 (SUSE のバージョン 13 と 14 はリリースされませんでした) <br> &#9679; Red Hat Enterprise Linux Server 7 および 8 |

### <a name="other-requirements"></a>その他の要件
| Windows | Linux (x64)|
|---|---|
| Windows PowerShell 5.1 (WMF 5.1 をダウンロードする)。 PowerShell Core はサポートされていません。| Linux Hardening を有効にすることはできません。  |
| .NET Framework 4.6.2 以降。 ||

### <a name="package-requirements-for-linux"></a>Linux のパッケージ要件

| 必須パッケージ | 説明 |最小バージョン|
|--------------------- | --------------------- | -------------------|
|Glibc |GNU C ライブラリ| 2.5-12 |
|Openssl| OpenSSL ライブラリ | 1.0 (TLS 1.1 と TLS 1.2 がサポートされます)|
|Curl | cURL Web クライアント | 7.15.5|
|Python-ctypes | Python 用の外部関数ライブラリ| Python 2.x または Python 3.x が必要 |
|PAM | Pluggable Authentication Module (プラグ可能な認証モジュール)|

| オプション パッケージ | 説明 | 最小バージョン|
|--------------------- | --------------------- | -------------------|
| PowerShell Core | PowerShell Runbook を実行するには、PowerShell Core をインストールする必要があります。 方法については、[Linux への PowerShell Core のインストール](/powershell/scripting/install/installing-powershell-core-on-linux)に関するページを参照してください。 | 6.0.0 |

### <a name="network-requirements"></a>ネットワークの要件

### <a name="proxy-server-use"></a>プロキシ サーバーの使用

Azure Automation と、拡張機能ベースの Hybrid Runbook Worker を実行しているマシンとの間の通信にプロキシ サーバーを使用する場合は、適切なリソースにアクセスできることを確認してください。 Hybrid Runbook Worker および Automation サービスからの要求のタイムアウトは 30 秒です。 3 回試行した後で、要求は失敗します。

### <a name="firewall-use"></a>ファイアウォールの使用

ファイアウォールを使用してインターネットへのアクセスを制限する場合は、アクセスを許可するようにファイアウォールを構成する必要があります。 次のポートと URL は、Hybrid Runbook Worker、および [Automation State Configuration](./automation-dsc-overview.md) が Azure Automation と通信するために必要です。

| プロパティ | 説明 |
|---|---|
|Port | 443 (送信インターネット アクセス用)|
|グローバル URL |*.azure-automation.net|
|US Gov バージニアのグローバル URL |*.azure-automation.us|
|エージェント サービス |`https://<workspaceId>.agentsvc.azure-automation.net`|

## <a name="create-hybrid-worker-group"></a>ハイブリッド Worker グループを作成する 

次の手順を実行して、Azure portal でハイブリッド Worker グループを作成します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. Automation アカウントに移動します。 

1. **[プロセス オートメーション]** で **[ハイブリッド Worker グループ]** を選択します。 

1. **[+ ハイブリッド Worker グループの作成]** を選択します。 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-groups-portal.png" alt-text="ポータルで [ハイブリッド Worker グループ] オプションを選択する。":::

1.  **[基本]**  タブの **[名前]** テキスト ボックスに、ハイブリッド Worker グループの名前を入力します。 

1. **[実行アカウントの資格情報を使用する]** オプション: 
   - **[いいえ]** を選択すると、ローカル システム アカウントを使用してハイブリッド拡張機能がインストールされます。
   - **[はい]** を選択した場合は、ドロップダウン リストから資格情報アセットを選択します。

1. **[次へ]** を選択して **[ハイブリッド Worker]** タブに進みます。このハイブリッド Worker グループに追加する Azure 仮想マシンまたは Azure Arc 対応サーバーを選択できます。 マシンを選択しない場合は、空のハイブリッド Worker グループが作成されます。 後でマシンを追加することもできます。 

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/basics-tab-portal.png" alt-text="[基本] タブに名前と資格情報を入力する。":::

1. **[マシンの追加]** を選択して、 **[ハイブリッド Worker としてマシンを追加する]** ページに移動します。 他のハイブリッド Worker グループの一部ではないマシンのみが表示されます。 

1. ハイブリッド Worker グループに追加するマシンの横にあるチェックボックスをオンにします。 Azure 以外のマシンが一覧に表示されていない場合は、Azure Arc Connected Machine エージェントがマシンにインストールされていることを確認します。  

1. **[追加]** を選択します。 

1. **[次へ]** を選択して、 **[確認と作成]** タブに移動します。 

1. **［作成］** を選択します ハイブリッド Worker 拡張機能がマシンにインストールされ、ハイブリッド Worker がハイブリッド Worker グループに登録されます。 ハイブリッド Worker はグループにすぐに追加されますが、拡張機能のインストールには数分かかる場合があります。 **[更新]** を選択して、新しいデータを表示します。 グループ名を選択して、ハイブリッド Worker の詳細を表示します。

   > [!NOTE]
   > 選択したマシンが既に別のハイブリッド Worker グループに含まれている場合、そのマシンはハイブリッド Worker グループに追加されません。

## <a name="add-a-machine-to-a-hybrid-worker-group"></a>ハイブリッド Worker グループにマシンを追加する

既存のハイブリッド Worker グループにマシンを追加できます。

1. **[プロセス オートメーション]** で、 **[ハイブリッド Worker グループ]** を選択し、次に、既存のハイブリッド Worker グループを選択して、 **[ハイブリッド Worker グループ]** ページに移動します。

1. **[ハイブリッド Worker グループ]** で、 **[ハイブリッド Worker]** を選択します。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-add-machine.png" alt-text="既存のグループにマシンを追加するための [追加] ボタン。":::

1. **[+ 追加]** を選択して、 **[ハイブリッド Worker としてマシンを追加する]** ページに移動します。 他のハイブリッド Worker グループの一部ではないマシンのみが表示されます。 

1. ハイブリッド Worker グループに追加するマシンの横にあるチェックボックスをオンにします。 Azure 以外のマシンが一覧に表示されていない場合は、Azure Arc Connected Machine エージェントがマシンにインストールされていることを確認します。

1. **[+ 追加]** を選択して、マシンをグループに追加します。 追加すると、マシンの種類が Azure 仮想マシンまたは Arc 対応サーバーとして表示されます。 **[プラットフォーム]** フィールドには、 **[エージェント ベース (V1)]** または **[拡張機能ベース (V2)]** として Worker が表示されます。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/hybrid-worker-group-platform.png" alt-text="エージェントまたは拡張機能ベースを示す [プラットフォーム] フィールド。":::

## <a name="delete-a-hybrid-runbook-worker"></a>Hybrid Runbook Worker を削除する

ポータルから Hybrid Runbook Worker を削除できます。

1. **[プロセス オートメーション]** で、 **[ハイブリッド Worker グループ]** を選択し、次に、ハイブリッド Worker グループを選択して、 **[ハイブリッド Worker グループ]** ページに移動します。

1. **[ハイブリッド Worker グループ]** で、 **[ハイブリッド Worker]** を選択します。

1. ハイブリッド Worker グループから削除するマシンの横にあるチェックボックスをオンにします。

1. **[削除]** を選択します。  **[ハイブリッド Worker の削除]** ダイアログ ボックスに、選択したハイブリッド Worker が完全に削除されるという警告が表示されます。 **[削除]** を選択します。 この操作により、**拡張機能ベース (V2)** Worker の拡張機能が削除されます。または、**エージェント ベース (V1)** のエントリがポータルから削除されます。 ただし、古いハイブリッド Worker は VM 上に残ります。 エージェントを手動でアンインストールするには、「[エージェントをアンインストールする](../azure-monitor/agents/agent-manage.md#uninstall-agent)」を参照してください。

   :::image type="content" source="./media/extension-based-hybrid-runbook-worker-install/delete-machine-from-group.png" alt-text="既存のグループから仮想マシンを削除します。":::

   > [!NOTE]
   > ハイブリッド Worker は、**エージェント ベース (V1)** と **拡張機能ベース (V2)** の両方のプラットフォームと共存できます。 **エージェント ベース (V1)** を既に実行しているハイブリッド Worker に **拡張機能ベース (V2)** をインストールすると、グループ内に Hybrid Runbook Worker の 2 つのエントリが表示されます。 1 つはプラットフォーム **拡張機能ベース (V2)** で、もう 1 つは **エージェント ベース (V1)** です。

## <a name="delete-a-hybrid-runbook-worker-group"></a>Hybrid Runbook Worker グループを削除する

ポータルから空の Hybrid Runbook Worker グループを削除できます。

1. **[プロセス オートメーション]** で、 **[ハイブリッド Worker グループ]** を選択し、次に、ハイブリッド Worker グループを選択して、 **[ハイブリッド Worker グループ]** ページに移動します。

1. **[削除]** を選択します。 ハイブリッド Worker グループにハイブリッド Worker として定義されているマシンを削除するという警告がダイアログ ボックスに表示されます。 グループに Worker が既に追加されている場合は、最初にグループから Worker を削除する必要があります。

1. **[はい]** を選択します。 ハイブリッド Worker グループが削除されます。

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

Azure Resource Manager (ARM) テンプレートを使用して、新しい Azure Windows VM を作成し、既存の Automation アカウントとハイブリッド Worker グループに接続できます。 ARM テンプレートの詳細については、「[ARM テンプレートとは](../azure-resource-manager/templates/overview.md)」を参照してください。

### <a name="review-the-template"></a>テンプレートを確認する

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "automationAccount": {
      "type": "string"
    },
    "automationAccountLocation": {
      "type": "string"
    },
    "workerGroupName": {
      "type": "string"
    },
    "virtualMachineName": {
      "type": "string",
      "defaultValue": "simple-vm",
      "metadata": {
        "description": "Name of the virtual machine."
      }
    },
    "adminUsername": {
      "type": "string",
      "metadata": {
        "description": "Username for the Virtual Machine."
      }
    },
    "adminPassword": {
      "type": "securestring",
      "minLength": 12,
      "metadata": {
        "description": "Password for the Virtual Machine."
      }
    },
    "vmLocation": {
      "type": "string",
      "defaultValue": "North Central US",
      "metadata": {
        "description": "Location for the VM."
      }
    },
    "vmSize": {
      "type": "string",
      "defaultValue": "Standard_DS1_v2",
      "metadata": {
        "description": "Size of the virtual machine."
      }
    },
    "osVersion": {
      "type": "string",
      "defaultValue": "2019-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter",
        "2016-Nano-Server",
        "2016-Datacenter-with-Containers",
        "2016-Datacenter",
        "2019-Datacenter",
        "2019-Datacenter-Core",
        "2019-Datacenter-Core-smalldisk",
        "2019-Datacenter-Core-with-Containers",
        "2019-Datacenter-Core-with-Containers-smalldisk",
        "2019-Datacenter-smalldisk",
        "2019-Datacenter-with-Containers",
        "2019-Datacenter-with-Containers-smalldisk"
      ],
      "metadata": {
        "description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version."
      }
    },
    "dnsNameForPublicIP": {
      "type": "string",
      "metadata": {
        "description": "DNS name for the public IP"
      }
    },
    "_CurrentDateTimeInTicks": {
      "type": "string",
      "defaultValue": "[utcNow('yyyy-MM-dd')]"
    }
  },
  "variables": {
    "nicName": "myVMNict",
    "addressPrefix": "10.0.0.0/16",
    "subnetName": "Subnet",
    "subnetPrefix": "10.0.0.0/24",
    "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
    "vmName": "[parameters('virtualMachineName')]",
    "virtualNetworkName": "MyVNETt",
    "publicIPAddressName": "myPublicIPt",
    "networkSecurityGroupName": "default-NSGt",
    "UniqueStringBasedOnTimeStamp": "[uniqueString(deployment().name, parameters('_CurrentDateTimeInTicks'))]"
  },
  "resources": [
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "[variables('publicIPAddressName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
        "dnsSettings": {
          "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
        }
      }
    },
    {
      "comments": "Default Network Security Group for template",
      "type": "Microsoft.Network/networkSecurityGroups",
      "apiVersion": "2020-08-01",
      "name": "[variables('networkSecurityGroupName')]",
      "location": "[parameters('vmLocation')]",
      "properties": {
        "securityRules": [
          {
            "name": "default-allow-3389",
            "properties": {
              "priority": 1000,
              "access": "Allow",
              "direction": "Inbound",
              "destinationPortRange": "3389",
              "protocol": "Tcp",
              "sourceAddressPrefix": "*",
              "sourcePortRange": "*",
              "destinationAddressPrefix": "*"
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/virtualNetworks",
      "name": "[variables('virtualNetworkName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
      ],
      "properties": {
        "addressSpace": {
          "addressPrefixes": [
            "[variables('addressPrefix')]"
          ]
        },
        "subnets": [
          {
            "name": "[variables('subnetName')]",
            "properties": {
              "addressPrefix": "[variables('subnetPrefix')]",
              "networkSecurityGroup": {
                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-08-01",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[variables('nicName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('publicIPAddressName')]",
        "[variables('virtualNetworkName')]"
      ],
      "properties": {
        "ipConfigurations": [
          {
            "name": "ipconfig1",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
              },
              "subnet": {
                "id": "[variables('subnetRef')]"
              }
            }
          }
        ]
      }
    },
    {
      "apiVersion": "2020-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[variables('vmName')]",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[variables('nicName')]"
      ],
      "identity": {
             "type": "SystemAssigned"
      } ,
      "properties": {
        "hardwareProfile": {
          "vmSize": "[parameters('vmSize')]"
        },
        "osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUsername')]",
          "adminPassword": "[parameters('adminPassword')]"
        },
        "storageProfile": {
          "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "[parameters('osVersion')]",
            "version": "latest"
          },
          "osDisk": {
            "createOption": "FromImage"
          }
        },
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
          ]
        }
      }
    },
    {
      "type": "Microsoft.Automation/automationAccounts",
      "apiVersion": "2021-06-22",
      "name": "[parameters('automationAccount')]",
      "location": "[parameters('automationAccountLocation')]",
      "properties": {
        "sku": {
          "name": "Basic"
        }
      },
      "resources": [
        {
          "name": "[concat(parameters('workerGroupName'),'/',guid('AzureAutomationJobName', variables('UniqueStringBasedOnTimeStamp')))]",
          "type": "hybridRunbookWorkerGroups/hybridRunbookWorkers",
          "apiVersion": "2021-06-22",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
            "[resourceId('Microsoft.Compute/virtualMachines', variables('vmName'))]"
          ],
          "properties": {
            "vmResourceId": "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
          }
        }
      ]
    },
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat(parameters('virtualMachineName'),'/HybridWorkerExtension')]",
      "apiVersion": "2020-12-01",
      "location": "[parameters('vmLocation')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))]",
        "[resourceId('Microsoft.Compute/virtualMachines', parameters('virtualMachineName'))]"
      ],
      "properties": {
        "publisher": "Microsoft.Azure.Automation.HybridWorker",
        "type": "HybridWorkerForWindows",
        "typeHandlerVersion": "0.1",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "AutomationAccountURL": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
        }
      }
    }
  ],
  "outputs": {
    "output1": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Automation/automationAccounts', parameters('automationAccount'))).AutomationHybridServiceUrl]"
    }
  }
}
```

テンプレート内に定義されている Azure リソース:

- [hybridRunbookWorkerGroups/hybridRunbookWorkers](/azure/templates/microsoft.automation/automationaccounts/hybridrunbookworkergroups/hybridrunbookworkers)
- [Microsoft.Compute/virtualMachines/extensions](/azure/templates/microsoft.compute/virtualmachines/extensions)

### <a name="review-parameters"></a>パラメーターの確認

このテンプレートで使用されているパラメーターを確認します。

|プロパティ |説明 |
|---|---|
|automationAccount| 既存の Automation アカウントの名前。 |
|automationAccountLocation | 既存の Automation アカウントのリージョン。 |
|workerGroupName | 既存のハイブリッド Worker グループの名前。 |
|virtualMachineName| 作成する VM の名前。 既定値は `simple-vm` です。|
|adminUsername| VM の管理者ユーザー名。 |
|adminPassword| VM 管理者のパスワード。 |
|vmLocation| 新しい VM のリージョン。 既定値は `North Central US` です。|
|vmSize| 新しい VM のサイズ。 既定値は `Standard_DS1_v2` です。 |
|osVersion| 新しい Windows VM の OS。 既定値は `2019-Datacenter` です。|
|dnsNameForPublicIP| パブリック IP の DNS 名。 |


## <a name="next-steps"></a>次のステップ

* オンプレミスのデータセンターや他のクラウド環境のプロセスを自動化するように Runbook を構成する方法を学習するには、「[Hybrid Runbook Worker での Runbook の実行](automation-hrw-run-runbooks.md)」をご覧ください。

* Hybrid Runbook Worker のトラブルシューティングを行う方法については、「[Hybrid Runbook Worker のトラブルシューティング](troubleshoot/hybrid-runbook-worker.md#general)」を参照してください。
