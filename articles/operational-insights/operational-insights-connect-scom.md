<properties
   pageTitle="System Center Operations Manager からオペレーション インサイトに接続する"
   description="Operations Manager を使用して Operational Insights に接続する方法を説明します。"
   services="operational-insights"
   documentationCenter=""
   authors="lauracr"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="lauracr"/>

# System Center Operations Manager からオペレーション インサイトに接続する


[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

オペレーション インサイトを既存の System Center Operations Manager 環境に接続することができます。これにより、データ収集に既存の Operations Manager エージェントを使用できます。Operational Insights での Operations Manager の使用の詳細については、「[Operations Manager と Operational Insights の連携に関する考慮事項](operational-insights-operations-manager.md)」を参照してください。

Operations Manager を使用して、次のワークロードのいずれかを監視する場合、Operations Manager をそのワークロードの実行アカウントとして設定する必要があります。アカウント設定の詳細については、「[Operations Manager と Operational Insights の連携に関する考慮事項](operational-insights-operations-manager.md)」を参照してください。

- SQL の評価
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE]オペレーション インサイトのサポートは、Operations Manager 2012 SP1 UR6 および Operations Manager 2012 R2 UR2 以降で使用できます。プロキシ サポートは System Center Operations Manager 2012 SP1 UR7 および System Center Operations Manager 2012 R2 UR3 に追加されました。

## オペレーション インサイトに Operations Manager を直接接続してエージェントを追加するには

1. Operations Manager コンソールで、**[管理]** をクリックします。

2. **[オペレーション インサイト]** ノードを展開して、**[オペレーション インサイトの接続]** をクリックします。

3. **[オペレーション インサイトに登録する]** リンクをクリックして画面の指示に従います。

4. 登録ウィザードの完了後、**[コンピューター/グループの追加]** をクリックします。![Operations Manager add a computer/group (Operations Manager でのコンピューター/グループの追加)](./media/operational-insights-connect-scom/om01.png)
5. **[コンピューターの検索]** ダイアログ ボックスで、Operations Manager で監視されるコンピューターまたはグループを検索できます。Operational Insights に追加するコンピューターまたはグループを選択し、**[追加]** をクリックしてから、**[OK]** をクリックします。![Operations Manager add computers (Operations Manager でのコンピューターの追加)](./media/operational-insights-connect-scom/om02.png)
## 次のステップ

[プロキシとファイアウォール設定の構成 (省略可能)](operational-insights-proxy-firewall.md)

<!---HONumber=August15_HO6-->