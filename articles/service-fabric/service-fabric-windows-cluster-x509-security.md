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
   ms.date="06/10/2016"
   ms.author="dkshir"/>

# 証明書を使用してスタンドアロンの Windows クラスターをセキュリティで保護する

この記事では、スタンドアロンの Windows クラスターのさまざまなノード間の通信をセキュリティで保護する方法と、クラスターに接続するクライアントを X.509 証明書を使用して認証する方法について説明しています。これにより、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。証明書セキュリティは、クラスターの作成時にクラスターで有効にしておく必要があります。ノード間のセキュリティ、クライアントとノードの間のセキュリティ、ロールベースのアクセス制御の詳細については、[クラスターのセキュリティ シナリオ](service-fabric-cluster-security.md)に関する記事を参照してください。

## 必要な証明書

まず、クラスターのノードの 1 つに[スタンドアロン クラスター パッケージをダウンロード](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)します。ダウンロードしたパッケージの中に、**ClusterConfig.X509.json** ファイルがあります。このファイルを*編集*モードで開き、**プロパティ** タブの **security** セクションを確認します。

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

このセクションでは、スタンドアロンの Windows クラスターをセキュリティで保護するために必要なすべての証明書に関する情報が示されています。証明書ベースのセキュリティを有効にするには、**ClusterCredentialType** と **ServerCredentialType** の値を *X509* に設定します。

>[AZURE.NOTE] [拇印](https://en.wikipedia.org/wiki/Public_key_fingerprint)は、証明書のプライマリ ID です。作成する証明書の拇印を確認するには、[証明書の拇印を取得する方法](https://msdn.microsoft.com/library/ms734695(v=vs.110).aspx)に関する記事を参照してください。

次の表は、クラスターのセットアップに必要な実際の証明書の一覧です。

|**CertificateInformation の設定**|**説明**|
|-----------------------|--------------------------|
|ClusterCertificate|クラスターのノード間の通信をセキュリティで保護するには、この証明書が必要です。2 つの異なる証明書を使用できます。プライマリ証明書と、障害発生時のためのセカンダリ証明書です。プライマリ証明書の拇印は **Thumbprint** セクションで設定し、セカンダリ証明書の拇印は **ThumbprintSecondary** 変数で設定します。|
|ServerCertificate|この証明書は、クライアントがこのクラスターに接続しようとしたときに、クライアントに提示されます。利便性を考えて、*ClusterCertificate* と *ServerCertificate* に同じ証明書を使用することもできます。2 つの異なるサーバー証明書を使用できます。プライマリ証明書と、障害発生時のためのセカンダリ証明書です。プライマリ証明書の拇印は **Thumbprint** セクションで設定し、セカンダリ証明書の拇印は **ThumbprintSecondary** 変数で設定します。 |
|ClientCertificateThumbprints|認証されたクライアントにインストールする証明書のセットです。クラスターとクラスター上で実行されているアプリケーションへのアクセスを許可するコンピューターには、いくつかの異なるクライアント証明書をインストールできます。各証明書の拇印は **CertificateThumbprint** 変数で設定します。**IsAdmin** を *true* に設定した場合、この証明書がインストールされたクライアントは、クラスターに対してさまざまな管理操作を実行できるようになります。**IsAdmin** が *false* の場合、クラスター上で実行中のアプリケーションにしか接続できません。|
|ClientCertificateCommonNames|**CertificateCommonName** には、最初のクライアント証明書の共通名を設定します。**CertificateIssuerThumbprint** は、この証明書の発行者の拇印です。共通名と発行者の詳細については、「[証明書の使用](https://msdn.microsoft.com/library/ms731899(v=vs.110).aspx)」を参照してください。|


## 証明書のインストール

クラスター間の通信をセキュリティで保護するには、最初にクラスター ノード用の X.509 証明書を取得する必要があります。さらに、承認されたコンピューターまたはユーザーだけがそのクラスターに接続できるように制限するには、該当するクライアント コンピューター用に証明書を取得し、インストールする必要があります。X.509 証明書を取得する手順については、[証明書の取得](https://msdn.microsoft.com/library/aa702761.aspx)に関する記事を参照してください。秘密キーを格納できるようにするには、**.pfx** 証明書を作成する必要があります。Azure サブスクリプションをお持ちの場合は、[X.509 証明書の取得](service-fabric-secure-azure-cluster-with-certs.md#acquirecerts)に関するセクションを参照して証明書を作成することもできます。証明書が用意できたら、次の手順でクラスター ノードにインストールできます。次の手順は、最新の Windows PowerShell 3.x が既にノードにインストールされていることを前提としています。ノードごとに次の手順を繰り返してください。クラスターおよびサーバー証明書と、それぞれのセカンダリ証明書について実行する必要があります。

- .pfx ファイルをノードにコピーします。

- 管理者として PowerShell ウィンドウを開き、次のコマンドを入力します。
	
		Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My `
		-FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $password -AsPlainText -Force)

*$password* を、その証明書の作成に使用したパスワードに置き換えます。$PfxFilePath を、そのノードにコピーした .pfx の完全なパスに置き換えます。

- 次のスクリプトを実行してこの証明書に対するアクセス制御を設定し、Service Fabric プロセスがそれを使用できるようにします。

		$cert = get-item Cert:\LocalMachine\My<CertificateThumbprint>
		$cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName

		#Note down the string output by this command, this is the container name for your private key. 

		$privateKeyFilePath = 'C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys<PrivateKeyContainerName>'
	
		#Observe the access rights currently assigned to this certificate.
		get-acl $privateKeyFilePath | fl

		#Set the ACL so that the Service Fabric process can access it.
		$acl = get-acl $privateKeyFilePath
		$acl.SetAccessRule((New-Object System.Security.Accesscontrol.FileSystemAccessRule("NT AUTHORITY\NetworkService","FullControl","Allow")))
		Set-Acl $privateKeyFilePath $acl -ErrorAction Stop 
	

クラスターへの接続を許可するコンピューターにクライアント証明書をインストールするときにも、この手順を使用してください。


## 次のステップ

ClusterConfig.X509.json ファイルの **security** セクションを構成した後は、「[クラスターの作成](service-fabric-cluster-creation-for-windows-server.md#createcluster)」セクションに進んで、ノードの構成とスタンドアロン クラスターの作成を行います。クラスターを作成する際は、必ず **ClusterConfig.X509.json** ファイルを使用してください。たとえば、コマンドは次のようになります。

	cd $ServiceFabricDeployAnywhereFolder
	.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.json -MicrosoftServiceFabricCabFilePath .\MicrosoftAzureServiceFabric.cab -AcceptEULA $true -Verbose


セキュリティで保護されたスタンドアロンの Windows クラスターを正常に実行し、認証されたクライアントがそのクラスターに接続できるようにセットアップしたら、「[Connect to a secure cluster using PowerShell (PowerShell を使用して、セキュリティで保護されたクラスターに接続する)](service-fabric-connect-to-secure-cluster.md#connectsecurecluster)」セクションを参考に、クラスターに接続してください。

<!---HONumber=AcomDC_0615_2016-->