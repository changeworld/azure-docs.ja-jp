<properties
   pageTitle="&quot;システムの更新&quot; でサーバーを更新する"
   description="Microsoft Azure Operational Insights で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用する方法について説明します。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders" />

# "システムの更新" でサーバーを更新する

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。ソリューションをインストールして、Operations Manager エージェントおよびオペレーション インサイトの基本構成モジュールを更新します。更新プログラムの情報が監視対象のサーバーから読み取られた後、更新データがクラウド内のオペレーション インサイト サービスに送信されて処理されます。更新データにはロジックが適用され、クラウド サービスによってそのデータが記録されます。不足している更新プログラムが見つかった場合、**[更新プログラム]** ダッシュボードに表示されます。**[更新プログラム]** ダッシュボードを使用して、更新プログラムの適用漏れに対処したり、更新プログラムを必要とするサーバーに、それらを適用するためのプランを作成したりすることができます。

## "システムの更新" を使用してサーバーを更新する

Microsoft Azure Operational Insights でシステムの更新を使用する前に、ソリューションがインストールされている必要があります。ソリューションのインストールについて詳しくは、[ソリューション ギャラリーを使用したソリューションの追加または削除](operational-insights-setup-workspace.md)に関する記事を参照してください。インストール後、監視対象のサーバーに不足している更新プログラムを確認するには、オペレーション インサイトの **[概要]** ページの **[System Update Assessment]** タイルを使用します。

### 更新プログラムに関する作業を行うには

1. **[概要]** ページの **[System Update Assessment]** タイルをクリックします。![[概要] ページの画像](./media/operational-insights-updates/updates01.png)
2. **[更新プログラム]** ダッシュボードで更新プログラムのカテゴリを表示します。![[更新プログラム] ページの画像](./media/operational-insights-updates/updates02.png)
3. ページの右にスクロールし、**[Type of Updates Missing]** ブレードを表示し、**[セキュリティ更新プログラム]**をクリックします。![[更新プログラム] ページの画像](./media/operational-insights-updates/updates03.png)
4. [検索] ページでは、インフラストラクチャ内のサーバーから不足していると検出されたセキュリティ更新プログラムの一覧が表示されます。不足している更新プログラムの詳細については、サポート情報の記事 ID (KBID) をクリックします。この例では、*KBID 3032323* です。![[更新プログラム] ページの画像](./media/operational-insights-updates/updates04.png)
5. Web ブラウザーでは、更新プログラムを説明しているサポート情報の記事が表示されます。![[更新プログラム] ページの画像](./media/operational-insights-updates/updates05.png)
6. 検出された情報を基に、不足している更新プログラムを適用するためのプランを作成することができます。

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->