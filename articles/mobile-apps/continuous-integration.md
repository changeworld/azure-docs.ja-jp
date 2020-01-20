---
title: Visual Studio App Center と Azure サービスを使用して、アプリのライフサイクルを自動化する
description: モバイル アプリケーションの継続的なビルドとインテグレーションを設定するのに役立つ、App Center などのサービスについて説明します。
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453183"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>継続的なビルドとインテグレーションによってアプリのライフサイクルを自動化する

開発者はコードを記述し、コード リポジトリにチェックインしますが、リポジトリにチェックインされたコミットに常に整合性があるとは限りません。 複数の開発者が同じプロジェクトで作業する場合は、統合によって問題が発生する可能性があります。 チームは、物事がうまくいかず、バグが山積みになり、プロジェクトの開発が遅れるという状況に陥る可能性があります。 開発者は、ソフトウェア コード全体がビルドされ、エラーを確認するためにテストされるまで待機する必要があります。これにより、プロセスの速度が低下し、反復が少なくなります。 

継続的なビルドとインテグレーションにより、開発者はコードのビルドとテストを簡略化することができます。その場合、変更内容をソース コード リポジトリにコミットし、テストと検証をビルド環境に導入します。 この方法で、彼らは自分のコードに対して常にテストを実行します。 ソース コードに加えられたすべての変更は、リポジトリに対してコミットが行われるたびに継続的にビルドされます。 チェックインのたびに、継続的インテグレーション (CI) サーバーでは、開発者が作成したすべてのテストが検証および実行されます。 テストに合格しなかった場合、コードは返送され、さらに変更が加えられます。 この方法では、開発者が作成されたビルドを中断することはありません。 また、自分のコンピューター上ですべてのテストをローカルに実行する必要がないため、開発者の生産性が向上します。 

## <a name="key-benefits"></a>主な利点
- パイプラインに対するビルド、テスト、およびデプロイを自動化します。
- 早期にバグを検出し、問題を修正して、確実にリリース速度を向上させます。
- より頻繁にコードをコミットし、アプリケーションを迅速にビルドします。
- 問題なくコードをすばやく変更できる柔軟性を実現します。
- 市場投入までの時間の短縮により、最初から最後まで優れた品質のコードのみで処理されるようになります。
- 小さなコードが一度に統合されるため、小さなコードをより効率的に変更できます。
- チームの透明性とアカウンタビリティを向上させることにより、お客様の顧客とお客様のチームから継続的なフィードバックが得られます。

ご利用のモバイル アプリで継続的インテグレーション パイプラインを有効にするには、次のサービスを使用します。

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) は、セキュリティで保護されたクラウド インフラストラクチャを使用して、チームが作業しているネイティブおよびクロス プラットフォームのアプリケーションをビルドするのに役立ちます。 Visual Studio App Center でリポジトリを簡単に接続し、コミットごとにクラウド内でアプリのビルドを開始することができます。 ビルド サーバーのローカルでの構成、複雑な構成、同僚のコンピューター上にビルドされているが自分のコンピューターにはビルドされていないコードの心配をする必要はありません。

Visual Studio App Center サービスの追加機能により、ご利用のワークフローをさらに自動化することができます。 App Center Distribute を使用すれば、テスト担当者とパブリック アプリ ストアにビルドを自動的にリリースできます。 また、App Center Test を使用すれば、クラウド内の数千の実際のデバイスと OS 構成に対する自動 UI テストを実行することができます。

**主な機能**
- 数分で継続的インテグレーションを設定し、アプリケーションをより頻繁かつ迅速にビルドします。
- GitHub、BitBucket、Azure DevOps、および GitLab と統合します。
- クラウドでホストされているマネージド コンピューター上で高速かつ安全なビルドを作成します。
- ご利用のビルドでテストを起動できるようにし、アプリが実際の iOS および Android デバイスでビルドされているかどうかを確認します。
- iOS、Android、macOS、Windows、Xamarin、React Native について、ネイティブおよびクロスプラットフォームのサポートを受けられます。
- クローン後、ビルド前、およびビルド後のスクリプトを追加することによって、ビルドをカスタマイズします。

**参照**
- [Visual Studio App Center でのサインアップ](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [App Center ビルドを使ってみる](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 Azure DevOps のサービスである [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) は、完全な機能を備えた継続的インテグレーションおよび継続的デリバリー (CD) サービスであり、目的の Git プロバイダーと連携します。 Azure を含むほとんどの主要なクラウド サービスにデプロイすることができます。 GitHub、GitHub Enterprise サーバー、GitLab、Bitbucket Cloud、または Azure Repos でコードを使用して作業を開始することができます。 その後、Microsoft Azure、Google Cloud Platform、アマゾン ウェブ サービス (AWS) のコードのビルド、テスト、およびデプロイを自動化できます。

**主な機能**
- **CI サーバーを設定するための簡素化されたタスクベースのエクスペリエンス:** Microsoft および非 Microsoft (Node.js、Java) ベースのサーバーのテクノロジに加え、ネイティブ (Android、iOS、Windows) とクロスプラットフォーム (Xamarin、Cordova、React Native) のモバイル アプリケーションの両方に CI サーバーを設定します。
- **任意の言語、プラットフォーム、クラウド:** Node.js、Python、Java、PHP、Ruby、Go、C/C++、C#、Android、iOS アプリケーションをビルド、テスト、デプロイします。 Linux、macOS、および Windows で並列実行します。 Azure、AWS、Google Cloud Platform などのクラウド プロバイダーにデプロイします。 ベータ チャネルとアプリ ストアを使用してモバイル アプリケーションを配布します。
- **ネイティブ コンテナーのサポート:** 新しいコンテナーを簡単に作成し、任意のレジストリにプッシュします。 コンテナーを独立したホストまたは Kubernetes にデプロイします。
- **高度なワークフロー:** ビルド チェーンと複数のフェーズにわたるビルドを簡単に作成できます。 YAML、テスト統合、リリース ゲート、レポートなどがサポートされます。
- **拡張可能:** Slack から SonarCloud までの数百の拡張機能を含む、コミュニティによってビルドされたさまざまなビルド、テスト、デプロイ タスクを使用します。 Jenkins などの、他の CI システムからデプロイすることもできます。 Web フックと REST API を使用すれば、統合が容易になります。
- **クラウドでホストされる無料ビルド:** これらのビルドは、パブリックおよびプライベート リポジトリで利用できます。
- **他のクラウド ベンダーへのデプロイのサポート:** ベンダーには AWS と Google Cloud Platform が含まれています。

**参照**
- [Azure Pipelines での作業の開始に関するガイド](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Azure DevOps を使用して作業を開始する](https://app.vsaex.visualstudio.com/signup/) 
- [クイック スタート](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

自分のアプリケーション ビルドに適したサービスの選択を楽にするには、[App Center Build と Azure Pipelines](/appcenter/build/choose-between-services) の比較に関する記事に従ってください。
