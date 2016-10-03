<properties
   pageTitle="Service Fabric クラスターで使用される証明書を Azure で追加、ロールオーバー、および削除する | Microsoft Azure"
   description="クラスターのセカンダリ証明書をアップロードした後、古いプライマリ証明書をロールオーバーする方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="chackdan"/>

# Azure Service Fabric クラスターの証明書の追加と削除

Service Fabric で X.509 証明書がどのように使用されるかを理解するために「[Service Fabric クラスターのセキュリティに関するシナリオ](service-fabric-cluster-security.md)」を読むことをお勧めします。先に進む前に、クラスター証明書とは何であり、何の目的で使用されるかを理解しておく必要があります。

Service Fabric では、クラスターの作成中に証明書セキュリティを構成するときに、2 つのクラスター証明書 (プライマリとセカンダリ) を指定できます。詳細については、「[Azure ポータルから Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)」または [Azure Resource Manager を使用した Azure クラスターの作成に関する記事](service-fabric-cluster-creation-via-Resource Manager.md) を参照してください。Resource Manager によるデプロイ中にクラスター証明書を 1 つだけ指定した場合は、それがプライマリ証明書として使用されます。クラスターの作成後に、新しい証明書をセカンダリ証明書として追加できます。

>[AZURE.NOTE] セキュリティ保護されたクラスターでは、常に (失効も期限切れもしていない) 有効な証明書 (プライマリまたはセカンダリ) を 1 つ以上デプロイする必要があります。デプロイしなかった場合、クラスターの機能が停止します。有効なすべての証明書が期限切れになる 90 日前に、システムは、警告トレースとノードの正常性に関する警告イベントを生成します。現時点では、この件に関して Service Fabric が電子メールやその他の通知を送信することはありません。


## ポータルを使用してセカンダリ証明書を追加する
セカンダリ証明書として別の証明書を追加するには、Azure Key Vault にその証明書をアップロードしてから、クラスター内の VM にデプロイします。詳細については、「[Deploy certificates to VMs from a customer-managed key vault](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)」 (ユーザーが管理する Key Vault から VM に証明書をデプロイする) を参照してください。

1. 方法については、「[Key Vault に X.509 証明書をアップロードする](service-fabric-secure-azure-cluster-with-certs.md#step-2-upload-the-x509-certificate-to-the-key-vault)」を参照してください。

2. [Azure ポータル](https://portal.azure.com/)にサインインし、この証明書を追加するクラスター リソースを参照します。
3. **[設定]** の **[セキュリティ]** をクリックして、[クラスター セキュリティ] ブレードを開きます。
4. ブレードの上部にある **[+証明書]** ボタンをクリックして、**[証明書の追加]** ブレードを開きます。
5. ドロップダウン リストから [セカンダリ証明書の拇印] を選択し、Key Vault にアップロードしたセカンダリ証明書の拇印を入力します。

>[AZURE.NOTE]
クラスター作成ワークフローとは異なり、ここでは Key Vault 情報の詳細は説明しません。その理由は、このブレードを開く時点までに、証明書の VM へのデプロイは実行済みであり、証明書は VMSS インスタンスのローカル証明書ストア内で既に利用可能になっているためです。

**[証明書]** をクリックします。デプロイが開始され、[クラスター セキュリティ] ブレードに青のステータス バーが表示されます。

![Azure ポータルの証明書拇印のスクリーン ショット][SecurityConfigurations_02]

デプロイが正常に完了したら、プライマリまたはセカンダリ証明書を使用して、クラスターの管理操作を実行できます。

![証明書デプロイ進行中のスクリーン ショット][SecurityConfigurations_03]

デプロイ完了後の [セキュリティ] ブレードのスクリーン ショットを次に示します。

![デプロイ後の証明書拇印のスクリーン ショット][SecurityConfigurations_08]


これで、追加したばかりの新しい証明書を使用して、クラスターの操作を実行できます。

>[AZURE.NOTE]
現時点では、ポータルでプライマリ証明書とセカンダリ証明書をスワップする方法はありません。その機能は準備中です。有効なクラスター証明書がある限り、クラスターは問題なく動作します。

## Resource Manager PowerShell を使用してセカンダリ証明書を追加し、プライマリ証明書になるようにスワップする

以下の手順は、Resource Manager の動作方法を理解していること、Resource Manager テンプレートを使用して少なくとも 1 つの Service Fabric クラスターをデプロイしていること、クラスターをセットアップするために使用したテンプレートが手元にあること、さらに JSON を使いこなせることを前提としています。

>[AZURE.NOTE]
操作を理解するため、または出発点として使用できるサンプル テンプレートとパラメーターを探している場合は、この [git-repo] からダウンロードできます (https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample)。

#### Resource Manager テンプレートを編集する 

[git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) のサンプルを使用して操作する場合は、5-VM-1-NodeTypes-Secure\_Step2.JSON で以下の変更を確認できます。セキュリティ保護されたクラスターをデプロイするには 5-VM-1-NodeTypes-Secure\_Step1.JSON を使用します。

1. クラスターをデプロイするために使用した Resource Manager テンプレートを開きます。
2. "string" 型の新しいパラメーター "secCertificateThumbprint" を追加します。作成時にポータルから、またはクイック スタート テンプレートからダウンロードした Resource Manager テンプレートを使用している場合は、そのパラメーターを検索してください。定義済みであることを確認できます。
3. "Microsoft.ServiceFabric/clusters" リソース定義を探します。プロパティで "Certificate" JSON タグが見つかります。これは次の JSON スニペットのようになります。
```JSON
      "properties": {
        "certificate": {
          "thumbprint": "[parameters('certificateThumbprint')]",
          "x509StoreName": "[parameters('certificateStoreValue')]"
        }
``` 

4. 新しいタグ "thumbprintSecondary" を追加し、値 "[parameters('secCertificateThumbprint')]" を指定します。

これで、リソース定義は次のようになります (テンプレートのソースによっては、下のスニペットと完全に同じではない場合があります)。ご覧のように、ここで実行しているのは、新しい証明書をプライマリとして指定し、現在のプライマリをセカンダリに移動していることです。この結果、現在の証明書が新しい証明書に 1 つのデプロイ手順でロールオーバーされます。

```JSON

      "properties": {
        "certificate": {
            "thumbprint": "[parameters('certificateThumbprint')]",
            "thumbprintSecondary": "[parameters('secCertificateThumbprint')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },

```

#### 追加した新しいパラメーターを反映するようにテンプレート ファイルを編集する

[git-repo](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Cert%20Rollover%20Sample) のサンプルを使用して操作する場合は、5-VM-1-NodeTypes-Secure.paramters\_Step2.JSON の変更から始めることができます。


Resource Manager テンプレート パラメーター ファイルを編集し、secCertificate の新しいパラメーターを追加し、既存のプライマリ証明書の詳細をセカンダリ証明書とスワップして、その詳細を新しい証明書の詳細に置き換えます。

```JSON
    "secCertificateThumbprint": {
      "value": "OLD Primary Certificate Thumbprint"
    },
   "secSourceVaultValue": {
      "value": "OLD Primary Certificate Key Vault location"
    },
    "secCertificateUrlValue": {
      "value": "OLD Primary Certificate location in the key vault"
     },
    "certificateThumbprint": {
      "value": "New Certificate Thumbprint"
    },
    "sourceVaultValue": {
      "value": "New Certificate Key Vault location"
    },
    "certificateUrlValue": {
      "value": "New Certificate location in the key vault"
     },

```

### テンプレートを Azure にデプロイする

1. これでテンプレートを Azure にデプロイする準備が整いました。Azure PS のバージョン 1 以降のコマンド プロンプトを開きます。
2. Azure アカウントにログインし、特定の Azure サブスクリプションを選択します。1 つ以上の Azure サブスクリプションにアクセスできるユーザーにとって、これは重要な手順です。


```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId <Subcription ID> 

```

テンプレートをデプロイする前にテストを実行します。クラスターの現在のデプロイ先であるリソース グループを使用します。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>

```

そのリソース グループに、テンプレートをデプロイします。クラスターの現在のデプロイ先であるリソース グループを使用します。New-AzureRmResourceGroupDeployment コマンドを実行します。既定値の **incremental** を使用するため、モードを指定する必要はありません。

>[AZURE.NOTE]
モードを [Complete (完了)] に設定すると、テンプレートにないリソースが誤って削除される可能性があります。このため、このシナリオでは使用しないでください。
   

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName <Resource Group that your cluster is currently deployed to> -TemplateFile <PathToTemplate>
```

同じ PowerShell コマンドの入力例は次のとおりです。

```powershell
$ResouceGroup2 = "chackosecure5"
$TemplateFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure_Step2.json"
$TemplateParmFile = "C:\GitHub\Service-Fabric\ARM Templates\Cert Rollover Sample\5-VM-1-NodeTypes-Secure.parameters_Step2.json"

New-AzureRmResourceGroupDeployment -ResourceGroupName $ResouceGroup2 -TemplateParameterFile $TemplateParmFile -TemplateUri $TemplateFile -clusterName $ResouceGroup2

```

デプロイが完了したら、新しい証明書を使用してクラスターに接続し、クエリをいくつか実行します。該当する場合は、古いプライマリ証明書を削除できます。

自己署名証明書を使用している場合は、ローカルの TrustedPeople 証明書ストアにインポートすることを忘れないでください。

```powershell
######## Set up the certs on your local box
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath c:\Mycertificates\chackdanTestCertificate9.pfx -Password (ConvertTo-SecureString -String abcd123 -AsPlainText -Force)

```
ここでのクイック リファレンスは、セキュリティ保護されたクラスターに接続するための次のコマンドです:
```powershell
$ClusterName= "chackosecure5.westus.cloudapp.azure.com:19000"
$CertThumbprint= "70EF5E22ADB649799DA3C8B6A6BF7SD1D630F8F3" 

Connect-serviceFabricCluster -ConnectionEndpoint $ClusterName -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $CertThumbprint  `
    -FindType FindByThumbprint `
    -FindValue $CertThumbprint `
    -StoreLocation CurrentUser `
    -StoreName My
```
ここでのクイック リファレンスは、クラスターの正常性を取得するための次のコマンドです:
```powershell
Get-ServiceFabricClusterHealth 
```
 
## ポータルを使用して古い証明書を削除する
古い証明書を削除してクラスターで使用されないようにするには、次の手順を実行します。

1. [Azure ポータル](https://portal.azure.com/)にサインインし、クラスターのセキュリティ設定に移動します。
2. 削除する証明書を右クリックします。
3. [削除] を選択し、プロンプトに従います。

[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png


## 次のステップ
クラスター管理の詳細については、次の記事を参照してください。

- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [クライアント用のロールベースのアクセスの設定](service-fabric-cluster-security-roles.md)


<!--Image references-->
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_02.png
[SecurityConfigurations_03]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_03.png
[SecurityConfigurations_05]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_05.png
[SecurityConfigurations_08]: ./media/service-fabric-cluster-security-update-certs-azure/SecurityConfigurations_08.png

<!---HONumber=AcomDC_0817_2016-->

