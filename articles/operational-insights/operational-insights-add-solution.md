<properties
    pageTitle="Operational Insights のソリューション"
    description="ソリューションを使用して Operational Insights に追加機能を追加できます。"
    services="operational-insights"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="operational-insights"
    ms.workload="operational-insights"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/02/2015"
    ms.author="banders"/>

# Operational Insights のソリューション

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure オペレーション インサイトには、基本の "構成の評価" モジュールが含まれています。ただし、[概要] ページからソリューション追加して、追加の機能を入手できます。

![ソリューション アイコンの画像](./media/operational-insights-add-solution/sol-gallery.png)

ソリューションを追加すると、データはインフラストラクチャのサーバーから収集され、Operational Insights サービスに送信されます。オペレーション インサイト サービスによる処理は数分から数時間かかることがあります。サービスによってデータが処理されると、オペレーション インサイトでそのデータを表示できます。

不要になったソリューションは、簡単に削除できます。ソリューションを削除すると、データは Operational Insights に送信されなくなります。それにより、1 日あたりのクォータに計上されるデータの量が減少します。

## Microsoft Monitoring Agent でサポートされているソリューション

現時点では、Microsoft Monitoring Agent を使用して Microsoft Azure Operational Insights に直接接続されているサーバーでは、次のような利用可能なソリューションの多くを使用できます。

- [システムの更新](operational-insights-updates.md)

- [ログの管理](operational-insights-log-collection.md)

- [マルウェア対策](operational-insights-antimalware.md)

- [変更の追跡](operational-insights-change-tracking.md)

- [SQL と Active Directory の評価](operational-insights-assessment.md)

ただし、次のソリューションは、Microsoft Monitoring Agent でサポート*されておらず*、System Center Operations Manager (SCOM) が必要です。

- [容量管理](operational-insights-capacity.md)

- [アラートの管理](operational-insights-alerts.md)

- [構成の評価](operational-insights-solutions.md#configuration-assessment)

これらのソリューションを Operations Manager で使用するガイダンスについては、「[Operations Manager と Operational Insights の連携に関する考慮事項](operational-insights-operations-manager.md)」を参照してください。

IIS ログの収集は、以下がインストールされたコンピューターでサポートされています。

- Windows Server 2012

- Windows Server 2012 R2

### ソリューションを追加するには


1. Operational Insights の [概要] ページで、**[Solutions Gallery (ソリューション ギャラリー)]** タイルをクリックします。


2. Operational Insights ソリューション ギャラリー ページでは、利用可能な各ソリューションの詳細を確認できます。Operational Insights に追加するソリューションの名前をクリックします。


3. 選択したソリューションのページに、ソリューションに関する詳細情報が表示されます。**[追加]** をクリックします。


4. [確認] ページで **[承認]** をクリックし、プライバシーに関する声明と使用条件に同意します。


5. 追加したソリューションの新しいタイルが、Operational Insights の [概要] ページに表示されます。Operational Insights サービスによってデータが処理されると、そのタイルが使用できるようになります。




### ソリューションを削除するには



1. Operational Insights の [概要] ページで、**[Solutions Gallery (ソリューション ギャラリー)]** タイルをクリックします。


2. Operational Insights ソリューション ギャラリー ページで削除するソリューションの **[削除]** をクリックします。


3. [確認] ページで **[はい]** をクリックすると、ソリューションが削除されます。

<!---HONumber=July15_HO2-->