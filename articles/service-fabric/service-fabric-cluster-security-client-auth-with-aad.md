
<properties
   pageTitle="Service Fabric クラスターのセキュリティ: Azure Active Directory によるクライアント認証 | Microsoft Azure"
   description="この記事では、プレビュー: クライアント認証用に Azure Active Directory (AAD) を使用する Service Fabric クラスターを作成する方法について説明します。"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/13/2016"
   ms.author="seanmck"/>

# クライアント認証用に Azure Active Directory を使用する Service Fabric クラスターを作成する

Azure Active Directory (AAD) を使用して Service Fabric クラスターの管理エンドポイントへのアクセスをセキュリティで保護することができます。この記事では、必要な AAD アーティファクトを作成する方法、クラスターの作成中にそれらを設定する方法、その後これらのクラスターに接続する方法について説明します。

## AAD での Service Fabric クラスターのモデル化

組織 (テナントと呼ばれます) は、AAD を使用してアプリケーションへのユーザー アクセスを管理できます。アプリケーションは、Web ベースのログイン UI を持つものとネイティブ クライアント エクスペリエンスを提供するものに分類されます。このドキュメントでは、すでにテナントを作成していることを前提としています。作成していない場合は、「[Azure Active Directory テナントを取得する方法](../active-directory/active-directory-howto-tenant.md)」を読むことから始めてください。

Service Fabric クラスターでは、Web ベースの [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) や [Visual Studio](service-fabric-manage-application-in-visual-studio.md) など、さまざまなエントリ ポイントから管理機能にアクセスできます。このため、クラスターへのアクセスを制御するには、2 つの AAD アプリケーション (Web アプリケーションとネイティブ アプリケーション) を作成します。

AAD の Service Fabric クラスターでの構成に関する手順の一部を簡略化するため、一連の Windows PowerShell スクリプトが作成されています。

>[AZURE.NOTE] これらの手順は、クラスターを作成する*前に*実行する必要があります。これは、スクリプトでクラスター名とエンドポイントの使用が発生した場合に、既に作成した値ではなく、予定された値にする必要があるためです。

1. コンピューターに[スクリプトをダウンロードします][sf-aad-ps-script-download]。

2. zip ファイルを右クリックして、**[プロパティ]** を選択し、**[ブロックの解除]** チェック ボックスをオンにして適用します。

3. zip ファイルを解凍します。

4. `SetupApplications.ps1` を実行します。パラメーターとして、TenantId、ClusterName、WebApplicationReplyUrl を指定します。次に例を示します。

    ```powershell
    .\SetupApplications.ps1 -TenantId '690ec069-8200-4068-9d01-5aaf188e557a' -ClusterName 'mycluster' -WebApplicationReplyUrl 'https://mycluster.westus.cloudapp.azure.com:19080/Explorer/index.html'
    ```

    **TenantId** は、Azure クラシック ポータルでテナントの URL を見るとわかります。その URL に埋め込まれている GUID が TenantId です。次に例を示します。

    https://<i></i>manage.windowsazure.com/microsoft.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/**690ec069-8200-4068-9d01-5aaf188e557a**/users

    **ClusterName** は、スクリプトによって作成される AAD アプリケーションのプレフィックスとして使用されます。それは AAD アーティファクトとそれらが使用される Service Fabric クラスターとのマッピングを容易にすることのみを目的としているため、実際のクラスター名と完全に一致させる必要はありません。

    **WebApplicationReplyUrl** は、サインイン プロセスの完了後に AAD がユーザーに返す既定のエンドポイントです。これには、クラスターの Service Fabric Explorer エンドポイントを設定します。既定値は次のとおりです。

    https://&lt;cluster_domain&gt;:19080/Explorer

    AAD テナント用の管理特権を持っているアカウントにサインインすることを求められます。これを実行すると、スクリプトは、Service Fabric クラスターを表す Web アプリケーションとネイティブ アプリケーションの作成に進みます。[Azure クラシック ポータル][azure-classic-portal]でテナントのアプリケーションを調べると、次の 2 つの新しいエントリがあることがわかります。

    - *ClusterName*\_Cluster
    - *ClusterName*\_Client

    次のセクションでクラスターを作成するとき、スクリプトによって、Azure Resource Manager (ARM) テンプレートが必要とする Json が出力されます。このため、PowerShell ウィンドウは開いたままにしてください。

    ![SetupApplication スクリプトの出力には、ARM テンプレートで必要な Json が含まれています][setupapp-script-output]

## クラスターを作成する

AAD アプリケーションが作成されたので、Service Fabric クラスターを作成できます。現時点では、Azure ポータルは Service Fabric クラスター用のAAD 認証をサポートしていないため、PowerShell または Visual Studio で ARM テンプレートを使用してそれを実行する必要があります。

AAD は、クラスターに対するクライアント認証にのみ使用されることに注意してください。セキュリティで保護されたクラスターを作成するには、証明書も用意する必要があります。この証明書は、クラスター内のノード間でセキュリティで保護された通信を行うためと、クラスターの管理エンドポイントにサーバー認証を提供するために使用されます。[Azure クイックスタート ギャラリーでセキュリティで保護されたクラスター用の ARM テンプレート][secure-cluster-arm-template]を見つけることができます。または、[Visual Studio の Service Fabric リソース グループ プロジェクト](service-fabric-cluster-creation-via-visual-studio.md)の readme に記載されている手順に従うことができます。

ピアとしての `SetupApplication` スクリプトからの ARM テンプレート スニペットの出力を fabricSettings、managementEndpoint などに追加します。ウィンドウを閉じると、スニペットが次のように表示されます。

```json
  "azureActiveDirectory": {
    "tenantId": "<your_tenant_id>",
    "clusterApplication": "<your_cluster_application_client_id>",
    "clientApplication": "<your_native_application_client_id>"
  }
```

clusterApplication は、前のセクションで作成した Web アプリケーションを参照します。その ID は、SetupApplication スクリプトの出力の中で見つけることができます (`WebAppId` として参照されています)。clientApplication はネイティブ アプリケーションを参照し、そのクライアント ID は SetupApplication の出力の中で NativeClientAppId として入手できます。

## ユーザーをロールに割り当てる

クラスターを表すアプリケーションを作成したら、Service Fabric によってサポートされるロール (read-only と admin) にユーザーを割り当てる必要があります。これは、[Azure クラシック ポータル][azure-classic-portal]で実行できます。

1. テナントに移動し、[アプリケーション] を選択します。
2. `myTestCluster_Cluster` のような名前を持つ Web アプリケーションを選択します。
3. [ユーザー] タブをクリックします。
4. 割り当てるユーザーを選択し、画面の下部にある **[割り当て]** ボタンをクリックします。

    ![ユーザーをロールに割り当てるためのボタン][assign-users-to-roles-button]

5. ユーザーに割り当てるロールを選択します。

    ![ユーザーをロールに割り当てる][assign-users-to-roles-dialog]

>[AZURE.NOTE] Service Fabric でのロールの詳細については、「[ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。

## クラスターに接続する

AAD 対応クラスターの Service Fabric Explorer に移動すると、自動的にセキュリティで保護されたログイン ページにリダイレクトされます。

Windows PowerShell や Visual Studio などのネイティブ クライアントから接続するには、AAD をサインイン メカニズムとして示した後、エンドポイントの ID を評価するために使用されるサーバー証明書の拇印を提供する必要があります。これら 2 つのエントリ ポイントの詳細を次に示します。

### Visual Studio からの接続

Visual Studio では、次に示すように、発行プロファイルを変更して必要な属性を追加します。

```xml
<ClusterConnectionParameters     
    ConnectionEndpoint="<your_cluster_endpoint>:19000"  
    AzureActiveDirectory="true"
    ServerCertThumbprint="<your_cert_thumbprint>"
    />
```

クラスターに発行するとき、クラスターに対する認証を実行できるログイン ウィンドウが Visual Studio によって表示されます。

![Visual Studio での発行時の AAD ログイン ウィンドウ][vs-publish-aad-login]

### Windows PowerShell からの接続

PowerShell では、次に示すように、Connect-ServiceFabricCluster コマンドレットに必要なパラメーターを指定できます。

```PowerShell
Connect-ServiceFabricCluster -AzureActiveDirectory -ConnectionEndpoint <cluster_endpoint>:19000 -ServerCertThumbprint <server_cert_thumbprint>
```

Visual Studio と同じように、認証用のセキュリティで保護されたログイン ウィンドウが PowerShell によって表示されます。

>[AZURE.NOTE] 既定では、PowerShell と Visual Studio で使用される Service Fabric TCP ゲートウェイは、ポート 19000 でリッスンします。別のポートを構成している場合は、接続エンドポイントを指定するときに、そのポートを使用する必要があります。

## 既知の問題

### 応答アドレスの不一致によるネイティブ クライアント認証エラー

Visual Studio や PowerShell などのネイティブ クライアントから認証する場合、次のようなエラーメッセージが表示されることがあります。

*応答アドレス http://localhost/ が、アプリケーション &lt;クラスターのクライアント アプリケーションの GUID&gt; 用に構成された応答アドレスと一致しません*

この問題を回避するには、既に存在するアドレス "urn:ietf:wg:oauth:2.0:oob" に加え、**http://<i></i>localhost** をクラスターのクライアント アプリケーション定義に対するリダイレクト URI として AAD に追加します。

## 次のステップ

- [Service Fabric クラスターのセキュリティ](service-fabric-cluster-security.md)の詳細を確認する
- [Visual Studio を使用してリモート クラスターにアプリケーションを発行する](service-fabric-publish-app-remote-cluster.md)方法を確認する

<!-- Links -->
[sf-aad-ps-script-download]: http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[secure-cluster-arm-template]: https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype-wad
[aad-graph-api-docs]: https://msdn.microsoft.com/ja-JP/library/azure/ad/graph/api/api-catalog
[azure-classic-portal]: https://manage.windowsazure.com

<!-- Images -->
[assign-users-to-roles-button]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles-button.png
[assign-users-to-roles-dialog]: ./media/service-fabric-cluster-security-client-auth-with-aad/assign-users-to-roles.png
[setupapp-script-output]: ./media/service-fabric-cluster-security-client-auth-with-aad/setupapp-script-arm-json-output.png
[vs-publish-aad-login]: ./media/service-fabric-cluster-security-client-auth-with-aad/vs-login-prompt.png

<!---HONumber=AcomDC_0615_2016-->