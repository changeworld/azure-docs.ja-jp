---
title: Azure Service Fabric Mesh の用語 | Microsoft Docs
description: Azure Service Fabric Mesh でよく使われる用語について説明します。
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7c3ce5571c54d6c613114ea49999e450934c8ff4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136200"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric Mesh の用語

Azure Service Fabric Mesh は、仮想マシン、ストレージ、ネットワークを管理することなく開発者がマイクロサービス アプリケーションをデプロイできるフル マネージド サービスです。 この記事では、Azure Service Fabric Mesh 関連ドキュメントで使用される用語の意味を理解するうえで参考となるように、Service Fabric で使用される用語について詳しく説明します。

## <a name="service-fabric"></a>Service Fabric

Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる、オープン ソースの分散システム プラットフォームです。 Service Fabric は Service Fabric Mesh の動力となるオーケストレーターです。 Service Fabric には、マイクロサービス アプリケーションをビルドし、実行するためのオプションがあります。 あらゆるフレームワークを利用してサービスを記述したり、複数の環境からアプリケーションを実行する場所を選択したりできます。

## <a name="deployment-and-application-models"></a>デプロイとアプリケーション モデル 

サービスをデプロイするには、その実行方法を記述する必要があります。 Service Fabric では、3 つの異なるデプロイ モデルをサポートしています。

### <a name="resource-model"></a>リソース モデル
アプリケーション、サービス、ネットワーク、ボリュームなど、Service Fabric に個別にデプロイできるあらゆるものがリソースです。 リソースは、Azure Resource Model スキーマと YAML ファイルまたは JSON ファイルを使用することで定義されます。

リソース モデルは、Service Fabric アプリケーションを記述する最も簡単な方法です。 その中心は、コンテナー化されたサービスの単純なデプロイと管理にあります。

リソースは Service Fabric が実行されているあらゆる場所にデプロイできます。

### <a name="native-model"></a>ネイティブ モデル
ネイティブ アプリケーション モデルからアプリケーションに Service Fabric への完全な低レベル アクセスが与えられます。 アプリケーションとサービスは、XML マニフェスト ファイルに登録済みタイプとして定義されます。

ネイティブ モデルは Reliable Services フレームワークに対応しています。このフレームワークは、C# と Java で、Service Fabric ランタイム API とクラスター管理 API へのアクセスを提供します。 ネイティブ モデルは任意のコンテナーと実行可能ファイルにも対応しています。

Service Fabric Mesh 環境では、ネイティブ モデルがサポートされていません。

### <a name="docker-compose"></a>Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) は Docker プロジェクトの一部です。 Service Fabric は、Docker Compose モデルを利用したアプリケーションのデプロイに一部対応していますす。

## <a name="environments"></a>環境

Service Fabric はオープンソースのプラットフォーム テクノロジであり、いくつかのサービスと製品がこれを基盤としています。 Microsoft では、次のオプションを提供しています。

 - **Service Fabric Mesh**: Microsoft Azure で Service Fabric アプリケーションを実行するための完全マネージド サービス。
 - **Azure Service Fabric**: Azure がホストする Service Fabric クラスター サービス。 Service Fabric と Azure インフラストラクチャの統合や、Service Fabric クラスターのアップグレードと構成管理を提供します。
 - **Service Fabric スタンドアロン**: さまざまな場所に (オンプレミスまたはクラウド プロバイダーに) [Service Fabric クラスターをデプロイする](/azure/service-fabric/service-fabric-deploy-anywhere)ための一連のインストール ツールと構成ツール。 Azure によって管理されません。
 - **Service Fabric 開発クラスター**: Windows、Linux、Mac で Service Fabric アプリケーションをローカル開発できます。

## <a name="environment-framework-and-deployment-model-support-matrix"></a>環境、フレームワーク、デプロイ モデルのサポート マトリックス
環境が異なると、フレームワークやデプロイ モデルのサポート レベルも異なります。 次の表では、サポートされているフレームワーク/デプロイ モデルの組み合わせをまとめてあります。

| アプリケーションの種類 | 記述モデル | Azure Service Fabric Mesh | Azure サービス クラスター (任意の OS)| ローカル クラスター - Windows | ローカル クラスター - Linux | ローカル クラスター - Mac | スタンドアロン クラスター (Windows)
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric Mesh アプリケーション | リソース モデル (YAML と JSON) | サポートされています |サポートされていません | サポートされています |サポートされていません | サポートされていません | サポートされていません |
|Service Fabric ネイティブ アプリケーション | ネイティブ アプリケーション モデル (XML) | サポートされていません| サポートされています|サポートされています|サポートされています|サポートされています|サポートされています|

次の表では、Service Fabric に対してさまざまなアプリケーション モデルとそのツールをまとめています。

| アプリケーションの種類 | 記述モデル | Visual Studio 2017 | Visual Studio 2015 | Eclipse | VS Code | SFCTL | AZ CLI | Powershell
|---|---|---|---|---|---|---|---|---|---|
| Service Fabric Mesh アプリケーション | リソース モデル (YAML と JSON) | サポートされています |サポートされていません |サポートされていません |サポートされていません |サポートされていません | サポートされています - Mesh 環境のみ | サポートされていません
|Service Fabric ネイティブ アプリケーション | ネイティブ アプリケーション モデル (XML) | サポートされています| サポートされています|サポートされています|サポートされています|サポートされています|サポートされています|サポートされています|

## <a name="next-steps"></a>次の手順

Service Fabric Mesh の詳細については、概要ページをお読みください。
- [Service Fabric Mesh 概要](service-fabric-mesh-overview.md)
