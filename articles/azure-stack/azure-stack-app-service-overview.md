---
title: 'App Service の概要: Azure Stack | Microsoft Docs'
description: Azure Stack 上の App Service の概要
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: 42258a352b05d97341f20c13bf837e1398dd8979
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37099964"
---
# <a name="app-service-on-azure-stack-overview"></a>Azure Stack 上の App Service の概要

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

Azure App Service on Azure Stack は、Azure Stack で利用できる Microsoft Azure の、サービスとしてのプラットフォーム (PaaS) サービスです。 このサービスにより、お客様の (内部または外部の) ユーザーはあらゆるプラットフォームやデバイス用の Web、API、Azure Functions アプリケーションを作成できるようになります。 お客様のアプリとオンプレミスのアプリケーションを統合し、ビジネス プロセスを自動化できます。 Azure Stack クラウドの運用者は、指定した共有仮想マシン (VM) リソースまたは専用 VM を使用して、完全に管理された VM 上でユーザーのアプリを実行できます。

Azure App Service を使用すると、ビジネス プロセスとホスティング クラウド API を自動化できます。 単一の統合サービスである Azure App Service では、さまざまなコンポーネント (Web サイト、RESTful API、ビジネス プロセス) を 1 つのソリューションにまとめることができます。

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Azure App Service on Azure Stack を勧める理由

App Service のいくつかの主要機能を次に示します。

- **複数の言語とフレームワーク**: App Service は、ASP.NET、Node.js、Java、PHP、Python を最高レベルでサポートしています。 また、App Service VM では、Windows PowerShell などのスクリプトや実行可能ファイルを実行することもできます。
- **DevOps の最適化**: 継続的インテグレーションと継続的配置を GitHub、ローカルの Git または BitBucket でセットアップできます。 テスト環境やステージング環境を介して更新を反映できます。 App Service でのアプリの管理には、Azure PowerShell またはクロスプラットフォーム コマンド ライン インターフェイス (CLI) を使用します。
- **Visual Studio の統合**: Visual Studio の専用ツールを使えば、アプリケーションの作成やデプロイ作業が効率的になります。

## <a name="app-types-in-app-service"></a>App Service でのアプリの種類

App Service にはアプリの種類が複数用意されており、それぞれ特定のワークロードをホストするよう設計されています。

- [Web Apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-overview) - Web サイトと Web アプリケーションをホストするためのアプリの種類。
- [API Apps](https://docs.microsoft.com/azure/app-service-api/app-service-api-apps-why-best-platform) - RESTful API をホストするためのアプリの種類。
- イベント ドリブンでサーバーレスのワークロードをホスティングするための Azure Functions。

ここで言うアプリとは、ワークロードの実行専用のホスティング リソースを指しています。 "Web アプリ" を例にとると、Web アプリをコンピューティング リソースとアプリケーション コードの両方の観点から捉えることに皆さんおそらく慣れていることでしょう。この両者が合わさって、ブラウザーに機能が提供されます。 しかし、App Service の Web アプリとは、アプリケーション コードをホストするために Azure Stack が提供するコンピューティング リソースです。

アプリケーションは、さまざまな種類の複数の App Service アプリで構成することもできます。 たとえば、アプリケーションが Web フロントエンドと RESTful API バックエンドで構成されている場合は、次のようなことを実行できます。

- 1 つの Web アプリに両方 (フロントエンドと API) をデプロイする
- フロントエンドのコードを Web アプリに、バックエンドのコードを API アプリにデプロイする

   ![データの監視を表示した App Service の [概要]](media/azure-stack-app-service-overview/image01.png)

## <a name="what-is-an-app-service-plan"></a>App Service プランとは

App Service のリソース プロバイダーは、Azure App Service で使用するものと同じコードを使用します。 したがって、一部の一般的な概念のみを示します。 App Service では、アプリケーションの価格コンテナーを App Service プランと呼びます。 アプリを保持するために使用される専用の仮想マシンのセットを表します。 特定のサブスクリプション内で、複数の App Service プランを利用できます。

Azure には、共有ワーカーと専用ワーカーがあります。 共有ワーカーは高密度のマルチテナント アプリ ホスティングをサポートします。この共有ワーカーは 1 セットしかありません。 専用サーバーは 1 つのテナントでのみ使用され、小、中、大という 3 つのサイズがあります。 オンプレミスの顧客のニーズは、必ずしもこれらの用語を使用して説明されるわけではありません。 Azure Stack 上の App Service では、リソース プロバイダー管理者は、使用可能にするワーカー層を定義できます。 独自のホスティング ニーズに基づき、複数の共有ワーカー セットや、異なる専用ワーカー セットを定義できます。 これらのワーカー層の定義を使用することで、独自の価格 SKU を定義できます。

## <a name="portal-features"></a>ポータルの機能

Azure Stack 上の App Service では、Azure App Service と同一の UI が使用されます。同じことが、バックエンドにも当てはまります。 しかし、Azure Stack では一部の機能が無効になり、機能しません。 Azure Stack では現在、このような機能に必要な Azure 固有の期待値やサービスはまだ使用できません。

## <a name="next-steps"></a>次の手順

- [Azure Stack 上の App Service を開始する前に](azure-stack-app-service-before-you-get-started.md)
- [App Service リソース プロバイダーのインストール](azure-stack-app-service-deploy.md)

[SQL Server リソース プロバイダー](azure-stack-sql-resource-provider-deploy.md)や [MySQL リソース プロバイダー](azure-stack-mysql-resource-provider-deploy.md)など、他の[サービスとしてのプラットフォーム (PaaS) サービス](azure-stack-tools-paas-services.md)を試すこともできます。
