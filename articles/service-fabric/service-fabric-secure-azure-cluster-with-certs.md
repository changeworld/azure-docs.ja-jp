<properties
   pageTitle="証明書を使用し、Service Fabric クラスターをセキュリティで保護する | Microsoft Azure"
   description="X.509 証明書を使用して Service Fabric をセキュリティで保護する方法"
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
   ms.date="05/27/2016"
   ms.author="chackdan"/>

# 証明書を使用し、Azure で Service Fabric クラスターをセキュリティで保護する

Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。このリソースは、特に運用ワークロードが実行されている場合などに、許可なくアクセスされるのを防ぐためにセキュリティで保護する必要があります。X.509 証明書を使用してセキュリティで保護された Service Fabric クラスターを設定するには、X.509 証明書を 1 つ以上必要とします。この証明書を Azure Key Vault にアップロードし、クラスター作成プロセスで使用します。

この記事は、クラスター作成プロセスの「[Step 3: Configure security](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)」 (手順3: セキュリティの構成) に相当します。Service Fabric での X.509 証明書の使用に関する詳細は、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)を参照してください。

3 つの手順があります

1. X.509 証明書を取得します。
2. Azure Key Vault に証明書をアップロードします。
3. Service Fabric クラスターの作成プロセスで、証明書の場所と詳細を指定します。

<a id="acquirecerts"></a>
## 手順 1: X.509 証明書を取得する

運用ワークロードを実行するクラスターの場合、[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) で署名された X.509 証明書を使用してクラスターをセキュリティで保護する必要があります。これらの証明書を取得する方法の詳細については、「[方法 : 証明書 (WCF) を取得する](http://msdn.microsoft.com/library/aa702761.aspx)」を参照してください。

テスト目的でのみ使用するクラスターの場合は、自己署名証明書を選択することができます。その方法については下の手順 2.5. で説明します。

## 手順 2: Key Vault に X.509 証明書をアップロードする

この手順は複雑なため、弊社が Git リポジトリにアップロードした PowerShell モジュールを使用してください。

### 手順 2.1
Azure PowerShell 1.0 以降がコンピューターにインストールされていることを確認します。まだインストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」の手順を実行します。

### 手順 2.2
*ServiceFabricRPHelpers* フォルダーをこの [Git リポジトリ](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)からコンピューターにコピーします。

### 手順 2.3
PowerShell ウィンドウを開き、モジュールをダウンロードしたディレクトリに移動します。次のコマンドを使用し、モジュールをインポートします。

```powershell
Import-Module .\ServiceFabricRPHelpers.psm1
```

### 手順 2.4
既に取得した証明書を使用している場合、次の手順に従います。それ以外の場合は手順 2.5 に進んでください。自己署名証明書を作成して Key Vault にデプロイする方法について説明しています。

既存のリソース グループと Key Vault を利用し、証明書を格納できます。あるいは、リソース グループや Key Vault がない場合、新しく作成できます。このスクリプトを使用し、デプロイをサポートするように最初に既存の Key Vault を構成する必要があります。

```powershell
Login-AzureRmAccount

Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

リソース グループと Key Vault に証明書をアップロードするには、次のスクリプトを実行します。リソース グループや Key Vault がない場合、作成されます。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
実際の値を入力したスクリプトの例は次のようになります。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

スクリプトが正常に終了すると、次のような出力が表示されます。手順 3 でセキュリティで保護されたクラスターを設定する際にこの出力が必要になります。

```
Certificate Thumbprint: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

SourceVault /Resource ID of the key vault :  /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault : https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea
```

これで、セキュリティで保護されたクラスターを設定するために必要な情報を入手できました。手順 3 に進みます。

### 手順 2.5
証明書が*なく*、新しい自己署名証明書を作成して Key Vault にアップロードする場合、次の手順を実行します。自己署名証明書は、製品クラスターではなく、テスト クラスターにのみ使用してください。

既存のリソース グループと Key Vault を利用し、証明書を格納できます。あるいは、リソース グループや Key Vault がない場合、新しく作成できます。このスクリプトを使用し、デプロイをサポートするように最初に既存の Key Vault を構成する必要があります。

```powershell
Login-AzureRmAccount
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

次のスクリプトは、リソース グループや Key Vault がない場合に新しく作成し、自己署名証明書を作成して Key Vault にアップロードし、新しい証明書を *OutputPath* に出力します。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```
*DnsName* 文字列には、コピーする証明書が CloneCert パラメーターで指定されていない場合に、証明書のサブジェクト代替名拡張に設定する 1 つ以上の DNS 名を指定します。最初の DNS 名は、サブジェクト名としても保存されます。自己署名証明書を指定しない場合、最初の DNS 名は発行者名としても保存されます。*Invoke-AddCertToKeyVault* コマンドレットは [New-SelfSignedCertificate](https://technet.microsoft.com/library/hh848633.aspx) コマンドレットを使用して自己署名証明書を作成します。

実際の値を入力したスクリプトの例は次のようになります。

```powershell
Login-AzureRmAccount
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

スクリプトが正常に終了すると、次のような出力が表示されます。手順 3 でこの出力が必要になります。

```
Certificate Thumbprint: 64881409F4D86498C88EEC3697310C15F8F1540F

SourceVault /Resource ID of the key vault : /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

Certificate URL /URL to the certificate location in the key vault: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea
```

## 手順 3: セキュリティで保護されたクラスターを設定する

Azure Key Vault に証明書がアップロードされたら、その証明書でセキュリティ保護されたクラスターを作成できます。この手順は、クラスター作成プロセスの「[手順 3: セキュリティの構成](service-fabric-cluster-creation-via-portal.md#step-3--configure-security)」に相当し、セキュリティ構成の設定方法を示します。

>[AZURE.NOTE]
必要な証明書は、[セキュリティ構成] のノード タイプ レベルで指定します。クラスター内のすべてのノード タイプについて、証明書を指定する必要があります。このドキュメントではポータルを使用した手順について説明していますが、Azure リソース マネージャー テンプレートを使用して実行することもできます。

![Azure ポータルのセキュリティ構成のスクリーン ショット][SecurityConfigurations_01]

### 必須のパラメーター

- **[セキュリティ モード]**: **[X509 証明書]** を選択し、X.509 証明書でセキュリティ保護されたクラスターを設定します。
- **[Cluster protection level (クラスター保護レベル)]**: 各値の意味については、「[保護レベルの理解](https://msdn.microsoft.com/library/aa347692.aspx)」を参照してください。ここでは 3 つの値 (EncryptAndSign、Sign、None) を利用できますが、確実でない場合は既定の EncryptAndSign をそのまま選択してください。
- **[Source Vault (ソース コンテナー)]**: これは Key Vault のリソース ID を示します。形式は次のとおりです。

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **[証明書 URL]**: これは証明書がアップロードされた Key Vault の場所の URL を示します。形式は次のとおりです。

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **[証明書の拇印]**: これは上の手順で指定した URL にある証明書の拇印を示します。

### 省略可能なパラメーター

 必要に応じて、クラスターに対する操作に使用するクライアント コンピューターに追加の証明書を指定することができます。既定では、必須のパラメーターで指定した拇印が、クライアント操作に許可されている承認済み拇印の一覧に追加されます。

**[Admin Client (管理クライアント)]**: この情報を使用して、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して管理者の読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。管理操作の承認に使用する証明書は複数指定できます。

- **[Authorize By (承認方法)]**: この証明書を検索する際に、サブジェクト名を使用するか拇印を使用するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が与えられます。
- **[サブジェクト名]**: これはサブジェクト名による承認を指定した場合にのみ必要です。
- **[発行者の拇印]**: クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。

**[Read Only Client (読み取り専用クライアント)]**: この情報を使用して、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。読み取り専用操作を許可する証明書を複数指定できます。

- **[Authorize By (承認方法)]**: この証明書を検索する際に、サブジェクト名を使用するか拇印を使用するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が与えられます。
- **[サブジェクト名]**: これはサブジェクト名による承認を指定した場合にのみ必要です。
- **[発行者の拇印]**: クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。

## 次のステップ
クラスターで証明書セキュリティを構成したら、クラスター作成プロセスの「[手順 4: クラスターの作成を完了する](service-fabric-cluster-creation-via-portal.md#step-4--complete-the-cluster-creation)」を再開してください。

クラスターで証明書セキュリティが作成されたら、後で[証明書を更新](service-fabric-cluster-security-update-certs-azure.md)できます。


<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-azure-secure-with-certs/SecurityConfigurations_02.png

<!---HONumber=AcomDC_0615_2016-->