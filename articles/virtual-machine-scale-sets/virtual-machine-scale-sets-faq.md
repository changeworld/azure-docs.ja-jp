---
title: "Azure 仮想マシン スケール セットの FAQ | Microsoft Docs"
description: "仮想マシン スケール セットについてよく寄せられる質問の回答を示します。"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/08/2017
ms.author: negat
ms.custom: na
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 8d0adfb995ecfb56ff33814e462a88f3d1102d58
ms.lasthandoff: 03/15/2017


---

# <a name="azure-virtual-machine-scale-sets-faq"></a>Azure 仮想マシン スケール セットの FAQ

この記事では、スケール セットについてよく寄せられる質問の回答を示します。

## <a name="autoscale"></a>Autoscale

### <a name="what-are-best-practices-for-azure-autoscale"></a>Azure の自動スケールのベスト プラクティスはありますか?

はい。 https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-best-practices を参照してください。

### <a name="where-do-i-find-the-metric-names-for-autoscaling-using-host-based-metrics"></a>ホスト ベースのメトリックを使用した自動スケールのメトリック名はどこで確認できますか?

https://azure.microsoft.com/documentation/articles/monitoring-supported-metrics/ を参照してください。

### <a name="are-there-any-examples-of-autoscaling-based-on-a-service-bus-topic-and-queue-length"></a>Service Bus のトピックとキューの長さに基づいた自動スケールの例はありますか?

はい。 参照:

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/

Service Bus キューの場合:

```json
"metricName": "MessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

ストレージ キューの場合:

```json
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

これらのサンプル値を適切なリソース URI に置き換えてください。


### <a name="should-we-autoscale-with-host-based-metrics-or-use-a-diagnostics-extension"></a>ホスト ベースのメトリックで自動スケールするか、診断拡張機能を使用する必要はありますか?

ホスト レベルのメトリックまたはゲスト OS ベースのメトリックを使用する、VM の自動スケール設定を作成できます。

サポートされているメトリックの一覧については、https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-autoscale-common-metrics を参照してください。 スケール セットの完全なサンプルについては、次のページを参照してください (この例では、ホスト レベルの CPU メトリックとメッセージ数メトリックを使用しています)。

https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-advanced-autoscale-virtual-machine-scale-sets

### <a name="how-can-i-set-alert-rules-on-a-scale-set"></a>スケール セットのアラート ルールを設定するにはどうすればよいですか?

PS または CLI を使用して、スケール セットのメトリックのアラートを作成できます。 参照:

https://azure.microsoft.com/documentation/articles/insights-powershell-samples/#create-alert-rules

https://azure.microsoft.com/documentation/articles/insights-cli-samples/#work-with-alerts

スケール セットの TargetResourceId は、/subscriptions/yoursubscriptionid/resourceGroups/yourresourcegroup/providers/Microsoft.Compute/virtualMachineScaleSets/yourvmssname のようになります。

アラートを作成するメトリックとして、VM のパフォーマンス カウンターを選択できます。

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-windows-vm-v2-as-a-guest-os

https://azure.microsoft.com/documentation/articles/insights-autoscale-common-metrics/#compute-metrics-for-linux-vm-v2-as-a-guest-os

### <a name="how-can-i-set-up-autoscale-on-a-scale-set-using-powershell"></a>PowerShell を使用してスケール セットの自動スケールを設定するにはどうすればよいですか?

https://msftstack.wordpress.com/2017/03/05/how-to-add-autoscale-to-an-azure-vm-scale-set/ を参照してください。




## <a name="certificates"></a>証明書

### <a name="how-do-you-securely-ship-a-certificate-into-the-vm--is-there-an-example-of-provisioning-a-scale-set-to-run-a-website-where-the-ssl-for-the-website-is-shipped-securely-from-a-certificate-configuration--the-common-certificate-rotation-operation-would-amount-to-a-configuration-update-operation"></a>証明書を VM に安全に配布するにはどうすればよいですか?   Web サイトの SSL が証明書の構成から安全に発行される Web サイトを実行するためのスケール セットのプロビジョニングの例はありますか?  一般的な証明書交換操作は構成の更新操作になります。

Key Vault から Windows 証明書ストアへの顧客の証明書の直接インストールがサポートされています。

スケール セットのコンテキストで、次のページを参照してください。

https://msdn.microsoft.com/library/mt589035.aspx

```json
        "secrets": [ {
              "sourceVault": {
                      "id": "/subscriptions/{subscriptionid}/resourceGroups/myrg1/providers/Microsoft.KeyVault/vaults/mykeyvault1"
          },
          "vaultCertificates": [ {
                      "certificateUrl": "https://mykeyvault1.vault.azure.net/secrets/{secretname}/{secret-version}",
                  "certificateStore": "certificateStoreName"
          } ]
        } ]
```

Windows と Linux の両方がサポートされています。

### <a name="self-signed-certificate-example"></a>自己署名証明書の例:

#### <a name="create-a-self-signed-cert-in-a-keyvault"></a>KeyVault に自己署名証明書を作成する

KeyVault に自己署名証明書を作成する方法の&1; つとして、https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/ の Service Fabric に関する記事の手順に従います。

PowerShell コマンドは次のとおりです。

```powershell
Import-Module "C:\Users\mikhegn\Downloads\Service-Fabric-master\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"

Login-AzureRmAccount

Invoke-AddCertToKeyVault -SubscriptionId <Your SubID> -ResourceGroupName KeyVault -Location westus -VaultName MikhegnVault -CertificateName VMSSCert -Password VmssCert -CreateSelfSignedCertificate -DnsName vmss.mikhegn.azure.com -OutputPath c:\users\mikhegn\desktop\
```

上記のコマンドにより、Resource Manager テンプレートの入力が提供されます。

#### <a name="change-resource-manager-template"></a>Resource Manager テンプレートを変更する

スケール セットの Resource の一部として、"virtualMachineProfile" に次のプロパティを追加します。

```json 
"osProfile": {
            "computerNamePrefix": "[variables('namingInfix')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]",
            "secrets": [
              {
                "sourceVault": {
                  "id": "[resourceId('KeyVault', 'Microsoft.KeyVault/vaults', 'MikhegnVault')]"
                },
                "vaultCertificates": [
                  {
                    "certificateUrl": "https://mikhegnvault.vault.azure.net:443/secrets/VMSSCert/20709ca8faee4abb84bc6f4611b088a4",
                    "certificateStore": "My"
                  }
                ]
              }
            ]
          }
```
 

### <a name="is-there-a-way-to-specify-an-ssh-key-pair-that-i-want-to-use-for-ssh-authentication-with-a-linux-scale-set-from-a-resource-manager-template"></a>Resource Manager テンプレートから、Linux スケール セットで SSH 認証に使用する SSH キー ペアを指定する方法はありますか?  

osProfile の REST API は、通常の VM の場合と同様です。
 
https://msdn.microsoft.com/library/azure/mt589035.aspx#linuxconfiguration
 
次の例のように、`osProfile` をテンプレートに含めます。

```json 
"osProfile": {
          "computerName": "[variables('vmName')]",
          "adminUsername": "[parameters('adminUserName')]",
          "linuxConfiguration": {
            "disablePasswordAuthentication": "true",
            "ssh": {
              "publicKeys": [
                {
                  "path": "[variables('sshKeyPath')]",
                  "keyData": "[parameters('sshKeyData')]"
                }
              ]
            }
          }
        }
```
 
この JSON ブロックは、次のクイックスタート テンプレートで使用されています。
 
https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
次のテンプレートの OS プロファイルも参照してください。
 
https://github.com/ExchMaster/gadgetron/blob/master/Gadgetron/Templates/grelayhost.json

### <a name="how-do-i-remove-deprecated-certificates"></a>使用されていない証明書を削除するにはどうすればよいですか? 

古い証明書は、コンテナー証明書の一覧から削除する必要があります。ただし、保持する必要がある証明書はすべてマシン上に残しておきます。 そうすることで、特定の証明書がすべての VM から削除されることはなくなりますが、スケール セットに作成された新しい VM にその証明書が追加されることもありません。 既存の VM から証明書を削除するには、証明書ストアから証明書を手動で削除するカスタム スクリプト拡張機能を作成する必要があります。
 
### <a name="how-do-i-take-an-existing-ssh-public-key-and-inject-it-into-the-scale-set-ssh-layer-during-provisioning--i-would-like-to-store-the-ssh-public-key-values-in-azure-key-vault-and-then-utilize-them-in-my-resource-manager-template"></a>プロビジョニング時に、既存の SSH 公開キーを取得し、スケール セットの SSH レイヤーに挿入するにはどうすればよいですか?  SSH 公開キー値を Azure Key Vault に格納し、Resource Manager テンプレートで使用したいと思っています。

SSH 公開キーを VM に提供するだけであれば、公開キーはシークレットではないため、Key Vault に配置する必要はありません。
 
SSH 公開キーは、Linux VM の作成時にプレーン テキストで提供できます。
例については、次のページを参照してください。

https://github.com/Azure/azure-quickstart-templates/blob/master/101-vm-sshkey/azuredeploy.json
 
具体的には次の処理が行われます。

```json
"linuxConfiguration": {  
          "ssh": {  
            "publicKeys": [ {  
              "path": "path",
              "keyData": "publickey"
            } ]
          }
```
 
linuxConfiguration の要素名 | 必須 | 型 | Description
--- | --- | --- | --- |  ---
ssh | いいえ | コレクション | Linux OS の SSH キーの構成を指定します。
path | はい | String | SSH キーまたは証明書を配置する Linux ファイル パスを指定します。
keyData | はい | String | Base64 でエンコードされた SSH 公開キーを指定します。
 
### <a name="when-i-run-update-azurermvmss-after-more-than-one-certificate-from-the-same-keyvault-i-get-the-following-error"></a>同じ KeyVault から複数の証明書を取得した後に Update-AzureRmVmss を実行すると、次のエラーが表示されます。
 
"Update-AzureRmVmss: リスト secrets の中でインスタンス /subscriptions/<my-subscription-id>/resourceGroups/internal-rg-dev/providers/Microsoft.KeyVault/vaults/internal-keyvault-dev が繰り返されていますが、これは許可されていません"。 同じ KeyVault から&2; つの証明書を追加できないのはなぜですか?
 
この動作は、既存の sourceVault の新しい vaultCertificate ではなく、同じコンテナーを&2; 回追加しようとしている場合に発生する可能性があります。 シークレットをさらに追加する場合、Add-AzureRmVmssSecret は正しく動作しません。
 
同じ Key Vault からシークレットをさらに追加する場合は、$vmss.properties.osProfile.secrets[0].vaultCertificates リストを更新する必要があります。
 
必要な入力構造は、https://msdn.microsoft.com/library/azure/mt589035.aspx で確認できます。
 
同じものを含む Key Vault を使用するスケール セット オブジェクトでシークレットを見つける必要があります。 その後、そのコンテナーに関連付けられているリストに証明書参照 (URL とシークレット ストア名) を追加する必要があります。

注: スケール セットの API を使用した VM からの証明書の削除は現在サポートされていません。
 
新しい VM には古い証明書はなくなりますが、古い証明書が既にデプロイされていた VM にはその証明書が引き続き存在します。
 
### <a name="is-there-a-way-to-get-certificates-pushed-to-the-scale-set-without-providing-the-password-when-the-certificate-is-in-secretstore-currently"></a>現在、証明書が SecretStore にあるときに、パスワードを指定せずに証明書をスケール セットにプッシュする方法はありますか?

パスワードをスクリプトにハードコードする必要はありません。デプロイ スクリプトを実行するためのアクセス許可を使用してパスワードを動的に取得できます。 証明書をシークレット ストアから Key Vault に移動するスクリプトがある場合、シークレット ストアの証明書取得コマンドによって、pfx ファイルのパスワードも出力されます。
 
### <a name="how-does-the-secrets-property-of-virtualmachineprofileosprofile-of-a-scale-set-work-why-do-you-need-sourcevault-when-you-have-to-specify-the-absolute-uri-to-a-certificate-with-certificateurl"></a>スケール セットの virtualMachineProfile.osProfile の secrets プロパティはどのように機能しますか?  certificateUrl で証明書の絶対 URI を指定する必要があるときに sourceVault が必要なのはなぜですか? 

OS プロファイルの secrets プロパティに Win RM 証明書参照が存在する必要があります。 

ソース コンテナーを示す目的は、CSM 内に存在する ACL ポリシーを適用できるようにすることです。 ソース コンテナーを指定しないと、Key Vault へのシークレットのデプロイやシークレットへのアクセスに必要なアクセス許可のないユーザーが、CRP を使用してこれらを実行できるようになります。 存在しないリソースにも ACL は存在します。

Key Vault の有効な URL を指定しても、指定した sourceVault ID が正しくない場合、操作をポーリングしたときにエラーが報告されます。
 
### <a name="if-i-add-secrets-to-an-existing-scale-set-does-it-inject-them-in-existing-instances-or-only-new-ones"></a>シークレットを既存のスケール セットに追加した場合、シークレットは既存のインスタンスに挿入されるのですか? それとも新しいインスタンスにのみ挿入されるのですか? 

証明書は、既存の VM も含めたすべての VM に追加されます。 スケール セットの upgradePolicy プロパティが "Manual" に設定されている場合は、VM で手動更新を実行したときに証明書が VM に追加されます。
 
### <a name="where-do-certificates-go-for-linux-vms"></a>Linux VM の証明書はどこに配置されますか?

https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/ を参照してください。
  
### <a name="how-do-you-add-a-new-vault-certificate-to-a-new-certificate-object"></a>新しいコンテナー証明書を新しい証明書オブジェクトに追加するにはどうすればよいですか?

唯一のシークレット オブジェクトである既存のシークレットにコンテナー証明書を追加する場合は、次の PowerShell の例に従います。
 
```powershell
$newVaultCertificate = New-AzureRmVmssVaultCertificateConfig -CertificateStore MY -CertificateUrl https://sansunallapps1.vault.azure.net:443/secrets/dg-private-enc/55fa0332edc44a84ad655298905f1809
 
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($newVaultCertificate)
 
Update-AzureRmVmss -VirtualMachineScaleSet $vmss -ResourceGroup $rg -Name $vmssName
```
 
### <a name="what-happens-to-certificates-if-you-reimage-a-vm"></a>VM を再イメージ化した場合、証明書はどうなりますか?

VM を再イメージ化すると、再イメージ化によって OS ディスク全体が削除されるため、証明書はなくなります。 
 
### <a name="what-happens-if-you-delete-a-certificate-from-the-key-vault"></a>Key Vault から証明書を削除するとどうなりますか?

Key Vault でシークレットを削除し、すべての VM を停止 - 割り当て解除した後に、VM を再度起動すると、エラーが発生します。 CRP は Key Vault からシークレットを取得する必要がありますが、それができないためにこのエラーが発生します。 この場合、スケール セット モデルから証明書を削除できます。 

CRP コンポーネントでは、顧客のシークレットは保持されません。 スケール セットのすべての VM を停止 - 割り当て解除すると、キャッシュが削除されます。 この場合、シークレットは Key Vault から取得されます。

(少なくとも単一ファブリック テナント モデルでは) ファブリックにシークレットのキャッシュされたコピーが存在するため、スケールアウトでこの問題が発生することはありません。
 
### <a name="why-do-we-have-to-specify-the-exact-location-for-the-certificate-url-as-referenced-here-per-httpsazuremicrosoftcomdocumentationarticlesservice-fabric-cluster-security"></a>https://azure.microsoft.com/documentation/articles/service-fabric-cluster-security/ に記載されているように、証明書 URL に正確な場所を指定する必要があるのはなぜですか? 
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
 
KeyVault のドキュメントに記載されているように、get-secret REST API は、バージョンが指定されていない場合にシークレットの最新バージョンを返します。
 
メソッド | URL
--- | ---
GET | https://mykeyvault.vault.azure.net/secrets/{secret-name}/{secret-version}?api-version={api-version}

{secret-name} を取得するシークレットの名前に置き換え、{secret-version} をそのシークレットのバージョンに置き換えます。 最新バージョンを取得する場合、シークレットのバージョンは除外できます。
  
### <a name="why-does-certificate-version-have-to-be-specified-when-using-key-vault"></a>Key Vault を使用するときに、証明書のバージョンを指定する必要があるのはなぜですか?

これは、VM にデプロイされた証明書をユーザーに明確に示すためです。

VM を作成し、Key Vault のシークレットを更新しても、その新しい証明書は既存の VM にダウンロードされません。 しかし、既存の VM はその新しい証明書を参照しているように見えます。また、新しい VM は新しいシークレットを取得します。 この混乱を避けるために、シークレットの明示的なバージョンを参照する必要があります。

### <a name="my-team-works-with-several-certificates-that-are-distributed-to-us-as-cer-public-keys-what-is-the-recommended-approach-is-for-deployment-of-these-certs-to-a-scale-set"></a>.cer 公開キーとして配布された複数の証明書をチームで使用しています。 これらの証明書をスケール セットにデプロイする場合の推奨される方法を教えてください。

X509ContentType = Pfx を指定することで、.cer ファイルだけを含む pfx ファイルを生成できます。 たとえば、C# または PowerShell で .cer ファイルを x509Certificate2 オブジェクトとして読み込み、https://msdn.microsoft.com/library/24ww6yzk(v=vs.110).aspx に示すメソッドを呼び出します。

### <a name="i-do-not-see-an-option-for-users-to-pass-in-certificates-as-base64-strings-that-most-other-resource-providers-provide"></a>ユーザーが、他のほとんどのリソース プロバイダーで提供される Base64 文字列として証明書を渡す方法がわかりません。

Resource Manager テンプレート内で、バージョン管理された最新の URL を抽出して、ご質問の動作をエミュレートすることができます。 Resource Manager テンプレートに次の JSON プロパティを含めることができます。

```json 
"certificateUrl": "[reference(resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults/secrets', parameters('vaultName'), parameters('secretName')), '2015-06-01').secretUriWithVersion]"
```
 
### <a name="do-we-have-to-wrap-certs-in-json-objects-in-keyvaults"></a>Key Vault で JSON オブジェクトに証明書をラップする必要はありますか?

これはスケール セット/VM の要件です。 コンテンツ タイプとして application/x-pkcs12 もサポートされています。 手順については、Instructions found here: http://www.rahulpnath.com/blog/pfx-certificate-in-azure-key-vault/ を参照してください。
 
.cer ファイルは現在サポートされていません。.cer ファイルを pfx コンテナーにエクスポートする必要があります。





## <a name="compliance"></a>コンプライアンス

### <a name="are-scale-sets-pci-compliant"></a>スケール セットは PCI 準拠ですか?

スケール セットは、Compute リソース プロバイダーの上のシン API レイヤーであり、すべて Azure サービス ツリー内の "コンピューティング プラットフォーム" 領域の一部です。

したがって、コンプライアンスの観点から言うと、スケール セットは Azure コンピューティング プラットフォームの基礎となる部分です。 そのため、スケール セットは、Compute リソース プロバイダー (CRP) 自体と同じチーム、ツール、プロセス、デプロイ方法、セキュリティ制御、JIT、監視、アラートなどを共有します。  Compute リソース プロバイダーは現在の PCI DSS 構成証明の一部であるため、スケール セットは PCI 準拠です。

詳細については、[https://www.microsoft.com/TrustCenter/Compliance/PCI](https://www.microsoft.com/TrustCenter/Compliance/PCI) を参照してください。






## <a name="extensions"></a>拡張機能

### <a name="how-do-you-delete-a-scale-set-extension"></a>スケール セット拡張機能を削除するにはどうすればよいですか?

PowerShell を使用した例を次に示します。

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" 

$vmss=Remove-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "extensionName"

Update-AzureRmVmss -ResourceGroupName "resource_group_name" -VMScaleSetName "vmssName" -VirtualMacineScaleSet $vmss
```
 
extensionName は `$vmss` で確認できます。
   
### <a name="is-there-a-scale-set-template-example-that-integrates-with-oms"></a>OMS と統合するスケール セット テンプレートの例はありますか?

次のページの&2; 番目の例をご覧ください。

https://github.com/krnese/AzureDeploy/tree/master/OMS/MSOMS/ServiceFabric
   
### <a name="extensions-seem-to-run-in-parallel-on-scale-sets-causing-my-custom-script-extension-to-fail-what-can-i-do-to-fix-this-behavior"></a>拡張機能がスケール セットで並列実行されることが原因で、カスタム スクリプト拡張機能が失敗するようです。 この動作を修正するにはどうすればよいですか?

https://msftstack.wordpress.com/2016/05/12/extension-sequencing-in-azure-vm-scale-sets/ を参照してください。 
 
 
### <a name="how-do-i-reset-the-password-for-scale-set-vms"></a>スケール セット VM のパスワードをリセットするにはどうすればよいですか?

VM アクセス拡張機能を使用します。

PowerShell を使用した例を次に示します。

```powershell
$vmssName = "myvmss"
$vmssResourceGroup = "myvmssrg"
$publicConfig = @{"UserName" = "newuser"}
$privateConfig = @{"Password" = "********"}
 
$extName = "VMAccessAgent"
$publisher = "Microsoft.Compute"
$vmss = Get-AzureRmVmss -ResourceGroupName $vmssResourceGroup -VMScaleSetName $vmssName
$vmss = Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name $extName -Publisher $publisher -Setting $publicConfig -ProtectedSetting $privateConfig -Type $extName -TypeHandlerVersion "2.0" -AutoUpgradeMinorVersion $true
Update-AzureRmVmss -ResourceGroupName $vmssResourceGroup -Name $vmssName VirtualMachineScaleSet $vmss
```
 
 
### <a name="how-do-i-add-an-extension-to-all-vms-in-my-scale-set"></a>スケール セットのすべての VM に拡張機能を追加するにはどうすればよいですか?

- 更新ポリシーが自動に設定されている場合は、拡張機能の新しいプロパティが含まれたテンプレートを再デプロイすると、すべての VM が更新されます。
- 更新ポリシーが手動に設定されている場合は、拡張機能を更新し、すべてのインスタンスで manualUpdate を実行する必要があります。
  
### <a name="if-the-extensions-associated-with-an-existing-scale-set-are-updated-would-they-affect-already-existing-vms-that-is-would-the-vms-show-up-as-not-matching-the-scale-set-model-or-would-they-be-ignored-when-an-existing-machine-is-service-healed--reimaged--etc-would-the-scripts-that-are-currently-configured-on-the-scale-set-be-executed-or-would-the-ones-that-were-configured-when-the-machine-was-first-created-be-used"></a>既存のスケール セットに関連付けられている拡張機能を更新した場合、既存の VM に影響を及ぼしますか  (つまり、スケール セット モデルと一致しないものとして VM が表示されるのでしょうか)?  それとも VM は無視されますか?  既存のマシンでサービス復旧や再イメージ化などを実行する場合、スケール セットで現在構成されているスクリプトと、そのマシンを最初に作成したときに構成されたスクリプトのどちらが実行されるのでしょうか?

- スケール セット モデル内の拡張機能の定義を更新した場合、upgradePolicy が自動に設定されていれば VM が更新されます。upgradePolicy が手動に設定されている場合は、モデルと一致しないことを示すフラグが設定されます。 

- 既存の VM でサービス復旧を実行した場合、これは VM の再起動と同様であり、拡張機能は再実行されません。 既存の VM を再イメージ化した場合、これは OS ドライブを最新モデルのソース イメージと特性に置き換えた場合と同様であり、拡張機能が実行されます。
 
### <a name="how-do-i-get-a-scale-set-to-join-an-ad-domain"></a>スケール セットを AD ドメインに参加させるにはどうすればよいですか?

たとえば、JsonADDomainExtension を使用して、次のように拡張機能を定義します。
```json
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "joindomain",
                                "properties": {
                                    "publisher": "Microsoft.Compute",
                                    "type": "JsonADDomainExtension",
                                    "typeHandlerVersion": "1.0",
                                    "settings": {
                                        "Name": "[parameters('domainName')]",
                                        "OUPath": "[variables('ouPath')]",
                                        "User": "[variables('domainAndUsername')]",
                                        "Restart": "true",
                                        "Options": "[variables('domainJoinOptions')]"
                                    },
                                    "protectedsettings": {
                                        "Password": "[parameters('domainJoinPassword')]"
                                    }
                                }
                            }
                        ]
                    }
```
 
### <a name="my-scale-set-extension-is-trying-to-install-something-that-requires-a-reboot-for-instance-commandtoexecute-powershellexe--executionpolicy-unrestricted-install-windowsfeature-name-fs-resource-manager-includemanagementtools"></a>スケール セット拡張機能で、再起動を必要とするもの ("commandToExecute"、"powershell.exe -ExecutionPolicy Unrestricted Install-WindowsFeature –Name FS-Resource-Manager –IncludeManagementTools" など) をインストールしようとしています。

DSC 拡張機能を使用できます。 OS が 2012 R2 の場合、Azure は WMF5.0 セットアップをプルし、再起動して構成を続行します。 
 
### <a name="how-can-i-enable-antimalware-on-my-scale-set"></a>スケール セットでマルウェア対策を有効にするにはどうすればよいですか?

PowerShell の例を次に示します。

```powershell
$rgname = 'autolap'
$vmssname = 'autolapbr'
$location = 'eastus'
 
# retrieve the most recent version number of the extension
$allVersions= (Get-AzureRmVMExtensionImage -Location $location -PublisherName "Microsoft.Azure.Security" -Type "IaaSAntimalware").Version
$versionString = $allVersions[($allVersions.count)-1].Split(".")[0] + "." + $allVersions[($allVersions.count)-1].Split(".")[1]
 
$VMSS = Get-AzureRmVmss -ResourceGroupName $rgname -VMScaleSetName $vmssname
echo $VMSS
Add-AzureRmVmssExtension -VirtualMachineScaleSet $VMSS -Name "IaaSAntimalware" -Publisher "Microsoft.Azure.Security" -Type "IaaSAntimalware" -TypeHandlerVersion $versionString
Update-AzureRmVmss -ResourceGroupName $rgname -Name $vmssname -VirtualMachineScaleSet $VMSS 
```

### <a name="i-need-to-execute-a-custom-script-hosted-on-a-private-storage-account-i-have-no-problems-when-the-storage-is-public-but-when-i-try-to-use-a-shared-access-signaturesas-it-fails-with-the-error-missing-mandatory-parameters-for-valid-shared-access-signature-i-know-that-linksas-works-fine-from-my-local-browser"></a>プライベート ストレージ アカウントでホストされているカスタム スクリプトを実行する必要があります。 ストレージがパブリックのときは問題ありませんが、Shared Access Signature(SAS) を使用しようとすると、"有効な Shared Access Signature で必須のパラメーターがありません" というエラーで失敗します。 ローカル ブラウザーから link+SAS が正常に動作していることはわかっています。

このシナリオを実現するには、ストレージ アカウント キーとストレージ アカウント名を使用して保護された設定を構成する必要があります。 https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/#template-example-for-a-windows-vm-with-protected-settings を参照してください。







## <a name="networking"></a>ネットワーク
 
### <a name="how-do-i-do-vip-swap-for-scale-sets-in-the-same-subscription-and-same-region"></a>同じサブスクリプションで同じリージョンのスケール セットの VIP スワップを実行するにはどうすればよいですか?

https://msftstack.wordpress.com/2017/02/24/vip-swap-blue-green-deployment-in-azure-resource-manager/ を参照してください。 
 
  
### <a name="what-is-the-resourceguid-property-on-a-nic-for-its-not-documented-here"></a>NIC の resourceGuid プロパティは何のためのものですか? ここには記載されていません。

これは一意の ID です。 将来的に、下位レイヤーでこの ID をログに記録する予定です。 
 
### <a name="how-do-i-specify-a-range-of-private-ip-addresses-for-static-private-ip-address-allocation"></a>静的プライベート IP アドレスの割り当てで、プライベート IP アドレスの範囲を指定するにはどうすればよいですか?

IP は指定したサブネットから選択されます。 

スケール セットの IP の割り当て方法は常に "動的" です。 ただし、これは、これらの IP が変わる可能性があるという意味ではありません。 PUT 要求で IP を指定しないということです。 言い換えると、サブネットを使用して静的なセットを指定します。 
    
### <a name="how-do-i-deploy-a-scale-set-into-an-existing-vnet"></a>スケール セットを既存の VNET にデプロイするにはどうすればよいですか? 

https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-existing-vnet を参照してください。 




## <a name="scale"></a>スケール

### <a name="why-would-you-ever-create-a-scale-set-with-fewer-than-2-vms"></a>これまで、2 台未満の VM でスケール セットを作成していたのはなぜですか?

理由の&1; つは、スケール セットのエラスティック プロパティを使用するためです。 たとえば、VM のランニング コストを支払わずにインフラストラクチャを定義するために、VM が含まれていないスケール セットをデプロイできます。 その後、VM をデプロイする準備ができたら、実稼働インスタンス数に合わせてスケール セットの "容量" を増やして VM をデプロイします。

もう&1; つの理由は、個々の VM で可用性セットを使用する場合と同様に、可用性を気にかけずにスケール セットを使用するためです。 スケール セットにより、代替可能な区別されていないコンピューティング ユニットを使用できるようになります。 この均一性は、スケール セットと可用性セットの主な差別化要素の&1; つです。 多くのステートレス ワークロードでは、個々のユニットは重視されないので、ワークロードが減少したら&1; つのコンピューティング ユニットにスケールダウンし、ワークロードが増加したときに多数のユニットに戻すことができます。

### <a name="how-do-you-change-the-number-of-vms-in-a-scale-set"></a>スケール セットの VM の数を変更するにはどうすればよいですか?

https://msftstack.wordpress.com/2016/05/13/change-the-instance-count-of-an-azure-vm-scale-set/ を参照してください。

### <a name="how-can-you-define-custom-alerts-for-when-certain-thresholds-are-reached"></a>特定のしきい値に達したときのカスタム アラートを定義するにはどうすればよいですか?

アラートを処理する方法にはある程度の柔軟性があります。たとえば、Resource Manager テンプレートの次の例のように、カスタマイズされた webhook を定義できます。
```json
   {
         "type": "Microsoft.Insights/autoscaleSettings",
           "apiVersion": "[variables('insightsApi')]",
                 "name": "autoscale",
                   "location": "[parameters('resourceLocation')]",
                     "dependsOn": [
                         "[concat('Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]"
                 ],
                 "properties": {
                         "name": "autoscale",
                     "targetResourceUri": "[concat('/subscriptions/',subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.Compute/virtualMachineScaleSets/', parameters('vmSSName'))]",
                     "enabled": true,
                     "notifications": [{
                               "operation": "Scale",
                              "email": {
                                     "sendToSubscriptionAdministrator": true,
                                     "sendToSubscriptionCoAdministrators": true,
                                     "customEmails": [
                                        "youremail@address.com"
                                     ]},
                              "webhooks": [{
                                    "serviceUri": "https://events.pagerduty.com/integration/0b75b57246814149b4d87fa6e1273687/enqueue",
                                    "properties": {
                                        "key1": "custommetric",
                                        "key2": "scalevmss"
                                    }
                                    }
                              ]}],
```

この例では、しきい値に達すると、アラートが PagerDuty に送信されます。



## <a name="troubleshooting"></a>トラブルシューティング

### <a name="how-do-i-enable-boot-diagnostics"></a>ブート診断を有効にするにはどうすればよいですか?

ストレージ アカウントを作成し、スケール セットの virtualMachineProfile に次の JSON ブロックを配置して、スケール セットを更新します。
```json
      "diagnosticsProfile": {
        "bootDiagnostics": {
          "enabled": true,
          "storageUri": "http://yourstorageaccount.blob.core.windows.net"
        }
      }
```

新しい VM を作成すると、VM の InstanceView にスクリーンショットなどの詳細が表示されます。次に例を示します。
 
```json
"bootDiagnostics": {
    "consoleScreenshotBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.screenshot.bmp",
    "serialConsoleLogBlobUri": "https://o0sz3nhtbmkg6geswarm5.blob.core.windows.net/bootdiagnostics-swarmagen-4157d838-8335-4f78-bf0e-b616a99bc8bd/swarm-agent-9574AE92vmss-0_2.4157d838-8335-4f78-bf0e-b616a99bc8bd.serialconsole.log"
  }
```

 

## <a name="updates"></a>更新プログラム

### <a name="how-to-i-update-my-scale-set-to-a-new-image-and-manage-patching"></a>スケール セットを新しいイメージに更新し、修正プログラムの適用を管理するにはどうすればよいですか?

https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set を参照してください。

### <a name="can-you-use-the-reimage-operation-to-reset-a-vm-without-changing-the-image-that-is-reset-a-vm-to-factory-settings-rather-than-to-a-new-image"></a>イメージを変更せずに、再イメージ化操作を使用して VM をリセットすることはできますか  (つまり、新しいイメージではなく、出荷時の設定に VM をリセットできますか)?

はい。 https://docs.microsoft.com/rest/api/virtualmachinescalesets/manage-all-vms-in-a-set を参照してください。

ただし、version = "latest" が指定されたプラットフォーム イメージをスケール セットが参照している場合、再イメージ化を呼び出すと、VM が新しい OS イメージに更新される可能性があります。







## <a name="vm-properties"></a>VM のプロパティ

### <a name="how-do-i-get-property-information-for-each-vm-without-having-to-make-multiple-calls-for-example-getting-the-fault-domain-for-each-vm-in-my-100-scale-set"></a>複数の呼び出しを必要とせずに、各 VM のプロパティ情報を取得するにはどうすればよいですか?  たとえば、100 台の VM のスケール セットの各 VM の障害ドメインを取得するにはどうすればよいですか?

次のリソース URI で REST API の `GET` を実行することで、ListVMInstanceViews を呼び出すことができます。

`/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Compute/virtualMachineScaleSets/<scaleset_name>/virtualMachines?$expand=instanceView&$select=instanceView`

### <a name="are-there-ways-to-pass-different-extension-arguments-to-different-vms-in-a-scale-set"></a>スケール セットの VM にそれぞれ異なる拡張機能の引数を渡す方法はありますか?

いいえ。ただし、拡張機能は、拡張機能が実行される VM の一意のプロパティ (マシン名など) に基づいて動作することができます。 さらに、拡張機能では http://169.254.169.254 でインスタンスのメタデータを照会して詳細情報を取得できます。

### <a name="why-are-there-gaps-between-my-scale-set-vm-machine-names-and-vm-ids-for-example-0-1-3"></a>スケール セット VM のマシン名や VM ID に欠落があるのはなぜですか  (0、1、3... など)?

欠落があるのは、スケール セットの過剰プロビジョニング プロパティが既定値の true に設定されているためです。 過剰プロビジョニングが true の場合、要求した数よりも多くの VM が作成され、その後、余分な VM が削除されます。 連続した名前付けと連続した NAT 規則が失われる代わりに、デプロイの信頼性が向上します。 このプロパティを false に設定することもできます。小規模のスケール セットの場合、false に設定してもデプロイの信頼性に大きな違いはありません。

### <a name="what-is-the-difference-between-deleting-a-vm-in-a-scale-set-vs-deallocating-the-vm-when-should-i-choose-one-over-the-other"></a>スケール セットの VM の削除と VM の割り当て解除の違いは何ですか?  どちらかを選択する必要があるのはどのような場合ですか?

主な違いは、割り当て解除では VHD は削除されないので、停止 - 割り当て解除に関連するストレージ コストが発生することです。 どちらかを選択するのは次のような場合です。

- コンピューティングの料金の支払いは停止し、VM のディスクの状態は保持したい場合。
- スケール セットのスケールアウトよりも高速で一連の VM を起動する場合。
  - このシナリオに関連して、独自の自動スケール エンジンを作成しており、エンド ツー エンドのスケーリングを高速化する場合。
  - (VM の選択的な削除や過剰プロビジョニング後の VM の削除により) スケール セットが FD/UD に均等に分散されていない場合。 停止 - 割り当て解除の後にスケール セットの使用を開始すると、FD/UD に VM が均等に分散されます。








 
   

