<properties
   pageTitle="Service Fabric クラスターをセキュリティで保護する | Microsoft Azure"
   description="Service Fabric クラスターをセキュリティで保護する方法。オプションの概要"
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
   ms.date="05/02/2016"
   ms.author="chackdan"/>

# Service Fabric クラスターのセキュリティ保護

Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。このリソースは、特に運用ワークロードが実行されている場合などに、許可なくアクセスされるのを防ぐためにセキュリティで保護する必要があります。この記事では、Service Fabric クラスターをセキュリティで保護するプロセスについて説明します。

## クラスターのセキュリティ シナリオ

Service Fabric には、次のシナリオのセキュリティが用意されています。

1. **ノード間のセキュリティ:** クラスター内の VM とコンピューターの間の通信をセキュリティで保護します。これにより、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

	![ノード間通信の図][Node-to-Node]

2. **クライアントとノードの間のセキュリティ:** クラスター内の Service Fabric と個々のノードの間の通信をセキュリティで保護します。この種類のセキュリティでは、クライアント側の通信を認証して保護するため、権限のあるユーザーのみがクラスターにデプロイされたクラスターとアプリケーションにアクセスできます。クライアントは、Windows セキュリティ資格情報または証明書のセキュリティ資格情報を通じて一意に識別されます。

	![クライアントとノードの間の通信の図][Client-to-Node]

	ノード間のセキュリティとクライアントとノードの間のセキュリティのいずれの場合でも、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx) と [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx)のいずれかを利用できます。ノード間のセキュリティとノードとクライアントの間のセキュリティの選択は互いに独立しているため、同じセキュリティを選択することも異なるセキュリティを使用することもできます。

	Azure Service Fabric では、クラスターを作成するときにノード タイプの構成で指定した X.509 サーバー証明書を使用します。これらの証明書の概要とその入手または作成方法はこの記事の最後に記載されています。

3. **ロールベースのアクセス制御 (RBAC):** クラスターでの管理者操作を特定の証明書セットに限定します。

## 証明書を使用して Service Fabric をセキュリティで保護する

セキュリティで保護された Service Fabric クラスターを設定するには、X.509 証明書を 1 つ以上必要とします。この証明書を Azure Key Vault にアップロードし、クラスター作成プロセスで使用します。

3 つの手順があります

1. X.509 証明書を取得します。
2. Azure Key Vault に証明書をアップロードします。
3. Service Fabric クラスターの作成プロセスで、証明書の場所と詳細を指定します。

### 手順 1: X.509 証明書を取得する

1. 運用ワークロードを実行するクラスターの場合、[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) で署名された X.509 証明書を使用してクラスターをセキュリティで保護する必要があります。これらの証明書を取得する方法の詳細については、「[方法 : 証明書 (WCF) を取得する](http://msdn.microsoft.com/library/aa702761.aspx)」を参照してください。
2. テスト目的でのみ使用するクラスターの場合は、自己署名証明書を選択することができます。その方法については下の手順 2.5. で説明します。

### 手順 2: Key Vault に X.509 証明書をアップロードする

この手順は複雑なため、弊社が Git リポジトリにアップロードした PowerShell モジュールを使用してください。

**手順 2.1**: [Git リポジトリ](https://github.com/ChackDan/Service-Fabric/tree/master/Scripts/ServiceFabricRPHelpers)からこのフォルダーをコンピューターにコピーします。

**手順 2.2**: Azure PowerShell 1.0 以降がコンピューターにインストールされていることを確認します。まだインストールされていない場合は、「[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)」の手順を実行することを強くお勧めします。

**手順 2.3**: PowerShell ウィンドウを開き、ServiceFabricRPHelpers.psm をインポートします(これは、手順 2.1 でダウンロードしたモジュールです)。

```
Remove-Module ServiceFabricRPHelpers
```

次のサンプルをコピーし、.psm1 へのパスを実際のコンピューター上のパスに合わせて変更します。

```
Import-Module "C:\Users\chackdan\Documents\GitHub\Service-Fabric\Scripts\ServiceFabricRPHelpers\ServiceFabricRPHelpers.psm1"
```

**手順 2.4**: 既に取得した証明書を使用している場合、次の手順に従います。それ以外の場合は手順 2.5 に進んでください。自己署名証明書を作成して Key Vault にデプロイする方法について説明しています。

Azure アカウントにサインインします。この PowerShell コマンドが何らかの理由で失敗する場合、Azure PowerShell が正しくインストールされているかどうかを確認することをお勧めします。

```
Login-AzureRmAccount
```

次のスクリプトを実行すると、新しいリソース グループ、Key Vault、またはその両方が作成されます (これらが存在しない場合)。**注意: 既存の Key Vault を使用している場合、このスクリプトを使用して、デプロイをサポートするように構成する必要があります。**

```
Set-AzureRmKeyVaultAccessPolicy -VaultName <Name of the Vault> -ResourceGroupName <string> -EnabledForDeployment
```

```
Invoke-AddCertToKeyVault -SubscriptionId <your subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -UseExistingCertificate -ExistingPfxFilePath <Full path to the .pfx file>
```
実際の値を入力したスクリプトの例は次のようになります。

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate2 -Password abcd123 -UseExistingCertificate -ExistingPfxFilePath C:\MyCertificates\ChackdanTestCertificate.pfx
```

スクリプトが正常に終了すると、次のような出力が表示されます。手順 3 でセキュリティで保護されたクラスターを設定する際にこの出力が必要になります。

- **証明書の拇印**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A

- **SourceVault** /Key Vault のリソース ID: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **証明書の URL** /Key Vault 内の証明書が保管されている場所の URL: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/ebc8df6300834326a95d05d90e0701ea

これで、セキュリティで保護されたクラスターを設定するために必要な情報を入手できました。手順 3 に進みます。

**手順 2.5**: 証明書が*なく*、新しい自己署名証明書を作成して Key Vault にアップロードする場合、次の手順を実行します。

Azure アカウントにログインします。この PowerShell コマンドが何らかの理由で失敗する場合、Azure PowerShell が正しくインストールされているかどうかを確認することをお勧めします。

```
Login-AzureRmAccount
```

次のスクリプトを実行すると、新しいリソース グループ、Key Vault、またはその両方が作成されます (これらが存在しない場合)。

```
Invoke-AddCertToKeyVault -SubscriptionId <you subscription id> -ResourceGroupName <string> -Location <region> -VaultName <Name of the Vault> -CertificateName <Name of the Certificate> -Password <Certificate password> -CreateSelfSignedCertificate -DnsName <string- see note below.> -OutputPath <Full path to the .pfx file>
```

スクリプトで指定した OutputPath に、スクリプトで Key Vault にアップロードした新しい自己署名証明書が格納されます。

>[AZURE.NOTE] DnsName 文字列には、コピーする証明書が CloneCert パラメーターで指定されていない場合に、証明書のサブジェクト代替名拡張に設定する 1 つ以上の DNS 名を指定します。最初の DNS 名は、サブジェクト名としても保存されます。自己署名証明書を指定しない場合、最初の DNS 名は発行者名としても保存されます。

自己署名証明書の作成の詳細については、[https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx) を参照してください。

実際の値を入力したスクリプトの例は次のようになります。

```
Invoke-AddCertToKeyVault -SubscriptionId 35389201-c0b3-405e-8a23-9f1450994307 -ResourceGroupName chackdankeyvault4doc -Location westus -VaultName chackdankeyvault4doc  -CertificateName chackdantestcertificate3 -Password abcd123 -CreateSelfSignedCertificate -DnsName www.chackdan.westus.azure.com -OutputPath C:\MyCertificates
```

これは自己署名証明書であるため、セキュリティで保護されたクラスターへの接続にこの証明書を使用するには、コンピューターの "信頼されたユーザー" ストアにインポートする必要があります。

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

```
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My -FilePath C:\MyCertificates\ChackdanTestCertificate.pfx -Password (Read-Host -AsSecureString -Prompt "Enter Certificate Password ")
```

スクリプトが正常に終了すると、次のような出力が表示されます。手順 3 でこの出力が必要になります。

- **証明書の拇印**: 64881409F4D86498C88EEC3697310C15F8F1540F

- **SourceVault** /Key Vault のリソース ID: /subscriptions/35389201-c0b3-405e-8a23-9f1450994307/resourceGroups/chackdankeyvault4doc/providers/Microsoft.KeyVault/vaults/chackdankeyvault4doc

- **証明書の URL** /Key Vault 内の証明書が保管されている場所の URL: https://chackdankeyvalut4doc.vault.azure.net:443/secrets/chackdantestcertificate3/fvc8df6300834326a95d05d90e0720ea

### 手順 3: セキュリティで保護されたクラスターを設定する

[Service Fabric クラスターの作成プロセス](service-fabric-cluster-creation-via-portal.md)に関するドキュメントに記載されている手順を、セキュリティ構成に関するセクションの前まで実行します。それからここにある指示までスキップし、セキュリティ構成を設定します。

>[AZURE.NOTE]
必要な証明書は、[セキュリティ構成] のノード タイプ レベルで指定します。クラスター内のすべてのノード タイプについて、証明書を指定する必要があります。このドキュメントではポータルを使用した手順について説明していますが、Azure リソース マネージャー テンプレートを使用して実行することもできます。

![Azure ポータルのセキュリティ構成のスクリーン ショット][SecurityConfigurations_01]

必須のパラメーター:

- **セキュリティ モード。** **[X509 証明書]** を選択します。セキュリティで保護されるクラスターを設定する Service Fabric を示します。
- **クラスター保護レベル。** 各値の意味については、「[保護レベルの理解](https://msdn.microsoft.com/library/aa347692.aspx)」を参照してください。ここでは 3 つの値 (EncryptAndSign、Sign、None) を利用できますが、確実でない場合は既定の EncryptAndSign をそのまま選択してください。
- **ソース コンテナー。** これは Key Vault のリソース ID を示します。形式は次のとおりです。

    ```
    /subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
    ```

- **証明書 URL。** 証明書がアップロードされた Key Vault の場所の URL を指します。形式は次のとおりです。

    ```
    https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
```
```
    https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
    ```

- **証明書の拇印。** 上の手順で指定した URL にある証明書の拇印を指します。

省略可能なパラメーター:

 - 必要に応じて、クラスターに対する操作に使用するクライアント コンピューターに追加の証明書を指定することができます。既定では、必須のパラメーターで指定した拇印が、クライアント操作に許可されている承認済み拇印の一覧に追加されます。

管理クライアント: この情報は、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して管理者の読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。管理操作の承認に使用する証明書は複数指定できます。

- **承認者。** この証明書を検索する際に、サブジェクト名を使用するか拇印を指定するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が与えられます。
- **サブジェクト名。** サブジェクト名による承認を指定した場合にのみ必要です。
- **発行者の拇印。**クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。

読み取り専用クライアント: この情報は、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。読み取り専用操作を許可する証明書を複数指定できます。

- **承認者。** この証明書を検索する際に、サブジェクト名を使用するか拇印を指定するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が与えられます。
- **サブジェクト名。** サブジェクト名による承認を指定した場合にのみ必要です。
- **発行者の拇印。** クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。

## クラスター内の証明書を更新する

プライマリとセカンダリという 2 つの証明書を指定できます。既定では、作成時に指定した証明書がプライマリになります。別の証明書を追加するには、その証明書をクラスター内の VM にデプロイする必要があります。上記の手順 2 で、Key Vault に新しい証明書をアップロードする方法が説明されています。最初の証明書の場合と同じように、これには同じ Key Vault を使用できます。詳細については、[ユーザーが管理する Key Vault から VM に証明書をデプロイする方法](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)に関するドキュメントを参照してください。

デプロイが完了したら、ポータルまたはリソース マネージャーで、Service Fabric に対して、使用できるセカンダリ証明書があることを指定します。必要な情報は拇印のみです。

セカンダリ証明書を追加する手順を次に示します。

1. ポータルに移動し、この証明書を追加するクラスター リソースを参照します。
2. **[設定]** で、証明書の設定をクリックし、セカンダリ証明書の拇印を入力します。
3. **[保存]** をクリックします。デプロイが開始され、正常に完了すると、クラスターに対する管理操作にプライマリ証明書かセカンダリ証明書を使用できるようになります。

![Azure ポータルの証明書拇印のスクリーン ショット][SecurityConfigurations_02]

古い証明書を削除してクラスターで使用されないようにするには、次の手順を実行します。

1. ポータルに移動し、クラスターのセキュリティ設定に移動します。
2. 古い証明書を削除します。
3. **[保存]** をクリックして新しいデプロイを開始します。デプロイが完了すると、削除した証明書がクラスターへの接続に使用されることはなくなります。

>[AZURE.NOTE] セキュリティで保護されたクラスターの場合、常に有効な (失効していない、期限切れではない) 証明書 (プライマリまたはセカンダリ) を 1 つ以上デプロイする必要があります。デプロイしないと、クラスターにアクセスできなくなります。


## Service Fabric で使用される証明書の種類

### X.509 証明書

一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証、暗号化、メッセージのデジタル署名に使用されています。これらの証明書の詳細については、MSDN ライブラリにある[証明書の使用](http://msdn.microsoft.com/library/ms731899.aspx)に関するセクションを参照してください。

>[AZURE.NOTE]
- 運用ワークロードを実行しているクラスターに使用する証明書の場合、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から取得する必要があります。
- 運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください。
- テスト目的でのみ使用するクラスターの場合は、自己署名証明書を選択することができます。

### サーバー証明書とクライアント証明書

#### サーバー X.509 証明書

サーバー証明書の主な作業は、クライアントに対するサーバー (ノード) の認証とサーバー (ノード) に対するサーバー (ノード) の認証です。クライアントまたはノードがノードを認証するときに行う初期チェックの 1 つは、サブジェクト フィールドの共通名の値を確認することです。この共通名、または証明書のサブジェクト代替名の 1 つが、使用可能な共通名の一覧に存在する必要があります。

「[セキュリティで保護された LDAP 証明書にサブジェクトの別名を追加する方法](http://support.microsoft.com/kb/931351)」という記事では、サブジェクト代替名 (SAN) で証明書を生成する方法について説明しています。

>[AZURE.NOTE] サブジェクト フィールドには複数の値を指定できます。各値には、その値を示す初期設定のプレフィックスを付けます。よく使われる初期設定は、共通名を示す "CN" です。たとえば、"CN = www.contoso.com" です。サブジェクト フィールドは空白にすることもできます。省略可能なサブジェクト代替名フィールドに入力する場合は、証明書の共通名と、1 つのサブジェクト代替名につき 1 つのエントリを指定する必要があります。これらの値は DNS 名値として入力します。

証明書の目的フィールド値には、"サーバー認証" や "クライアント認証" などの適切な値を含めるようにしてください。

#### クライアント証明書

通常、クライアント証明書はサードパーティの証明機関から発行されません。通常、現在のユーザーの所在地の個人用ストアには、ルート機関が配置した "クライアント認証" 用のクライアント証明書が含まれています。相互認証が必要な場合、クライアントはこのような証明書を使用できます。

>[AZURE.NOTE] Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。クライアント証明書は管理に利用できません。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


### セキュリティ保護されたクラスターに接続する

1. 次のコマンドを実行し、"Connect-serviceFabricCluster" PowerShell コマンドの実行に使用するコンピューターに証明書を設定します。

    ```powershell
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
            -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
            -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
    ```

2. 次の PowerShell コマンドを実行して、セキュリティで保護されたクラスターに接続します。証明書の詳細は、クラスターのセットアップ時に指定したものと同じです。

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
              -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
              -StoreLocation CurrentUser -StoreName My
    ```

    たとえば、上の PowerShell コマンドの結果は次のようになります。

    ```powershell
    Connect-serviceFabricCluster -ConnectionEndpoint sfcluster4doc.westus.cloudapp.azure.com:19000 `
              -KeepAliveIntervalInSec 10 `
              -X509Credential -ServerCertThumbprint C179E609BBF0B227844342535142306F3913D6ED `
              -FindType FindByThumbprint -FindValue C179E609BBF0B227844342535142306F3913D6ED `
              -StoreLocation CurrentUser -StoreName My
    ```

## 次のステップ

- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)
- [アプリケーションのセキュリティと RunAs](service-fabric-application-runas-security.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0518_2016-->