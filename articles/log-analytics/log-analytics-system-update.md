---
title: Log Analytics のシステム更新評価ソリューション | Microsoft Docs
description: Log Analytics で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2016
ms.author: banders

---
# <a name="system-update-assessment-solution-in-log-analytics"></a>Log Analytics のシステム更新評価ソリューション
Log Analytics で、システムの更新ソリューションを使用して、インフラストラクチャのサーバーに不足している更新プログラムを効率よく適用することができます。 ソリューションのインストール後、監視対象のサーバーに不足している更新プログラムを確認するには、OMS の **[概要]** ページの **[システム更新評価]** タイルを使用します。

不足している更新プログラムが見つかった場合、 **[Updates]** (更新プログラム) ダッシュボードに表示されます。 **[更新プログラム]** ダッシュボードを使用して、更新プログラムの適用漏れに対処したり、更新プログラムを必要とするサーバーに、それらを適用するためのプランを作成したりすることができます。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* 「 [Add Log Analytics solutions from the Solutions Gallery (ソリューション ギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」で説明されている手順に従ってシステム更新評価ソリューションを OMS ワークスペースに追加します。  さらに手動で構成する必要はありません。

## <a name="system-update-data-collection-details"></a>システム更新データ収集の詳細
システム更新の評価では、有効になっているエージェントを使用して、メタデータおよび状態データを収集します。

次の表は、システム更新評価におけるデータの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |![あり](./media/log-analytics-system-update/oms-bullet-green.png) |![あり](./media/log-analytics-system-update/oms-bullet-green.png) |![なし](./media/log-analytics-system-update/oms-bullet-red.png) |![いいえ](./media/log-analytics-system-update/oms-bullet-red.png) |![あり](./media/log-analytics-system-update/oms-bullet-green.png) |1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後 |

次の表は、システムの更新の評価によって収集されるデータ型の例を示しています。

| **データの種類** | **フィールド** |
| --- | --- |
| Metadata |BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、 NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime |
| State |StateChangeEventId、StateId、NewHealthState、OldHealthState、コンテキスト、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified |

### <a name="to-work-with-updates"></a>更新プログラムに関する作業を行うには
1. **[概要]** ページの **[システム更新評価]** タイルをクリックします。  
    ![[システム更新評価] タイル](./media/log-analytics-system-update/sys-update-tile.png)
2. **[更新プログラム]** ダッシュボードで更新プログラムのカテゴリを表示します。  
    ![[更新プログラム]ダッシュボード](./media/log-analytics-system-update/sys-updates02.png)
3. ページを右方向にスクロールして **[Windows の重要な更新プログラム/セキュリティ更新プログラム]** ブレードを表示し、**[分類]** の下の **[セキュリティ更新プログラム]** をクリックします。  
    ![セキュリティ更新プログラム](./media/log-analytics-system-update/sys-updates03.png)
4. [ログ検索] ページには、インフラストラクチャのサーバーに不足していることが検出されたセキュリティ更新プログラムの一覧に関するさまざまな情報が表示されます。 更新プログラムの詳細情報を表示するには、**[一覧]** をクリックします。  
    ![検索結果 - 一覧](./media/log-analytics-system-update/sys-updates04.png)
5. [ログ検索] ページに、各更新プログラムに関する詳細情報が表示されます。 KBID 番号の横にある **[表示]** をクリックして、Microsoft サポート Web サイトの該当する記事を表示します。  
    ![ - サポート技術情報の表示](./media/log-analytics-system-update/sys-updates05.png)
6. Web ブラウザーが開き、更新プログラムに関する Microsoft サポート Web ページが新しいタブに表示されます。 不足している更新プログラムの情報を確認します。  
    ![Microsoft サポート Web ページ](./media/log-analytics-system-update/sys-updates06.png)
7. 確認した情報を基に、不足している更新プログラムを手動で適用するためのプランを作成できます。または、この後の手順に従って、更新プログラムを自動的に適用できます。
8. 不足している更新プログラムを自動的に適用する場合は、**[更新プログラム]** ダッシュボードに戻り、**[更新プログラムの実行]** で、**[クリックすると更新実行のスケジュールを設定できます]** をクリックします。  
    ![[更新プログラムの実行 - スケジュール設定] タブ](./media/log-analytics-system-update/sys-updates07.png)
9. **[更新プログラムの実行]** ページの **[スケジュール設定]** タブで、**[追加]** をクリックして新しい更新プログラムの実行を作成します。  
    ![[スケジュール設定] タブ - 追加](./media/log-analytics-system-update/sys-updates08.png)
10. **[新しい更新プログラムの実行]** ページで、更新プログラムの実行の名前を入力し、個々のコンピューターまたはコンピューター グループを追加し、スケジュールを定義した後、**[保存]** をクリックします。  
    ![新しい更新プログラムの実行](./media/log-analytics-system-update/sys-updates09.png)
11. **[更新プログラムの実行]** ページの **[スケジュール設定]** タブに、スケジュール設定した新しい更新プログラムが表示されます。  
    ![[スケジュール設定] タブ](./media/log-analytics-system-update/sys-updates10.png)
12. 更新プログラムの実行が開始されると、**[実行中]** タブに情報が表示されます。  
    ![[実行中] タブ](./media/log-analytics-system-update/sys-updates11.png)
13. 更新プログラムの実行が完了すると、**[完了]** タブに状態が表示されます。
14. 更新が更新プログラムから適用された場合は、**[Windows の重要な更新プログラム/セキュリティ更新プログラム]** ブレードに表示される更新プログラムの数が減少します。  
    ![[Windows の重要な更新プログラム/セキュリティ更新プログラム] ブレード - 更新プログラムの数が減少](./media/log-analytics-system-update/sys-updates12.png)

## <a name="next-steps"></a>次のステップ
* [ログを検索](log-analytics-log-searches.md) してシステム更新に関する詳しいデータを確認します。

<!--HONumber=Oct16_HO2-->


