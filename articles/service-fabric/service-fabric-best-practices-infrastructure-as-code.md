---
title: コードとしての Azure Service Fabric インフラストラクチャに関するベスト プラクティス
description: コードとしてのインフラストラクチャとして Azure Service Fabric を管理するためのベスト プラクティスと設計に関する考慮事項。
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ae7eaa628e0e3cfc256b7cd01035d9e068f47867
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110671119"
---
# <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

運用環境のシナリオでは、Resource Manager テンプレートを使用して Azure Service Fabric クラスターを作成します。 Resource Manager テンプレートでは、制御できるリソースのプロパティが多く、一貫性のあるリソース モデルが保証されます。

Windows および Linux 用のサンプルの Resource Manager テンプレートは、[GitHub の Azure サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates)で入手できます。 これらのテンプレートは、クラスター テンプレートの作成を始める際に使用できます。 `azuredeploy.json` と `azuredeploy.parameters.json` をダウンロードし、独自の要件に合わせて編集します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

上でダウンロードした `azuredeploy.json` テンプレートと `azuredeploy.parameters.json` テンプレートをデプロイするには、次の Azure CLI コマンドを使用します。

```azurecli
ResourceGroupName="sfclustergroup"
Location="westus"

az group create --name $ResourceGroupName --location $Location 
az deployment group create --name $ResourceGroupName  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

PowerShell を使用してリソースを作成する

```powershell
$ResourceGroupName="sfclustergroup"
$Location="westus"
$Template="azuredeploy.json"
$Parameters="azuredeploy.parameters.json"

New-AzResourceGroup -Name $ResourceGroupName -Location $Location
New-AzResourceGroupDeployment -Name $ResourceGroupName -TemplateFile $Template -TemplateParameterFile $Parameters
```

## <a name="service-fabric-resources"></a>Service Fabric のリソース

Azure Resource Manager を使用して、Service Fabric クラスターにアプリケーションとサービスを デプロイできます。 詳しくは、「[アプリケーションとサービスを Azure Resource Manager のリソースとして管理する](./service-fabric-application-arm-resource.md)」をご覧ください。 以下に示すのは、Resource Manager テンプレートのリソースに含める Service Fabric アプリケーション固有のリソースのベスト プラクティスです。

```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

Azure Resource Manager を使用してアプリケーションをデプロイするには、最初に Service Fabric アプリケーション パッケージ [sfpkg を作成する](./service-fabric-package-apps.md#create-an-sfpkg)必要があります。 次の Python スクリプトは、sfpkg を作成する方法の例です。

```python
# Create SFPKG that needs to be uploaded to Azure Storage Blob Container
microservices_sfpkg = zipfile.ZipFile(
    self.microservices_app_package_name, 'w', zipfile.ZIP_DEFLATED)
package_length = len(self.microservices_app_package_path)

for root, dirs, files in os.walk(self.microservices_app_package_path):
    root_folder = root[package_length:]
    for file in files:
        microservices_sfpkg.write(os.path.join(
            root, file), os.path.join(root_folder, file))

microservices_sfpkg.close()
```

## <a name="virtual-machine-os-automatic-upgrade-configuration"></a>仮想マシン OS の自動アップグレード構成

仮想マシンのアップグレードはユーザーが開始する操作です。Service Fabric クラスター ノードの更新プログラムの管理のために、[仮想マシン スケール セットの自動イメージ アップグレードを有効にする](how-to-patch-cluster-nodes-windows.md)ことをお勧めします。 パッチ オーケストレーション アプリケーション (POA) は、Azure 以外でホストされているクラスターを対象とした別のソリューションです。 POA は Azure で使用できますが、これをホストするには、スケール セットの OS 自動イメージ アップグレードを単に有効にするよりも多くの管理が必要になります。 以下は、OS の自動アップグレードを有効にするための、仮想マシン スケール セット Resource Manager テンプレートのプロパティです。

```json
"upgradePolicy": {
   "mode": "Automatic",
   "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade": true,
        "disableAutomaticRollback": false
    }
},
```
Service Fabric と OS の自動アップグレードを使用する場合、Service Fabric で実行されているサービスの高可用性を維持するために、新しい OS イメージは更新ドメインごとに 1 つずつロールアウトされます。 Service Fabric で OS の自動アップグレードを利用するには、クラスターが、Silver 以上の持続性層を使用するように構成されている必要があります。

次のレジストリ キーが false に設定されていることを確認して、調整されていない更新を Windows ホスト マシンが開始しないようにします。HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU

次の仮想マシン スケール セット テンプレートのプロパティを設定して、Windows Update を無効にします。
```json
"osProfile": {
        "computerNamePrefix": "{vmss-name}",
        "adminUsername": "{your-username}",
        "secrets": [],
        "windowsConfiguration": {
          "provisionVMAgent": true,
          "enableAutomaticUpdates": false
        }
      },
```

## <a name="service-fabric-cluster-upgrade-configuration"></a>Service Fabric クラスターのアップグレード構成

以下は、自動アップグレードを有効にするための、Service Fabric クラスター テンプレートのプロパティです。

```json
"upgradeMode": "Automatic",
```

クラスターを手動でアップグレードするには、cab/deb 配布をクラスター仮想マシンにダウンロードして、次の PowerShell を実行します。

```powershell
Copy-ServiceFabricClusterPackage -Code -CodePackagePath <"local_VM_path_to_msi"> -CodePackagePathInImageStore ServiceFabric.msi -ImageStoreConnectionString "fabric:ImageStore"
Register-ServiceFabricClusterPackage -Code -CodePackagePath "ServiceFabric.msi"
Start-ServiceFabricClusterUpgrade -Code -CodePackageVersion <"msi_code_version">
```

## <a name="next-steps"></a>次のステップ

* Windows Server を実行している VM またはコンピューター上にクラスターを作成する:[Windows Server 用の Service Fabric クラスターの作成](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Linux を実行している VM またはコンピューター上にクラスターを作成する:[Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
