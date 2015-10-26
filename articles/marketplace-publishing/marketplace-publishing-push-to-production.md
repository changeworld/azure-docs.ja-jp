<properties
   pageTitle="Azure Marketplace へのプランのデプロイ | Microsoft Azure"
   description="プラン (仮想マシン イメージ、開発者向けサービス、データ サービスなど) を Azure Marketplace にデプロイする方法を手順に従って学習します。"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="AzureStore"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio" />

# Azure Marketplace へのプランのデプロイ
プラン (テスト済み顧客シナリオやマーケティング コンテンツなど) の内容に問題がなく、発行の準備が整ったら、**[発行]** タブで**本番へのプッシュ**を要求します。

1.	チュートリアル ページの次の 4 つの手順が、問題なく完了している必要があります。

  ![図][img-pubportal-walkthru-checked]

2. 左側のリストから **[発行]** タブを選択します。

  ![図][img-pubportal-menu-publish]

3. このオプションを選択すると、次のページが表示されます。**[本番にプッシュするための承認を要求]** オプションをクリックします。要求が行われると、承認チームが最終レビューを実行し、その後、プランが Marketplace で公開されます。プランの状態は**「一覧表示済み」**に変更されます。

  ![図][img-pubportal-publish-pushproduction]

> [AZURE.IMPORTANT]**「ドラフト」**状態の間は、プランをいつでも削除できます (つまり、**ステージングにプッシュ**や**本番にプッシュ**されていない状態)。ドラフトを削除するページの下部にある **[履歴]** タブの **[ドラフトの破棄]** ボタンをクリックします。

> **仮想マシン イメージ**の場合、データ センター全体をレプリケーションするには、24 ～ 48 時間掛かります。レプリケーションが完了すると、プランが [Azure Marketplace](http://azure.microsoft.com/marketplace) に一覧表示されます。

## 次のステップ
プランが公開されたら、顧客シナリオをテストし、ステージング環境でテストおよび検証したとおりに、運用環境でもすべてのコントラクトと機能が適切に機能することを検証します。

## 関連項目
- [Getting Started: How to publish an offer to the Azure Marketplace (概要: Azure Marketplace へのプランの発行方法)](marketplace-publishing-getting-started.md)

[img-pubportal-walkthru-checked]: media/marketplace-publishing-push-to-production/pubportal-walkthru-checked.png
[img-pubportal-menu-publish]: media/marketplace-publishing-push-to-production/pubportal-menu-publish.png
[img-pubportal-publish-pushproduction]: media/marketplace-publishing-push-to-production/pubportal-publish-pushproduction.png

<!---HONumber=Oct15_HO3-->