<properties 
   pageTitle="System Center Operations Manager からオペレーション インサイトに接続する" 
   description="Operations Manager を使用してオペレーション インサイトに接続する方法を説明します." 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# System Center Operations Manager からオペレーション インサイトに接続する 

オペレーション インサイトを既存の System Center Operations Manager 環境に接続することができます。これにより、既存の Operations Manager エージェントをオペレーション インサイト エージェントとして使用できます。

 >[AZURE.NOTE] オペレーション インサイトのサポートは、Operations Manager 2012 SP1 UR6 および Operations Manager 2012 R2 UR2 以降で使用できます。プロキシ サポートは、SCOM 2012 SP1 UR7 および SCOM 2012 R2 UR3 に追加されました。

## オペレーション インサイトへの SCOM の接続とエージェントの追加

1. Operations Manager コンソールで、**[管理]** をクリックします。

2. **[オペレーション インサイト]** ノードを展開して、**[オペレーション インサイトの接続]** をクリックします。

3. **[オペレーション インサイトに登録する]** リンクをクリックして画面の指示に従います。 

4. 登録ウィザードの完了後、**[コンピューター/グループの追加]** をクリックします。

5. **[コンピューターの検索]** ダイアログ ボックスで、Operations Manager で監視されるコンピューターまたはグループを検索できます。オペレーション インサイトに追加するコンピューターまたはグループを選択し、**[追加]** をクリックします。**[OK]** をクリックします。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ

[プロキシとファイアウォール設定の構成 (省略可能)](https://msdn.microsoft.com/library/azure/dn884643.aspx)

<!--HONumber=52-->