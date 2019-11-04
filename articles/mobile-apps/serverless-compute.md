---
title: Azure Functions とその他のサービスでサーバーレス モバイル アプリケーション バックエンドを構築する
description: 堅牢なサーバーレス モバイル アプリケーション バックエンドを構築するためのコンピューティング サービスについて説明します。
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795199"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>コンピューティング サービスを利用してモバイル バックエンド コンポーネントを構築する
あらゆるモバイル アプリケーションが、データ ストレージ、ビジネス ロジック、セキュリティを担当するバックエンドを必要とします。 バックエンド コードをホストし、実行するインフラストラクチャを管理するには、一連のサーバーのサイズ調整、プロビジョニング、スケーリングを行い、ハードウェアを含めた OS の更新を管理し、セキュリティ パッチを適用し、このようなインフラストラクチャ コンポーネントすべてを対象にパフォーマンス、可用性、フォールト トレランスを監視する必要があります。 このような場合に便利なのがサーバーレス アーキテクチャです。開発者が管理するサーバー、OS、関連するソフトウェア/ハードウェアの更新プログラムがないからです。 開発にかかる多くの時間と費用を節約できることで、市場に投入するまでの期間を短縮し、アプリケーションの構築にエネルギーを集中させることができます。

## <a name="benefits-of-compute"></a>コンピューティングの利点
- サーバーの抽象化: ホスティング、パッチ適用、セキュリティに煩わされることがなく、開発者はコードだけに集中できます。
- 短時間で効率的なスケーリングにより、リソースが自動的にプロビジョニングされるか、必要とする規模でオンデマンド プロビジョニングされます。
- 高い可用性とフォールト トレランス
- マイクロ課金により、コードが実際に実行されたときだけ課金されます。
- 自分で選択した言語によりクラウドで実行されるコードを記述します。

次のサービスを利用し、モバイル アプリでサーバーレス コンピューティング機能を有効にします。

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) は、お好きなプログラミング言語で記述したコードを、サーバーについて気にせずに実行できる、イベント ドリブン型のコンピューティング エクスペリエンスです。 開発者はアプリケーションやアプリケーションを実行するためのインフラストラクチャを管理する必要があります。 Functions はオンデマンドでスケーリングされ、コードが実行された時間の分だけ支払います。 Azure Functions は、モバイル アプリケーション向けの API を実装する優れた方法です。実装と保守管理が非常に簡単であり、HTTP 経由でアクセスできるからです。

**主な機能**
- **イベント駆動型であり、スケーラブル**。開発者は**トリガーとバインド**を利用し、関数が呼び出されるタイミングと関数に接続されるデータを定義できます。
- **独自の依存関係を使用** - Azure Functions では NuGet と NPM をサポートしているので、お気に入りのライブラリを使用できます。
- **セキュリティの統合**: Azure Active Directory、Facebook、Google、Twitter、Microsoft アカウントなどの OAuth プロバイダーにより、HTTP によってトリガーされる関数を保護できます。
- **簡単な手順で統合**: さまざまな [Azure サービス](/azure/azure-functions/functions-overview#integrations)と、サービスとしてのソフトウェア (SaaS) サービスを利用できます。
- **柔軟な開発**: 関数をポータル内で直接作成したり、継続的インテグレーションを設定し、GitHub や Azure DevOps Services などのサポート対象開発ツールを使用してコードをデプロイしたりできます。
- Functions ランタイムは**オープン ソース**であり、[GitHub](https://github.com/azure/azure-webjobs-sdk-script) で入手できます。
-  **強化された開発体験**: 開発者は自分にとって好ましいエディターか、ツールや DevOps 機能が組み込まれた監視機能を持つ、使いやすい Web インターフェイスを利用し、ローカルでコード作成、テスト、デバッグできます。
- **多彩なプログラミング言語とホスティング オプション** - C#、Node.js、Java、Javascript、Python を利用して開発できます。
- **従量課金制の価格モデル** - コードの実行に要した時間に対してのみ課金されます。

**参照**
- [Azure Portal](https://portal.azure.com)
- [ドキュメント](/azure/azure-functions/)
- [Azure Functions 開発者ガイド](/azure/azure-functions/functions-reference)
- [クイック スタート](/azure/azure-functions/functions-create-first-function-vs-code)
- [サンプル](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure App Service](https://azure.microsoft.com/services/app-service/) Azure App Service を使用すると、インフラストラクチャを管理することなく、任意のプログラミング言語で Web アプリケーションや RESTful API を構築し、ホストできます。 Azure Web Apps では、自動スケールと高可用性が実現されるほか、Windows と Linux の両方がサポートされています。さらに、GitHub、Azure DevOps、または任意の Git リポジトリからの自動デプロイが可能になります。

**主な機能**
- **複数の言語とフレームワーク**: ASP.NET、ASP.NET Core、Java、Ruby、Node.js、PHP、Python をサポートしています。 また、PowerShell などのスクリプトや実行可能ファイルをバックグラウンド サービスとして実行できます。
- **DevOps の最適化** - 継続的インテグレーションと継続的デプロイを、Azure DevOps、GitHub、BitBucket、Docker Hub、または Azure Container Registry で設定できます。 App Service でのアプリの管理には、Azure PowerShell またはクロスプラットフォーム コマンド ライン インターフェイス (CLI) を使用します。
- **高可用性を備えたグローバル スケーリング**: 手動または自動でスケールアップまたはスケールアウトできます。
- **SaaS プラットフォームおよびオンプレミス データへの接続**: エンタープライズ システム (SAP など)、SaaS サービス (Salesforce など)、インターネット サービス (Facebook など) 向けに用意された 50 を超えるコネクタから選択できます。 また、ハイブリッド接続と Azure Virtual Networks を利用して、オンプレミスのデータにアクセスできます。
- **セキュリティとコンプライアンス** - App Service は ISO、SOC、PCI に準拠しています。 Azure Active Directory またはソーシャル ログイン (Google、Facebook、Twitter、Microsoft) でユーザーを認証します。 IP アドレス制限を作成し、サービス ID を管理します。
- **アプリケーション テンプレート**: WordPress、Joomla、Drupal など、Azure Marketplace にある広範囲の一覧からアプリケーション テンプレートを選択します。
- **Visual Studio の統合**: Visual Studio の専用ツールを利用することで、作成、デプロイ、デバッグの作業が効率的になります。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [ドキュメント](/azure/app-service/)
- [クイック スタート](/azure/app-service/app-service-web-get-started-dotnet)
- [サンプル](/azure/app-service/samples-cli)
- [チュートリアル](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) を使用すると、ホストされている Kubernetes 環境を管理できます。これによって、コンテナー オーケストレーションの知識がなくてもコンテナー化されたアプリケーションを迅速かつ簡単にデプロイおよび管理できるようになります。 また、アプリケーションをオフラインにすることなく、要求に応じてリソースをプロビジョニング、アップグレード、スケーリングすることにより、実行中の操作およびメンテナンスの負担もなくなります。 

**主な機能**
- コンテナーに**既存のアプリケーションを簡単に移行**し、AKS 内で実行できます。
- マイクロサービスを基盤にしたアプリケーションの**デプロイと管理がシンプルになります**。
- **AKS 用のセキュリティで保護された DevOps** により、スピードとセキュリティのバランスがとれ、大規模なコードを短期間で作成できます。
- **AKS と ACI による簡単なスケーリング**: 数秒で起動するポッドを ACI 内でプロビジョニングします。
- オンデマンドの **IoT デバイスのデプロイと管理**。
- TensorFlow や KubeFlow などのツールを利用した**機械学習モデルのトレーニング**。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [ドキュメント](/azure/aks/)
- [クイック スタート](/azure/aks/kubernetes-walkthrough-portal)
- [チュートリアル](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances (ACI)](https://azure.microsoft.com/services/container-instances/) は、単純なアプリケーション、タスク自動化、ジョブ作成など、分離されたコンテナーで操作できるあらゆるシナリオ向けの、優れたソリューションです。 VM を管理することなく、アプリを短期間で開発します。

**主な機能**
- **高速の起動時間**: ACI を利用すると、VM をプロビジョニングして管理する必要がなく、数秒で Azure でコンテナーを開始できます。
- **パブリック IP 接続とカスタム DNS 名**。
- **ハイパーバイザーレベルのセキュリティ**を使用すると、VM 内の場合と同じように、コンテナー内でアプリケーションが分離されます。
- **カスタム サイズ**: CPU のコア数とメモリを厳密に指定することで最適な稼働率が得られます。 支払額は、その要求内容に基づいて秒単位で課金されるので、実際のニーズに応じて支出をきめ細やかに調整することができます。
- **永続的なストレージ**: ACI では、Azure Files 共有を直接マウントし、状態を取得し、保持します。
- **Linux と Windows のコンテナー**: 同じ API でスケジュールされます。

**参照**
- [Azure Portal](https://portal.azure.com/)
- [ドキュメント](/azure/container-instances/)
- [クイック スタート](/azure/container-instances/container-instances-quickstart-portal)
- [サンプル](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [チュートリアル](/azure/container-instances/container-instances-tutorial-prepare-app)