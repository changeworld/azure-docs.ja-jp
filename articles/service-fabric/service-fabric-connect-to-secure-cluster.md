<properties
   pageTitle="クラスターへのクライアント アクセスの認証 | Microsoft Azure"
   description="証明書を使用して Service Fabric クラスターへのクライアント アクセスを認証する方法、およびクライアントとクラスター間の通信をセキュリティで保護する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="rwike77"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/18/2016"
   ms.author="ryanwi"/>

# セキュリティ保護されたクラスターに接続する
クライアントが Service Fabric クラスター ノードに接続する際には、証明書セキュリティを使用してクライアント認証が行われ、セキュリティ保護された通信が確立します。これにより、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。証明書セキュリティは、クラスターの作成時に、クラスターであらかじめ有効にしておく必要があります。クラスターをセキュリティで保護するには、少なくとも 2 つの証明書 (クラスターおよびサーバー用の証明書と、クライアント アクセス用の証明書) を使用する必要があります。追加のセカンダリ証明書とクライアント アクセス用証明書も使用することをお勧めします。クラスターのセキュリティ シナリオの詳細については、 [クラスター セキュリティ](service-fabric-cluster-security.md)に関する記事を参照してください。

証明書セキュリティを使用して、クライアントとクラスター ノード間の通信をセキュリティで保護するには、クライアント証明書を取得して、ローカル コンピューターの個人用ストア、または現在のユーザーの個人用ストアにインストールします。クライアントがクラスターを認証できるように、サーバー証明書の拇印も必要になります。

次の PowerShell コマンドレットを実行して、クラスターへのアクセスに使用するコンピューターでクライアント証明書を設定します。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
        -Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

これが自己署名証明書である場合は、セキュリティで保護されたクラスターへの接続にこの証明書を使用する前に、コンピューターの "信頼されたユーザー" ストアにインポートする必要があります。

```powershell
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\TrustedPeople `
-FilePath C:\docDemo\certs\DocDemoClusterCert.pfx `
-Password (ConvertTo-SecureString -String test -AsPlainText -Force)
```

<a id="connectsecurecluster"></a>
## PowerShell を使用して、セキュリティで保護されたクラスターに接続する

次の PowerShell コマンドを実行して、セキュリティで保護されたクラスターに接続します。証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

たとえば、上記の PowerShell コマンドの結果は次のようになります。*ServerCertThumbprint* はクライアント ノードにインストールされているサーバー証明書の拇印であり、*FindValue* は管理用クライアント証明書の拇印です。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```

## FabricClient API を使用して、セキュリティで保護されたクラスターに接続する
次の [FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) です。クラスター内のノードには、[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) で設定された [RemoteCommonNames プロパティ](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx)に共通名または SAN の DNS 名が表示される、有効な証明書が必要です。これにより、クライアントとクラスター ノード間の相互認証が可能になります。

```csharp
string clientCertThumb = "71DE04467C9ED0544D021098BCD44C71E183414E";
string serverCertThumb = "A8136758F4AB8962AF2BF3F27921BE1DF67F4326";
string CommonName = "www.clustername.westus.azure.com";
string connection = "clustername.westus.cloudapp.azure.com:19000";

X509Credentials xc = GetCredentials(clientCertThumb, serverCertThumb, CommonName);
FabricClient fc = new FabricClient(xc, connection);
Task<bool> t = fc.PropertyManager.NameExistsAsync(new Uri("fabric:/any"));
try
{
    bool result = t.Result;
    Console.WriteLine("Cluster is connected");
}
catch (AggregateException ae)
{
    Console.WriteLine("Connect failed: {0}", ae.InnerException.Message);
}
catch (Exception e)
{
    Console.WriteLine("Connect failed: {0}", e.Message);
}

...

static X509Credentials GetCredentials(string clientCertThumb, string serverCertThumb, string name)
{
    X509Credentials xc = new X509Credentials();

    // Client certificate
    xc.StoreLocation = StoreLocation.CurrentUser;
    xc.StoreName = "MY";
    xc.FindType = X509FindType.FindByThumbprint;
    xc.FindValue = thumb;

    // Server certificate
    xc.RemoteCertThumbprints.Add(thumb);
    xc.RemoteCommonNames.Add(name);

    xc.ProtectionLevel = ProtectionLevel.EncryptAndSign;
    return xc;
}
```


## 次のステップ

- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)
- [アプリケーションのセキュリティと RunAs](service-fabric-application-runas-security.md)

<!---HONumber=AcomDC_0720_2016-->