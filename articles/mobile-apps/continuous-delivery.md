---
title: Visual Studio App Center と Azure サービスを使用してモバイル アプリケーションのデプロイとリリースを自動化する
description: モバイル アプリケーションの継続的デリバリー パイプラインの設定に役立つ、App Center などのサービスについて学習します。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795119"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>継続的デリバリー サービスを使用してモバイル アプリケーションのデプロイとリリースを自動化する

開発者はコードを記述し、コード リポジトリにチェックインしますが、リポジトリにチェックインされたコミットに常に整合性があるとは限りません。 複数の開発者が同じプロジェクトで作業していると、統合で問題が発生し、チームは、事が運ばない、バグが累積したままになる、プロジェクトの開発が遅れるといった状況になります。 開発者は、ソフトウェア コード全体がビルドされ、エラーを確認するためにテストされるまで待機する必要があります。これにより、プロセスの速度が低下し、反復が少なくなります。

**継続的デリバリー**では、アプリケーションをテスト担当者のグループまたは会社の従業員 (ベータ テスト用) と App Store (実稼働用) のどちらに配布するかに関係なく、モバイル アプリケーションのデプロイとリリースを自動化します。 これにより、デプロイのリスクが減り、迅速な反復が促進され、お客様に新しい変更を継続的にリリースできるようになります。

## <a name="distribute-application-binaries-to-beta-testers"></a>ベータ テスト担当者にアプリケーション バイナリを配布する
モバイル アプリケーションのベータ テストは、アプリケーション開発プロセス時の重要な手順の 1 つです。 アプリケーションのバグと問題を早期に検出し、アプリケーションの品質を向上させて運用環境で使用できるようにするためのフィードバックを得るのに役立ちます。

モバイル アプリで継続的デリバリー パイプラインを有効にするには、次のサービスを使用します。

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 配布](/appcenter/distribution/)は、開発者がビルドをエンドユーザーのデバイスに迅速にリリースするためのツールです。 完全なインストール ポータル エクスペリエンスにより、配布はベータ アプリ テスト担当者向けの強力なソリューションであるだけでなく、パブリック アプリ ストアを通じて配布するための便利な代替手段となります。 開発者は、App Center ビルドとパブリック アプリケーション ストアの統合により、配布ワークフローをさらに自動化できます。

**主な機能**
- **アプリをベータ テスト担当者とユーザーに配布**し、すべてのテスト担当者が最新バージョンのアプリケーションを確実に使用するようにします。
- **テスト担当者に新しいリリースを通知**します。この場合、テスト担当者がダウンロード フローを再度実行することはありません。
- さまざまなバージョンのアプリケーションの**配布グループを管理**します。
- **ストアへの配布** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- **プラットフォーム サポート** - iOS、Android、macOS、tvOS、Xamarin、React Native、Unity、Cordova。
- プロビジョニング プロファイルに iOS デバイスを自動的に登録します。

**参照**
- [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center Distribute を使ってみる](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) は、完全な機能を備えた継続的インテグレーション (CI) および継続的デリバリー (CD) サービスであり、任意の Git プロバイダーと連携し、Azure サービスを含むほとんどの主要なクラウド サービスにデプロイすることができます。 GitHub、GitHub Enterprise サーバー、GitLab、Bitbucket Cloud、または Azure Repos でコードを使用して作業を開始することができます。 その後、Microsoft Azure、Google Cloud Platform、アマゾン ウェブ サービスのコードのビルド、テスト、およびデプロイを自動化できます。

**主な機能**
- **ネイティブ (Android、iOS、Windows) およびクロスプラットフォーム (Xamarin、Cordova、React Native) のモバイル アプリケーション**の両方に CI サーバーを設定するためのタスクベースの簡略化されたエクスペリエンス。
- **任意の言語、プラットフォーム、クラウド** - Node.js、Python、Java、PHP、Ruby、Go、C/C++、C#、Android、iOS アプリをビルド、テスト、およびデプロイします。 Linux、macOS、および Windows で並列実行します。 Azure、AWS、GCP などのクラウド プロバイダーにデプロイします。 ベータ チャネルとアプリ ストアを使用してモバイル アプリケーションを配布します。
- **ネイティブ コンテナーのサポート** - 新しいコンテナーを簡単に作成し、任意のレジストリにプッシュします。 コンテナーを独立したホストまたは Kubernetes にデプロイします。
- **高度なワークフローと機能** - 簡単なビルド チェーンと複数のフェーズにわたるビルド。 YAML、テスト統合、リリース ゲート、レポートなどがサポートされます。
- **拡張可能** - コミュニティによってビルドされたさまざまなビルド、テスト、デプロイ タスクを使用します (Slack から SonarCloud までの数百の拡張機能)。 Jenkins などの、他の CI システムからデプロイすることもできます。 Webhook と REST API は統合に役立ちます
- パブリックおよびプライベート リポジトリ用の**無料のクラウドでホストされたビルド**。
- AWS、GCP などの**他のクラウド ベンダーへのデプロイをサポート**します。

**参照**
- [Azure Pipelines での作業の開始に関するガイド](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps を使用して作業を開始する](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>アプリケーションをアプリ ストアに直接配布する
アプリケーションを運用環境で利用する準備ができており、一般に使用する場合は、アプリ ストアに送信して、お客様がダウンロードできるようにする必要があります。 アプリケーションをアプリ ストアに直接配布するための方法は複数あります。 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center 配布](/appcenter/distribution/stores/)サービスを使用すると、モバイル アプリケーションをアプリ ストアに直接公開できます。 アプリケーションをエンド ユーザーがダウンロードできるようになったら、App Center ポータルから直接アプリケーション バイナリを公開できます。  

以下に直接配布できます。
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play ストア](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Apple で開発および管理されている App Store では、ユーザーは iOS、MacOS、WatchOS、および tvOS デバイス用に開発されたアプリケーションを参照してダウンロードできます。 開発者は、iOS アプリを一般に使用できるように Apple Store に送信する必要があります。

### <a name="google-play"></a>Google Play

Google Play は Android OS 向けの公式アプリ ストアであり、ユーザーは Android デバイス用に開発され、Google 経由で公開されたアプリケーションを参照してダウンロードすることができます。

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) は、企業データを保護しながら、従業員の生産性を向上させるのに役立つ、Enterprise Mobility Management (EMM) 領域のクラウドベース サービスです。 Intune では、次のことが可能です。 
- 会社のデータにアクセスするために従業員が使用するモバイル デバイスと PC を管理する。
- 従業員が使用するモバイル アプリケーションを管理する。
- 従業員が会社の情報にアクセスして共有する方法を制御することで、その情報を保護する。
- デバイスとアプリケーションが会社のセキュリティ要件に準拠していることを確認する。
    
## <a name="deploy-updates-directly-to-users-devices"></a>更新プログラムをユーザーのデバイスに直接デプロイする

### <a name="codepush"></a>CodePush
App Center の [CodePush](/appcenter/distribution/codepush/) サービスを使用すると、Apache Cordova および React Native 開発者は、モバイル アプリケーションの更新プログラムをユーザーのデバイスに直接デプロイできます。 これは、開発者が特定の更新プログラム (JS、HTML、CSS、およびイメージの変更など) を公開できる中央リポジトリとして機能することによって動作します。 その後、アプリケーションでは、提供されたクライアント SDK を使用して、リポジトリから更新プログラムを照会できます。 これにより、エンド ユーザーとのより明確かつ直接的なエンゲージメント モデルを作成することができます。同時に、バグに対処することができ、また、バイナリをリビルドしたり、パブリック アプリ ストアを通じて再配布することなく、小さな機能を追加することができます。

**主な機能**
- Cordova および React Native 開発者は、ストアでリリースすることなく、モバイル アプリケーションの更新プログラムをユーザーのデバイスに直接デプロイできます。
- バグの修正、またはバイナリをリビルドし、それぞれのストアを通じて再配布する必要のない小さな機能の追加/削除の場合に便利です。

**参照**
- [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center でコード プッシュを使ってみる](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)