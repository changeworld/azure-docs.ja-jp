---
title: Azure Migrate での評価と依存関係の視覚化のトラブルシューティング
description: Azure Migrate での評価と依存関係の視覚化をトラブルシューティングするためのヘルプを提供します。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 3098e85fd21b6185defc4bbcf0a71d412846ab25
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722132"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>評価と依存関係の視覚化のトラブルシューティング

この記事は、[Azure Migrate: Server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool) でクリックします。


## <a name="assessment-readiness-issues"></a>アセスメントの準備状態

評価の準備状態を修正するには、次のようにします。

**問題点** | **解決策**
--- | ---
サポートされていないブートの種類 | Azure では、ブートの種類が EFI の VM はサポートされません。 移行を実行する前に、ブートの種類を BIOS に変換することをお勧めします。 <br/><br/>Azure Migrate Server Migration を使用して、そのような VM の移行を処理することができます。 それにより、移行中に VM のブートの種類が BIOS に変換されます。
条件付きでサポートされる Windows オペレーティング システム | オペレーティング システムはサポート期間が終了しており、[Azure でのサポート](https://aka.ms/WSosstatement)のためにはカスタム サポート契約 (CSA) が必要です。 Azure に移行する前にアップグレードを検討してください。
サポートされていない Windows オペレーティング システム | Azure では、[特定の Windows OS バージョン](https://aka.ms/WSosstatement)のみがサポートされています。 Azure に移行する前に、コンピューターのアップグレードを検討してください。
条件付きで動作が保証されている Linux OS | Azure では、[特定の Linux OS バージョン](../virtual-machines/linux/endorsed-distros.md)のみがサポートされます。 Azure に移行する前に、コンピューターのアップグレードを検討してください。
動作が保証されていない Linux OS | マシンは Azure で起動する可能性がありますが、オペレーティング システムは Azure ではサポートされていません。 Azure に移行する前に、[サポートされている Linux バージョン](../virtual-machines/linux/endorsed-distros.md)へのアップグレードを検討してください。
オペレーティング システムが不明です | VM のオペレーティング システムが、vCenter Server で "その他" として指定されました。 この動作により、Azure Migrate による VM の Azure 対応性の検証はブロックされます。 コンピューターを移行する前に、オペレーティング システムが Azure によって[サポートされている](https://aka.ms/azureoslist)ことを確認してください。
サポートされていないビット バージョン | 32 ビット オペレーティング システムの VM は Azure で起動する可能性がありますが、Azure に移行する前に、64 ビットにアップグレードすることをお勧めします。
Microsoft Visual Studio のサブスクリプションが必要です | コンピューターで実行されている Windows クライアント オペレーティング システムは、Visual Studio のサブスクリプションによってのみサポートされます。
必要なストレージ パフォーマンスの VM が見つかりません | そのマシンに必要なストレージ パフォーマンス (1 秒あたりの入力/出力操作数 (IOPS) とスループット) が Azure VM のサポート範囲を超えています。 移行前に、そのマシンのストレージ要件を緩和します。
必要なネットワーク パフォーマンスの VM が見つかりません | そのマシンに必要なネットワーク パフォーマンス (入力/出力) が Azure VM のサポート範囲を超えています。 そのマシンのネットワーク要件を緩和します。
指定した場所で VM が見つかりません | 移行前に、別のターゲットの場所を指定します。
不適切なディスクが 1 つ以上あります | VM に接続されている 1 つ以上のディスクで、Azure の要件が満たされていません。<br/><br/> Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium マネージド ディスクのディスク制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> VM に接続されている各ディスクについて、ディスクのサイズが (Ultra SSD ディスクでサポートされている) 64 TB 未満であることを確認します。<br/><br/> そうでない場合は、Azure に移行する前にディスクのサイズを減らすか、または Azure で複数のディスクを使用して[ストライピングし](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)、ストレージの上限を高くします。 各ディスクで必要とされるパフォーマンス (IOPS とスループット) が Azure の[仮想マシンの管理ディスク](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)でサポートされていることを確認してください。
不適切なネットワーク アダプターが 1 つ以上あります。 | 移行前に、マシンから未使用のネットワーク アダプターを削除します。
ディスクの数が制限を超えています | 移行前に、マシンから未使用のディスクを削除します。
ディスクのサイズが制限を超えています | Azure Migrate: 現在、Server Assessment では、Ultra SSD ディスクはサポートされておらず、Premium ディスクの制限 (32 TB) に基づいてディスクが評価されます。<br/><br/> ただし、Azure では (Ultra SSD ディスクでサポートされる) 最大 64 TB のディスク サイズまでサポートされています。 移行の前にディスクを 64 TB 未満に圧縮するか、または Azure で複数のディスクを使用して[ストライピングし](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)、ストレージの上限を高くします。
指定した場所でディスクを使用できません | 移行前に、ディスクがターゲットの場所にあることを確認します。
指定した冗長性でディスクを使用できません | 評価の設定 (既定では LRS) で定義されているストレージの冗長性の種類をディスクで使用する必要があります。
内部エラーが原因でディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
必要なコアとメモリを備えた VM が見つかりません | Azure が適切な VM の種類を見つけられなかったことになります。 移行前に、オンプレミスのマシンのメモリとコアの数を削減します。
内部エラーが原因で VM の適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のディスクの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。
内部エラーが原因で 1 つ以上のネットワーク アダプターの適合性を決定できませんでした | そのグループの評価を新しく作成してみます。

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM が "条件付きで対応" である

Server Assessment では、Server Assessment の既知のギャップのため、Linux VM が "条件付きで対応" とマークされます。

- ギャップのため、オンプレミスの VM にインストールされている Linux OS のマイナー バージョンを検出できません。
- たとえば、RHEL 6.10 の場合、現在の Server Assessment では、RHEL 6 のみが OS バージョンとして検出されます。
-  特定のバージョンの Linux のみが Azure によって動作保証されるため、Linux VM は現在 Server Assessment において条件付き対応とマークされます。
- オンプレミスの VM で実行されている Linux OS が Azure で動作保証済みかどうかは、[Azure Linux サポート](https://aka.ms/migrate/selfhost/azureendorseddistros)のページを参照して特定できます。
-  ディストリビューションの動作保証を確認した後は、この警告を無視してかまいません。

## <a name="azure-skus-bigger-than-on-premises"></a>オンプレミスより大きい Azure SKU

Azure Migrate Server Assessment では、評価の種類に基づいて、現在のオンプレミスの割り当てより多くのコアとメモリを備えた Azure VM SKU が推奨される場合があります。


- VM SKU の推奨は、評価のプロパティによって異なります。
- これは、Server Assessment で実行する評価の種類によって影響を受けます: "*パフォーマンスベース*" または "*オンプレミス*"。
- パフォーマンスベースの評価では、Server Assessment によりオンプレミスの VM の利用状況データ (CPU、メモリ、ディスク、ネットワークの利用状況) が考慮されて、オンプレミスの VM に対して適切なターゲット VM SKU が決定されます。 また、有効な使用率を決定するときに快適性係数が追加されます。
- オンプレミスのサイズ設定では、パフォーマンス データは考慮されず、オンプレミスの割り当てに基づいてターゲット SKU が推奨されます。

これが推奨に与える影響を確認するため、次の例を見てみましょう。

オンプレミスの VM は、4 つのコアと 8 GB のメモリを備え、CPU 使用率は 50%、メモリ使用率は 50%、指定された快適性係数は 1.3 です。

-  評価が**オンプレミス**の場合は、4 コアと 8 GB のメモリを備えた Azure VM SKU が推奨されます。
- 評価がパフォーマンスベースの場合は、CPU およびメモリの有効な使用率 (4 コアの 50% * 1.3 = 2.6 コア、8 GB メモリの 50% * 1.3 = 5.3 GB メモリ) に基づき、4 コア (最も近いサポートされるコア数) と 8 GB メモリ (最も近いサポートされるメモリ サイズ) の最も安価な VM SKU が推奨されます。
- 評価のサイズ決定に関する[詳細を参照](concepts-assessment-calculation.md#assessments-in-server-assessment)してください。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>オンプレミスより大きい Azure ディスク SKU

Azure Migrate Server Assessment では、評価の種類に基づいて、より大きいディスクが推奨される場合があります。
- Server Assessment でのディスクのサイズ設定は、サイズ設定基準とストレージの種類という 2 つの評価プロパティに依存します。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が**自動**である場合、ディスクの IOPS とスループットの値を考慮して、ターゲット ディスクの種類 (Standard HDD、Standard SSD、または Premium) が特定されます。 その後、そのディスクの種類からディスク SKU が推奨され、この推奨ではオンプレミスのディスクのサイズ要件が考慮されます。
- サイズ設定基準が**パフォーマンスベース**で、ストレージの種類が **Premium** である場合、オンプレミスのディスクの IOPS、スループット、サイズの要件に基づいて、Azure の Premium ディスク SKU が推奨されます。 サイズ設定基準が**オンプレミス**で、ストレージの種類が **Standard HDD**、**Standard SSD**、または **Premium** である場合は、同じロジックを使ってディスクのサイズ設定が行われます。

たとえば、オンプレミスのディスクと 32 GB のメモリを使っていても、ディスクの読み取りと書き込みの集計 IOPS が 800 IOPS である場合、Server Assessment では Premium ディスク (高い IOPS 要件であるため) が推奨された後、必要な IOPS とサイズに対応できるディスク SKU が推奨されます。 この例では、適合する最も近いものは P15 (256 GB、1100 IOPS) になります。 オンプレミスのディスクで必要なサイズは 32 GB でしたが、オンプレミスのディスクの高い IOPS 要件により、Server Assessment ではさらに大きいディスクが推奨されます。

## <a name="utilized-corememory-percentage-missing"></a>使用済みコア/メモリの割合がありません

Azure Migrate アプライアンスで関連するオンプレミス VM のパフォーマンス データを収集できない場合、Server Assessment では "PercentageOfCoresUtilizedMissing" または "PercentageOfMemoryUtilizedMissing" が報告されます。

- これは、評価期間中に VM がオフにされた場合に発生する可能性があります。 アプライアンスでは、オフになっていた VM のパフォーマンス データを収集することはできません。
- メモリ カウンターのみが取得されず、Hyper-V VM を評価しようとしている場合は、これらの VM で動的メモリが有効になっているかどうかを確認してください。 Hyper-V VM のみの既知の問題のため、動的メモリが有効になっていない VM のメモリ使用率データは、Azure Migrate アプライアンスで収集できません。
- いずれかのパフォーマンス カウンターが不足している場合、Azure Migrate Server Assessment は割り当てられたコアとメモリにフォールバックし、対応する VM サイズが推奨されます。

## <a name="is-the-operating-system-license-included"></a>オペレーティング システムのライセンスが含まれているか

現在、Azure Migrate Server Assessment では、Windows マシンのオペレーティング システムのライセンス コストのみが考慮されます。 Linux マシンのライセンス コストは現在は考慮されません。

## <a name="how-does-performance-based-sizing-work"></a>パフォーマンスベースのサイズ設定のしくみ

Server Assessment では、オンプレミスのマシンのパフォーマンス データを継続的に収集し、それを使って Azure で VM の SKU とディスクの SKU を推奨します。 パフォーマンスベースのデータの収集方法を[確認](concepts-assessment-calculation.md#calculate-sizing-performance-based)してください。


## <a name="dependency-visualization-in-azure-government"></a>Azure Government での依存関係の視覚化

Azure Migrate の依存関係視覚化機能は Service Map に依存しています。 現在、Azure Government では Service Map を使用できないため、Azure Government ではこの機能を使用できません。

## <a name="dependencies-dont-show-after-agent-install"></a>エージェントのインストール後に依存関係が表示されない

オンプレミスの VM に依存関係視覚化エージェントをインストールした後、Azure Migrate ポータルで依存関係が表示されるまで 15 分から 30 分かかります。 30 分以上待っている場合は、Microsoft Monitoring Agent (MMA) が Log Analytics ワークスペースに接続できることを確認してください。

Windows VM の場合:
1. コントロール パネルで MMA を開始します。
2. **[Microsoft Monitoring Agent のプロパティ]**  >  **[Azure Log Analytics (OMS)]** で、ワークスペースの **[状態]** が緑色になっていることを確認します。
3. 状態が緑色でない場合は、ワークスペースを削除して、再度 MMA に追加してみてください。

    ![MMA のステータス](./media/troubleshoot-assessment/mma-properties.png)

Linux VM の場合、MMA と依存関係エージェントのインストールに関するすべてのコマンドが正常に実行されたことを確認します。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

- **MMS エージェント**:サポートされている [Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) オペレーティング システムと [Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) オペレーティング システムを確認します。
- **依存関係エージェント**: サポートされている [Windows および Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) オペレーティング システムを確認します。

## <a name="visualize-dependencies-for--hour"></a>最大 1 時間分の依存関係を視覚化する

Azure Migrate を使用すると、過去 1 か月間の特定の日付に戻ることができますが、依存関係を視覚化できる期間は最大 1 時間です。

たとえば、依存関係マップにある期間機能を使用して、昨日の依存関係を表示することが可能ですが、1 時間の期間のみ表示できます。

ただし、Azure Monitor ログを使用すれば、長期間にわたって[依存関係データのクエリ](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)を実行できます。

## <a name="visualized-dependencies-for--10-machines"></a>最大 10 個のマシンの依存関係を視覚化する

Azure Migrate Server Assessment では、最大 10 個の VM を含む[グループの依存関係を視覚化](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)できます。 大きいグループの場合、依存関係を視覚化するには VM を小さいグループに分割することをお勧めします。

## <a name="machines-show-install-agent"></a>マシンに "エージェントのインストール" が表示される

依存関係の視覚化が有効になっているマシンを Azure に移行した後、マシンでは、次の動作により、"依存関係の表示" ではなく "エージェントのインストール" アクションが表示される場合があります。


- Azure に移行した後、オンプレミスのマシンはオフになり、同等の VM が Azure で起動されます。 これらのマシンは、別々の MAC アドレスを取得します。
- ユーザーがオンプレミスの IP アドレスを保持するかどうかにより、マシンは異なる IP アドレスも取得する場合があります。
- MAC と IP アドレスの両方がオンプレミスと異なる場合、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われません。 この場合、依存関係の表示ではなく、エージェントをインストールするオプションが表示されます。
- Azure へのテスト移行の後、オンプレミスのマシンでは想定どおりにオンの状態が維持されます。 Azure にスピン アップされる同等のマシンでは、異なる MAC アドレスが取得され、異なる IP アドレスが取得されることがあります。 これらのマシンから送信される Azure Monitor ログ トラフィックをブロックしない限り、Azure Migrate ではオンプレミスのマシンと Service Map 依存関係データの関連付けは行われず、依存関係が表示されるのではなく、エージェントをインストールするオプションが表示されます。


## <a name="capture-network-traffic"></a>ネットワーク トラフィックをキャプチャする

次のようにネットワーク トラフィック ログを収集します。

1. [Azure portal](https://portal.azure.com) にサインインする
2. F12 キーを押して開発者ツールを起動します。 必要な場合は、 **[ナビゲーション時にエントリをクリア]** の設定をオフにします。
3. **[ネットワーク]** タブを選択し、ネットワーク トラフィックのキャプチャを開始します。
   - Chrome では、 **[Preserve log]\(ログの保持\)** を選択します。 自動で記録が開始されるはずです。 赤い円は、トラフィックがキャプチャされていることを示しています。 赤い円が表示されない場合は、黒い円を選択して開始します。
   - Microsoft Edge または Internet Explorer では、自動で記録が開始されるはずです。 開始されない場合は、緑色の再生ボタンを選択します。
4. エラーを再現してみます。
5. 記録中にエラーが発生したら、記録を停止し、記録されたアクティビティのコピーを保存します。
   - Chrome では、右クリックして **[Save as HAR with content]\(内容を HAR ファイルに保存する\)** を選択します。 この操作により、ログが .har ファイルとして圧縮されエクスポートされます。
   - Microsoft Edge または Internet Explorer で、 **[キャプチャしたトラフィックのエクスポート]** オプションを選択します。 この操作により、ログが圧縮されてエクスポートされます。
6. **[コンソール]** タブを選択し、いずれかの警告またはエラーを確認します。 コンソール ログを保存するには次の操作を行います。
   - Chrome では、コンソール ログのどこかを右クリックします。 **[名前を付けて保存]** を選択し、ログをエクスポートして zip 圧縮します。
   - Microsoft Edge または Internet Explorer では、エラーを右クリックして **[すべてコピー]** を選択します。
7. 開発者ツールを閉じます。

## <a name="next-steps"></a>次のステップ

評価を[作成](how-to-create-assessment.md)するか[カスタマイズ](how-to-modify-assessment.md)します。
