<properties
   pageTitle="セキュリティで保護されたプライベート クラスターへの接続 | Microsoft Azure"
   description="この記事では、スタンドアロンまたはプライベートのクラスター内での通信と、クライアントとクラスターの間での通信をセキュリティで保護する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="dsk-2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/08/2016"
   ms.author="dkshir"/>

# X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護

この記事では、スタンドアロンの Windows クラスターのさまざまなノード間の通信をセキュリティで保護する方法と、クラスターに接続するクライアントを X.509 証明書を使用して認証する方法について説明しています。これにより、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。証明書セキュリティは、クラスターの作成時にクラスターで有効にしておく必要があります。

ノード間のセキュリティ、クライアントとノードの間のセキュリティ、ロールベースのアクセス制御などのクラスター セキュリティの詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事を参照してください。

## 必要な証明書

まず、クラスターのノードの 1 つに[スタンドアロン クラスター パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)します。ダウンロードしたパッケージの中に、**ClusterConfig.X509.MultiMachine.json** ファイルがあります。このファイルを開き、**properties** セクションの下にある **security** のセクションを確認します。

    "security": {
        "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
        "ClusterCredentialType": "X509",
        "ServerCredentialType": "X509",
        "CertificateInformation": {
			"ClusterCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ServerCertificate": {
                "Thumbprint": "[Thumbprint]",
                "ThumbprintSecondary": "[Thumbprint]",
                "X509StoreName": "My"
            },
            "ClientCertificateThumbprints": [{
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            }, {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }],
            "ClientCertificateCommonNames": [{
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint" : "[Thumbprint]",
                "IsAdmin": true
            }]
        }
    },

このセクションでは、スタンドアロンの Windows クラスターをセキュリティで保護するために必要な証明書に関する情報が示されています。証明書ベースのセキュリティを有効にするには、**ClusterCredentialType** と **ServerCredentialType** の値を *X509* に設定します。

>[AZURE.NOTE] [拇印](https://en.wikipedia.org/wiki/Public_key_fingerprint)は、証明書のプライマリ ID です。作成する証明書の拇印を確認するには、[証明書の拇印を取得する方法](https://msdn.microsoft.com/library/ms734695.aspx)に関する記事を参照してください。

次の表は、クラスターのセットアップに必要な証明書の一覧です。

|**CertificateInformation の設定**|**説明**|
|-----------------------|--------------------------|
|ClusterCertificate|クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。2 つの異なる証明書を使用できます。プライマリ証明書と、アップグレードのためのセカンダリ証明書です。プライマリ証明書の拇印は **Thumbprint** セクションで設定し、セカンダリ証明書の拇印は **ThumbprintSecondary** 変数で設定します。|
|ServerCertificate|この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。利便性を考えて、*ClusterCertificate* と *ServerCertificate* に同じ証明書を使用することもできます。2 つの異なるサーバー証明書を使用できます。プライマリ証明書と、アップグレードのためのセカンダリ証明書です。プライマリ証明書の拇印は **Thumbprint** セクションで設定し、セカンダリ証明書の拇印は **ThumbprintSecondary** 変数で設定します。 |
|ClientCertificateThumbprints|認証されたクライアントにインストールする証明書のセットです。クラスターへのアクセスを許可するコンピューターには、いくつかの異なるクライアント証明書をインストールできます。各証明書の拇印は **CertificateThumbprint** 変数で設定します。**IsAdmin** を *true* に設定した場合、この証明書がインストールされたクライアントは、クラスターに対して管理者権限による管理操作を実行できるようになります。**IsAdmin** が *false* の場合、この証明書がインストールされたクライアントはユーザー アクセス権限 (通常は読み取り専用) で許可される操作のみ実行できます。役割の詳細については、「[ロールベースのアクセス制御 (RBAC)](service-fabric-cluster-security.md/#role-based-access-control-rbac)」をご覧ください。 |
|ClientCertificateCommonNames|**CertificateCommonName** には、最初のクライアント証明書の共通名を設定します。**CertificateIssuerThumbprint** は、この証明書の発行者の拇印です。共通名と発行者の詳細については、「[証明書の使用](https://msdn.microsoft.com/library/ms731899.aspx)」を参照してください。|

次に、クラスター、サーバー、クライアント証明書が指定されているクラスター構成の例を示します。

 ```
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "2016-09-26",
    "nodes": [{
        "nodeName": "vm0",
		"metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
      "nodeName": "vm1",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
      "iPAddress": "10.7.0.6",
      		"metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
		"diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
		}
        "security": {
            "metadata": "The Credential type X509 indicates this is cluster is secured using X509 Certificates. The thumbprint format is - d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ServerCertificate": {
                    "Thumbprint": "a8 13 67 58 f4 ab 89 62 af 2b f3 f2 79 21 be 1d f6 7f 43 26",
                    "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpoint": "19001",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## X.509 証明書の取得
クラスター内の通信をセキュリティで保護するには、最初にクラスター ノード用の X.509 証明書を取得する必要があります。さらに、承認されたコンピューターまたはユーザーだけがそのクラスターに接続できるように制限するには、クライアント コンピューター用に証明書を取得し、インストールする必要があります。

運用ワークロードを実行するクラスターの場合、[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) で署名された X.509 証明書を使用してクラスターをセキュリティで保護する必要があります。これらの証明書を取得する方法の詳細については、「[方法 : 証明書 (WCF) を取得する](http://msdn.microsoft.com/library/aa702761.aspx)」を参照してください。

テスト目的で使用するクラスターの場合は、自己署名証明書を選択することができます。

## 省略可能: 自己署名証明書の作成
正しく保護できる自己署名証明書を作成する方法の 1 つが、*C:\\Program Files\\Microsoft SDKs\\Service Fabric\\ClusterSetup\\Secure* ディレクトリの Service Fabric SDK フォルダーにある *CertSetup.ps1* スクリプトを使用する方法です。このファイルを編集し、適切な名前の証明書を作成します。

次に、保護されたパスワードを含む PFX ファイルにその証明書をエクスポートします。まず、証明書の拇印を取得する必要があります。certmgr.exe アプリケーションを実行します。**Local Computer\\Personal** フォルダーに移動して、先ほど作成した証明書を探します。その証明書をダブルクリックして開き、 [*詳細*] タブを選択して、下にスクロールして [*拇印*] を表示します。その拇印の値を次の PowerShell コマンドにコピーします。スペースは削除してください。保護のために *$pswd* 値を適切で安全なパスワードに変更して、PowerShell を実行します。

```   
$pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
Get-ChildItem -Path cert:\localMachine\my<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
```

コンピューターにインストールされている証明書の詳細を表示するには、次の PowerShell コマンドを実行できます。

```
$cert = Get-Item Cert:\LocalMachine\My<Thumbprint>
Write-Host $cert.ToString($true)
```

または、Azure サブスクリプションをお持ちの場合は、[X.509 証明書の取得](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts)に関するセクションを参照して証明書を作成することもできます。

## 証明書のインストール
証明書を作成した後、それをクラスター ノードにインストールできます。最新の Windows PowerShell 3.x が既にノードにインストールされている必要があります。ノードごとに次の手順を繰り返してください。クラスター証明書とサーバー証明書に加え、すべてのセカンダリ証明書について実行する必要があります。

1. .pfx ファイルをノードにコピーします。
2. 管理者として PowerShell ウィンドウを開き、次のコマンドを入力します。*$pswd* を、この証明書の作成に使用したパスワードに置き換えます。*$PfxFilePath* を、そのノードにコピーした .pfx の完全なパスに置き換えます。

    ```
    $pswd = "1234"
    $PfcFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```

3. 次に、この証明書に対するアクセス制御を設定し、Network Service アカウントで実行される Service Fabric プロセスが次のスクリプトを実行することでそれを使用できるようにします。証明書とサービス アカウントの "NETWORK SERVICE" の拇印を指定します。certmgr.exe ツールを使用し、証明書の [秘密キーの管理] を見ることで、証明書の ACL が正しいかどうか確認できます。

    ```
    param
    (
        [Parameter(Position=1, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$pfxThumbPrint,

        [Parameter(Position=2, Mandatory=$true)]
        [ValidateNotNullOrEmpty()]
        [string]$serviceAccount
        )

        $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; };

        # Specify the user, the permissions and the permission type
        $permission = "$($serviceAccount)","FullControl","Allow"
        $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission;

        # Location of the machine related keys
        $keyPath = $env:ProgramData + "\Microsoft\Crypto\RSA\MachineKeys";
        $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName;
        $keyFullPath = $keyPath + $keyName;

        # Get the current acl of the private key
        $acl = (Get-Item $keyFullPath).GetAccessControl('Access')

        # Add the new ace to the acl of the private key
        $acl.SetAccessRule($accessRule);

        # Write back the new acl
        Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop

        #Observe the access rights currently assigned to this certificate.
        get-acl $keyFullPath| fl
        ```

4. 各サーバーの証明書について、上記の手順を繰り返します。また、クラスターへの接続を許可するコンピューターにクライアント証明書をインストールするときにも、この手順を使用できます。

## セキュリティで保護されたクラスターの作成
**ClusterConfig.X509.MultiMachine.json** ファイルの **security** セクションを構成した後は、「[クラスターの作成](service-fabric-cluster-creation-for-windows-server.md#createcluster)」セクションに進んで、ノードの構成とスタンドアロン クラスターの作成を行います。クラスターを作成する際は、必ず **ClusterConfig.X509.MultiMachine.json** ファイルを使用してください。たとえば、コマンドは次のようになります。

```
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true
```

セキュリティで保護されたスタンドアロンの Windows クラスターを正常に実行し、認証されたクライアントがそのクラスターに接続できるようにセットアップしたら、「[PowerShell を使用して、セキュリティで保護されたクラスターに接続する](service-fabric-connect-to-secure-cluster.md#connectsecurecluster)」を参考にして、クラスターに接続してください。次に例を示します。

```
Connect-ServiceFabricCluster -ConnectionEndpoint 10.7.0.4:19000 -KeepAliveIntervalInSec 10 -X509Credential -ServerCertThumbprint 057b9544a6f2733e0c8d3a60013a58948213f551 -FindType FindByThumbprint -FindValue 057b9544a6f2733e0c8d3a60013a58948213f551 -StoreLocation CurrentUser -StoreName My
```

クラスター内のコンピューターのいずれかにログオンしている場合、証明書が既にローカルにインストールされているため、Powershell コマンドを実行するだけでクラスターに接続し、ノードの一覧を表示できます。

```
Connect-ServiceFabricCluster
Get-ServiceFabricNode
```
クラスターを削除するには、次のコマンドを呼び出します。

```
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json   -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab
```

<!---HONumber=AcomDC_0921_2016-->