<properties
   pageTitle="&quot;ログの管理&quot; を使用する方法"
   description="Microsoft Azure オペレーション インサイトの &quot;ログの管理&quot; を使用すると、監視対象のサーバーから収集されたログ イベントを確認することができます。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="operational-insights"
   ms.date="07/02/2015"
   ms.author="banders"/>

# "ログの管理" の使用

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft Azure Operational Insights でログの管理を使用するには、ソリューションが事前にインストールされている必要があります。このパックは構成が必要であり、既定では何も収集されません。そのため、新しいワークスペースではすべて、既定で有効化されます。削除した場合は、ソリューション ギャラリーからもう一度追加することができます。[ソリューション ギャラリーを使用してソリューションを追加または削除する方法に関するページ](operational-insights-add-solution.md)を参照してください。

イベントを収集するためのログは新たに追加することができます。また、ログの収集対象とするイベント レベルや重要度を指定することもできます。"ログの管理" の構成後、収集ポリシーが適用され、イベントの収集が開始されます。監視対象のサーバーから収集されたログ イベントの最初の分析結果には、Operational Insights の **[概要]** ページの **[ログの管理]** タイルを使用してアクセスできます。

![[ログの管理] タイルの画像](./media/operational-insights-log-collection/overview-log-mgt.png)

このタイルから **[ログの管理]** ページにアクセスすると、ログに記録されているイベントの概要を確認できます。他の種類のデータと同様、ログ検索機能を直接使用することもできます。


ページには次のカテゴリの詳細が表示されます。

- Windows イベント ログ
- IIS ログ
- 追加した他のログ

![[ログの管理] ダッシュボードの画像](./media/operational-insights-log-collection/gallery-logmgt-01.png)

![[ログの管理] ダッシュボードの画像](./media/operational-insights-log-collection/gallery-logmgt-02.png)

## IIS ログ ファイル形式

現時点でサポートされている IIS ログ形式は W3C のみですが、最も一般的な形式であり、IIS 7 や IIS 8 でも既定の形式として使われているのでご安心ください。NCSA または IIS のネイティブ形式で記録されているログは、オペレーション インサイトでは一切収集されません。W3C 形式であっても、既定では、一部のフィールドが記録されません。形式の詳細については、「[ログ記録する W3C フィールドを選択する (IIS 7)](https://technet.microsoft.com/library/cc754702(v=WS.10).aspx))」を参照してください。


> [AZURE.TIP]ログ検索の利便性を高めるために、IIS の **[ログ]** を使用して、各 Web サイトのログ フィールドをすべて選択するようお勧めします。また、新しいログの **[ログ ファイル ロールオーバー]** スケジュールは、**[毎時間]** に変更することをお勧めします。クラウドにアップロードされるファイルのサイズが小さくなるので帯域幅の節約になります。


### Operations Manager または直接接続されたエージェントから Windows イベント ログを収集するには

1. **[概要]** ページの **[ログの管理]** タイルをクリックします。

2. **[ログの管理]** ダッシュボードの **[構成]** タイルをクリックします。

3. 情報の収集元となるイベント ログの名前を入力します。使用する名前がわからない場合は、**イベント ビューアー**で Windows イベント ログのプロパティを選択し、**フル ネーム** フィールドから名前をコピーして、**[次のイベント ログからイベントを収集する]** ボックスに貼り付けます。

4. **[+]** をクリックしてログを追加します。

5. ログの収集対象とするイベントのレベルまたは重要度を選択します。**成功の監査**イベントと**失敗の監査**イベントは、このリリースではサポートされません。

6. 情報の収集対象の各ログについて以上の手順を繰り返し、**[保存]** をクリックします。

7. 数分でオペレーション インサイトにイベントが表示され、データを検索できるようになります。



### Operations Manager または直接接続されたエージェントから IIS ログを収集するには

1. **[概要]** ページの **[ログの管理]** タイルをクリックします。

2. **[ログの管理]** ダッシュボードの **[構成]** タイルをクリックします。

3. **[IIS ログ]** の **[Operations Manager からログを収集する]** を選択します。


### Azure 診断から IIS ログまたは Windows イベントを収集するには
この構成は、Operational Insights ポータルではなく、Azure の管理ポータルで行うことができます。ご使用のワークスペースから **[ストレージ]** タブに移動し、そのストレージ アカウントからログの収集を有効にすることができます。


### ログ ファイルを操作するには

1. **[概要]** ページの **[ログの管理]** タイルをクリックします。

2. **[ログの管理]** ダッシュボードでイベント ログを表示し、操作対象のログを選択します。

3. **[検索]** ページで、詳細な情報を確認するいずれかの項目をクリックします。

4. 最初の結果から新たにログ検索を実行したうえでログを分析し、それらを相互に関連付けることができます。


## その他のリソース
- [Azure Operational Insights における IIS ログ形式の要件](http://blogs.technet.com/b/momteam/archive/2014/09/19/iis-log-format-requirements-in-system-center-advisor.aspx)
- 他のデータ ソースや他の種類のログも実装して欲しいという要望がコミュニティから寄せられています。それらについては、[フィードバック フォーラム](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)をご覧ください。

<!---HONumber=July15_HO2-->