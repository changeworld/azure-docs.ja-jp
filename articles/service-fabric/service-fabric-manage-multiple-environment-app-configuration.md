---
title: "Azure Service Fabric の複数の環境向けにアプリケーションを管理する | Microsoft Docs"
description: "Azure Service Fabric アプリケーションは、1 台から数千台のコンピューターで構成されたさまざまな規模のクラスターで実行できます。 場合によっては、このようなさまざまな環境に合わせて異なる方法でアプリケーションを構成したい場合があります。 この記事では、環境ごとに異なるアプリケーション パラメーターを定義する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: f406eac9-7271-4c37-a0d3-0a2957b60537
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/09/2017
ms.author: mikhegn
ms.openlocfilehash: 6bc986236d50106485dce151f1f74622f56400c5
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="manage-applications-for-multiple-environments"></a>複数の環境向けのアプリケーションの管理

Azure Service Fabric クラスターは、1 台から数千台のコンピューターを使って作成することができます。 ほとんどの場合、ローカル開発クラスター、共有開発クラスター、運用クラスターなど、複数のクラスター構成にアプリケーションを展開する必要があります。 これらのクラスターはすべて、その中でコードを実行する必要がある異なる環境と見なされます。 アプリケーション バイナリは変更しなくてもこのようなさまざまな環境で実行できますが、通常は、アプリケーションの構成は変える必要があります。

2 つの簡単な例を次に示します。
  - サービスは定義されたポートでリッスンしますが、環境によってそのポートを変える必要があります
  - 環境ごとにデータベースに対して異なるバインド資格情報を指定する必要があります

## <a name="specifying-configuration"></a>構成の指定

指定する構成は、2 つのカテゴリに分類できます。

- サービスの実行方法に適用される構成
  - たとえば、エンドポイントのポート番号や、サービスのインスタンスの数など
  - この構成は、アプリケーションまたはサービスのマニフェスト ファイルで指定します
- アプリケーションのコードに適用される構成
  - たとえば、データベースのバインド情報
  - この構成は、構成ファイルまたは環境変数で指定できます

> [!NOTE]
> アプリケーションおよびサービスのマニフェスト ファイルの属性の中には、パラメーターをサポートしていないものがあります。
> そのような場合は、展開ワークフローの一部として置換文字列に依存する必要があります。 Visual Studio Team Services では、置換トークン https://marketplace.visualstudio.com/items?itemName=qetza.replacetokens のような拡張機能を使うことができます。Jenkins では、スクリプト タスクを実行して値を置き換えることができます。
>

## <a name="specifying-parameters-during-application-creation"></a>アプリケーション作成時のパラメーターの指定

Service Fabric で名前付きアプリケーション インスタンスを作成するときは、パラメーターで渡すことができます。 その方法は、アプリケーション インスタンスの作成方法によって異なります。

  - PowerShell では、[`New-ServiceFabricApplication`](https://docs.microsoft.com/en-us/powershell/module/servicefabric/new-servicefabricapplication?view=azureservicefabricps) コマンドレットはハッシュテーブルとしてアプリケーション パラメーターを受け取ります。
  - sfctl では、[`sfctl application create`](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-sfctl-application#sfctl-application-create) コマンドは JSON 文字列としてパラメーターを受け取ります。 install.sh スクリプトは sfctl を使います。
  - Visual Studio では、アプリケーション プロジェクトの Parameters フォルダーにパラメーター ファイルのセットが用意されています。 これらのパラメーター ファイルは、Visual Studio Team Service または Team Foundation Server を使って、Visual Studio から発行するときに使われます。 Visual Studio では、パラメーター ファイルは Deploy-FabricApplication.ps1 スクリプトに渡されます。

## <a name="next-steps"></a>次の手順
以下の記事では、ここで説明したいくつかの概念の使い方が説明されています。

- [Service Fabric のサービス用に環境変数を指定する方法](service-fabric-how-to-specify-environment-variables.md)
- [Service Fabric でパラメーターを使用してサービスのポート番号を指定する方法](service-fabric-how-to-specify-port-number-using-parameters.md)
- [構成ファイルをパラメーター化する方法](service-fabric-how-to-parameterize-configuration-files.md)

- [環境変数リファレンス](service-fabric-environment-variables-reference.md)
