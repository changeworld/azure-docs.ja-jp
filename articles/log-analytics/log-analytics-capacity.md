<properties
    pageTitle="Log Analytics の容量管理ソリューション | Microsoft Azure"
    description="Log Analytics の容量計画ソリューションを利用して、System Center Virtual Machine Manager で管理する Hyper-V サーバーの容量を把握できます。"
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>


# <a name="capacity-management-solution-in-log-analytics"></a>Log Analytics の容量管理ソリューション


Log Analytics の容量計画ソリューションを利用して、System Center Virtual Machine Manager で管理する Hyper-V サーバーの容量を把握できます。 このソリューションでは、System Center Operations Manager と System Center Virtual Machine Manager の両方が必要です。 直接接続型のエージェントのみを使用している場合、容量計画は利用できません。 このソリューションをインストールすると、Operations Manager エージェントが更新されます。 このソリューションは監視対象サーバーのパフォーマンス カウンターを読み取り、使用状況データを処理のためにクラウド上の OMS サービスに送ります。 使用状況データにロジックが適用されて、クラウド サービスによってそのデータが記録されます。 時間の経過と共に、使用状況パターンが識別されて、現在の消費量に基づいて容量が予測されます。

たとえば、予測によって、追加のプロセッサ コアまたは追加のメモリが個々のサーバーに必要になるときがわかります。 この例では、予測により 30 日以内にサーバーに追加メモリが必要になることが示されています。 これにより、サーバーの次のメンテナンス期間 (たとえば 2 週間ごとに実施される) 中にメモリのアップグレードを計画できます。

>[AZURE.NOTE] 容量管理ソリューションはワークスペースに追加できません。 容量管理ソリューションをインストールしているお客様は、引き続きソリューションを利用できます。  

容量管理ソリューションは、お客様が報告した次の問題を解決するために現在更新中です。

- Virtual Machine Manager と Operations Manager を使用する必要がある
- グループに基づいてカスタマイズしたり、フィルターを適用したりできない
- 1 時間に 1 回というデータ集計の頻度が不十分
- VM レベルの分析がない
- データの信頼性

新しい容量ソリューションには、次のメリットがあります。

- 信頼性と精度が高まり、きめ細かなデータ収集がサポートされる
- VMM 不要の Hyper-V サポート
- PowerBI におけるメトリックの視覚化
- VM レベルの使用率の分析


## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

- Operations Manager は、容量管理ソリューションに必須です。
- Virtual Machine Manager は、容量管理ソリューションに必須です。
- Operations Manager を Virtual Machine Manager (VMM) に接続する必要があります。 システムの接続の詳細については、「 [VMM を Operations Manager と接続する方法](http://technet.microsoft.com/library/hh882396.aspx)」をご覧ください。
- Operations Manager を Log Analytics に接続する必要があります。
- 「 [ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って容量管理ソリューションを OMS ワークスペースに追加します。  さらに手動で構成する必要はありません。


## <a name="capacity-management-data-collection-details"></a>容量管理データ収集の詳細

容量管理では、有効になっているエージェントを使用して、パフォーマンス データ、メタデータ、状態データを収集します。

次の表は、容量管理におけるデータの収集手段と、データ収集方法に関する各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
|---|---|---|---|---|---|---|
|Windows|![いいえ](./media/log-analytics-capacity/oms-bullet-red.png)|![あり](./media/log-analytics-capacity/oms-bullet-green.png)|![なし](./media/log-analytics-capacity/oms-bullet-red.png)|            ![はい](./media/log-analytics-capacity/oms-bullet-green.png)|![あり](./media/log-analytics-capacity/oms-bullet-green.png)| 時間単位|

次の表は、容量管理によって収集されるデータ型の例を示しています。

|**データの種類**|**フィールド**|
|---|---|
|Metadata|BaseManagedEntityId、ObjectStatus、OrganizationalUnit、ActiveDirectoryObjectSid、PhysicalProcessors、 NetworkName、IPAddress、ForestDNSName、NetbiosComputerName、VirtualMachineName、LastInventoryDate、HostServerNameIsVirtualMachine、IP Address、NetbiosDomainName、LogicalProcessors、DNSName、DisplayName、DomainDnsName、ActiveDirectorySite、PrincipalName、OffsetInMinuteFromGreenwichTime|
|パフォーマンス|ObjectName、CounterName、PerfmonInstanceName、PerformanceDataId、PerformanceSourceInternalID、SampleValue、TimeSampled、TimeAdded|
|状態|StateChangeEventId、StateId、NewHealthState、OldHealthState、コンテキスト、TimeGenerated、TimeAdded、StateId2、BaseManagedEntityId、MonitorId、HealthState、LastModified、LastGreenAlertGenerated、DatabaseTimeModified|

## <a name="capacity-management-page"></a>[容量管理] ページ


 容量管理ソリューションをインストールすると、監視対象のサーバーの容量を表示することができます。それには、OMS の **[概要]** ページの **[容量計画]** タイルを使用します。

![[容量計画] タイルの画像](./media/log-analytics-capacity/oms-capacity01.png)

このタイルをクリックすると、 **[容量管理]** ダッシュボードが開き、サーバーの容量の要約が表示されます。 このページには、クリック可能な以下のタイルが表示されます。

- *仮想マシンのカウント*: 仮想マシンの容量について残りの日数を示します。
- *コンピューティング*: プロセッサ コアと使用可能なメモリを示します。
- *ストレージ*: 使用されているディスク領域と平均ディスク待機時間を示します。
- *検索*: OMS システムで任意のデータを検索するときに使用できるデータ エクスプローラーです。

![[容量管理] ダッシュ ボードの画像](./media/log-analytics-capacity/oms-capacity02.png)


### <a name="to-view-a-capacity-page"></a>容量に関するページを表示するには

- **[概要]** ページで、**[容量管理]** をクリックし、**[Compute]** または **[Storage]** をクリックします。

## <a name="compute-page"></a>[コンピューティング] ページ

Microsoft Azure OMS の **[コンピューティング]** ダッシュボードを使用すると、インフラストラクチャに関連する使用率、容量の予想日数、効率性に関する容量情報を確認できます。 仮想マシン ホストでの CPU コアおよびメモリの使用状況を確認するには、 **[使用率]** 領域を使用します。 予測ツールを使用すると、特定の日付範囲に利用できると予想される容量を推定できます。 **[効率]** 領域を使用すると、仮想マシン ホストの効率性を確認できます。 リンクされた項目をクリックするとその項目の詳細を表示できます。

次のカテゴリの Excel ブックを生成できます。

- コア使用率の高いホスト上位
- メモリ使用率の高いホスト上位
- 仮想マシンの効率が悪いホスト上位
- 使用率別ホスト上位
- 使用率別ホスト下位

![容量管理の [コンピューティング] ページの画像](./media/log-analytics-capacity/oms-capacity03.png)


**[コンピューティング]** ダッシュボードには次の領域が表示されます。

**使用率**: 仮想マシン ホストの CPU コアとメモリの使用率が表示されます。

- *使用中のコア*: すべてのホストについての合計 (使用された CPU の % にホストの物理コアの数を掛けた値)。
- *空きコア*: 合計物理コア数から使用中のコアを引いた値。
- *使用可能なコアの割合*: 空き物理コア数を物理コア数合計で割った値。
- *VM あたりの仮想コア数*: システム内の合計仮想コア数を、システム内の仮想マシンの合計数で割った値。
- *仮想コアと物理コアの比率*: システム内の仮想マシンによって使用されている物理コア数に対する合計物理コア数の比率。
- *使用可能な仮想コアの数*: 仮想コアと物理コアの比率に使用可能な物理コア数を掛けた値。
- *メモリ使用量*: すべてのホストによって使用されているメモリの合計。
- *空きメモリ*: 合計物理メモリから使用中のメモリを引いた値。
- *使用可能なメモリの割合*: 空き物理メモリを合計物理メモリで割った値。
- *VM あたりの仮想メモリ*: システム内の合計仮想メモリをシステム内の仮想マシンの合計数で割った値。
- *仮想メモリと物理メモリの比率*: システム内の合計仮想メモリをシステム内の合計物理メモリで割った値。
- *使用可能な仮想メモリ*: 仮想メモリと物理メモリの比率に使用可能な物理メモリを掛けた値。

**予測ツール**

予測ツールを使用すると、リソースの使用率の履歴傾向を見ることができます。 これには、仮想マシン、メモリ、コア、およびストレージの利用状況の傾向が含まれます。 予測機能は、予測アルゴリズムを使用して、各リソースがいつごろなくなるかがわかるようにします。 これを参考にして適切な容量計画を計算し、追加容量 (メモリ、コア、ストレージなど) を購入する必要がある時期を知ることができます。

**効率性**

- *アイドル VM*: 指定された期間の CPU およびメモリの使用率が 10% 未満。
- *使用超過 VM*: 指定された期間の CPU およびメモリの使用率が 90% 超。
- *アイドル ホスト*: 指定された期間の CPU およびメモリの使用率が 10% 未満。
- *使用超過ホスト*: 指定された期間の CPU およびメモリの使用率が 90% 超。

### <a name="to-work-with-items-on-the-compute-page"></a>[コンピューティング] ページの項目を操作するには

1. **[Compute]** ダッシュボードの **[使用率]** 領域で、使用されている CPU コアとメモリに関する容量の情報を確認します。
2. 項目をクリックすると、 **[検索]** ページにその項目が表示され、詳細な情報が表示されます。
3. **予測** ツールでは、日付スライダーを移動して選択した日付に使用される容量の予測を表示します。
4. **[効率]** 領域で、仮想マシンおよび仮想マシン ホストに関する容量の効率性の情報を確認します。

## <a name="direct-attached-storage-page"></a>[直接接続ストレージ] ページの使用方法

OMS の **[直接接続ストレージ]** ダッシュボードを使用して、ストレージの使用率、ディスクのパフォーマンス、および予想されるディスク容量日数に関する情報を表示できます。 仮想マシン ホストのディスク領域の使用率を見るには、 **[使用率]** 領域を使用します。 **[ディスク パフォーマンス]** 領域を使用すると、仮想マシン ホストのディスクのスループットと待機時間を見ることができます。 また、予測ツールを使用すると、特定の日付範囲に利用できると予想される容量を推定できます。 リンクされた項目をクリックするとその項目の詳細を表示できます。

この容量情報から、次のカテゴリの Excel ブックを生成できます。

- ホスト別ディスク領域使用率上位
- ホスト別平均待機時間上位

**[ストレージ]** ページには次の領域が表示されます。

- *使用率*: 仮想マシン ホストのディスク領域の使用状況を表示します。
- *ディスク領域合計*: すべてのホストの合計 (論理ディスク領域)
- *使用済みディスク領域*: すべてのホストの合計 (使用済みの論理ディスク領域)
- *使用可能なディスク領域*: 合計ディスク領域から使用済みディスク領域を引いたもの
- *使用済みのディスクの割合*: 使用済みディスク領域を合計ディスク領域で割った値
- *使用可能なディスクの割合*: 使用可能なディスク領域を合計ディスク領域で割った値

![容量管理の [直接接続ストレージ] ページの画像](./media/log-analytics-capacity/oms-capacity04.png)

**ディスク パフォーマンス**

OMS を使用すると、ディスク領域の使用状況の履歴傾向を見ることができます。 予測機能は、将来の使用状況を推測するためのアルゴリズムを使用します。 領域の使用状況の場合、具体的には、予測機能によりディスク領域が不足するのがいつごろかを予測できます。 これは、適切なストレージを計画し、ストレージを追加購入する必要がある時期を把握するのに役立ちます。

**予測ツール**

予測ツールを使用すると、ディスク領域の使用率の履歴傾向を見ることができます。 また、ディスク領域が不足するタイミングを予想できます。 これは、適切な容量を計画し、ストレージ容量を追加購入する必要がある時期を把握するのに役立ちます。

### <a name="to-work-with-items-on-the-direct-attached-storage-page"></a>[直接接続ストレージ] ページの項目を使用するには

1. **[直接接続ストレージ]** ダッシュボードの **[使用率]** 領域では、ディスク使用率情報を見ることができます。
2. リンク項目をクリックすると、 **[検索]** ページにその項目が表示され、詳細な情報が表示されます。
3. **[ディスク パフォーマンス]** 領域では、ディスクのスループットと待機時間の情報を見ることができます。
4. **予測ツール**では、日付スライダーを移動して選択した日付に使用される容量の予測を表示します。


## <a name="next-steps"></a>次のステップ

- [Log Analytics のログ検索機能](log-analytics-log-searches.md) を使用して、詳細な容量管理データを確認してください。



<!--HONumber=Oct16_HO2-->


