---
title: Azure Service Fabric でサービス用の環境変数を指定する方法 | Microsoft Docs
description: Service Fabric でアプリケーション用の環境変数を使う方法について説明します
documentationcenter: .net
author: mikkelhegn
manager: markfuss
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/06/2017
ms.author: mikhegn
ms.openlocfilehash: 4325b3acd3cbc73ee5976021bebe96c267b2a6dd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206030"
---
# <a name="how-to-specify-environment-variables-for-services-in-service-fabric"></a>Service Fabric のサービス用に環境変数を指定する方法

この記事では、Service Fabric でサービスまたはコンテナーの環境変数を指定する方法を説明します。

## <a name="procedure-for-specifying-environment-variables-for-services"></a>サービス用の環境変数を指定する手順

この例では、コンテナーの環境変数を設定します。 アプリケーションおよびサービス マニフェストは既にあるものとします。

1. ServiceManifest.xml ファイルを開きます。
1. `CodePackage` 要素に、環境変数ごとに新しい `EnvironmentVariables` 要素と `EnvironmentVariable` 要素を追加します。

    ```xml
      <CodePackage Name="MyCode" Version="CodeVersion1">
      ...
        <EnvironmentVariables>
          <EnvironmentVariable Name="MyEnvVariable" Value="DeafultValue"/>
          <EnvironmentVariable Name="HttpGatewayPort" Value="19080"/>
        </EnvironmentVariables>
      </CodePackage>
    ```

    環境変数は、アプリケーション マニフェスト内で上書きできます。

1. アプリケーション マニフェストで環境変数を上書きするには、`EnvironmentOverrides` 要素を使います。

    ```xml
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontEndServicePkg" ServiceManifestVersion="1.0.0" />
        <EnvironmentOverrides CodePackageRef="MyCode">
          <EnvironmentVariable Name="MyEnvVariable" Value="OverrideValue"/>
        </EnvironmentOverrides>
      </ServiceManifestImport>
    ```

## <a name="next-steps"></a>次の手順
この記事で説明されている主要な概念の一部について詳しくは、「[複数の環境のアプリケーション パラメーターを管理する](service-fabric-manage-multiple-environment-app-configuration.md)」をご覧ください。

Visual Studio で使用可能なその他のアプリケーション管理機能については、 [Visual Studio での Service Fabric アプリケーションの管理](service-fabric-manage-application-in-visual-studio.md)に関する記事をご覧ください。