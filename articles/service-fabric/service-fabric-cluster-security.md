<properties
   pageTitle="Service Fabric クラスターをセキュリティで保護する | Microsoft Azure"
   description="Service Fabric クラスターのためのセキュリティ シナリオと、これらのシナリオの実装に使用されるテクノロジについて説明します。"
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
   ms.date="08/10/2016"
   ms.author="chackdan"/>

# Service Fabric クラスターのセキュリティに関するシナリオ

Azure Service Fabric クラスターは、ユーザーが所有するリソースの 1 つです。このリソースは、特に運用ワークロードが実行されている場合などに、許可なくアクセスされるのを防ぐためにセキュリティで保護する必要があります。この記事では、Azure またはスタンドアロンで実行されるクラスターのセキュリティに関するシナリオと、そのようなシナリオを実装するために使用するさまざまなテクノロジの概要を示します。クラスターのセキュリティに関するシナリオは次のとおりです。

- ノード間のセキュリティ
- クライアントとノードの間のセキュリティ
- ロールベースのアクセス制御 (RBAC)

## ノード間のセキュリティ
クラスター内の VM やマシンの間の通信をセキュリティで保護します。これにより、クラスターへの参加が許可されているコンピューターのみが、クラスター内でホストされているアプリケーションとサービスに参加できます。

![ノード間通信の図][Node-to-Node]

Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx) (Windows Server マシンの場合) を利用できます。
### ノード間の証明書セキュリティ
Service Fabric では、クラスターを作成するときにノード タイプの構成で指定した X.509 サーバー証明書を使用します。これらの証明書の概要とその入手または作成方法はこの記事の最後に記載されています。

証明書セキュリティは、Azure ポータル、Azure Resource Manager テンプレート、またはスタンドアロン JSON テンプレートを使用してクラスターを作成する際に構成されます。プライマリ証明書と、証明書のロールオーバーに使用されるオプションのセカンダリ証明書を指定できます。指定するプライマリ証明書とセカンダリ証明書は、[クライアントとノードの間のセキュリティ](#client-to-node-security)に指定する管理用クライアント証明書と読み取り専用クライアント証明書とは異なります。

Azure のクラスターで証明書セキュリティを構成する方法については、「[証明書を使用し、Azure で Service Fabric クラスターをセキュリティで保護する](service-fabric-secure-azure-cluster-with-certs.md)」または [Azure Resource Manager テンプレートを使用したクラスターの設定](service-fabric-cluster-creation-via-arm.md)に関する記事を参照してください。

スタンドアロン Windows Server の場合は、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### ノード間の Windows セキュリティ
スタンドアロン Windows Server の場合は、「[Windows 上のスタンドアロン クラスターを Windows セキュリティで保護する](service-fabric-windows-cluster-windows-security.md)」を参照してください。

## クライアントとノードの間のセキュリティ
クライアントの認証を行い、クラスター内のクライアントと個々のノードの間の通信をセキュリティで保護します。この種類のセキュリティでは、クライアント側の通信を認証して保護するため、権限のあるユーザーのみがクラスターにデプロイされたクラスターとアプリケーションにアクセスできます。クライアントは、Windows セキュリティ資格情報または証明書のセキュリティ資格情報を通じて一意に識別されます。

![クライアントとノードの間の通信の図][Client-to-Node]

Azure で実行するクラスターまたは Windows で実行するスタンドアロン クラスターには、[証明書セキュリティ](https://msdn.microsoft.com/library/ff649801.aspx)または [Windows セキュリティ](https://msdn.microsoft.com/library/ff649396.aspx)を利用できます。

### クライアントとノードの間の証明書セキュリティ
 クライアントとノードの間の証明書セキュリティを構成するには、Azure ポータル、Resource Manager テンプレート、またはスタンドアロン JSON テンプレートでクラスターを作成するときに、管理用クライアント証明書やユーザー クライアント証明書を指定します。指定する管理用クライアント証明書とユーザー クライアント証明書は、[ノード間のセキュリティ](#node-to-node-security)に指定するプライマリ証明書とセカンダリ証明書とは異なります。

管理用証明書を使用してクラスターに接続するクライアントには、管理機能へのフル アクセス権があります。読み取り専用ユーザー クライアント証明書を使用してクラスターに接続するクライアントには、管理機能に対する読み取りアクセス権しかありません。つまり、これらの証明書は、この記事の中で後で説明するロールベースのアクセス制御 (RBAC) に使用されます。

Azure クラスターで証明書のセキュリティを構成する方法については、「[証明書を使用し、Azure で Service Fabric クラスターをセキュリティで保護する](service-fabric-secure-azure-cluster-with-certs.md)」または「[Azure Resource Manager テンプレートを使用して Service Fabric クラスターをセットアップする](service-fabric-cluster-creation-via-arm.md)」を参照してください。

スタンドアロン Windows Server の場合は、「[X.509 証明書を使用した Windows でのスタンドアロン クラスターの保護](service-fabric-windows-cluster-x509-security.md)」を参照してください。

### Azure でのクライアントとノードの間の Azure Active Directory (AAD) セキュリティ
Azure で実行されているクラスターは、Azure Active Directory (AAD) を使用して管理エンドポイントへのアクセスをセキュリティで保護することもできます。必要な AAD アーティファクトを作成する方法、クラスターの作成中にそれらを設定する方法、その後これらのクラスターに接続する方法については、「[クライアント認証用に Azure Active Directory を使用する Service Fabric クラスターを作成する](service-fabric-cluster-security-client-auth-with-aad.md)」を参照してください。

## セキュリティに関する推奨事項
Azure クラスターについては、クライアントの認証に AAD セキュリティ、ノード間のセキュリティに証明書を使用することをお勧めします。

スタンドアロン Windows Server クラスターについては、Windows Server 2012 R2 と Active Directory を使用している場合、グループ管理アカウント (GMA) で Windows セキュリティを使用することをお勧めします。それ以外の場合は、引き続き Windows アカウントで Windows セキュリティを使用してください。

## ロールベースのアクセス制御 (RBAC)
アクセス制御を使用すると、クラスター管理者は、ユーザーのグループごとに特定のクラスター操作へのアクセスを制限して、クラスターのセキュリティを強化できます。クラスターに接続するクライアント用に、2 種類の異なるアクセス制御 (管理者ロールとユーザー ロール) がサポートされています。

管理者には、管理機能へのフル アクセス権 (読み取り/書き込み機能など) があります。ユーザーには、既定で管理機能 (クエリ機能など) と、アプリケーションとサービスを解決する機能への読み取りアクセス権のみがあります。

クラスターの作成時に、管理者ロールとユーザー クライアント ロールを指定して、それぞれに個別の ID (証明書、AAD など) を設定します。既定のアクセス制御の設定と、既定の設定を変更する方法の詳細については、「[ロール ベースのアクセス制御 (Service Fabric クライアント用)](service-fabric-cluster-security-roles.md)」を参照してください。


## X.509 証明書と Service Fabric
一般的に、X.509 デジタル証明書は、クライアントとサーバーの認証、暗号化、メッセージのデジタル署名に使用されています。これらの証明書の詳細については、「[証明書の使用](http://msdn.microsoft.com/library/ms731899.aspx)」を参照してください。

次のような、考慮すべき重要な点がいくつかあります。

- 運用環境のワークロードを実行しているクラスターに使用する証明書は、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から取得する必要があります。
- 運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください。
- 自己署名入りの証明書は使用できますが、運用環境のクラスターではなく、テスト環境のクラスターにのみ使用してください。

### サーバー X.509 証明書

サーバー証明書の主な作業は、クライアントに対するサーバー (ノード) の認証とサーバー (ノード) に対するサーバー (ノード) の認証です。クライアントまたはノードがノードを認証するときに行う初期チェックの 1 つは、サブジェクト フィールドの共通名の値を確認することです。この共通名、または証明書のサブジェクト代替名の 1 つが、使用可能な共通名の一覧に存在する必要があります。

「[セキュリティで保護された LDAP 証明書にサブジェクトの別名を追加する方法](http://support.microsoft.com/kb/931351)」という記事では、サブジェクト代替名 (SAN) で証明書を生成する方法について説明しています。

サブジェクト フィールドには複数の値を指定できます。各値には、その値を示す初期設定のプレフィックスを付けます。よく使われる初期設定は、共通名を示す "CN" です。たとえば、"CN = www.contoso.com" です。サブジェクト フィールドは空白にすることもできます。省略可能なサブジェクト代替名フィールドに入力する場合は、証明書の共通名と、1 つのサブジェクト代替名につき 1 つのエントリを指定する必要があります。これらの値は DNS 名値として入力します。

証明書の目的フィールド値には、"サーバー認証" や "クライアント認証" などの適切な値を含めるようにしてください。

### クライアント X.509 証明書

通常、クライアント証明書はサードパーティの証明機関から発行されません。通常、現在のユーザーの所在地の個人用ストアには、ルート機関が配置した "クライアント認証" 用のクライアント証明書が含まれています。相互認証が必要な場合、クライアントはこのような証明書を使用できます。

>[AZURE.NOTE] Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。クライアント証明書は管理に利用できません。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## 次のステップ

セキュリティで保護されたクラスターを設定する方法についてはこちらを参照してください。

- [Azure クラスターを証明書を使用して保護する](service-fabric-secure-azure-cluster-with-certs.md)

クラスターの設定を行った後は、クラスターのアップグレードについてこちらを参照してください。

- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [ロール オーバーまたは新しい証明書の追加](service-fabric-cluster-security-update-certs-azure.md)

アプリケーションのセキュリティの詳細についてはこちらを参照してください。

- [アプリケーションのセキュリティと RunAs](service-fabric-application-runas-security.md)

- [サービスの通信のセキュリティ確保](service-fabric-reliable-services-secure-communication.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

<!---HONumber=AcomDC_0817_2016-->