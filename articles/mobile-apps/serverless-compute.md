---
title: Azure Functions とその他のサービスでサーバーレス モバイル アプリケーション バックエンドを構築する
description: 堅牢なサーバーレス モバイル アプリケーション バックエンドを構築するために使用されるコンピューティング サービスについて説明します。
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240145"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>コンピューティング サービスを利用してモバイル バックエンド コンポーネントを構築する
あらゆるモバイル アプリケーションが、データ ストレージ、ビジネス ロジック、セキュリティを担当するバックエンドを必要とします。 バックエンド コードをホストして実行するインフラストラクチャを管理するには、複数のサーバーのサイズ指定、プロビジョニング、およびスケーリングを行う必要があります。 また、OS の更新プログラムと関連するハードウェアを管理し、セキュリティ パッチを適用する必要があります。 さらに、パフォーマンス、可用性、フォールト トレランスのために、これらのインフラストラクチャ コンポーネントをすべて監視する必要があります。 

このような場合に便利なのがサーバーレス アーキテクチャです。管理するサーバー、管理する OS、管理する関連ソフトウェアまたはハードウェアの更新プログラムがないためです。 サーバーレス アーキテクチャにより開発にかかる多くの時間と費用を節約することで、市場に投入するまでの期間を短縮し、アプリケーションの構築にエネルギーを集中させることができます。

## <a name="benefits-of-compute"></a>コンピューティングの利点
- サーバーの抽象化により、ホスティング、パッチ適用、セキュリティに煩わされることがなくなり、コードだけに集中できます。
- 短時間で効率的なスケーリングにより、リソースが自動的にプロビジョニングされるか、必要とする規模でオンデマンド プロビジョニングされます。
- 高い可用性とフォールト トレランス
- マイクロ課金により、コードが実際に実行されたときだけ課金されます。
- クラウドで実行されるコードを任意の言語で記述します。

次のサービスを利用し、モバイル アプリでサーバーレス コンピューティング機能を有効にします。

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) は、任意のプログラミング言語で記述したコードを、サーバーについて気にせずに実行するために使用できる、イベント ドリブン型のコンピューティング エクスペリエンスです。 アプリケーションやアプリケーションを実行するためのインフラストラクチャを管理する必要はありません。 Functions はオンデマンドでスケーリングされ、コードが実行された時間の分だけ支払います。 Azure Functions は、モバイル アプリケーション用の API を実装する優れた方法です。 実装と保守は簡単で、HTTP を介してアクセスできます。

**主な機能**
- イベント駆動型であり、スケーラブル。トリガーとバインドを使用して、関数が呼び出されるタイミングと関数が接続するデータを定義できます。
- Functions は NuGet と NPM をサポートしているため、独自の依存関係を使用できます。これにより、お気に入りのライブラリを使用できます。
- セキュリティの統合により、Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーを使用して、HTTP によってトリガーされる関数を保護できます。
- 簡潔化された統合。さまざまな [Azure サービス](/azure/azure-functions/functions-overview)とサービスとしてのソフトウェア (SaaS) サービスを利用できます。
- 柔軟な開発。関数を Azure portal 内で直接作成したり、継続的インテグレーションを設定し、GitHub や Azure DevOps Services などのサポート対象開発ツールを使用してコードをデプロイしたりできます。
- Functions ランタイムはオープン ソースであり、[GitHub](https://github.com/azure/azure-webjobs-sdk-script) で入手できます。
- 強化された開発体験により、任意のエディターか、ツールや DevOps 機能が組み込まれた監視機能を持つ使いやすい Web インターフェイスを使用して、ローカルでコードの作成、テスト、デバッグができます。
- C#、Node.js、Java、Javascript、Python など、開発用の多彩なプログラミング言語とホスティング オプション。
- 従量課金制の価格モデルにより、コードの実行に要した時間に対してのみ課金されます。

**参照**
- [Azure Portal](https://portal.azure.com)
- [Azure Functions のドキュメント](/azure/azure-functions/)
- [Azure Functions 開発者ガイド](/azure/azure-functions/functions-reference)
- [クイック スタート](/azure/azure-functions/functions-create-first-function-vs-code)
- [サンプル](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) を使用すると、インフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションや RESTful API を構築し、ホストできます。 Azure Web Apps では、自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。

**主な機能**
- 複数の言語とフレームワーク (ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、Python) のサポート。 また、PowerShell などのスクリプトや実行可能ファイルをバックグラウンド サービスとして実行できます。
- Azure DevOps、GitHub、BitBucket、Docker Hub、または Azure Container Registry を使用した継続的インテグレーションと継続的デプロイによって実現される DevOps の最適化。 App Service でのアプリの管理には、Azure PowerShell またはクロスプラットフォーム コマンド ライン インターフェイス (CLI) を使用します。
- 高可用性を備えたグローバル スケーリングにより、手動または自動でスケールアップまたはスケールアウトできます。
- SaaS プラットフォームおよびオンプレミス データへの接続により、エンタープライズ システム (SAP など)、SaaS サービス (Salesforce など)、インターネット サービス (Facebook など) 向けに用意された 50 を超えるコネクタから選択できます。 また、ハイブリッド接続と Azure Virtual Networks を使用して、オンプレミスのデータにアクセスできます。
- Azure App Service は ISO、SOC、PCI に準拠しています。 Azure Active Directory またはソーシャル メディア (Google、Facebook、Twitter、Microsoft など) のサインインでユーザーを認証します。 IP アドレス制限を作成し、サービス ID を管理します。
- WordPress、Joomla、Drupal など、Azure Marketplace の豊富なアプリケーション テンプレートの一覧からアプリケーション テンプレートを選択します。
- Visual Studio との統合により、Visual Studio の専用ツールを利用して、作成、デプロイ、デバッグの作業を効率化します。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [Azure App Service のドキュメント](/azure/app-service/)
- [クイック スタート](/azure/app-service/app-service-web-get-started-dotnet)
- [サンプル](/azure/app-service/samples-cli)
- [チュートリアル](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) では、ホストされている Kubernetes 環境を管理します。 AKS では、コンテナー オーケストレーションの専門知識がなくても、コンテナー化されたアプリケーションを迅速かつ簡単にデプロイして管理できます。 また、継続的な操作やメンテナンスの負担もなくなります。 AKS は、アプリケーションをオフラインにすることなく、リソースをオンデマンドでプロビジョニング、アップグレード、スケーリングします。

**主な機能**
- コンテナーに既存のアプリケーションを簡単に移行し、AKS 内で実行できます。
- マイクロサービスを基盤にしたアプリケーションのデプロイと管理がシンプルになります。
- AKS 用のセキュリティで保護された DevOps により、スピードとセキュリティのバランスがとれ、大規模なコードを短期間で作成できます。
- AKS と Azure Container Instances を使用して、数秒で開始する Container Instances 内にポッドをプロビジョニングすることで、簡単にスケーリングできます。
- IoT デバイスをオンデマンドでデプロイおよび管理できます。
- TensorFlow や KubeFlow などのツールを使用して機械学習モデルをトレーニングできます。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [Azure Kubernetes Service のドキュメント](/azure/aks/)
- [クイック スタート](/azure/aks/kubernetes-walkthrough-portal)
- [チュートリアル](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの優れたソリューションです。 VM を管理することなく、アプリを短期間で開発します。

**主な機能**
- 短い起動時間。Container Instances を使用すると、VM をプロビジョニングして管理する必要なく、数秒で Azure でコンテナーを開始できます。
- パブリック IP 接続とカスタム DNS 名。
- VM 内の場合と同じように、コンテナー内でのアプリケーションの分離を保証するハイパーバイザーレベルのセキュリティ。
- CPU のコア数とメモリを厳密に指定することで得られる、カスタム サイズによる最適な稼働率。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。
- 状態を取得して永続化するための永続的ストレージ。 Container Instances は Azure Files 共有の直接マウントを提供します。
- 同じ API でスケジュールされる Linux と Windows のコンテナー。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [Azure Container Instances のドキュメント](/azure/container-instances/)
- [クイック スタート](/azure/container-instances/container-instances-quickstart-portal)
- [サンプル](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [チュートリアル](/azure/container-instances/container-instances-tutorial-prepare-app)