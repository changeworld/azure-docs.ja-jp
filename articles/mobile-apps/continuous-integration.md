---
title: Visual Studio App Center と Azure サービスを使用して、アプリのライフサイクルを自動化する
description: モバイル アプリケーションの継続的なビルドとインテグレーションを設定するのに役立つ、App Center などのサービスについて学習します。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795103"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>継続的なビルドとインテグレーションによってアプリのライフサイクルを自動化する

開発者はコードを記述し、コード リポジトリにチェックインしますが、リポジトリにチェックインされたコミットに常に整合性があるとは限りません。 複数の開発者が同じプロジェクトで作業していると、統合で問題が発生し、チームは、事が運ばない、バグが累積したままになる、プロジェクトの開発が遅れるといった状況になります。 開発者は、ソフトウェア コード全体がビルドされ、エラーがないか確認するためにテストされるまで待機する必要があります。これにより、プロセスの速度が低下し、反復が少なくなります。 

**継続的なビルドとインテグレーション**により、開発者はコードのビルドとテストを簡略化することができます。その場合、変更内容をソース コード リポジトリにコミットするだけでなく、テストと検証をビルド環境に導入することで、常にコードに対してテストを実行します。 ソース コードに加えられたすべての変更は、リポジトリに対してコミットが行われるたびに継続的にビルドされます。 チェックインのたびに、CI サーバーでは、開発者が作成したすべてのテストを確認して実行します。 テストに合格しなかった場合、コードは返送され、さらに変更が加えられます。 これにより、開発者は作成されたビルドを中断したり、コンピューター上ですべてのテストをローカルに実行したりしなくて済むため、開発者の生産性が高まります。 

## <a name="key-benefits"></a>主な利点
- パイプラインの**自動化された**ビルド、テスト、およびデプロイ。
- 早期に**バグを検出し、問題を修正**して、確実にリリース速度を向上させます。
- より頻繁に**コードをコミット**し、アプリケーションを迅速にビルドします。
- 問題を発生させることなく、コードをすばやく変更するための**柔軟性**。
- **市場投入までの時間の短縮**により、最初から最後まで優れた品質のコードのみで確実に処理されるようになります。
- 小規模なコードを同時に統合できるようにする、**小さなコードの変更**。
- **チームの透明性とアカウンタビリティを向上させる**ことにより、お客様からだけでなく、チームからも**継続的なフィードバック**が得られます。

モバイル アプリで継続的インテグレーション パイプラインを有効にするには、次のサービスを使用します。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center ビルド](/appcenter/build/) サービスは、セキュリティで保護されたクラウド インフラストラクチャを使用して、チームが作業しているネイティブおよびクロス プラットフォームのアプリケーションをビルドするのに役立ちます。 App Center でリポジトリを簡単に接続し、コミットごとにクラウドでアプリのビルドを開始できます。その場合、ビルド サーバーのローカルでの構成、複雑な構成、同僚のコンピューター上にビルドされているが自分のコンピューターにはビルドされていないコードの心配をする必要はありません。

App Center サービスの機能の追加により、ワークフローをさらに自動化することができます。 App Center 配布を利用して、テスト担当者とパブリック アプリ ストアにビルドを自動的にリリースするか、App Center テストを使用して、クラウド内の数千もの実際のデバイスと OS 構成で自動 UI テストを行います。

**主な機能**
- 数分で**継続的インテグレーションを設定**し、アプリケーションをより頻繁かつ迅速にビルドします。
- **GitHub、BitBucket、Azure DevOps、および GitLab** と統合します。
- 管理対象の、クラウドでホストされているコンピューター上での**高速かつ安全なビルド**。
- **ビルドで "テストの起動" を有効にし**、アプリが実際の iOS および Android デバイスでビルドされているかどうかを確認します。
- **ネイティブおよびクロスプラットフォームのサポート** - iOS、Android、macOS、Windows、Xamarin、React Native。
- クローン後、ビルド前、およびビルド後のスクリプトを追加することによって、**ビルドをカスタマイズ**します。

**参照**
- [App Center にサインアップする](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center ビルドを使ってみる](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 Azure DevOps のサービスである、[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) は、完全な機能を備えた継続的インテグレーション (CI) および継続的デリバリー (CD) サービスであり、任意の Git プロバイダーと連携し、Azure を含むほとんどの主要なクラウド サービスにデプロイすることができます。 GitHub、GitHub Enterprise サーバー、GitLab、Bitbucket Cloud、または Azure Repos でコードを使用して作業を開始することができます。 その後、Microsoft Azure、Google Cloud Platform、アマゾン ウェブ サービスのコードのビルド、テスト、およびデプロイを自動化できます。

**主な機能**
- Microsoft および非 Microsoft (Node.js、Java) ベースのサーバーのテクノロジに加え、ネイティブ (Android、iOS、Windows) およびクロスプラットフォーム (Xamarin、Cordova、React Native) のモバイル アプリケーションの両方に CI サーバーを設定するためのタスクベースの簡略化されたエクスペリエンス。
- **任意の言語、プラットフォーム、クラウド** - Node.js、Python、Java、PHP、Ruby、Go、C/C++、C#、Android、iOS アプリケーションをビルド、テスト、デプロイします。 Linux、macOS、および Windows で並列実行します。 Azure、AWS、GCP などのクラウド プロバイダーにデプロイします。 ベータ チャネルとアプリ ストアを使用してモバイル アプリケーションを配布します。
- **ネイティブ コンテナーのサポート** - 新しいコンテナーを簡単に作成し、任意のレジストリにプッシュします。 コンテナーを独立したホストまたは Kubernetes にデプロイします。
- **高度なワークフロー** - 簡単なビルド チェーンと複数のフェーズにわたるビルド。 YAML、テスト統合、リリース ゲート、レポートなどがサポートされます。
- **拡張可能** - コミュニティによってビルドされたさまざまなビルド、テスト、デプロイ タスクを使用します (Slack から SonarCloud までの数百の拡張機能)。 Jenkins などの他の CI システムからデプロイし、統合に役立つ Webhook と REST API を使用することもできます
- パブリックおよびプライベート リポジトリ用の**無料のクラウドでホストされたビルド**。
- アマゾン ウェブ サービス、Google Cloud Platform などの**他のクラウド ベンダーへのデプロイがサポートされます**。

**参照**
- [Azure Pipelines での作業の開始に関するガイド](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps を使用して作業を開始する](https://app.vsaex.visualstudio.com/signup/) 
- [クイック スタート](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

アプリケーションのビルドに適したサービスを選択するために、[App Center ビルドと Azure Pipelines](/appcenter/build/choose-between-services) の比較に関する記事に従ってください。
