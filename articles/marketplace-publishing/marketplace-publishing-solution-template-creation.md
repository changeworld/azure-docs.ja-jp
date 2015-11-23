<properties
   pageTitle="Marketplace 用ソリューション テンプレートを作成するためのガイド | Microsoft Azure"
   description="Azure Marketplace で購入できる複数 VM イメージのソリューション テンプレートを作成、認定、およびデプロイする方法を詳しく説明します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="10/28/2015"
      ms.author="hascipio; v-divte" />

# Azure Marketplace 向けソリューション テンプレートの作成ガイド
手順 1. [Account Creation and Registration (アカウントの作成および登録)][link-acct-creation] を完了したら、[Technical Pre-requisites for creating a Solution Template (ソリューション テンプレートを作成するための技術的な前提条件)](marketplace-publishing-solution-template-creation-prerequisites.md) に記載される Azure と互換性のあるソリューション テンプレートの作成について説明します。Azure Marketplace 向けの [発行ポータル][link-pubportal]での、複数 VM のソリューション テンプレートの作成手順を紹介します。

## 発行ポータルで、ソリューション テンプレート プランを作成します。
[https://publish.windowsazure.com](http://publish.windowsazure.com) に移動します。 **[発行ポータル](https://publish.windowsazure.com/)に初めてログインする場合は、会社の販売者プロファイルが登録されているアカウントを使用します。** 発行ポータルでは、他の従業員を共同管理者として後から追加できます。

### 1\.[ソリューション テンプレート] を選択する

  ![図][img-pubportal-menu-sol-templ]

### 2\.新しいソリューション テンプレートを作成する

  ![図][img-pubportal-sol-templ-new]

### 3\.最初にトポロジを作成する
ソリューション テンプレートは、作成したすべてのトポロジの 「親」 となります。1 つのプランまたはソリューション テンプレートでは、複数のトポロジを定義できます。プランをステージングにプッシュすると、すべてのトポロジも一緒にプッシュされます。プランを定義する手順を次に示します。-トポロジの作成 – **"トポロジ識別子"** は通常、ソリューション テンプレートのトポロジ名です。トポロジ識別子は、次に示すように URL で使用されます。

  Azure Marketplace : http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure プレビュー ポータル : https://ms.portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}

- 新しいバージョンを追加する  

### 4\.トポロジのバージョンの認定を受ける
トポロジの特定バージョンをプロビジョニングするために必要な、すべてのファイルを含む zip ファイルをアップロードします。この zip ファイルには、ルートにリンクされている *mainTemplate.json* ファイル、 *createUiDefinition.json* ファイル、および必要とされるすべてのスクリプトを含める必要があります。

zip ファイルをアップロードしたら、[**証明書の要求**]　をクリックします。Microsoft の認定チームがファイルを確認し、トポロジを認定します。

> [AZURE.TIP]開発者がソリューション テンプレートのトポロジを作成して認定を取得する間、会社のビジネス、マーケティング、法務の部門はマーケティングおよび法的なコンテンツの作業を進めることができます。

## 次のステップ
ソリューション テンプレートを作成し、認定に必要なファイルを含む zip ファイルを送信したら、ステージング環境でテストする前に、「[マーケティング コンテンツを使用したプラン作成の実行](marketplace-publishing-push-to-staging.md)」の指示に従います。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

**VM イメージ** - [Azure での仮想マシン イメージについて](https://msdn.microsoft.com/library/azure/dn790290.aspx)

**VM 拡張機能** - [VM エージェントと VM 拡張機能の概要](https://msdn.microsoft.com/library/azure/dn832621.aspx) - [Azure VM 拡張機能とその機能](https://msdn.microsoft.com/library/azure/dn606311.aspx)

**ARM** - [Azure リソース マネージャーのテンプレートの作成](../resource-group-authoring-templates/) - [単純な ARM テンプレートの例](https://github.com/rjmax/ArmExamples)

**Storage Account Throttles (ストレージ アカウントのスロットル)** - [How to Monitor for Storage Account Throttling (ストレージ アカウントのスロットルの監視方法)](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) - [Premium Storage](../storage/storage-premium-storage-preview-portal/#scalability-and-performance-targets-whja-JPing-premium-storage)

[img-pubportal-menu-sol-templ]: media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]: media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]: marketplace-publishing-microsoft-accounts-creation-registration.md
[link-pubportal]: https://publish.windowsazure.com

<!---HONumber=Nov15_HO3-->