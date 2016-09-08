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
   ms.date="08/22/2016"
   ms.author="hascipio" />

# Azure Marketplace でプランを発行して管理する方法
この記事では、Azure Marketplace に掲載されるソリューションを開発者が作成、デプロイ、管理し、Azure の他の顧客やパートナーに有償で提供するための方法を紹介しています。

## Azure Marketplace とは何ですか?
Azure Marketplace では、Azure の利用者向けに、オンプレミスまたはクラウド ベースのソリューションおよびアプリケーションの開発に役立つサービスを提供しています。また、基幹アプリケーションや他の Azure の利用者用の革新的なアプリケーションやサービスを迅速に開発するために、[Azure 認定](http://azure.com/certified)サービスを構成要素として使用することができます。

Azure の発行元にとって、Azure Marketplace は、クラウド ベースのアプリケーションやモバイル ソリューションを短期間で開発しようとしている他の開発者、ISV、および IT プロフェッショナルに革新的なソリューションやサービスを配布し販売するしくみです。

## サポートされるプランの種類
まず、発行元は、会社がどのような種類のソリューションを提供するかを定義します。Azure Marketplace では、次の 3 種類のサービスを提供しています。

- **仮想マシン イメージ**は、完全にインストールされたオペレーティング システムと 1 つ以上のアプリケーションを含む事前に構成されたイメージです。Azure Virtual Machines サービスで仮想マシンの作成やデプロイに必要な情報は、仮想マシン イメージにより提供されます。

    >[AZURE.NOTE] **たとえば**、Azure の発行元として、革新的なデータベース サービスを有する VM を作成して検証します。この VM は、Azure の他の利用者が進んで調達して独自のクラウド サービス環境にデプロイするほど魅力的なものです。

- **開発者向けサービス**は、アプリケーション開発またはシステム管理に使用される、完全に管理されたサービスです。Azure でクラウド規模のアプリケーションを迅速に開発できる機能を提供します。

    >[AZURE.NOTE] **たとえば**、Azure の発行元として、履歴データに基づいて予測を提供する API アクセスが可能なサービス (Azure または他の場所でホストされる) を開発します。このサービスは、ソリューションを構築する Azure の他の利用者が利用できます。その他の検索および調達サービスや、それぞれのサービスのユーザー向けに、このサービスを Azure Marketplace にデプロイすることができます。

- **ソリューション テンプレート**は、個別に存在するさまざまな Azure サービス (他の販売者から提供されているサービスを含む) を参照できるデータの構造です。Azure の利用者は、このテンプレートを通じて、そのサービスやデータを単一の体系的な方法でデプロイすることができます。

    >[AZURE.NOTE] **たとえば**、Azure の発行元として、一連のサービスを Azure 全体からバンドルします。このサービスによって、負荷分散された、可用性の高い、セキュリティで保護されたクラウド サービスをわずか数クリックですばやくデプロイすることができます。Azure の他の利用者は、手動で特定したり同じまたは類似した Azure サービスを構成したりするのではなく、このソリューション テンプレートを調達することで、時間の節約という価値を見いだすことができます。

いくつかの手順は、異なる種類のソリューションで共通して使用されます。この記事では、ソリューションの種類ごとに必要な手順の概要を示します。

## 1\.前提条件

> [AZURE.NOTE] Azure Marketplace では、作業を開始する前に、[事前承認](http://azure.com/certified)が必要です。

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


## 手順 3.プランの発行後の管理
- [仮想マシン プランの作成後ガイド](marketplace-publishing-vm-image-post-publishing.md)
- [プランまたは SKU の非技術的な詳細を更新する方法](marketplace-publishing-vm-image-post-publishing.md#2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku)
- [プランまたは SKU の技術的な詳細を更新する方法](marketplace-publishing-vm-image-post-publishing.md#1-how-to-update-the-technical-details-of-a-sku)
- [表示されているプランに新しい SKU を追加する方法](marketplace-publishing-vm-image-post-publishing.md#3-how-to-add-a-new-sku-under-a-listed-offer)
- [表示されている SKU のデータ ディスク数を変更する方法](marketplace-publishing-vm-image-post-publishing.md#4-how-to-change-the-data-disk-count-for-a-listed-sku)
- [表示されているプランを Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md#5-how-to-delete-a-listed-offer-from-the-azure-marketplace)
- [表示されている SKU を Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md#6-how-to-delete-a-listed-sku-from-the-azure-marketplace)
- [表示されている SKU の現在のバージョンを Azure Marketplace から削除する方法](marketplace-publishing-vm-image-post-publishing.md#7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace)
- [表示価格を運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#8-how-to-revert-listing-price-to-production-values)
- [課金モデルを運用時の運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#9-how-to-revert-billing-model-to-production-values)
- [表示されている SKU の可視性の設定を運用時の値に戻す方法](marketplace-publishing-vm-image-post-publishing.md#10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value)
- [How to change your Cloud Solution Provider reseller incentive (Cloud Solution Provider のリセラー インセンティブを変更する方法)](marketplace-publishing-csp-incentive.md)
- [Seller Insights レポートの概要](marketplace-publishing-report-seller-insights.md)
- [支払いレポートを理解する](marketplace-publishing-report-payout.md)
- [Get support as a publisher (発行元としてサポートを受ける)](marketplace-publishing-get-publisher-support.md)

## その他のリソース
- [Azure PowerShell の設定](marketplace-publishing-powershell-setup.md)

<!---HONumber=AcomDC_0824_2016-->