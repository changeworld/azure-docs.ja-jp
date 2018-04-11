---
title: システムおよびローカル セキュリティ アカウントのもとで Azure Service Fabric サービスを実行する | Microsoft Docs
description: システムおよびローカル セキュリティ アカウントのもとで Service Fabric アプリケーションを実行する方法について説明します。  サービスを安全に実行するために、セキュリティ プリンシパルを作成し、RunAs ポリシーを適用します。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/21/2018
ms.author: mfussell
ms.openlocfilehash: 3df5374911ee6381f25d08d23d565cdf8a7cd12f
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2018
---
# <a name="run-a-service-as-a-local-user-account-or-local-system-account"></a>ローカル ユーザー アカウントまたはローカル システム アカウントとしてサービスを実行する
Azure Service Fabric を使用すると、別のユーザー アカウントを使用してクラスターで実行しているアプリケーションをセキュリティで保護することができます。 既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。 Service Fabric はまた、ローカル ユーザー アカウントまたはローカル システム アカウントのもとでアプリケーションを実行する機能も提供します。これは、アプリケーション マニフェスト内で RunAs ポリシーを指定することによって実行されます。 サポートされているローカル システム アカウントの種類は、**LocalUser**、**NetworkService**、**LocalService**、**LocalSystem** です。  Windows スタンドアロン クラスターで Service Fabric を実行している場合は、[Active Directory ドメイン アカウント](service-fabric-run-service-as-ad-user-or-group.md)または[グループ管理サービス アカウント](service-fabric-run-service-as-gmsa.md)でサービスを実行することができます。

また、1 人以上のユーザーを各グループに追加してまとめて管理できるように、ユーザー グループを定義および作成することもできます。 これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。

> [!NOTE] 
> サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルのエンドポイント リソースを宣言する場合は、**SecurityAccessPolicy** を指定する必要があります。  詳細については、[HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる](service-fabric-assign-policy-to-endpoint.md)をご覧ください。 
>

## <a name="create-local-user-groups"></a>ローカル ユーザー グループの作成
ユーザー グループを定義して作成し、1 人以上のユーザーをグループに追加できます。 これは、異なるサービス エントリ ポイントに対して複数のユーザーが存在し、グループ レベルで使用できる特定の共通の特権が必要な場合に便利です。 次の例では、管理者特権を持つローカル グループ **LocalAdminGroup** を示します。 このアプリケーション マニフェストの例では、2 人のユーザー Customer1 と Customer2 がこのローカル グループのメンバーになっています。

```xml
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
```

## <a name="create-local-users"></a>ローカル ユーザーの作成
アプリケーション内のサービスをセキュリティで保護するために使用できるローカル ユーザーを作成できます。 アプリケーション マニフェストの Principals セクションでアカウントの種類として **LocalUser** が指定されている場合、Service Fabric はアプリケーションがデプロイされているコンピューターにローカル ユーザー アカウントを作成します。 既定では、これらのアカウントにアプリケーション マニフェストで指定されているものと同じ名前は付けられません (たとえば、次のアプリケーション マニフェストの例では Customer3)。 代わりに、動的に生成され、ランダムなパスワードが与えられます。

```xml
<Principals>
  <Users>
     <User Name="Customer3" AccountType="LocalUser" />
  </Users>
</Principals>
```

アプリケーションで (たとえば、NTLM 認証を有効にするために) すべてのマシンでユーザー アカウントとパスワードが同じである必要がある場合は、クラスター マニフェストで **NTLMAuthenticationEnabled** を true に設定する必要があります。 また、クラスター マニフェストでは、すべてのマシンにわたって同じパスワードを生成するために使用される **NTLMAuthenticationPasswordSecret** も指定する必要があります。

```xml
<Section Name="Hosting">
      <Parameter Name="EndpointProviderEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationEnabled" Value="true"/>
      <Parameter Name="NTLMAuthenticationPassworkSecret" Value="******" IsEncrypted="true"/>
 </Section>
```

## <a name="assign-policies-to-the-service-code-packages"></a>サービス コード パッケージにポリシーを割り当てる
**ServiceManifestImport** の **RunAsPolicy** セクションでは、コード パッケージの実行に使用されるアカウントが Principals セクションから指定されます。 また、Principals セクションのユーザー アカウントとサービス マニフェストのパッケージが関連付けられます。 これは、Setup または Main エントリ ポイントに対して指定することも、 `All` を指定して両方に適用することもできます。 次の例では、異なるポリシーの適用を示します。

```xml
<Policies>
<RunAsPolicy CodePackageRef="Code" UserRef="LocalAdmin" EntryPointType="Setup"/>
<RunAsPolicy CodePackageRef="Code" UserRef="Customer3" EntryPointType="Main"/>
</Policies>
```

**EntryPointType** が指定されていない場合は、既定で `EntryPointType=”Main”` に設定されます。 **SetupEntryPoint** の指定は、高い特権を必要とする特定のセットアップ操作をシステム アカウントで実行するときに特に便利です。 詳細については、「[サービス スタートアップ スクリプトをローカル ユーザー アカウントまたはローカル システム アカウントとして実行する](service-fabric-run-script-at-service-startup.md)」を参照してください。 実際のサービス コードは低い特権のアカウントで実行できます。

## <a name="apply-a-default-policy-to-all-service-code-packages"></a>すべてのサービス コード パッケージに既定のポリシーを適用する
**DefaultRunAsPolicy** セクションを使用して、特定の **RunAsPolicy** が定義されていないすべてのコード パッケージに既定のユーザー アカウントを指定します。 アプリケーションによって使用されるサービス マニフェストで指定されているほとんどのコード パッケージが同じユーザーで実行される必要がある場合、そのユーザー アカウントで既定の RunAs ポリシーのみを定義することができます。 次の例では、コード パッケージで **RunAsPolicy** が指定されていない場合、コード パッケージは Principals セクションで指定されている **MyDefaultAccount** を使用して実行するように指定しています。

```xml
<Policies>
  <DefaultRunAsPolicy UserRef="MyDefaultAccount"/>
</Policies>
```

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>次の手順
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
