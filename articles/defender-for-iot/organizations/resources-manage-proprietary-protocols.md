---
title: 独自のプロトコルを管理する (Horizon)
description: Defender for IoT Horizon では、独自のプロトコルを実行する IoT および ICS デバイスをセキュリティ保護するために使用されるオープン開発環境 (ODE) を提供します。
ms.date: 11/09/2021
ms.topic: reference
ms.openlocfilehash: 6a9995ec70f061381f1315e03c3781537733d97f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347287"
---
# <a name="defender-for-iot-horizon"></a>Defender for IoT Horizon

Defender for IoT Horizon には、独自のプロトコルを実行する IoT および ICS デバイスをセキュリティ保護するために使用されるオープン開発環境 (ODE) が含まれています。

Horizon は以下を提供します。

  - 一般的、独自、カスタムのプロトコル、またはすべての標準に逸脱したプロトコルの無制限で完全なサポート。 
  - DPI 開発における新しいレベルの柔軟性と範囲。
  - 新しいバージョンにアップグレードすることなく、可視性と制御を指数的に拡張するツール。
  - 機密情報を危険にさらすことなく、独自の開発を可能にするセキュリティ。

Horizon SDK を使用して、自動化された Defender for IoT ネットワーク分析プログラムによって処理できるように、ネットワーク トラフィックをデコードする dissector プラグインを設計します。

プロトコル dissector は外部プラグインとして開発され、監視、アラート、およびレポートの機能を提供するサービスなど、広範な種類の Defender for IoT サービスに統合されます。

オープン開発環境 (ODE) SDK の操作とプロトコル プラグインの作成の詳細については、<ms-horizon-support@microsoft.com> に問い合わせてください。

プラグインを開発したら、Horizon Web コンソールを使用して次のことを行うことができます。

  - プラグインをアップロードする
  - プラグインの有効化と無効化を切り替える  
  - プラグインを監視およびデバッグしてパフォーマンスを評価する
  - 独自のプロトコルに基づいてカスタム アラートを作成する。 それらをコンソールに表示し、パートナー ベンダーに転送します。 

    :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-plugin.png" alt-text="Horizon プラグインを通じてアップロードします。"::: 

この機能は、管理者、Cyberx、またはサポート ユーザーが使用できます。

Horizon コンソールにサインインするには:

1. CLI を使用してセンサーにサインインします。
2. ファイル `/var/cyberx/properties/horizon.properties` で、`ui.enabled` プロパティを `true` (`horizon.properties:ui.enabled=true`) に変更します。
3. センサー コンソールにサインインします。 
4. メイン メニューから **[Horizon]** を選択します。 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-from-the-menu.png" alt-text="メイン メニューから [Horizon] を選択します。":::  

Horizon コンソールには、Defender for IoT によって提供されるインフラストラクチャ プラグインと、作成およびアップロードしたその他のプラグインが表示されます。 

   :::image type="content" source="media/how-to-manage-proprietary-protocols/infrastructure.png" alt-text="Horizon インフラストラクチャのスクリーンショット。":::

## <a name="upload-plugins"></a>プラグインをアップロードする

独自の dissector プラグインを作成してテストした後は、Horizon コンソールからアップロードおよび監視できます。

アップロードするには:

1. コンソールから **[アップロード]** を選択 ます。

   :::image type="content" source="media/how-to-manage-proprietary-protocols/upload-a-plugin.png" alt-text="プラグインに対してアップロードを選択します。":::

2. プラグインをドラッグまたは参照します。 アップロードに失敗した場合は、エラー メッセージが表示されます。

オープン開発環境 (ODE) SDK の操作とプロトコル プラグインの作成の詳細については、<ms-horizon-support@microsoft.com> に問い合わせてください。

## <a name="enable-and-disable-plugins"></a>プラグインの有効化と無効化を切り替える

トグル ボタンを使用して、プラグインを有効または無効にします。 無効にした場合、トラフィックは監視されなくなります。

インフラストラクチャ プラグインを無効にすることはできません。

## <a name="monitor-plugin-performance"></a>プラグインのパフォーマンスを監視する 

Horizon コンソールの [概要] ウィンドウには、アップロードしたプラグインに関する基本的な情報が表示され、無効にしたり有効にしたりすることができます。

:::image type="content" source="media/how-to-manage-proprietary-protocols/horizon-overview.png" alt-text="Horizon の概要ページのスクリーンショット。"::: 

| Application | アップロードしたプラグインの名前。 |
|--|--|
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/toggle-icon.png" border="false"::: | プラグインのオンとオフを切り替えます。 プラグインをオフにすると、センサーはプラグインで定義されているプロトコル トラフィックを処理しません。 |
| 時刻 | データが最後に分析された時刻。 5 秒ごとに更新されます。 |
| PPS | 1 秒間に送信されたパケットの数。 |
| 帯域幅 | 過去 5 秒以内に検出された平均帯域幅。 |
| 不正形式 | 不正形式の検証は、プロトコルが肯定的に検証された後に使用されます。 プロトコルに基づいてパケットを処理できなかった場合は、エラー応答が返されます。<br/> <br />この列は、過去 5 秒間に発生した不正形式エラーの数を示します。 |
| 警告 | パケットは構造と仕様に一致しますが、プラグインの警告の構成に基づく予期しない動作があります。 |
| エラー | パケットがプロトコル定義と一致するという基本プロトコル検証に失敗したパケットの数。  ここに表示される数値は、過去 5 秒間に検出されたエラーの数を示します。 |
| :::image type="icon" source="media/how-to-manage-proprietary-protocols/monitor-icon.png" border="false"::: | プラグインで検出された不正形式と警告の詳細を確認します。 |

### <a name="plugin-performance-details"></a>プラグインのパフォーマンスの詳細

プラグインで検出された不正形式と警告を分析して、リアルタイムのプラグインのパフォーマンスを監視することができます。 画面をフリーズして詳細な調査のためにエクスポートするためのオプションが使用可能です。

:::image type="content" source="media/how-to-manage-proprietary-protocols/snmp-monitor.png" alt-text="SNMP モニターの概要のスクリーンショット。":::

### <a name="horizon-logs"></a>Horizon ログ

Horizon 解析情報は、解析の詳細、解析ログ、エクスポート ログにエクスポートできます。

:::image type="content" source="media/how-to-manage-proprietary-protocols/export-logs-details.png" alt-text="エクスポート ログの解析の詳細。":::

## <a name="trigger-horizon-alerts"></a>Horizon アラートをトリガーする 

Horizon フレームワーク トラフィックの dissector に基づいて任意のプロトコルのカスタム アラートをトリガーすることにより、企業内のアラート管理を強化します。 

これらのアラートを使用して情報を伝達することができます。  

  - 独自の Horizon プラグイン内のプロトコルおよび基になるプロトコルに基づくトラフィックの検出について。

  - すべてのプロトコル レイヤーのプロトコル フィールドの組み合わせについて。 たとえば、MODBUS が実行されている環境で、特定の IP アドレスとイーサネットの宛先でメモリ レジスタに対する書き込みコマンドがセンサーによって検出された場合のアラート、または特定の IP アドレスへのアクセスが実行されたときのアラートを生成することができます。

アラートは、Horizon アラートのルールの条件が満たされたときにトリガーされます。

  :::image type="content" source="media/how-to-manage-proprietary-protocols/custom-alert-rules.png" alt-text="Horizon のサンプル カスタム ルール。":::

さらに、Horizon カスタム アラートを使用すると、独自のアラート タイトルとメッセージを作成できます。 解決されたプロトコルのフィールドと値をアラート メッセージのテキストに埋め込むこともできます。

条件に基づくカスタムのアラートのトリガーとメッセージングを使用すると、特定のネットワーク アクティビティを特定し、セキュリティ、IT、および運用チームを効果的に更新することができます。

### <a name="working-with-horizon-alerts"></a>Horizon アラートの操作

Horizon カスタム アラート ルールによって生成されたアラートは、センサーと管理コンソールの [アラート] ウィンドウに、および転送ルールを使用しているときには統合パートナー システムに表示されます。 

Horizon によって生成されたアラートは、確認またはミュートできます。 アラート イベントをポリシー ベースラインに対して学習できないため、学習オプションはカスタム アラートでは使用できません。

アラート情報は、転送ルールが使用されている場合にパートナー ベンダーに転送されます。

Horizon カスタム アラートの重大度は重要です。

Horizon カスタム アラートには、 **[このイベントの管理]** セクションの下に、組織のセキュリティ チームによってアラートが生成されたことを示す静的テキストが含まれます。

### <a name="required-permissions"></a>必要なアクセス許可

Defender for IoT  ユーザーとして定義されたユーザーには、Horizon カスタム アラート ルールを作成するアクセス許可があります。

### <a name="about-creating-rule-conditions"></a>ルールの条件の作成について

ルールの条件は、アラートをトリガーするために検出される必要があるネットワーク トラフィックを記述します。 ルールの条件には、フィールド、演算子、および値からなる 1 つまたは複数のセットを構成できます。 **と** を使用して、条件セットを作成します。

ルールの条件または条件セットが満たされると、アラートが送信されます。 条件ロジックが有効でない場合は、通知されます。

  :::image type="content" source="media/how-to-manage-proprietary-protocols/and-condition.png" alt-text="カスタム ルールには AND 条件を使用します。":::

また、1 つのプロトコルに対して複数のルールを作成することもできます。 つまり、ルールの条件が満たされると、作成したルールごとにアラートがトリガーされます。

### <a name="about-titles-and-messages"></a>タイトルとメッセージについて

アラート メッセージには、入力する英数字と、検出されたトラフィック変数を含めることができます。 たとえば、検出されたソースと宛先のアドレスをアラート メッセージに含めます。 さまざまな言語がサポートされます。

### <a name="about-alert-recommendations"></a>アラートの推奨事項について 

Horizon カスタム アラートには、 **[このイベントの管理]** セクションの下に、組織のセキュリティ チームによってアラートが生成されたことを示す静的テキストが含まれます。 アラートのコメントを使用して、アラートを読み取る個人とチーム間のコミュニケーションを改善します。 

## <a name="create-horizon-alert-rules"></a>Horizon アラート ルールを作成する

この記事では、アラート ルールの作成方法について説明します。

Horizon カスタム アラートを作成するには:

1. Horizon コンソールのプラグイン メニューからプラグインを右クリックします。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/plugins-menu.png" alt-text="メニューからプラグインを右クリックします。":::

2. **[Horizon カスタム アラート]** を選択します。 選択したプラグインの **[ルール]** ウィンドウが開きます。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/sample-rule-window.png" alt-text="プラグインのサンプル ルール ウィンドウが開きます。":::

3. [タイトル] フィールドにタイトルを入力します。

4. [メッセージ] フィールドにアラート メッセージを入力します。 検出されたフィールド パラメーターをメッセージに含めるには、波かっこ `{}` を使用します。 最初のかっこを入力すると、関連するフィールドが表示されます。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/rule-window.png" alt-text="検出されたフィールドを含めるには、ルール ウィンドウで {} を使用します。":::

5. アラートの条件を定義します。

   :::image type="content" source="media/how-to-manage-proprietary-protocols/define-conditions.png" alt-text="アラートの条件を定義します。":::

6. **変数** を選択します。 変数は、プラグインで構成されたフィールドを表します。

7. **演算子** を選択します。

    - 等しい
    
    - 等しくない
    
    - より小さい
    
    - 以下
    
    - より大きい
    
    - 以上

8. 数値として **値** を入力します。 選択した変数が MAC アドレスまたは IP アドレスの場合は、値をドット形式 10 進数のアドレスから 10 進数形式に変換する必要があります。 <https://www.ipaddressguide.com/ip> などの IP アドレス変換ツールを使用します。

    :::image type="content" source="media/how-to-manage-proprietary-protocols/ip-address-value.png" alt-text="変換された IP アドレスの値。":::

9. **[AND]** を選択して、条件セットを作成します。

10. **[SAVE]\(保存\)** を選択します。 ルールが [ルール] セクションに追加されます。

### <a name="edit-and-delete-horizon-custom-alert-rules"></a>Horizon カスタム アラート ルールを編集および削除する

必要に応じて、編集および削除のオプションを使用します。 一部のルールは埋め込まれているため、編集または削除できません。

### <a name="create-multiple-rules"></a>複数のルールを作成する

複数のルールを作成すると、ルールの条件または条件セットが有効な場合に、アラートがトリガーされます。

## <a name="see-also"></a>関連項目

[アラートで提供される情報の表示](how-to-view-information-provided-in-alerts.md)
