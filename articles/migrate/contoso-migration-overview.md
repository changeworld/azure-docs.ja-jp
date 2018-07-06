---
title: Contoso での Azure への移行の概要 | Microsoft Docs
description: Contoso が自社のオンプレミスのデータセンターを Azure に移行するときに使用する移行戦略とシナリオの概要について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: ec0308bb2e39c3801305748f19783e70d58b0b7e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232218"
---
# <a name="contoso-migration-overview"></a>Contoso での移行: 概要


これは、架空の組織 Contoso が自社のオンプレミスのインフラストラクチャを [Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/) クラウドに移行する際の方法を示すシリーズのうち、最初の記事です。 

このシリーズの記事では、Azure への移行とハイブリッド環境での作業におけるさまざまなオプションを理解して試すのに役立つデプロイの例を説明しています。 これらの記事は Contoso 社が移行作業を遂行する方法を示すものですが、全体を通じて、一般読者向けのヒントや特定の手順も記載しています。

## <a name="introduction"></a>はじめに

Azure には、クラウド サービスの包括的なセットが用意されています。 開発者や IT プロフェッショナルは、これらのサービスを利用し、データセンターのグローバルなネットワークを通じて、さまざまなツールやフレームワークをベースにアプリケーションを構築、デプロイ、および管理することができます。 ビジネスはデジタル シフトに関連する課題に直面しているため、Azure クラウドはリソースや操作の最適化、お客様や従業員との連携、および製品の変換の方法を理解できるように支援します。

ただし、速度と柔軟性、最小限のコスト、パフォーマンス、および信頼性の面でのクラウドのすべての利点にもかかわらず、多くの組織ではまだしばらくの間、オンプレミス データセンターを実行する必要があることも認識しています。 クラウドの採用の障壁に対処するために、Azure では、オンプレミスのデータセンターと Azure パブリック クラウドの間に橋を構築するハイブリッド クラウド戦略を提供しています。 たとえば、バックアップなどの Azure クラウド リソースを使用してオンプレミスのリソースを保護したり、Azure 分析を使用してオンプレミスのワークロードを把握したりすることができます。 

ハイブリッド クラウド戦略の一環として、Azure ではオンプレミスのアプリやワークロードをクラウドに移行するためのソリューションを用意しています。 簡単な手順で、オンプレミス リソースを包括的に評価し、Azure クラウドではどのように実行されるかを把握することができます。 次に、入手した詳しい評価に基づき、自信を持ってリソースを Azure に移行することができます。 Azure でリソースを稼働させたら、リソースを最適化して、アクセス、柔軟性、セキュリティ、および信頼性を保持したり向上させたりすることができます。

## <a name="migration-strategies"></a>移行方法

クラウドへの移行戦略は、リホスト、リファクター、リアーキテクト、リビルドという 4 つの大きなカテゴリに分類できます。 どの戦略を採用するかは、ビジネス ドライバーや移行の目標によって変わってきます。 複数の戦略を採用する場合もあります。 たとえば、単純なアプリやビジネス上重要でないアプリはリホスト (リフトアンドシフト) し、より複雑でビジネスに不可欠なアプリはリアーキテクトするという方針も採用できます。 ここからは、戦略について見ていきましょう。


**戦略** | **定義** | **いつ使用するか** 
--- | --- | --- 
**リホスト** | 多くの場合、"リフトアンドシフト" の移行と呼ばれます。 このオプションでは、コードを変更せずに、既存のアプリをすばやく Azure に移行できます。 各アプリはそのままの状態で移行されるので、コード変更に伴うリスクとコストなしにクラウドのメリットを享受できます。 | アプリをクラウドにすばやく移動する必要がある場合。<br/><br/> アプリを変更せずに移動したい場合。<br/><br/> アプリが移行後に [Azure IaaS](https://azure.microsoft.com/overview/what-is-iaas/) のスケーラビリティを活かせるように設計されている場合。<br/><br/> ビジネスにとって重要なアプリであるが、アプリの機能をすぐに変更する必要がない場合。
**リファクター** | 多くの場合 "再パッケージ化" と呼ばれます。リファクタリングでは、アプリを [Azure PaaS](https://azure.microsoft.com/overview/what-is-paas/) に接続してクラウド サービスを使用できるようにするために、アプリに最小限の変更を加える必要があります。<br/><br/> たとえば、既存のアプリを Azure App Service または Azure Kubernetes Service (AKS) に移行できます。<br/><br/> また、リレーショナル データベースおよび非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB などのオプションにリファクタリングすることもできます。 | Azure で動作するようにアプリを簡単に再パッケージ化できる場合。<br/><br/> Azure が提供する革新的な DevOps プラクティスを適用したい場合、またはワークロード向けのコンテナー戦略を使用した DevOps を検討している場合。<br/><br/> リファクタリングでは、既存のコード ベースの移植性と使用可能な開発スキルを考慮する必要があります。
**リアーキテクト** | 移行のためのリアーキテクトは、アプリのアーキテクチャを最適化してクラウドのスケーラビリティを活かすために、アプリの機能とコード ベースを変更、拡張することを中心とした作業となります。<br/><br/> たとえば、モノリシック アプリケーションを、相互に連携し、簡単にスケーリングできるマイクロサービスのグループに分割できます。<br/><br/> また、リレーショナル データベースと非リレーショナル データベースを、Azure SQL Database Managed Instance、Azure Database for MySQL、Azure Database for PostgreSQL、Azure Cosmos DB など、フル マネージドの DBaaS ソリューションにリアーキテクトすることもできます。 | アプリにメジャー リビジョンを施し、新機能を組み込んだり、クラウド プラットフォームで作業を効果的に行ったりする必要がある場合。<br/><br/> 既存のアプリケーションへの投資を活かしたい場合、スケーラビリティ要件を満たしたい場合、革新的な Azure DevOps プラクティスを適用したい場合、仮想マシンの使用を最小限に抑えたい場合。
**リビルド** | リビルドでは、Azure のクラウド テクノロジを利用してアプリを最初からリビルドし、進化させることができます。<br/><br/> たとえば、サーバーレス、Azure AI、Azure SQL Database Managed Instance、Azure Cosmos DB などのクラウドネイティブ テクノロジを活用して、新しいアプリを構築することができます。 | 開発は迅速に行いたいが、既存のアプリの機能と寿命が限られている場合。<br/><br/> ビジネスのイノベーションの加速 (Azure が提供する DevOps のプラクティスを含む)、クラウドネイティブ テクノロジを活用した新しいアプリケーションの構築、AI、ブロックチェーン、IoT の進化の活用に対する準備が整っている場合。

## <a name="migration-articles"></a>移行に関する記事

Contoso での移行に関するシリーズに含まれる記事について、下の表に概要をまとめました。  

- シナリオは複雑になってきています。随時追加する予定です。
- 各移行シナリオは、少しずつ異なるビジネス目標に沿って作成されています。このビジネス目標によって移行戦略が決まります。
- 各デプロイ シナリオでは、ビジネス ドライバーと目標、提案されたアーキテクチャ、移行を実施するための手順、クリーンアップの推奨事項、移行完了後の次の手順についての情報が記載されています。

**記事** | **詳細** | **状態**
--- | --- | ---
記事 1: 概要 (本記事) | Contoso の移行戦略、記事シリーズ、使用されるサンプル アプリの概要を示します。 | 使用可能
[記事 2: Azure インフラストラクチャのデプロイ](contoso-migration-overview.md) | Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように準備するかを説明します。 Contoso のすべての移行シナリオで、同じインフラストラクチャが使用されます。 | 使用可能
[記事 3: オンプレミス リソースの評価](contoso-migration-assessment.md) | Contoso が、VMware 上で実行されるオンプレミスの 2 階層アプリ、SmartHotel の評価をどのように実行するかを示します。 アプリの VM については [Azure Migrate](migrate-overview.md) サービスで、アプリの SQL Server データベースについては [Azure Database Migration Assistant](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017) で評価を行います。 | 使用可能
[記事 4: Azure VM および SQL Managed Instance へのリホスト](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso が SmartHotel アプリを Azure に移行する方法を説明します。 アプリの Web VM は [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) を使用して移行し、アプリのデータベースは [Azure Database Migration](https://docs.microsoft.com/azure/dms/dms-overview) サービスを使用して SQL Managed Instance に移行します。 | 使用可能
[記事 5: Azure VM へのリホスト](contoso-migration-rehost-vm.md) | Contoso が Site Recovery サービスを使用して SmartHotel アプリの VM を移行する方法を示します。
[記事 6: Azure VM および SQL Server 可用性グループへのリホスト](contoso-migration-rehost-vm-sql-ag.md) | Contoso が SmartHotel アプリを移行する方法を示します。 Contoso は、Site Recovery を使用してアプリの VM を移行し、Database Migration サービスを使用してアプリのデータベースを SQL Server AlwaysOn 可用性グループに移行します。 | 使用可能
[記事 7: Linux アプリの Azure VM へのリホスト](contoso-migration-rehost-linux-vm.md) | Contoso が Site Recovery を使用してアプリの VM を Azure に移行する方法を説明します。 | 対応予定
[記事 8: Linux アプリの Azure VM および Azure MySQL Server へのリホスト](contoso-migration-rehost-linux-vm-mysql.md) | Contoso が Site Recovery を使用して アプリの VM を移行する方法と、MySQL Workbench を使用して Azure MySQL Server インスタンスに移行する方法を説明します。 | 対応予定



### <a name="demo-apps"></a>デモ アプリ

この記事では、SmartHotel と osTicket の 2 つのデモ アプリを使用します。

- **SmartHotel360**: このアプリは、Azure を使用するときのためのテスト アプリとして Microsoft が開発したものです。 オープン ソースとして提供されており、[GitHub](https://github.com/Microsoft/SmartHotel360) からダウンロードできます。 これは、SQL Server データベースに接続されている ASP.NET アプリです。 現在、このアプリは、Windows Server 2008 R2 と SQL Server 2008 R2 が実行されている 2 つの VMware VM 上に存在しています。 アプリの VM はオンプレミスでホストされており、vCenter Server で管理されています。
- **osTicket**: Linux 上で実行される、オープン ソースのサービス デスク向け発券アプリです。 [GitHub](https://github.com/osTicket/osTicket) からダウンロードできます。 現在、このアプリは、Apache 2、PHP 7.0、MySQL 5.7 を使用し、Ubuntu 16.04LTS が実行されている、2 つの VMware VM 上に存在しています。
    

## <a name="next-steps"></a>次の手順

Contoso が移行に備えて、オンプレミスと Azure のインフラストラクチャをどのように設定するかを[説明します](contoso-migration-infrastructure.md)。



