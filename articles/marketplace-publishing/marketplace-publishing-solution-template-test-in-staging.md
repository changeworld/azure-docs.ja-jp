<properties
   pageTitle="Marketplace のソリューション テンプレート プランのテスト | Microsoft Azure"
   description="Azure Marketplace のソリューション テンプレート プランをテストする方法を理解します。"
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
   ms.date="12/04/2015"
   ms.author="hascipio; v-divte" />

# ステージングでのソリューション テンプレート プランのテスト
ステージングとは、プランをプライベートの「サンドボックス」にデプロイすることです。そこでは、本番にプッシュする前に、機能をテストおよび検証できます。ステージングでは、プランはそれをデプロイしたお客様に表示されるのと同じように表示されます。プランをステージングにプッシュするには認定を受ける必要があります。

プランがステージングされると、[Azure ポータル](https://portal.azure.com/)でプランを表示したり、テストしたりできるようになります。

次の手順に従って、プランをステージングにプッシュし、[Azure ポータル](https://portal.azure.com/)でテストしてください。

1.	[[発行ポータル]](https://publish.windowsazure.com) > **[ソリューション テンプレート]** タブ > 使用するプラン > **[発行]** > **[ステージングにプッシュ]** の順に移動します。
2.	プランのプレビューおよびテストに使用する Azure サブスクリプションの一覧を指定します。
3.	前の手順で使用したサブスクリプション ID を使用して、Azure プレビュー ポータルにサインインします。
4.	以下の点について、Azure プレビュー ポータルで少なくとも 1 回テストを実行します。
  -	マーケティング コンテンツが Azure Marketplace に正しく表示されているか
  -	トポロジのエンド ツー エンドのデプロイメント
  -	パフォーマンス テストとストレス テストの実行
  -	トポロジがベスト プラクティスに準拠しているか

## 次のステップ
結果に問題がなければ、プラン発行の最終段階である**手順 4** の「[Marketplace へのプランのデプロイ](marketplace-publishing-push-to-production.md)」に進むことができます。問題があれば、プランに変更を加え、もう一度認定を要求してください。

> [AZURE.NOTE]マーケティング コンテンツの変更であれば、認定は必要ありません。

公開元のタスクの詳細については、「 [概要: Azure Marketplace へのプランの発行方法](marketplace-publishing-getting-started.md)」を参照してください。

<!---HONumber=AcomDC_1210_2015-->