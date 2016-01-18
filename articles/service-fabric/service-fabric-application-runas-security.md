<properties
   pageTitle="Service Fabric アプリケーションの RunAs セキュリティ ポリシーの概要 | Microsoft Azure"
   description="アプリケーションが開始前に特権アクションを実行する必要がある SetupEntry ポイントを含む Service Fabric アプリケーションをシステムおよびローカル セキュリティ アカウントで実行する方法の概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="bscholl"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/24/2015"
   ms.author="mfussell"/>

# RunAs: 異なるセキュリティ アクセス許可での Service Fabric アプリケーションの実行
Azure Service Fabric では、**RunAs** と呼ばれる別のユーザー アカウントを使用して、クラスターで実行しているアプリケーションをセキュリティで保護できます。また、そのユーザー アカウントを使用するアプリケーションによって使用されるファイル、ディレクトリ、証明書などのリソースも保護されます。

既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。また、アプリケーションのマニフェストで指定されているローカル ユーザー アカウントを使用してアプリケーションを実行することもできます。RunAs でサポートされるアカウントの種類は、**LocalUser**、**NetworkService**、**LocalService**、**LocalSystem** です。

> [AZURE.NOTE]ドメイン アカウントは、Azure Active Directory が使用可能な Windows Server のデプロイメントでサポートされます。

ユーザー グループを定義して作成し、ユーザーをそのグループに追加してまとめて管理できます。これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に、特に便利です。

## SetupEntryPoint の RunAs ポリシーを設定する

[アプリケーション モデル](service-fabric-application-model.md)で説明したように、**SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *network* アカウント)。通常、**EntryPoint** で指定された実行可能ファイルは、実行時間の長いサービス ホストで別々のセットアップのエントリ ポイントを持つことで、長時間にわたって高い権限でサービス ホスト実行可能ファイルを実行することを回避できます。**EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (**SetupEntryPoint** で再起動)。

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

### RunAs ポリシーを構成する

サービスにセットアップ エントリ ポイントを構成した後は、サービスの実行に使用するセキュリティ アクセス許可をアプリケーション マニフェストで変更できます。次の例では、管理者アカウントの特権で実行するようにサービスを構成する方法を示します。

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

次に、**ServiceManifestImport** セクションで、このプリンシパルを **SetupEntryPoint** に適用するためのポリシーを構成します。これにより、**MySetup.bat** ファイルは管理者特権で実行する必要があることを Service Fabric に通知します。メイン エントリ ポイントに対してポリシーを適用*しない*と、**MyServiceHost.exe** のコードは、システム **NetworkService** アカウントで実行します。これはすべてのサービス エントリ ポイントが RunAs で実行する既定のアカウントです。

それでは、MySetup.bat ファイルを Visual Studio プロジェクトに追加して、Administrator 特権をテストしてみます。Visual Studio でサービス プロジェクトを右クリックし、MySetup.bat の新しいファイル呼び出しを追加します。

次に、サービス パッケージに MySetup.bat ファイルが含まれていることを確認します。既定では、含まれていません。ファイルを選択し、右クリックしてコンテキスト メニューを表示し、**[プロパティ]** を選択します。[プロパティ] ダイアログ ボックスで、**[出力ディレクトリにコピー]** が **[新しい場合はコピーする]** に設定されていることを確認します。これを示したものが下記のスクリーンショットです。

![SetupEntryPoint バッチ ファイルの Visual Studio CopyToOutput][image1]

次に、MySetup.bat ファイルを開き、次のコマンドを追加します。

~~~
REM Set a system environment variable. This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

次に、ソリューションをビルドして、開発用のローカル クラスターにデプロイします。サービスが開始した後、Service Fabric Explorer で示されるように、MySetup.bat が成功したことを 2 つの方法で確認できます。PowerShell コマンド プロンプトを起動し、次を入力します。

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine")
MyValue
~~~

サービスがデプロイされ、Service Fabric エクスプローラーで開始したノードの名前をメモします (たとえば、Node 1)。次に、アプリケーション インスタンスの作業フォルダーに移動し、**TestVariable** の値を示す out.txt ファイルを探します。たとえば、これが Node 2 にデプロイされた場合、**MyApplicationType** の次のパスに移動します。

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  SetupEntryPoint から PowerShell コマンドを起動する
**SetupEntryPoint** ポイントから PowerShell を実行するには、PowerShell ファイルを指し示すバッチ ファイルで **PowerShell.exe** を実行します。最初に、PowerShell ファイルをサービス プロジェクトに追加します (例: **MySetup.ps1**)。このファイルがサービス パッケージにも含まれるように、*[新しい場合はコピーする]* プロパティを忘れずに設定します。システム環境変数 **TestVariable** を設定する PowerShell ファイル MySetup.ps1 を起動するバッチ ファイルの例を次に示します。

PowerShell ファイルを起動するための MySetup.bat。

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

PowerShell ファイルで、システム環境変数を設定するために次を追加します。

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## サービスに RunAsPolicy を適用する
上の手順では、SetupEntryPoint に RunAs ポリシーを適用する方法を説明しました。ここでは、サービス ポリシーとして適用できる別のプリンシパルを作成する方法をもう少し詳しく説明します。

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
アプリケーション内のサービスをセキュリティで保護するために使用できるローカル ユーザーを作成できます。アプリケーション マニフェストの Principals セクションでアカウントの種類として **LocalUser** が指定されている場合、Service Fabric はアプリケーションがデプロイされているコンピューターにローカル ユーザー アカウントを作成します。既定では、これらのアカウントには、アプリケーション マニフェストで指定されているものと同じ名前は与えられません (例: 下のサンプルの Customer3)。代わりに、動的に生成され、ランダムなパスワードが与えられます。

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

## サービス コード パッケージにポリシーを割り当てる
**ServiceManifestImport** の **RunAsPolicy** セクションにより、コード パッケージの実行に使用されるアカウントが Principals セクションから指定されます。また、Principals セクションのユーザー アカウントとサービス マニフェストのパッケージが関連付けられます。これは、Setup または Main エントリ ポイントに対して指定することも、または All を指定して両方に適用することもできます。次の例では、異なるポリシーの適用を示します。

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

**EntryPointType** が指定されていない場合は、既定で EntryPointType=”Main” に設定されます。**SetupEntryPoint** の指定は、高い特権を必要とする特定のセットアップ操作をシステム アカウントで実行するときに特に便利です。実際のサービス コードは低い特権のアカウントで実行できます。

### すべてのサービス コード パッケージに既定のポリシーを適用する
**DefaultRunAsPolicy** セクションは、特定の **RunAsPolicy** が定義されていないすべてのコード パッケージに対する既定のユーザー アカウントを指定するために使用します。アプリケーションによって使用されるサービス マニフェストで指定されているほとんどのコード パッケージが同じ RunAs ユーザーで実行する必要がある場合、すべてのコード パッケージに **RunAsPolicy** を指定する代わりに、そのユーザー アカウントで既定の RunAs ポリシーを定義するだけで済ますことができます。次の例では、コード パッケージで **RunAsPolicy** が指定されていない場合、コード パッケージは Principals セクションで指定されている **MyDefaultAccount** を使用して実行するように指定しています。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## HTTP と HTTPS エンドポイントの SecurityAccessPolicy を割り当てる
サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルを使用するエンドポイント リソースが宣言されている場合は、**SecurityAccessPolicy** を指定して、これらのエンドポイントに割り当てられているポートに、サービスが実行する RunAs ユーザー アカウントのアクセス制御リストが正しく適用されるようにする必要があります。そうしないと、**http.sys** はサービスにアクセスできず。クライアントからの呼び出しは失敗します。次の例では、Customer3 アカウントを **ServiceEndpointName** エンドポイントに適用し、フル アクセス権限を付与しています。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

HTTPS エンドポイントの場合、クライアントに返す証明書の名前も指示する必要があります。そのために **EndpointBindingPolicy** を利用できます。アプリケーション マニフェストの証明書セクションに証明書を定義します。

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
これまでに説明したさまざまな設定を示すアプリケーション マニフェストの例を次に示します。

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

<!---HONumber=AcomDC_0107_2016-->