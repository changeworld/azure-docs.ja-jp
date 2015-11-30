<properties
   pageTitle="Service Fabric クラスターをセキュリティで保護する方法 | Microsoft Azure"
   description="Service Fabric クラスターをセキュリティで保護する方法について説明します。オプションの概要"
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
   ms.date="11/10/2015"
   ms.author="chackdan"/>

# 証明書を使用して Service Fabric をセキュリティで保護する方法

セキュリティで保護された Service Fabric クラスターを設定するには、少なくとも 1 つのサーバー証明書または x509 証明書が必要です。次に、その証明書を Azure Key Vault にアップロードし、[Service Fabric クラスターの作成プロセス](service-fabric-cluster-creation-via-portal.md)に関するページを参照して、クラスターの作成プロセスで証明書を使用します。

3 つの手順があります

1. x509 証明書を取得します
2. Azure Key Vault に証明書をアップロードします。
3. Service Fabric クラスターの作成プロセスで、証明書の場所と詳細を指定します。

詳しく説明する前に、シナリオの基本について説明します。

##  対象のシナリオ

Service Fabric には、次のシナリオのセキュリティが用意されています。

1. クラスターのノード間の通信をセキュリティで保護します。
2. ファブリック クライアントとクラスター内の特定のノード間の通信をセキュリティで保護します。
3. ロールベースの Access Control (RBAC) - 管理者が行う証明書のセットに対するクラスターの読み取り専用操作を制限できます。   

Service Fabric は、クラスターを作成するときにノード タイプの構成で指定した x509 サーバー証明書を使用します。これらの証明書の概要と、その取得または作成の方法については、このページの末尾のドキュメントを参照してください。

 
## x509 証明書を取得する

1. 運用ワークロードを実行するクラスターの場合、[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) で署名された x509 証明書を使用してクラスターをセキュリティで保護する必要があります。証明書の取得の詳細については、[http://msdn.microsoft.com/library/aa702761.aspx](http://msdn.microsoft.com/library/aa702761.aspx) を参照してください。
2. テスト目的でのみ使用するクラスターの場合は、自己署名証明書を選択することができます。


## テスト目的で自己署名証明書を作成する

自己署名証明書の作成の詳細については、[https://technet.microsoft.com/library/hh848633.aspx](https://technet.microsoft.com/library/hh848633.aspx) を参照してください。
    
次に私がテスト用証明書を作成するときに使用している PS を紹介しますが、前述のドキュメントを読み、ニーズに合っていることを確認してから使用してください。```
$password = Read-Host -AsSecureString 
``` ```
New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName ChackdanTestCertificate | Export-PfxCertificate -FilePath E:\MyCertificates\ChackdanTestCertificate.pfx -Password $password
```

**注** DnsName <String> には、コピーする証明書が CloneCert パラメーターで指定されていない場合に、証明書のサブジェクト代替名の拡張に設定する 1 つ以上の DNS 名を指定します。最初の DNS 名は、サブジェクト名としても保存されます。自己署名証明書を指定しない場合、最初の DNS 名は発行者名としても保存されます。

## x509 証明書を Key Vault にアップロードする

証明書を Key Vault にアップロードする手順については、[Key Vault ドキュメントのリンク](https://azure.microsoft.com/documentation/articles/key-vault-get-started/)を参照してください。

ソース コンテナー URL、証明書 URL、証明書の拇印をメモしておきます。これらのデータは、セキュリティで保護された Service Fabric クラスターを設定するときに必要です。必要なデータは次のような内容です。



1. **KeyVault のリソース ID/ソース コンテナー URL**: /subscriptions/6c653126-e4ba-42cd-a1dd-f7bf96af7a47/resourceGroups/chackdan-keyvault/providers/Microsoft.KeyVault/vaults/chackdan-kmstest
2. **Key Vault の証明書が保管されている場所の URL**: https://chackdan-kmstest.vault.azure.net:443/secrets/MyCert/dcf17bdbb86b42ad864e8e827c268431 
3. **証明書の拇印**: 2118C3BCE6541A54A0236E14ED2CCDD77EA4567A



##セキュリティで保護されたクラスターを設定する 

使用する必要がある証明書は、[セキュリティ構成] の [NodeType] レベルで指定します。クラスター内のすべての NodeType について、証明書を指定する必要があります。このドキュメントではポータルを使用した手順について説明していますが、ARM テンプレートを使用して実行することもできます。

![SecurityConfigurations\_01][SecurityConfigurations_01]

必須のパラメーター

- **[セキュリティ モード]** では必ず [x509 証明書] を選択します。この選択は、Service Fabric にセキュリティで保護されたクラスターを設定することを示します。 
- **[クラスターの保護レベル]** については、この[保護レベルのドキュメント](https://msdn.microsoft.com/library/aa347692.aspx)を参照して各値の意味を理解してください。ただし、ここでは、EncryptAndSign、Sign、None を使用します。特に意図がない場合は、既定の "EncryptAndSign" のままにすることをお勧めします。
- **[ソース コンテナー]** は、Key Vault のリソース ID を指します。形式は次のとおりです。```
/subscriptions/<Sub ID>/resourceGroups/<Resource group name>/providers/Microsoft.KeyVault/vaults/<vault name>
```

- **[証明書 URL]** は、証明書がアップロードされた Key Vault の場所の URL を指します。形式は次のとおりです。```
https://<name of the vault>.vault.azure.net:443/secrets/<exact location>
https://chackdan-kmstest-eastus.vault.azure.net:443/secrets/MyCert/6b5cc15a753644e6835cb3g3486b3812
```
- **[証明書の拇印]** は、上の手順で指定した URL にある証明書の拇印を指します。

省略可能なパラメーター - 必要に応じて、クラスターに対する操作に使用するクライアント コンピューターに追加の証明書を指定することができます。既定では、必須のパラメーターで指定した拇印が、クライアント操作に許可されている承認済み拇印の一覧に追加されます。

[管理クライアント] - この情報は、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して管理者の読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。管理操作の承認に使用する証明書は複数指定できます。


- **[承認者]** - この証明書を検索する際に、サブジェクト名を使用するか拇印を指定するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が高い方法です。


- **[サブジェクト名]** は、サブジェクト名による承認を指定した場合にのみ必要です。
- **[発行者の拇印]** では、クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。

[読み取り専用クライアント] - この情報は、クラスター管理エンドポイントに接続しているクライアントが、クラスターに対して読み取り専用の操作を実行できる適切な資格情報を示していることを検証します。読み取り専用操作の承認に使用する証明書は複数指定できます。


- **[承認者]** - この証明書を検索する際に、サブジェクト名を使用するか拇印を指定するかを Service Fabric に示します。承認にサブジェクト名を使用することは推奨されませんが、柔軟性が高い方法です。

- **[サブジェクト名]** は、サブジェクト名による承認を指定した場合にのみ必要です。
- **[発行者の拇印]** では、クライアントからサーバーに対して資格情報を提示するときにサーバー側で実行可能なチェック レベルを追加できます。


## クラスター内の証明書を更新する方法

Service Fabric では、プライマリとセカンダリという 2 つの証明書を指定できます。作成時に指定した証明書は既定でプライマリになります。別の証明書を追加するには、その証明書をクラスター内の VM にデプロイする必要があります。方法については、[ユーザーが管理する Key Vault から VM に証明書をデプロイする方法](http://blogs.technet.com/b/kv/archive/2015/07/14/vm_2d00_certificates.aspx)に関するドキュメントを参照してください。

デプロイが完了したら、ポータルまたは ARM で、Service Fabric に対して、使用できるセカンダリ証明書があることを指定します。必要な情報は拇印のみです。

ポータルで、その証明書を追加するクラスター リソースを参照し、証明書設定をクリックし、セカンダリ証明書の拇印を入力して [保存] をクリックします。デプロイが開始され、正常に完了すると、クラスターに対する管理操作にプライマリ証明書とセカンダリ証明書の両方を使用できるようになります。

![SecurityConfigurations\_02][SecurityConfigurations_02]

いずれかの証明書を使用しなくなったら、削除できます。新しいデプロイを開始できるように、証明書の削除後は必ず [保存] をクリックしてください。デプロイが完了すると、削除した証明書はクラスターへの接続に使用できなくなります。セキュリティで保護されたクラスターの場合、常に有効な (失効していない、期限切れではない) 証明書を 1 つ以上デプロイする必要があります。デプロイしないと、クラスターにアクセスできなくなります。

いずれかの証明書の有効期限が近づいているかどうかを判断できる診断イベントもあります。



## x509 証明書の概要

一般的に、x509 デジタル証明書は、クライアントとサーバーの認証、暗号化、メッセージのデジタル署名に使用されています。x509 証明書の詳細については、[http://msdn.microsoft.com/library/ms731899.aspx](http://msdn.microsoft.com/library/ms731899.aspx) を参照してください。

**注**

1. 運用ワークロードで実行されているクラスターに使用する証明書の場合、正しく構成された Windows Server 証明書サービスを使用して作成するか、認定済みの[証明機関 (CA)](https://en.wikipedia.org/wiki/Certificate_authority) から取得する必要があります。
2. 運用環境では、MakeCert.exe などのツールで作成した一時証明書またはテスト証明書を使用しないでください。
3. テスト目的でのみ使用するクラスターの場合は、自己署名証明書を選択することができます。 


##サーバー証明書とクライアント証明書の概要

**サーバー/X509 証明書**

サーバー証明書の主な作業は、クラスターに対するサーバー (ノード) の認証と、サーバー (ノード) に対するサーバー (ノード) の認証です。クライアントまたはノードが認証するときの初期チェックの 1 つは、サブジェクト フィールドの共通名の値と、構成済みの使用可能な共通名一覧の値を比較して、一覧に値があることを確認することです。この共通名、または証明書のサブジェクト代替名の 1 つが、使用可能な共通名の一覧に存在する必要があります。

[http://support.microsoft.com/kb/931351](http://support.microsoft.com/kb/931351) の記事では、サブジェクト代替名 (SAN) で証明書を生成する方法について説明しています。
 
**注:** サブジェクト フィールドには複数の値を指定できます。各値には、その値を示す初期設定のプレフィックスを付けます。よく使われる初期設定は、共通名を示す "CN" です。たとえば、"CN = www.contoso.com" です。サブジェクト フィールドは空白にすることもできます。また、サブジェクト代替名フィールドも省略可能です。設定する場合は、証明書の共通名と、1 つのサブジェクト代替名につき 1 つのエントリを指定する必要があります。これらの値は DNS 名値として入力します。

また、証明書の目的フィールド値には、"サーバー認証" や "クライアント認証" などの適切な値を含めるようにしてください。

**クライアント証明書**

通常、クライアント証明書はサードパーティの証明機関から発行されません。通常、ユーザーの所在地の個人用ストアには、ルート機関が配置した "クライアント認証" 用の証明書が含まれています。相互認証が必要な場合、クライアントはこのような証明書を使用できます。Service Fabric クラスター上のすべての管理操作には、サーバー証明書が必要です。クライアント証明書は使用しないでください。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
- [Service Fabric クラスターのアップグレード プロセスと機能](service-fabric-cluster-upgrade.md)
- [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)。
- [Service Fabric の正常性モデルの概要](service-fabric-health-introduction.md)

<!--Image references-->
[SecurityConfigurations_01]: ./media/service-fabric-cluster-security/SecurityConfigurations_01.png
[SecurityConfigurations_02]: ./media/service-fabric-cluster-security/SecurityConfigurations_02.png

<!---HONumber=Nov15_HO4-->