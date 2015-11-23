<properties
   pageTitle="Marketplace の VM プランのテスト | Microsoft Azure"
   description="Azure Marketplace の VM イメージをテストする方法を理解します。"
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

# ステージングでの Marketplace の VM プランのテスト

ステージングとは、SKU をプライベートの「サンドボックス」にデプロイすることです。そこから、Marketplace にデプロイする前に、機能をテストおよび検証できます。ステージングでは、SKU はそれをデプロイしたお客様に表示されるのと同じように表示されます。VM イメージをステージングにプッシュするには認定を受ける必要があります。

## 手順 1: ステージングへのプランのプッシュ

1. **[発行]** タブで **[ステージングにプッシュ]** をクリックします。

  ![図](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)

2. この時点で発行ポータルによって通知されたエラーまたは不一致を修正します。
3.	上記のスクリーンショットに示されているように、ポップアップ ボックス内の [Azure プレビュー ポータル](https://portal.azure.com)でプランをプレビューするために使用する Azure サブスクリプションのリストを指定します。
4. 前の手順で示されている上記のいずれかの Azure サブスクリプションを使用して、[Azure プレビュー ポータル](http://portal.azure.com)にログインします。
5. プランを検索して、VM イメージについて以下の点を検証します。
  1. マーケティング コンテンツがギャラリーに正しく表示されているか

      ![img-map-portal](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

  2. VM イメージのエンド ツー エンドのデプロイ

> [AZURE.IMPORTANT]プランは、本番にプッシュする準備が整っている発行ポータル (**[発行]** > **[本番にプッシュするための承認を要求]**) を介して Microsoft に通知するまで、ステージング状態のままです。この時期が、プランのライブへの移行の準備段階で必要な項目をチームのすべてのメンバーにチェックさせるのに最適です。

> データ センター全体をレプリケーションするには、24 ～ 48 時間掛かります。レプリケーションが完了すると、プランが [Azure Marketplace](http://azure.microsoft.com/marketplace) に一覧表示されます。

## 次のステップ
これで、プランは「ステージングされた」状態になり、その機能とマーケティング コンテンツがテストされたので、最終プランおよび/または SKU 発行段階である**手順 4** の「[Deploying your offer to the Marketplace (Marketplace へのプランのデプロイ)](marketplace-publishing-push-to-production.md)」に進むことができます。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

<!---HONumber=Nov15_HO3-->