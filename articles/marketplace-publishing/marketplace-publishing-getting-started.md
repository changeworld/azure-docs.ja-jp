<properties
   pageTitle="プランを作成して Marketplace にデプロイする方法の概要 |Microsoft Azure"
   description="承認済みの Microsoft 開発者になり、仮想マシン イメージ、テンプレート、データ サービス、または開発者向けサービスを作成して Azure Marketplace にデプロイするために必要な手順を理解します。"
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
   ms.date="07/05/2016"
   ms.author="hascipio" />

# Azure Marketplace でプランを発行して管理する方法
この記事では、Azure Marketplace に掲載されるソリューションを開発者が作成、デプロイ、管理し、Azure の他の顧客やパートナーに有償で提供するための方法を紹介しています。

まず、発行元は、会社がどのような種類のソリューションを提供するかを定義します。Azure Marketplace では複数のソリューションがサポートされており、Azure Marketplace に正常に発行するには、各ソリューションでわずかに異なる作業を行う必要があります。

## プランの種類
|プランの種類| 定義 |
|---|---|
|Virtual Machine イメージ | 完全にインストールされたオペレーティング システムと 1 つ以上のアプリケーションを含む事前に構成された仮想マシン (VM) イメージ。仮想マシン イメージのサービス/データには、単一の VM イメージは、またはソリューション テンプレートによって結びつけられた複数の VM イメージを含めることができます。Azure Virtual Machines サービスで仮想マシンの作成やデプロイに必要な情報は、仮想マシン イメージ ("イメージ") により提供されます。1 つのイメージは、1 つのオペレーティング システム仮想ハード ドライブと、0 個以上のデータ ディスク仮想ハード ドライブとで構成されます。顧客は、単一のイメージから、任意の数の仮想マシンをデプロイできます。|
|開発者サービス| インフォメーション ワーカー、ビジネス アナリスト、開発者、IT 担当者が、カスタム アプリケーション開発またはシステム管理において使用できる完全に管理されたサービス。開発者サービスは、Azure でクラウド スケールのアプリケーションを短期間で開発できるようにする機能を提供します。開発者サービスを購入するお客様は、Azure サブスクリプションを保有している必要があります。発行元には、Microsoft Azure Marketplace Publisher Agreement で詳述されているとおり、お客様の開発者サービスの利用状況を計測し、利用状況の情報を Microsoft に報告する責任があります。|
|ソリューション テンプレート|"Azure Resource Manager (ARM) ソリューション テンプレート" は、バラバラに存在するさまざまなサービスやデータ (他の発行元から提供されているものを含む) を参照できるデータの構造です。Azure をご利用のお客様は、このテンプレートを通じて、そのサービスやデータを単一の体系的な方法でデプロイすることができます。|

いくつかの手順は、異なる種類のソリューションで共通して使用されます。この記事では、ソリューションの種類ごとに必要な手順の概要を示します。

## 1\.前提条件

> [AZURE.NOTE] Azure Marketplace では、作業を開始する前に、事前承認が必要です。これは、データ サービスの発行元に対しては適用されません。

1. [Microsoft Azure Certified の事前承認を申請する](marketplace-publishing-azure-certification.md)
2. [Microsoft 開発者アカウントを作成して登録する](marketplace-publishing-accounts-creation-registration.md)
3. [技術面以外の前提条件を満たす](marketplace-publishing-pre-requisites.md)

## 2\.プランの発行
### 2\.1 プラン固有の技術的な前提条件を満たす
- [VM の技術的な前提条件](marketplace-publishing-vm-image-creation-prerequisites.md)
- [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md)

### 2\.2 プランの作成
1. プランごとのガイドに沿ってプランを作成します。
    - [VM プランの作成](marketplace-publishing-vm-image-creation.md)
    - [ソリューション テンプレート プランの作成](marketplace-publishing-solution-template-creation.md)
2. [プランのマーケティング コンテンツの作成](marketplace-publishing-push-to-staging.md)

### 2\.3 ステージング環境でのプランのテスト
- [ステージング環境での VM プランのテスト](marketplace-publishing-vm-image-test-in-staging.md)
- [ステージングでのソリューション テンプレート プランのテスト](marketplace-publishing-solution-template-test-in-staging.md)

### 2\.4 Marketplace へのプランのデプロイ
- [Azure Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)

### 仮想マシン イメージ固有 ###
- [Creating a VM Image on-premise (オンプレミスでの VM イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)
- [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines/virtual-machines-windows-hero-tutorial.md)


- [Troubleshooting Common Publishing Problems in the Marketplace (Marketplace での発行に関してよくある問題のトラブルシューティング)](marketplace-publishing-support-common-issues.md)
- 使用されるポータルの詳細については、「[必要なポータル](marketplace-publishing-portals.md)」を参照してください。


## 3\.プランの発行後の管理
- [仮想マシン プランの作成後ガイド](marketplace-publishing-vm-image-post-publishing.md)
- [プランまたは SKU の非技術的な詳細を更新する方法](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [Azure Marketplace からプランまたは SKU を削除する方法](marketplace-publishing-vm-image-post-publishing.md#4-how-to-delete-a-live-offer-or-sku-from-the-azure-marketplace)
- [How to change your Cloud Solution Provider reseller incentive (Cloud Solution Provider のリセラー インセンティブを変更する方法)](marketplace-publishing-csp-incentive.md)
- [Seller Insights レポートの概要](marketplace-publishing-report-seller-insights.md)
- [支払いレポートを理解する](marketplace-publishing-report-payout.md)
- [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)

## その他のリソース
- [Azure PowerShell の設定](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0706_2016-->