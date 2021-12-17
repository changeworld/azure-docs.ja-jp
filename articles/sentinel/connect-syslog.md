---
title: Microsoft Sentinel に Syslog データを接続する | Microsoft Docs
description: アプライアンスと Microsoft Sentinel の間で Linux マシン上のエージェントを使用して、Syslog をサポートするマシンまたはアプライアンスを Microsoft Sentinel に接続します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: a67454bb13dfed49d15d0d8385af4a354e2387ee
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721768"
---
# <a name="collect-data-from-linux-based-sources-using-syslog"></a>Syslog を使用して Linux ベースのソースからデータを収集する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**Syslog** は、Linux に共通のイベント ログ プロトコルです。 Linux のデバイスやアプライアンスに組み込まれた Syslog デーモンを使用して、指定した種類のローカル イベントを収集し、それらのイベントを、**Linux 用 Log Analytics エージェント** (旧称 OMS エージェント) を使用して Microsoft Sentinel に送信することができます。

この記事では、Syslog を使用してデータ ソースを Microsoft Sentinel に接続する方法について説明します。 この方法でサポートされるデータ コネクタの詳細については、[データ コネクタのリファレンス](data-connectors-reference.md)に関するページを参照してください。

## <a name="architecture"></a>Architecture

VM またはアプライアンスに Log Analytics エージェントがインストールされている場合は、インストール スクリプトによって、UDP ポート 25224 でエージェントにメッセージを転送するローカル Syslog デーモンが構成されます。 メッセージの受信後、メッセージはエージェントによって、HTTPS を介して Log Analytics ワークスペースに送信されます。そこで、メッセージは **[Microsoft Sentinel] > [ログ]** の Syslog テーブルに取り込まれます。

詳細については、「[Azure Monitor の Syslog データ ソース](../azure-monitor/agents/data-sources-syslog.md)」を参照してください。

:::image type="content" source="media/connect-syslog/syslog-diagram.png" alt-text="この図は、Syslog ソースから Microsoft Sentinel ワークスペースへのデータ フローを示しています。この場合は、データ ソース デバイスに、Log Analytics エージェントが直接インストールされています。":::

Log Analytics エージェントのローカル インストールを許可しない一部の種類のデバイスについては、代わりに、Linux ベースの専用ログ フォワーダーにエージェントをインストールできます。 転送元のデバイスは、ローカル デーモンではなく、このフォワーダー上の Syslog デーモンに Syslog イベントを送信するように構成されている必要があります。 フォワーダー上の Syslog デーモンから、UDP を介して Log Analytics エージェントにイベントが送信されます。 この Linux フォワーダーで、大量の Syslog イベントを収集することが予期されている場合は、代わりに TCP を介して、Syslog デーモンからエージェントにイベントが送信されます。 いずれの場合も、エージェントは次にそこから、Microsoft Sentinel 内の Log Analytics ワークスペースにイベントを送信します。

:::image type="content" source="media/connect-syslog/syslog-forwarder-diagram.png" alt-text="この図は、Syslog ソースから Microsoft Sentinel ワークスペースへのデータ フローを示しています。この場合、別個のログ転送デバイスに Log Analytics エージェントがインストールされています。":::

> [!NOTE]
> - アプライアンスが **Syslog を介して Common Event Format (CEF)** をサポートしている場合は、より完全なデータセットが収集され、データは収集時に解析されます。 このオプションを選択し、「[デバイスまたはアプライアンスの CEF 形式のログを Microsoft Sentinel に取得する](connect-common-event-format.md)」の手順に従う必要があります。
>
> - Log Analytics では、**rsyslog** または **syslog-ng** の各デーモンによって送信されたメッセージの収集がサポートされています。rsyslog は既定のデーモンです。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux (RHEL)、CentOS、Oracle Linux 版の既定の syslog デーモン (**sysklog**) は *サポートされません*。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。

Syslog コレクションを構成する際には以下の 3 つの手順があります。

- **Linux デバイスまたはアプライアンスを構成します**。 これは、Log Analytics エージェントがインストールされるデバイスを指します。これは、イベントの発生元と同じデバイスであるか、イベントを転送するログ コレクターです。

- エージェントにイベントを送信することになる Syslog デーモンの場所に対応する **アプリケーションのログ設定を構成** します。

- **Log Analytics エージェント自体を構成します**。 これは Microsoft Sentinel 内から行われ、構成はインストール済みのすべてのエージェントに送信されます。

## <a name="configure-your-linux-machine-or-appliance"></a>Linux マシンまたはアプライアンスを構成する

1. Microsoft Sentinel のナビゲーション メニューから、 **[データ コネクタ]** を選択します。

1. データ コネクタ ギャラリーから **[Syslog]** を選択してから、 **[コネクタ ページを開く]** を選択します。

    お使いのデバイスの種類が、Microsoft Sentinel の **データ コネクタ ギャラリー** の一覧に表示される場合は、汎用 Syslog コネクタではなく、そのデバイスのコネクタを選択します。 お使いのデバイスの種類について、追加の手順や特別な手順がある場合は、ブックや分析ルール テンプレートのようなカスタム コンテンツと一緒にデバイスのコネクタ ページに表示されます。

1. Linux エージェントをインストールします。 **[Choose where to install the agent:]\(エージェントのインストール先を選択してください:\)** で、以下の操作を実行します。

    |マシンの種類  |Instructions  |
    |---------|---------|
    |**Azure Linux VM の場合**     |    1. **[Azure Linux 仮想マシンにエージェントをインストールする]** を展開します。 <br><br>2. **[Azure Linux 仮想マシン用のエージェントをダウンロードしてインストールする >]** リンクを選択します。<br><br>3. **[仮想マシン]** ブレードで、エージェントをインストールする仮想マシンを選択してから、 **[接続]** を選択します。 接続する各 VM に対してこの手順を繰り返します。     |
    |**その他の Linux マシンの場合**     |     1. **[Azure 以外の Linux マシンにエージェントをインストールする]** を展開します。 <br><br>2. **[Azure 以外の Linux マシン用のエージェントをダウンロードしてインストールする >]** リンクを選択します。<br><br>3. **[エージェント管理]** ブレードで、 **[Linux サーバー]** タブを選択してから、**Linux 用エージェントのダウンロードとオンボード** のためのコマンドをコピーして、それを Linux マシンで実行します。<br><br>        Linux エージェントのインストール ファイルのローカル コピーを取っておく場合は、"エージェントのダウンロードとオンボード" コマンドの上にある **[Linux エージェントのダウンロード]** リンクを選択します。 |
    |     |         |

   > [!NOTE]
   > 組織のセキュリティ ポリシーに従って、これらのデバイスのセキュリティ設定を構成してください。 たとえば、組織のネットワーク セキュリティ ポリシーに合わせてネットワーク設定を構成し、デーモンのポートとプロトコルをセキュリティ要件に合わせて変更できます。

### <a name="using-the-same-machine-to-forward-both-plain-syslog-and-cef-messages"></a>同じマシンを使用してプレーンな Syslog *と* CEF メッセージの両方を転送する

既存の [CEF ログ フォワーダー マシン](connect-log-forwarder.md)を使用して、プレーンな Syslog ソースからもログを収集して転送できます。 ただし、両方の形式のイベントを Microsoft Sentinel に送信すると、イベントが重複することになるため、これを避けるには次の手順を行う必要があります。

[CEF ソースからのデータ収集](connect-common-event-format.md)を既に設定していて、Log Analytics エージェントを構成済みの場合:

1. CEF 形式でログを送信する各マシンで Syslog 構成ファイルを編集して、CEF メッセージの送信に使用されているファシリティを削除する必要があります。 これで、CEF で送信されるファシリティは、Syslog で送信されません。 この方法の詳細については、「[Linux エージェントでの Syslog の構成](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)」を参照してください。

1. これらのマシンで次のコマンドを実行して、エージェントと Microsoft Sentinel の Syslog 構成との同期を無効にする必要があります。 これで、前の手順で構成に加えた変更が上書きされなくなります。

    ```c
    sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
    ```

## <a name="configure-your-devices-logging-settings"></a>デバイスのログ設定を構成する

多くのデバイスの種類には、**データ コネクタ** ギャラリーに表示される独自のデータ コネクタが用意されています。 これらのコネクタの中には、Microsoft Sentinel でログ収集を適切に設定するために、特別な追加の手順が必要なものがあります。 これらの手順に、Kusto 関数に基づくパーサーの実装が含まれる場合があります。

ギャラリーの一覧に示されるすべてのコネクタについて、ポータルのそれぞれのコネクタ ページや、[Microsoft Sentinel データ コネクタのリファレンス](data-connectors-reference.md) ページのそれらのセクションに、具体的な手順が表示されます。


## <a name="configure-the-log-analytics-agent"></a>Log Analytics エージェントの構成

1. [Syslog コネクタ] ブレードの下部で、 **[ワークスペースのエージェント構成を開く >]** リンクを選択します。

1. **[エージェント構成]** ブレードで、 **[Syslog]** タブを選択します。次に、収集するコネクタのファシリティを追加します。 **[Add facility]\(ファシリティの追加\)** を選択し、ファシリティのドロップダウン リストから選択します。

    - Syslog アプライアンスがそのログ ヘッダーに含めるファシリティを追加します。

    - 収集するデータで異常な SSH ログイン検出を使用する場合は、 **[auth]** と **[authpriv]** を追加します。 追加情報については、[次のセクション](#configure-the-syslog-connector-for-anomalous-ssh-login-detection)を参照してください。

1. 監視しようとしているすべてのファシリティを追加したら、収集しない重大度のチェック ボックスをオフにします。 既定では、これらのすべてにチェックマークが付いています。

1. **[適用]** を選択します。

1. 仮想マシンまたはアプライアンスで、指定したファシリティが送信されていることを確認します。

## <a name="find-your-data"></a>データの検索

1. **[ログ]** で syslog ログ データを照会するには、クエリ ウィンドウで「`Syslog`」と入力します。

1. 「[Azure Monitor ログ クエリでの関数の使用](../azure-monitor/logs/functions.md)」で説明されているクエリ パラメーターを使用して、Syslog メッセージを解析できます。 その後、新しい Log Analytics 関数としてクエリを保存し、新しいデータ型として使用できます。

### <a name="configure-the-syslog-connector-for-anomalous-ssh-login-detection"></a>異常な SSH ログイン検出用に Syslog コネクタを構成する

> [!IMPORTANT]
> 異常な SSH ログイン検出は、現在、**プレビュー段階** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Microsoft Sentinel は Syslog データに機械学習 (ML) を適用して、異常な Secure Shell (SSH) ログイン アクティビティを識別できます。 シナリオには以下が含まれます。

- あり得ない移動 - 2 つの成功したログイン イベントが、この 2 つのログイン イベントの期間内に到達できない 2 つの場所で発生した場合。

- 予期しない場所 - 成功したログイン イベントの発生した場所が疑わしい場合。 たとえば、最近検出されることのなかった場所などです。
 
この検出には、Syslog データ コネクタの特定の構成が必要です。 

1. 上の手順 2 の [[Log Analytics エージェントの構成]](#configure-the-log-analytics-agent) で、**auth** と **authpriv** の両方が監視対象のファシリティとして選択されていること、すべての重大度が選択されていることを確認します。 

2. Syslog 情報が収集されるまで、十分な時間をかけます。 その後、 **[Microsoft Sentinel] - [ログ]** に移動して、次のクエリをコピーして貼り付けます。
    
    ```kusto
    Syslog
    | where Facility in ("authpriv","auth")
    | extend c = extract( "Accepted\\s(publickey|password|keyboard-interactive/pam)\\sfor ([^\\s]+)",1,SyslogMessage)
    | where isnotempty(c)
    | count 
    ```
    
    必要に応じて **[Time range]\(時間の範囲\)** を変更し、 **[実行]** を選択します。
    
    結果の数が 0 の場合は、コネクタの構成を確認し、クエリに指定した期間内に監視対象のコンピュータで成功したログイン アクティビティがあったことを確認します。
    
    結果の数が 0 より大きい場合、Syslog データは、異常な SSH ログインの検出用に適しています。 この検出は、 **[分析]**  >   **[Rule templates]\(ルール テンプレート\)**  >  **[(Preview) Anomalous SSH Login Detection]\((プレビュー) 異常な SSH ログインの検出\)** で有効にします。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Syslog オンプレミス アプライアンスを Microsoft Sentinel に接続する方法について説明しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
