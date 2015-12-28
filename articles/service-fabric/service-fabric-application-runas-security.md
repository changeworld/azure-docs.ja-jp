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
Service Fabric では、"RunAs" と呼ばれる別のユーザー アカウントを使用して、クラスターで実行しているアプリケーションをセキュリティで保護できます。さらに、そのユーザー アカウントを使用するアプリケーションによって使用されるファイル、ディレクトリ、証明書などのリソースも保護されます。

既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。また、アプリケーションのマニフェストで指定されているローカル ユーザー アカウントを使用してアプリケーションを実行することもできます。RunAs でサポートされるアカウントの種類は、**LocalUser**、**NetworkService**、**LocalService**、**LocalSystem** です。

> [AZURE.NOTE]ドメイン アカウントは、Active Directory が使用可能な Windows Server のデプロイメントでサポートされます。

ユーザー グループを定義して作成し、ユーザーをこのグループに追加してまとめて管理できます。これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に、特に便利です。

## セットアップ EntryPoint に対する RunAs ポリシーの設定

[アプリケーション モデル](service-fabric-application-model.md)で説明したように、**SetupEntryPoint** は、他のエントリポイントの前に、Service Fabric と同じ資格情報で実行する特権を持つエントリ ポイントです (通常は *Network* アカウント)。通常、**EntryPoint** で指定された実行可能ファイルは、実行時間の長いサービス ホストで別々のセットアップのエントリ ポイントを持つことで、長時間にわたって高い権限でサービス ホスト exe を実行することを回避できます。**EntryPoint** で指定された実行可能ファイルは、**SetupEntryPoint** が正常に終了した後に実行されます。結果のプロセスは、終了またはクラッシュした場合に、監視されて再起動されます (**SetupEntryPoint** で再起動)。

サービスの SetupEntryPoint およびメイン EntryPoint を示す簡単なサービス マニフェストの例を次に示します。

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

### RunAs ポリシーの構成

サービスに SetupEntryPoint を構成した後は、サービスの実行に使用するセキュリティ アクセス許可をアプリケーション マニフェストで変更できます。次の例では、管理者アカウントの特権で実行するようにサービスを構成する方法を示します。

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

次に、**ServiceManifestImport** セクションで、このプリンシパルを **SetupEntryPoint** に適用するためのポリシーを構成します。これにより、MySetup.bat ファイルは Administrator 特権で実行する必要があることを Service Fabric に通知します。メイン エントリ ポイントに対してポリシーを適用*しない*と、MyServiceHost.exe のコードは、すべてのサービス エントリ ポイントが RunAs で実行する既定のアカウントであるシステム NetworkService アカウントで実行します。

それでは、MySetup.bat ファイルを Visual Studio プロジェクトに追加して、Administrator 特権をテストしてみます。Visual Studio でサービス プロジェクトを右クリックし、MySetup.bat の新しいファイル呼び出しを追加します。次に、このファイルをサービス パッケージに含める必要があります。既定では含まれません。MySetup.bat ファイルをパッケージに含めるには、ファイルを右クリックしてコンテキスト メニューを表示し、[プロパティ] を選択して、[プロパティ] ダイアログで **[出力ディレクトリにコピー]** を **[新しい場合はコピーする]** に設定します。これを示したものが下記のスクリーンショットです。

![SetupEntryPoint バッチ ファイルの Visual Studio CopyToOutput][Image1]

次に、MySetup.bat ファイルを開き、次のコマンドを追加します。

~~~
REM Set a system environment variable.This requires administrator privilege
setx -m TestVariable "MyValue"
echo System TestVariable set to > test.txt
echo %TestVariable% >> test.txt

REM To delete this system variable us
REM REG delete "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v TestVariable /f
~~~

次に、ソリューションをビルドして、開発用のローカル クラスターにデプロイします。サービスが開始した後、Service Fabric Explorer で示されるように、MySetup.bat が成功したことを 2 つの方法で確認できます。PowerShell コマンド プロンプトを起動し、次を入力します。

~~~
PS C:\ [Environment]::GetEnvironmentVariable("TestVariable","Machine") MyValue
~~~

もう 1 つの方法は、サービスがデプロイされて開始されたノードの名前を Service Fabric Explorer で確認し (例: Node 1)、アプリケーション インスタンスの作業フォルダーに移動して、out.txt ファイルで **TestVariable** の値を調べます。たとえば、これが Node 2 にデプロイされた場合、MyApplicationType の次のパスに移動します。

~~~
C:\SfDevCluster\Data\_App\Node.2\MyApplicationType_App\work\out.txt
~~~

##  SetupEntryPoint からの PowerShell コマンドの起動
**SetupEntryPoint** ポイントから PowerShell を実行するには、PowerShell ファイルを指し示すバッチ ファイルで PowerShell.exe を実行します。最初に、PowerShell ファイル (例: MySetup.ps1) をサービス プロジェクトに追加します。このファイルがサービス パッケージにも含まれるように、*[新しい場合はコピーする]* プロパティを忘れずに設定します。システム環境変数 *TestVariable* を設定する PowerShell ファイル MySetup.ps1 を起動するバッチ ファイルの例を次に示します。

PowerShell ファイルを起動するための MySetup.bat。

~~~
powershell.exe -ExecutionPolicy Bypass -Command ".\MySetup.ps1"
~~~

PowerShell ファイルで、システム環境変数を設定するために次を追加します。

```
[Environment]::SetEnvironmentVariable("TestVariable", "MyValue", "Machine")
[Environment]::GetEnvironmentVariable("TestVariable","Machine") > out.txt
```

## サービスへの RunAsPolicy の適用 
上の手順では、SetupEntryPoint に RunAs ポリシーを適用する方法を説明しました。ここでは、サービス ポリシーとして適用できる別のプリンシパルを作成する方法をもう少し詳しく説明します。

### ローカル ユーザー グループの作成
ユーザー グループを定義して作成し、ユーザーをこのグループに追加できます。これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に、特に便利です。次の例では、Administrator 特権を持つローカル グループ **LocalAdminGroup** を示します。2 人のユーザー Customer1 と Customer2 がこのローカル グループのメンバーになっています。

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
アプリケーション内のサービスをセキュリティで保護するために使用できるローカル ユーザーを作成できます。アプリケーション マニフェストの Principals セクションでアカウントの種類として LocalUser が指定されている場合、Service Fabric はアプリケーションがデプロイされているコンピューターにローカル ユーザー アカウントを作成します。既定では、これらのアカウントにはアプリケーション マニフェストで指定されている同じ名前は設定されず (たとえば、次の例では “Customer3”)、名前は動的に生成されてランダムなパスワードが設定されます。

~~~
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
~~~
 
<!-- If an application requires that the user account and password be same on all machines (e.g. to enable NTLM authentication), the cluster manifest must set NTLMAuthenticationEnabled to true and also specify an NTLMAuthenticationPasswordSecret that will be used to generate the same password across all machines.

<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
-->

## サービス コード パッケージへのポリシーの割り当て
**ServiceManifestImport** の **RunAsPolicy** セクションでは、コード パッケージの実行に使用する必要がある Principals セクションのアカウントが指定されており、サービス マニフェストのコード パッケージと Principals セクションのユーザー アカウントが関連付けられています。これは、Setup または Main エントリ ポイントに対して指定することも、または All を指定して両方に適用することもできます。次の例では、異なるポリシーの適用を示します。

~~~
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
~~~

**EntryPointType** が指定されていない場合は、既定で EntryPointType=”Main” に設定されます。**SetupEntryPoint** の指定は、高い特権を必要とする特定のセットアップ操作はシステム アカウントで実行する必要があり、実際のサービス コードは低い特権のアカウントで実行できる場合に、特に便利です。

### すべてのサービス コード パッケージへの既定ポリシーの適用
**DefaultRunAsPolicy** セクションは、特定の **RunAsPolicy** が定義されていないすべてのコード パッケージに対する既定のユーザー アカウントを指定するために使用します。アプリケーションによって使用されるサービス マニフェストで指定されているほとんどのコード パッケージが同じ RunAs ユーザーで実行する必要がある場合、すべてのコード パッケージに **RunAsPolicy** を指定する代わりに、そのユーザー アカウントで既定の RunAs ポリシーを定義するだけで済ますことができます。たとえば、次の例では、コード パッケージで **RunAsPolicy** が指定されていない場合、コード パッケージは Principals セクションで指定されている MyDefaultAccount を使用して実行するように指定しています。

~~~
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
~~~

## http および https エンドポイントに対する SecurityAccessPolicy の割り当て
サービスに RunAs ポリシーを適用し、サービス マニフェストで http プロトコルを使用するエンドポイント リソースが宣言されている場合は、**SecurityAccessPolicy** を指定して、これらのエンドポイントに割り当てられているポートに、サービスが実行する RunAs ユーザー アカウントの ACL が正しく適用されるようにする必要があります。そうしないと、http.sys はサービスにアクセスできず。クライアントからの呼び出しは失敗します。次の例では、Customer3 アカウントを *ServiceEndpointName* エンドポイントに適用し、フル アクセス権限を付与しています。

~~~
<Policies>
   <RunAsPolicy CodePackageRef="Code" UserRef="Customer1" />
   <!--SecurityAccessPolicy is needed if RunAsPolicy is defined and the Endpoint is http -->
   <SecurityAccessPolicy ResourceRef="EndpointName" PrincipalRef="Customer1" />
</Policies>
~~~

https エンドポイントの場合は、**EndpointBindingPolicy** のクライアントに返す証明書の名前も指定する必要があります。これは、アプリケーション マニフェストの certificates セクションで定義されています。

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
* [サービス マニフェストでのリソースの指定](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png

<!---HONumber=AcomDC_1217_2015-->