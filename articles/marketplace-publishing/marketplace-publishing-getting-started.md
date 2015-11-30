<properties
   pageTitle="プランを作成して Marketplace にデプロイする方法の概要 |Microsoft Azure"
   description="承認済みの Microsoft 販売者になり、仮想マシン イメージ、テンプレート、データ サービス、または開発者サービスを作成して Azure Marketplace にデプロイするために必要な手順を理解します。"
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
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Azure Marketplace へのプランの発行方法
この記事は、販売者がソリューションを作成して Azure Marketplace にデプロイし、Azure の他の顧客やパートナーが購入および使用できるようにするのに役立ちます。

まず、発行元は、会社がどのような種類のソリューションを提供するかを定義します。Azure Marketplace では複数のソリューションがサポートされており、Azure Marketplace に正常に発行するには、各ソリューションでわずかに異なる作業を行う必要があります。

ソリューションの種類:

- 仮想マシン イメージ
- 開発者サービス
- データ サービス
- ソリューション テンプレート

いくつかの手順は、異なる種類のソリューションで共通して使用されます。この記事では、ソリューションの種類ごとに必要な手順の概要を示します。

> [AZURE.NOTE]Azure Marketplace では、作業を開始する前に、事前承認が必要です。これは、データ サービスの発行元に対しては適用されません。

||仮想マシン イメージ |開発者サービス | データ サービス | ソリューション テンプレート | |----|----|----|----|----| | **事前承認を得る** | [Microsoft Azure Certified][link-certification] | [Microsoft Azure Certified][link-certification] | 該当なし | [Microsoft Azure Certified][link-certification] | | **手順 1: 販売者アカウントの登録** | [Microsoft 販売者アカウント: 作成および登録][link-accts] | [Microsoft 販売者アカウント: 作成および登録][link-accts] | [Microsoft 販売者アカウント: 作成および登録][link-accts] | [Microsoft 販売者アカウント: 作成および登録][link-accts] | |**手順 2: プランを作成する**| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| [技術以外の一般的な前提条件](marketplace-publishing-pre-requisites.md)| || [VM の技術的な前提条件][link-single-vm-prereq] | 開発者サービスの技術的な前提条件 | データ サービスの技術的な前提条件 | [ソリューション テンプレートの技術的な前提条件](marketplace-publishing-solution-template-creation-prerequisites.md) | ||[VM イメージの発行ガイド][link-single-vm] | 開発者向けサービスの発行ガイド | データ サービスの発行ガイド | [ソリューション テンプレートの発行ガイド](marketplace-publishing-solution-template-creation.md) | || [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | [Azure Marketplace のマーケティング コンテンツ ガイド][link-pushstaging] | | **手順 3: ステージングへのプランのプッシュ** | [VM のプランをステージングでテストする](marketplace-publishing-vm-image-test-in-staging.md) |開発者サービスのプランをステージングでテストする | データ サービスのプランをステージングでテストする | [ソリューション テンプレートをステージングでテストする](marketplace-publishing-solution-template-test-in-staging.md) | | **手順 4: プランを Marketplace にデプロイする** | [プランを Marketplace にデプロイする][link-pushprod] | [プランを Marketplace にデプロイする][link-pushprod] | [プランを Marketplace にデプロイする][link-pushprod] | [プランを Marketplace にデプロイする][link-pushprod] |

## サポート
- [Get support as a publisher (発行元としてサポートを受ける)][suppt-general]
- [Understanding seller insights reporting (Seller Insights レポートを理解する)][suppt-rpt-insights]
- [Understanding Payout reporting (支払いレポートを理解する)][suppt-rpt-payouts]
- [Troubleshooting Common Publishing Problems in the Marketplace (Marketplace での発行に関してよくある問題のトラブルシューティング)][suppt-common]

## その他のリソース
- 使用されるポータルの詳細については、「[Portals You Will Need (必要なポータル)](marketplace-publishing-portals.md)」を参照してください。

**仮想マシン**

- [Azure PowerShell の設定](marketplace-publishing-powershell-setup.md)
- [Creating a VM Image on-premise (オンプレミスでの VM イメージの作成)](marketplace-publishing-vm-image-creation-on-premise.md)
- [Windows を実行する仮想マシンを Azure プレビュー ポータルで作成する](../virtual-machines-windows-tutorial/)

[suppt-general]: marketplace-publishing-get-publisher-support.md
[suppt-rpt-insights]: marketplace-publishing-report-seller-insights.md
[suppt-rpt-payouts]: marketplace-publishing-report-payout.md
[suppt-common]: marketplace-publishing-support-common-issues.md
[link-certification]: marketplace-publishing-azure-certification.md
[link-accts]: marketplace-publishing-accounts-creation-registration.md
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md
[link-pushprod]: marketplace-publishing-push-to-production.md

<!---HONumber=Nov15_HO4-->