---
title: gMSA アカウントで Azure Service Fabric サービスを実行する | Microsoft Docs
description: Service Fabric の Windows スタンドアロン クラスターで、gMSA としてサービスを実行する方法を説明します。
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: mfussell
ms.openlocfilehash: 5f93285061708172b9b6ac40dc97fce08f7b2a86
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>グループ管理サービス アカウントでのサービスの実行
Windows Server のスタンドアロン クラスターで、RunAs ポリシーを使用してサービスをグループ管理サービスアカウント (gMSA) として実行できます。  既定では、Service Fabric アプリケーションは、Fabric.exe プロセスを実行しているアカウントで実行されます。 異なるアカウントで実行中のアプリケーションは、共有のホスト環境にある場合でも、互いからより強固に保護されることになります。 これは、ドメイン内のオンプレミスの Active Directory を使用しており、Azure Active Directory (Azure AD) ではないことに注意してください。 gMSA を使用することで、パスワードや暗号化されたパスワードがアプリケーション マニフェストに格納されることがなくなります。  また、[Active Directory ユーザーまたはグループ](service-fabric-run-service-as-ad-user-or-group.md)としてサービスを実行することもできます。

次の例では、*svc-Test$* という名前の gMSA アカウントを作成する方法、クラスター ノードにグループ管理サービス アカウントをデプロイする方法、およびユーザー プリンシパルを構成する方法を示します。

前提条件:
- ドメインには KDS ルート キーが必要です。
- ドメインが Windows Server 2012 以降の機能レベルである必要があります。

1. Active Directory ドメイン管理者に、`New-ADServiceAccount` コマンドレットを使用してグループ管理サービス アカウントを作成してもらい、すべてのサービス ファブリック クラスター ノードが `PrincipalsAllowedToRetrieveManagedPassword` に含まれるようにします。 `AccountName`、`DnsHostName`、および `ServicePrincipalName` は一意である必要があります。

    ```poweshell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Service Fabric サービス ノードのそれぞれ (例: `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`) で、gMSA をインストールしてテストします。
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. ユーザー プリンシパルを構成し、そのユーザーを参照する RunAsPolicy を構成します。
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> サービスに RunAs ポリシーを適用し、サービス マニフェストで HTTP プロトコルのエンドポイント リソースを宣言する場合は、**SecurityAccessPolicy** を指定する必要があります。  詳細については、[HTTP と HTTPS エンドポイントのセキュリティ アクセス ポリシーを割り当てる](service-fabric-assign-policy-to-endpoint.md)をご覧ください。 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
次の手順については、次の記事を参照してください。
* [アプリケーション モデルを理解する](service-fabric-application-model.md)
* [サービス マニフェストにリソースを指定する](service-fabric-service-manifest-resources.md)
* [アプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
