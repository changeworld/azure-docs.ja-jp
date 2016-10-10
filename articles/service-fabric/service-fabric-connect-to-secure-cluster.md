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
   ms.date="08/25/2016"
   ms.author="ryanwi"/>

# セキュリティで保護されたクラスターに AAD なしで接続する
クライアントが Service Fabric クラスター ノードに接続する際には、証明書セキュリティを使用してクライアント認証が行われ、セキュリティ保護された通信が確立します。この認証により、許可されたユーザーのみが、クラスターやデプロイ済みアプリケーションにアクセスし、管理タスクを実行できるようになります。証明書セキュリティは、クラスターの作成時に、クラスターであらかじめ有効にしておく必要があります。クラスターをセキュリティで保護するには、少なくとも 2 つの証明書 (クラスターおよびサーバー用の証明書と、クライアント アクセス用の証明書) を使用する必要があります。追加のセカンダリ証明書とクライアント アクセス用証明書も使用することをお勧めします。クラスターのセキュリティ シナリオの詳細については、 [クラスター セキュリティ](service-fabric-cluster-security.md)に関する記事を参照してください。

証明書セキュリティを使用して、クライアントとクラスター ノード間の通信をセキュリティで保護するには、クライアント証明書を取得してインストールします。この証明書は、現在のユーザーまたはローカル コンピューターの個人用 (マイ) ストアにインストールすることができます。クライアントがクラスターを認証できるように、サーバー証明書の拇印も必要になります。


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
<a id="connectsecureclustercli"></a>
## セキュリティで保護されたクラスターに Azure CLI を使用して AAD なしで接続する

セキュリティで保護されたクラスターに接続する方法を以下の Azure CLI コマンドで説明します。証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。
 
ご使用の証明書に証明機関 (CA) が含まれている場合は、次の例のようにパラメーター `--ca-cert-path` を追加する必要があります。

```
 azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
複数の CA がある場合は、コンマで区切ります。

 
証明書の共通名が接続エンドポイントと一致しない場合は、`--strict-ssl-false` パラメーターを指定して検証を省略することができます。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 --strict-ssl-false 
```
 
CA 検証を省略したい場合、次のコマンドのように ``--reject-unauthorized-false`` パラメーターを追加します。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized-false 
```
 
自己署名証明書で保護されたクラスターに接続する場合は、次のコマンドを実行して、CA 検証と共通名検証の両方を無効にしてください。

```
azure servicefabric cluster connect --connection-endpoint https://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl-false --reject-unauthorized-false
```

接続後、他の CLI コマンドを実行してクラスターの対話操作を実行することができます。

<a id="connectsecurecluster"></a>
## セキュリティで保護されたクラスターに PowerShell を使用して AAD なしで接続する

次の PowerShell コマンドを実行して、セキュリティで保護されたクラスターに接続します。証明書の詳細は、クラスター ノード上の証明書と一致する必要があります。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <Cluster FQDN>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <Certificate Thumbprint> `
          -FindType FindByThumbprint -FindValue <Certificate Thumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

*ServerCertThumbprint* はクライアント ノードにインストールされているサーバー証明書の拇印であり、*FindValue* は管理用クライアント証明書の拇印です。以下に示したのは、パラメーターを指定した状態のコマンドの例です。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint clustername.westus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint A8136758F4AB8962AF2BF3F27921BE1DF67F4326 `
          -FindType FindByThumbprint -FindValue 71DE04467C9ED0544D021098BCD44C71E183414E `
          -StoreLocation CurrentUser -StoreName My
```




## FabricClient API を使用して、セキュリティで保護されたクラスターに接続する

FabricClient API の詳細については、[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) を参照してください。クラスター内のノードには、[FabricClient](https://msdn.microsoft.com/library/system.fabric.fabricclient.aspx) で設定された [RemoteCommonNames プロパティ](https://msdn.microsoft.com/library/azure/system.fabric.x509credentials.remotecommonnames.aspx)に共通名または SAN の DNS 名が表示される、有効な証明書が必要です。このプロセスに従うことで、クライアントとクラスター ノードの間の相互認証が可能になります。

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

<!---HONumber=AcomDC_0928_2016-->