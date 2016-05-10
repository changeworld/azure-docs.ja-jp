<properties
	pageTitle="コスト管理の構成 | Microsoft Azure"
	description="DevTest ラボのコスト管理機能を構成する方法について説明します。"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/18/2016"
	ms.author="tarcher"/>

# コスト管理の構成

## 概要

DevTest ラボのコスト管理機能は、ラボのコストを追跡するのに役立ちます。この記事では、**月間推定コスト傾向**グラフを使用して、現在のカレンダー月の現時点までの推定コストおよび現在のカレンダー月の月末の予測コストを表示する方法を示します。

## 月間推定コスト傾向グラフを有効にする

月間推定コスト傾向グラフを有効にするには、次の手順に従います。

1. [Azure ポータル](https://portal.azure.com)にサインインします。

1. **[参照]** をタップし、一覧の **[DevTest ラボ]** をタップします。

1. ラボの一覧で目的のラボをタップします。

1. **[設定]** をタップします。

	![設定](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. ラボの **[設定]** ブレードで、**[コスト ポリシー]** の **[コストしきい値]** をタップします。

	![メニュー](./media/devtest-lab-configure-cost-management/menu.png)
 
1. **[コストしきい値]** ブレードで、この機能を有効にする場合は **[オン]** をタップし、無効にする場合は **[オフ]** をタップします。

1. **[保存]** をタップします。

この機能を有効にすると、グラフに推定コストと予測コストが表示されるまで数時間かかる場合があります。これは、サービスはこの情報を収集するために 1 時間ごとに実行されますが、ライブ データが収集されてから実行されるのは数時間後になるためです。たとえば、午前 1 時に VM を起動するとします。その VM に関連付けられているコストは、おそらく、2 ～ 3 時間はコスト グラフに組み込まれません。
 
次のスクリーン ショットはコスト グラフの例を示しています。

![コスト グラフ](./media/devtest-lab-configure-cost-management/graph.png)

**[推定コスト]** 値は現在のカレンダー月のこれまでの推定コストであるのに対し、**[予測コスト]** は現在のカレンダー月全体の推定コストです。

グラフの上に示されているとおり、グラフに表示されるコストは、[従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)プラン料金を使用した*推定*コストです。また、コスト計算には次のものは含まれて*いません*。

- プラン料金。現時点では、Microsoft または Microsoft パートナーとネゴシエートしたプラン料金 (サブスクリプションの下に表示される) は使用できません。従量課金料金を使用します。
- 税金
- 割引
- 請求通貨。現時点では、ラボのコストは USD 通貨でのみ表示されます。

## 次のステップ

次に試す操作をいくつか以下に示します。

- [ラボ ポリシーを定義する](./devtest-lab-set-lab-policy.md) - ラボとその VM の使用方法を制御するために使用されるさまざまなポリシーの設定方法を確認します。 
- [カスタム イメージを作成する](./devtest-lab-create-template.md) - VM を作成する場合は、ベースとしてカスタム イメージまたは Marketplace イメージを指定します。この記事では、VHD ファイルからカスタム イメージを作成する方法について説明します。
- [Marketplace イメージを構成する](./devtest-lab-configure-marketplace-images.md) - DevTest ラボでは、Azure Marketplace イメージに基づく新しい VM を作成することができます。この記事では、ラボで新しい VM を作成する際に使用できるようにする Azure Marketplace イメージ (該当するものがある場合) を指定する方法について説明します。
- [ラボで VM を作成する](./devtest-lab-add-vm-with-artifacts.md) - ベース イメージ (カスタムまたは Marketplace) から新しい VM を作成する方法と、VM でアーティファクトを操作する方法について説明します。

<!---HONumber=AcomDC_0427_2016-->