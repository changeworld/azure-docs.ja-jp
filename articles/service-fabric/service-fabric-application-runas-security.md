<properties
   pageTitle="Service Fabric アプリケーションとサービス セキュリティ ポリシーの概要 | Microsoft Azure"
   description="アプリケーションが開始前に特権アクションを実行する必要がある SetupEntry ポイントを含む Service Fabric アプリケーションをシステムおよびローカル セキュリティ アカウントで実行する方法の概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/22/2016"
   ms.author="mfussell"/>

# アプリケーションのセキュリティ ポリシーの構成
Azure Service Fabric では、別のユーザー アカウントを使用してクラスターで実行しているアプリケーションをセキュリティで保護できます。また、そのユーザー アカウントでデプロイするときにアプリケーションによって使用されるファイル、ディレクトリ、証明書などのリソースも保護されます。これにより、実行中のアプリケーションは、共有のホスト環境にある場合でも、互いから保護されます。

既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。また、Service Fabric では、アプリケーションのマニフェストで指定されているローカル ユーザー アカウントまたはローカル システム アカウントを使用して、アプリケーションを実行することもできます。サポートされているローカル システム アカウントの種類は、**LocalUser**、**NetworkService**、**LocalService**、**LocalSystem** です。

 スタンドアロンのインストーラーを使用して、データ センターの Windows Server で Service Fabric を実行する際は、Active Directory (AD) ドメイン アカウントを使用できます。

ユーザー グループを定義して作成し、ユーザーをそのグループに追加してまとめて管理できます。これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。

## SetupEntryPoint サービスのポリシーを構成する

[アプリケーション モデル](service-fabric-application-model.md)で説明したように、**SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *NetworkService* アカウント)。通常、**EntryPoint** で指定された実行可能ファイルは、実行時間の長いサービス ホストで別々のセットアップのエントリ ポイントを持つことで、長時間にわたって高い権限でサービス ホスト実行可能ファイルを実行することを回避できます。**EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (**SetupEntryPoint** で再起動)。

サービスの SetupEntryPoint とメイン EntryPoint を示す簡単なサービス マニフェストの例を次に示します。

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="Config" Version="1.0.0" />
</ServiceManifest>
~~~

### ローカル アカウントを使用してポリシーを構成する

サービスにセットアップ エントリ ポイントを構成した後は、サービスの実行に使用するセキュリティ アクセス許可をアプリケーション マニフェストで変更できます。次の例では、管理者ユーザー アカウントの特権で実行するようにサービスを構成する方法を示します。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupAdminUser" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupAdminUser">
            <MemberOf>
               <SystemGroup Name="Administrators" />
            </MemberOf>
         </User>
      </Users>
   </Principals>
</ApplicationManifest>
~~~

最初に、SetupAdminUser などのユーザー名で **Principals** セクションを作成します。これは、ユーザーが Administrators システム グループのメンバーであることを示します。

次に、**ServiceManifestImport** セクションで、このプリンシパルを **SetupEntryPoint** に適用するためのポリシーを構成します。これにより、**MySetup.bat** ファイルは管理者特権で実行する必要があることを Service Fabric に通知します。メイン エントリ ポイントに対してポリシーを適用 "*しない*" と、**MyServiceHost.exe** のコードは、システム **NetworkService** アカウントで実行されます。これはすべてのサービス エントリ ポイントが RunAs で実行する既定のアカウントです。

それでは、MySetup.bat ファイルを Visual Studio プロジェクトに追加して、Administrator 特権をテストしてみます。Visual Studio でサービス プロジェクトを右クリックし、MySetup.bat という名前の新しいファイルを追加します。

次に、サービス パッケージに MySetup.bat ファイルが含まれていることを確認します。既定では、含まれていません。ファイルを選択し、右クリックしてコンテキスト メニューを表示し、**[プロパティ]** を選択します。[プロパティ] ダイアログ ボックスで、**[出力ディレクトリにコピー]** が **[新しい場合はコピーする]** に設定されていることを確認します。次のスクリーン ショットを確認してください。

![SetupEntryPoint バッチ ファイルの Visual Studio CopyToOutput][image1]

次に、MySetup.bat ファイルを開き、次のコマンドを追加します。

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > out.txt
echo %TestVariable% >> out.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

ソリューションをビルドして、開発用のローカル クラスターにデプロイします。サービスが開始した後、Service Fabric Explorer で示されるように、MySetup.bat が成功したことを 2 つの方法で確認できます。PowerShell コマンド プロンプトを起動し、次を入力します。

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

サービスがデプロイされ、Service Fabric エクスプローラーで開始したノードの名前をメモします (たとえば、Node 2)。次に、アプリケーション インスタンスの作業フォルダーに移動し、**TestVariable** の値を示す out.txt ファイルを探します。たとえば、このサービスが Node 2 にデプロイされた場合は、**MyApplicationType** の次のパスに移動します。

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

###  ローカル システム アカウントを使用してポリシーを構成する
多くの場合、上述のように管理者アカウントではなく、ローカル システム アカウントを使用して起動スクリプトを実行することが推奨されます。管理者として RunAs ポリシーを実行すると、マシンでユーザー アクセス制御 (UAC) が既定で有効になっているため、通常はうまく動作しません。このような場合、**ローカル ユーザーを管理者グループに追加するのではなく、SetupEntryPoint を LocalSystem として実行することが推奨されます**。次の例では、LocalSystem として実行するように SetupEntryPoint を設定します。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="SetupLocalSystem" EntryPointType="Setup" />
      </Policies>
   </ServiceManifestImport>
   <Principals>
      <Users>
         <User Name="SetupLocalSystem" AccountType="LocalSystem" />
      </Users>
   </Principals>
</ApplicationManifest>
~~~

##  SetupEntryPoint から PowerShell コマンドを起動する
**SetupEntryPoint** ポイントから PowerShell を実行するには、PowerShell ファイルを指し示すバッチ ファイルで **PowerShell.exe** を実行します。最初に、PowerShell ファイル (**MySetup.ps1** など) をサービス プロジェクトに追加します。このファイルがサービス パッケージにも含まれるように、*[新しい場合はコピーする]* プロパティを忘れずに設定します。次の例では、システム環境変数 **TestVariable** を設定する PowerShell ファイル MySetup.ps1 を起動するバッチ ファイルを示します。


PowerShell ファイルを起動するための MySetup.bat。

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

PowerShell ファイルで、システム環境変数を設定するために次を追加します。

~~~
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
~~~

**注:** 既定では、バッチ ファイルが実行されると、**work** と呼ばれるアプリケーション フォルダーでファイルを検索します。この場合、MySetup.bat を実行するときに、アプリケーションの**コード パッケージ** フォルダーである同じフォルダー内に MySetup.ps1 を見つける必要があります。このフォルダーを変更するには、次に示すように作業フォルダーを設定します。

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    </ExeHost>
</SetupEntryPoint>
~~~

## ローカル デバッグにコンソールのリダイレクトを使用する
場合によっては、デバッグ目的でスクリプトを実行してコンソールの出力を確認することが役立ちます。そのために、出力をファイルに書き込むコンソール リダイレクト ポリシーを設定できます。ファイル出力はアプリケーションがデプロイおよび実行されるノード上の **log** と呼ばれるアプリケーションのフォルダーに書き込まれます (前の例の検索場所を参照)。

**注:** アプリケーションのフェールオーバーに影響する可能性があるため、運用環境でデプロイされたアプリケーションのコンソール リダイレクト ポリシーは決して使用しないでください。ローカル開発とデバッグ目的のため**だけ**に使用します。

次の例では、FileRetentionCount 値を使用したコンソール リダイレクトの設定を示しています。

~~~
<SetupEntryPoint>
    <ExeHost>
    <Program>MySetup.bat</Program>
    <WorkingFolder>CodePackage</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="10"/>
    </ExeHost>
</SetupEntryPoint>
~~~

ここで **Echo** コマンドを書き込むように、MySetup.ps1 ファイルを変更すると、これはデバッグのために出力ファイルに書き込みます。

~~~
Echo "Test console redirection which writes to the application log folder on the node that the application is deployed to"
~~~

**自分のスクリプトをデバッグしたら、すぐにこのコンソール リダイレクト ポリシーを削除してください**

## サービス コード パッケージのポリシーを構成する 
前の手順では、SetupEntryPoint に RunAs ポリシーを適用する方法を説明しました。ここでは、サービス ポリシーとして適用できる別のプリンシパルを作成する方法をもう少し詳しく説明します。

### ローカル ユーザー グループの作成
ユーザー グループを定義して作成し、ユーザーをグループに追加できます。これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に、特に便利です。次の例では、管理者特権を持つローカル グループ **LocalAdminGroup** を示します。2 人のユーザー、Customer1 と Customer2 がこのローカル グループのメンバーになっています。

~~~
<Principals>
 <Groups>
   <Group Name="LocalAdminGroup">
     <Membership>
       <SystemGroup Name="Administrators"/>
     </Membership>
   </Group>
 </Groups>
  <Users>
     <User Name="Customer1">
        <MemberOf>
           <Group NameRef="LocalAdminGroup" />
        </MemberOf>
     </User>
    <User Name="Customer2">
      <MemberOf>
        <Group NameRef="LocalAdminGroup" />
      </MemberOf>
    </User>
  </Users>
</Principals>
~~~

### ローカル ユーザーの作成
アプリケーション内のサービスをセキュリティで保護するために使用できるローカル ユーザーを作成できます。アプリケーション マニフェストの Principals セクションでアカウントの種類として **LocalUser** が指定されている場合、Service Fabric はアプリケーションがデプロイされているコンピューターにローカル ユーザー アカウントを作成します。既定では、これらのアカウントには、アプリケーション マニフェストで指定されているものと同じ名前は付きません (例: 次の例の Customer3)。代わりに、動的に生成され、ランダムなパスワードが与えられます。

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~

<!-- If an application requires that the user account and password be same on all machines (for example, to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true. The cluster manifest must also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

### サービス コード パッケージにポリシーを割り当てる
**ServiceManifestImport** の **RunAsPolicy** セクションでは、コード パッケージの実行に使用されるアカウントが Principals セクションから指定されます。また、Principals セクションのユーザー アカウントとサービス マニフェストのパッケージが関連付けられます。これは、Setup または Main エントリ ポイントに対して指定することも、`All` を指定して両方に適用することもできます。次の例では、異なるポリシーの適用を示します。

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

**EntryPointType** が指定されていない場合は、既定で EntryPointType=”Main” に設定されます。**SetupEntryPoint** の指定は、高い特権を必要とする特定のセットアップ操作をシステム アカウントで実行するときに特に便利です。実際のサービス コードは低い特権のアカウントで実行できます。

### すべてのサービス コード パッケージに既定のポリシーを適用する
**DefaultRunAsPolicy** セクションは、特定の **RunAsPolicy** が定義されていないすべてのコード パッケージに既定のユーザー アカウントを指定するために使用します。アプリケーションによって使用されるサービス マニフェストで指定されているほとんどのコード パッケージが同じユーザーで実行される必要がある場合、そのユーザー アカウントで既定の RunAs ポリシーのみを定義することができます。次の例では、コード パッケージで **RunAsPolicy** が指定されていない場合、コード パッケージは Principals セクションで指定されている **MyDefaultAccount** を使用して実行するように指定しています。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~
### Active Directory ドメイン グループまたはユーザーを使用する
スタンドアロン インストーラーを使用して Windows Server にインストールされた Service Fabric では、Active Directory (AD) ユーザーまたはグループ アカウントの資格情報でサービスを実行することができます。注: これは、ドメイン内のオンプレミスの Active Directory であり、Azure Active Directory (AAD) ではありません。ドメイン ユーザーまたはグループを使用すると、アクセス許可が付与されている、ドメイン内の他のリソース (ファイル共有など) にアクセスできます。

次の例は、*TestUser* という AD ユーザーと、*MyCert* という証明書を使用して暗号化されたドメイン パスワードを示しています。`Invoke-ServiceFabricEncryptText` Powershell コマンドを使用して、シークレット暗号化テキストを作成できます。作成方法の詳細については、「[Service Fabric アプリケーションでのシークレットの管理](service-fabric-application-secret-management.md)」を参照してください。パスワードの暗号化を解除するための証明書の秘密キーは、ローカル コンピューターにアウトオブバンド方式でデプロイされる必要があります (Azure では Resource Manager を使用します)。これで、Service Fabric は、サービス パッケージをマシンにデプロイする際に、シークレットの暗号化を解除し、ユーザー名とこれらの資格情報で実行するように AD による認証を実行できるようになります。

~~~
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
~~~


## HTTP と HTTPS エンドポイントの SecurityAccessPolicy を割り当てる
サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルを使用するエンドポイント リソースが宣言されている場合は、これらのエンドポイントに割り当てられているポートに、サービスが実行する RunAs ユーザー アカウントのアクセス制御リストが正しく適用されるように、**SecurityAccessPolicy** を指定する必要があります。それ以外の場合は、**http.sys** はサービスにアクセスできず、クライアントからの呼び出しで失敗します。次の例では、Customer3 アカウントを **ServiceEndpointName** エンドポイントに適用し、フル アクセス権限を付与しています。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

HTTPS エンドポイントの場合、クライアントに返す証明書の名前も指示する必要があります。そのために **EndpointBindingPolicy** を利用できます。アプリケーション マニフェストの certificates セクションで証明書が定義されています。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
  <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
  <!--EndpointBindingPolicy is needed if the EndpointName is secured with https -->
  <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
</Policies
~~~


## 完全なアプリケーション マニフェストの例
次のアプリケーション マニフェストでは、さまざまな設定を示しています。

~~~
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application3Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="Stateless1_InstanceCount" DefaultValue="-1" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="Stateless1Pkg" ServiceManifestVersion="1.0.0" />
      <ConfigOverrides />
      <Policies>
         <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
         <RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup" />
        <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
         <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
        <!--EndpointBindingPolicy is needed the EndpointName is secured with https -->
        <EndpointBindingPolicy EndpointRef="EndpointName" CertificateRef="Cert1" />
     </Policies>
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="Stateless1">
         <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
   <Principals>
      <Groups>
         <Group Name="LocalAdminGroup">
            <Membership>
               <SystemGroup Name="Administrators" />
            </Membership>
         </Group>
      </Groups>
      <Users>
         <User Name="LocalAdmin">
            <MemberOf>
               <Group NameRef="LocalAdminGroup" />
            </MemberOf>
         </User>
         <!--Customer1 below create a local account that this service runs under -->
         <User Name="Customer1" />
         <User Name="MyDefaultAccount" AccountType="NetworkService" />
      </Users>
   </Principals>
   <Policies>
      <DefaultRunAsPolicy UserRef="LocalAdmin" />
   </Policies>
   <Certificates>
	 <EndpointCertificate Name="Cert1" X509FindValue="FF EE E0 TT JJ DD JJ EE EE XX 23 4T 66 "/>
  </Certificates>
</ApplicationManifest>
~~~


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_0928_2016-->