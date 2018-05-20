---
title: 証明書共通名を使用する Azure Service Fabric クラスターを作成する | Microsoft Docs
description: テンプレートから、証明書共通名を使用する Service Fabric クラスターを作成する方法を説明します。
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2018
ms.author: ryanwi;aljo
ms.openlocfilehash: 84d9ae29840841d930fa70dc30dcda800a1b46c6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>拇印ではなく証明書共通名を使用する Service Fabric クラスターをデプロイする
2 つの証明書が同じ拇印を持つことはできず、そのことがクラスター証明書のロール オーバーや管理を困難にしています。 ただし、複数の証明書で同じ共通名や件名を持つことはできます。  証明書共通名を使用するクラスターにより、証明書の管理が大幅に単純化されます。 この記事では、Service Fabric クラスターを、証明書の拇印ではなく証明書共通名を使用するようにデプロイする方法について説明します。
 
## <a name="get-a-certificate"></a>証明書を取得する
最初に、[証明機関 (CA)](https://wikipedia.org/wiki/Certificate_authority) から証明書を取得します。  証明書共通名は、クラスターのホスト名にする必要があります。  たとえば、"myclustername.southcentralus.cloudapp.azure.com" とします。  

テスト目的の場合は、無料またはオープンな証明機関から CA の署名証明書を取得できます。

> [!NOTE]
> Azure Portal で、Service Fabric クラスターをデプロイするときに生成されるものを含め、自己署名証明書はサポートされません。

## <a name="upload-the-certificate-to-a-key-vault"></a>キー コンテナーに証明書をアップロードする
Azure 内で、Service Fabric クラスターは仮想マシン スケール セットにデプロイされます。  キー コンテナーに証明書をアップロードします。  クラスターがデプロイされると、クラスターが実行されている仮想マシン スケール セットに証明書がインストールされます。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzureRmResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzureKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>サンプル テンプレートをダウンロードして更新する
この記事では、[セキュリティで保護された 5 ノード クラスターの例](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure)にあるテンプレートとテンプレート パラメーターを使用します。 *azuredeploy.json* ファイルと *azuredeploy.parameters.json* ファイルをコンピューターにダウンロードします。

### <a name="update-parameters-file"></a>パラメーター ファイルを更新する
最初に、テキスト エディターで *azuredeploy.parameters.json* ファイルを開き、次のパラメーター値を追加します。
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

次に、*certificateCommonName*、*sourceVaultValue*、および *certificateUrlValue* のパラメーター値を、前のスクリプトから返された値に設定します。
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>テンプレート ファイルを更新する
次に、テキスト エディターで *azuredeploy.json* ファイルを開き、証明書共通名をサポートするために 3 つの更新を加えます。

1. **parameters** セクションに *certificateCommonName* パラメーターを追加します。
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    ```

    また、*certificateThumbprint* を削除することを検討します。これはもう不要の可能性があります。

2. *sfrpApiVersion* 変数の値を "2018-02-01" に設定します。
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. **Microsoft.Compute/virtualMachineScaleSets** リソースで、拇印ではなく証明書の設定の共通名を使用するように仮想マシン拡張機能を更新します。  **virtualMachineProfile**->**extenstionProfile**->**extensions**->**properties**->**settings**->**certificate** で、`"commonNames": ["[parameters('certificateCommonName')]"],` を追加し、`"thumbprint": "[parameters('certificateThumbprint')]",` を削除します。
    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": ["[parameters('certificateCommonName')]"],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4.  **Microsoft.ServiceFabric/clusters** リソースで、API バージョンを "2018-02-01" に更新します。  次の例のように、**certificateCommonNames** の設定 (**commonNames** プロパティを含む) の追加と、**certificate** の設定 (thumbprint プロパティを含む) の削除も行います。
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
        "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
        "addonFeatures": [
            "DnsService",
            "RepairManager"
        ],        
        "certificateCommonNames": {
            "commonNames": [
            {
                "certificateCommonName": "[parameters('certificateCommonName')]",
                "certificateIssuerThumbprint": ""
            }
            ],
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
        ...
    ```

## <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
変更を加えた後、更新したテンプレートを再度デプロイします。

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzureRmAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>次の手順
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [クラスター証明書のロールオーバー](service-fabric-cluster-rollover-cert-cn.md)について学習します
* [クラスター証明書の更新と管理を行います](service-fabric-cluster-security-update-certs-azure.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png