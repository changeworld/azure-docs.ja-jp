<properties 
    pageTitle="オペレーション インサイト インテリジェンス パック" 
    description="インテリジェンス パックを使用してオペレーション インサイトに追加機能を追加できます。" 
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
    ms.topic="article" 
    ms.date="03/20/2015" 
    ms.author="banders"/>

# オペレーション インサイト インテリジェンス パック

Microsoft Azure オペレーション インサイトには、基本の "構成の評価" モジュールが含まれています。ただし、[概要] ページからインテリジェンス パックを追加して、追加の機能を入手できます。

![インテリジェンス パックのアイコンの画像](./media/operational-insights-add-intelligence-pack/overview-intelpacks.png)

インテリジェンス パックを追加すると、データはインフラストラクチャのサーバーから収集され、オペレーション インサイト サービスに送信されます。オペレーション インサイト サービスによる処理は数分から数時間かかることがあります。サービスによってデータが処理されると、オペレーション インサイトでそのデータを表示できます。

不要になったインテリジェンス パックは、簡単に削除できます。インテリジェンス パックを削除すると、データはオペレーション インサイトに送信されなくなります。それにより、1 日あたりのクォータに計上されるデータの量が減少します。

## Microsoft Monitoring Agent でサポートされているインテリジェンス パック

現時点では、Microsoft Monitoring Agent を使用して Microsoft Azure オペレーション インサイトに直接接続されているサーバーでは、次のような利用可能なインテリジェンス パックの多くを使用できます。

- [システムの更新](operational-insights-updates.md)

- [ログの管理](operational-insights-log-collection.md)

- [マルウェア対策](operational-insights-antimalware.md)

- [変更の追跡](operational-insights-change-tracking.md)

- [SQL と Active Directory の評価](operational-insights-assessment.md)

ただし、次のインテリジェンス パックは、Microsoft Monitoring Agent でサポートされて *いません*。

- [容量管理](operational-insights-capacity.md)

- [構成の評価](operational-insights-configuration-assessment.md)

IIS ログの収集は、以下がインストールされたコンピューターでサポートされています。

- Windows Server 2012

- Windows Server 2012 R2

### インテリジェンス パックを追加するには


1. オペレーション インサイトの [概要] ページで、**[インテリジェンス パック]** タイルをクリックします。


2. オペレーション インサイト インテリジェンス パック ギャラリー ページでは、利用可能な各インテリジェンス パックの詳細を確認できます。オペレーション インサイトに追加するインテリジェンス パックの名前をクリックします。


3. 選択したインテリジェンス パックのページに、インテリジェンス パックに関する詳細情報が表示されます。**[追加]** をクリックします。


4. [確認] ページで **[承認]** をクリックし、プライバシーに関する声明と使用条件に同意します。


5. 追加したインテリジェンス パックの新しいタイルが、オペレーション インサイトの [概要] ページに表示されます。オペレーション インサイト サービスによってデータが処理されると、そのタイルが使用できるようになります。




### インテリジェンス パックを削除するには



1. オペレーション インサイトの [概要] ページで、**[インテリジェンス パック]** タイルをクリックします。


2. オペレーション インサイト インテリジェンス パック ギャラリー ページで、削除するインテリジェンス パックの **[削除]** をクリックします。


3. [確認] ページで **[はい]** をクリックすると、インテリジェンス パックが削除されます。




<!--HONumber=52-->