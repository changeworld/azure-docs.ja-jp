<properties
   pageTitle="Marketplace のソリューション テンプレート プランのテスト | Microsoft Azure"
   description="Azure Marketplace のソリューション テンプレート プランをテストする方法を理解します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/08/2015"
   ms.author="hascipio; v-divte" />

# ステージングでのソリューション テンプレート プランのテスト
ステージングとは、プランをプライベートの「サンドボックス」にデプロイすることです。そこでは、本番にプッシュする前に、機能をテストおよび検証できます。ステージングでは、プランはそれをデプロイしたお客様に表示されるのと同じように表示されます。プランを**ステージングにプッシュするには認定を受ける必要があります**。

プランがステージングされると、[Azure プレビュー ポータル](https://ms.portal.azure.com/)でプランを表示したり、テストしたりできるようになります。

次の手順に従って、プランをステージングにプッシュし、[Azure プレビュー ポータル](https://ms.portal.azure.com/)でテストを実行してください。

1.	[[発行ポータル]](https://publish.windowsazure.com) に移動し、**[ソリューション テンプレート]** タブ、使用するプラン、**[発行]** を選択し、**[ステージングにプッシュ]** をクリックします。
2.	プランのプレビューおよびテストに使用する Azure サブスクリプションの一覧を指定します。
3.	前の手順でプランをステージングしたときに使用したものと同じサブスクリプション ID を使用して、Azure プレビュー ポータルにログインします。
4.	以下の点について、Azure プレビュー ポータルで少なくとも 1 回テストを実行します。
  -	マーケティング コンテンツがギャラリーに正しく表示されているか
  -	トポロジのエンド ツー エンドのデプロイ
  -	パフォーマンス テストとストレス テストの実行
  -	トポロジがベスト プラクティスに準拠しているか

## 次のステップ
結果に問題がなければ、プラン発行の最終段階である**手順 4** の「[Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)」に進むことができます。問題があれば、プランに変更を加え、もう一度認定を要求してください。

> [AZURE.NOTE]マーケティング コンテンツの変更であれば、認定は必要ありません。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

<!---HONumber=Oct15_HO3-->