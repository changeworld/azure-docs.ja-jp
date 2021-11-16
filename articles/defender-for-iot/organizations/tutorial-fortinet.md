---
title: Fortinet を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Microsoft Defender for IoT と Fortinet を統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 0556cab649a87b7c0309671acc60d9756a3d03c6
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283659"
---
# <a name="tutorial-integrate-fortinet-with-microsoft-defender-for-iot"></a>チュートリアル: Fortinet を Microsoft Defender for IoT と統合する

このチュートリアルは、Fortinet と Microsoft Defender for IoT を統合し、使用する方法を学ぶのに役立ちます。

Microsoft Defender for IoT を使用すると、ICS デバイス、脆弱性、脅威に関する即時の分析情報を 1 時間以内に提供する、ICS 対応の自己学習エンジンによって IIoT、ICS、SCADA のリスクを軽減できます。  Defender for IoT は、エージェント、ルール、署名、特殊なスキル、または環境に関する事前の知識などに依存することなく、これを実現します。

Defender for IoT と Fortinet は、IoT および ICS ネットワークに対する攻撃を検出して停止する技術パートナーシップを確立しました。

Fortinet と Microsoft Defender for IoT により、以下が回避されます。

- プログラマブル ロジック コントローラー (PLC) への承認されていない変更。

- ICS および IoT デバイスを自身のネイティブ プロトコル経由で操作するマルウェア。

- 偵察ツールによるデータの収集。

- 構成ミスや悪意のある攻撃者が原因で発生するプロトコル違反。

Defender for IoT は、IoT および ICS ネットワーク内の異常な動作を検出し、その情報を FortiGate と FortiSIEM に次のように提供します。

- **可視性:** Defender for IoT によって提供される情報により、FortiSIEM 管理者は、以前には表示されなかった IoT および ICS ネットワークへの可視性を手に入れることができます。

- **悪意のある攻撃のブロック:** FortiGate 管理者は、Defender for IoT によって検出された情報を使用して、異常な動作の原因が大混乱したアクターまたは正しく構成されていないデバイスであるかどうかには関係なく、それによって運用、利益、またはユーザーに損害が発生する前に、その動作を停止するためのルールを作成できます。

FortiSIEM、Fortinet のマルチベンダー セキュリティ インシデント、イベント管理ソリューションによって、可視性、相関関係、自動応答、修復が 1 つのスケーラブルなソリューションに提供されます。

[ビジネス サービス] ビューを使用すると、ネットワークやセキュリティ運用を管理する複雑さが軽減され、リソースが解放され、さらに侵害検出機能が強化されます。 FortiSIEM は、侵害をその発生前に停止するために、相互相関を提供すると共に、機械学習や UEBA を適用して応答性を向上させています。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> - Fortinet で API キーを作成する
> - マルウェア関連のアラートをブロックする転送ルールを設定する
> - 疑わしいアラートの発生元をブロックする
> - Defender for IoT のアラートを FortiSIEM に送信する
> - Fortigate ファイアウォールを使用して悪意のあるソースをブロックする

Azure アカウントをまだお持ちではない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="prerequisites"></a>前提条件

このルールの前提条件はありません。

## <a name="create-an-api-key-in-fortinet"></a>Fortinet で API キーを作成する

アプリケーション プログラミング インターフェイス (API) キーは、API がアクセスを要求しているアプリケーションまたはユーザーを識別できるようにする、一意に生成されるコードです。 Microsoft Defender for IoT と Fortinet が正しく通信するには、API キーが必要です。

**Fortinet で API キーを作成するには**:

1. FortiGate で、 **[システム]**  >  **[管理者プロファイル]** の順に移動します。

1. 次のアクセス許可を持つプロファイルを作成します。

    | パラメーター | [選択] |
    |--|--|
    | **Security Fabric** | なし |
    | **Fortiview** | なし |
    | **ユーザーとデバイス** | なし |
    | **ファイアウォール** | Custom |
    | **ポリシー** | 読み取り/書き込み |
    | **アドレス** | 読み取り/書き込み  |
    | **サービス** | なし |
    | **[スケジュール]** | なし |
    | **ログとレポート** | なし |
    | **Network** | なし |
    | **システム** | なし |
    | **セキュリティ プロファイル** | なし |
    | **VPN** | なし |
    | **WAN Opt とキャッシュ** | なし |
    | **WiFi とスイッチ** | なし |

1. **[システム]**  >  **[管理者]** の順に移動し、次のようにフィールドを設定して新しい **REST API 管理者** を作成します。

    | パラメーター | 説明 |
    | --------- | ----------- |
    | **ユーザー名** | 転送ルール名を入力します。 |
    | **コメント** | 転送する最小セキュリティ レベルのインシデントを入力します。 たとえば、 **[マイナー]** が選択されている場合は、マイナー アラートとこの重大度レベルを超えるすべてのアラートが転送されます。 |
    | **管理者プロファイル** | ドロップダウン リストから、前の手順で定義したプロファイル名を選択します。 |
    | **PKI グループ** | スイッチを **[無効]** に切り替えます。 |
    | **CORS での配信元の許可** | スイッチを **[有効]** に切り替えます。 |
    | **ログインを信頼されたホストに制限する** | センサーの IP アドレスと、FortiGate に接続する管理コンソールを追加します。 |

API キーは再度提供されないため、生成されたら保存します。

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="新しい API キーが自動的に生成されることを説明するスクリーンショット。":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>マルウェア関連のアラートをブロックする転送ルールを設定する

FortiGate ファイアウォールを使用して、疑わしいトラフィックをブロックできます。

**マルウェア関連のアラートをブロックする転送ルールを設定するには**:

1. Microsoft Defender for IoT 管理コンソールにサインインします。

1. 左側のウィンドウで、 **[転送]** を選択します。

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="センサーでの [転送] ウィンドウのオプションのスクリーンショット。":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. **[転送ルールの作成]** を選択し、次のルールのパラメーターを定義します。

    | パラメーター | 説明 |
    | --------- | ----------- |
    | **名前** | 転送ルールにわかりやすい名前を入力します。 |
    | **重大度の選択** | ドロップダウン メニューから、転送する最小セキュリティ レベルのインシデントを選択します。 たとえば、 **[マイナー]** が選択されている場合は、マイナー アラートとこの重大度レベルを超えるすべてのアラートが転送されます。 |
    | **プロトコル** | 特定のプロトコルを選択するには、 **[固有]** を選択し、このルールが適用されるプロトコルを選択します。 既定では、すべてのプロトコルが選択されます。 |
    | **エンジン** | このルールが適用される特定のセキュリティ エンジンを選択するには、 **[固有]** を選択し、エンジンを選択します。 既定では、すべてのセキュリティ エンジンが含まれます。 |
    | **システム通知** | センサーの "*オンライン*" と "*オフライン*" の状態を転送します。 このオプションは、オンプレミス管理コンソールにログインしている場合にのみ使用できます。 |

1. [アクション] セクションで **[追加]** を選択し、ドロップダウン メニューから **[FortiGate への送信]** を選択します。

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="[転送ルールの作成] ウィンドウの [アクションの追加] セクションのスクリーンショット。":::

1. FortiGate 転送ルールを構成するには、次のパラメーターを設定します。

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="[転送ルールの作成] ウィンドウの構成のスクリーンショット。":::

    | パラメーター | 説明 |
    |--|--|
    | **Host** | FortiGate サーバーの IP アドレスを入力します。 |
    | **API キー** | FortiGate で作成した [API キー](#create-an-api-key-in-fortinet)を入力します。 |
    | **受信インターフェイス** | ファイアウォールの受信インターフェイス ポートを入力します。 |
    | **送信インターフェイス** | ファイアウォールの送信インターフェイス ポートを入力します。 |
    | **ステータス メッセージを**| FortiGate ファイアウォール経由で疑わしいソースのブロックを有効にするには、次のオプションで **√** が表示されていることを確認します。 <br> -  **[無効な関数コードをブロックする]** : プロトコル違反 - ICS プロトコル仕様に違反している無効なフィールド値 (潜在的な悪用) <br /> -  **[不正な PLC プログラミング/ファームウェア更新をブロックする]** : 承認されていない PLC の変更 <br /> -  **[承認されていない PLC 停止をブロックする]** : PLC の停止 (ダウンタイム) <br> -  **[マルウェア関連のアラートをブロックする]** : 産業用マルウェアの試みのブロック (TRITON、NotPetya など)。 <br> -  **(省略可能)** **[自動ブロック]** のオプションを選択できます。 自動ブロックが選択されている場合、ブロックは自動的かつ直ちに実行されます。 <br /> -  **[承認されていないスキャンをブロックする]** : 承認されていないスキャン (潜在的な偵察) |

1. **[送信]** を選択します。

## <a name="block-the-source-of-suspicious-alerts"></a>疑わしいアラートの発生元をブロックする

疑わしいアラートの発生元は、今後発生しないようにブロックすることができます。

**疑わしいアラートの発生元をブロックするには**:

1. 管理コンソールにサインインし、左側のメニューから **[アラート]** を選択します。

1. Fortinet 統合に関連するアラートを選択します。

1. 疑わしいソースを自動的にブロックするには、 **[ソースのブロック]** を選択します。

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="[アラート] ウィンドウのスクリーンショット。":::

1. [確認してください] ダイアログ ボックスで、 **[OK]** を選択します。

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>Defender for IoT のアラートを FortiSIEM に送信する

Defender for IoT アラートにより、次のような広範なセキュリティ イベントに関する情報が提供されます。

- 学習されたベースライン ネットワーク アクティビティからの逸脱

- マルウェアの検出

- 疑わしい運用上の変更に基づいた検出

- ネットワークの異常

- プロトコル仕様からのプロトコルの逸脱

アラートを FortiSIEM サーバーに送信し、その [分析] ウィンドウにアラート情報が表示されるように Defender for IoT を構成できます。

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="[分析] ウィンドウのスクリーンショット。":::

その後、各 Defender for IoT アラートは、FortiSIEM 側での他のどのような構成も必要とせずに解析され、FortiSIEM にセキュリティ イベントとして表示されます。 既定では、次のイベントの詳細が表示されます。

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="[イベントの詳細] ウィンドウにイベントの詳細が表示されているスクリーンショット。":::

次に、Defender for IoT の転送ルールを使用して、アラート情報を FortiSIEM に送信できます。

**Defender for IoT の転送ルールを使用して、アラート情報を FortiSIEM に送信するには**:

1. センサーまたは管理コンソールの左側のペインから、 **[転送]** を選択します。

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="[転送] ウィンドウの転送ルールのビューのスクリーンショット。":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. **[転送ルールの作成]** を選択し、ルールのパラメーターを定義します。

    | パラメーター | 説明 |
    |--|--|
    | **名前** | 転送ルールにわかりやすい名前を入力します。 |
    | **重大度の選択** | 転送するインシデントの最小セキュリティ レベルを選択します。 たとえば、 **[マイナー]** が選択されている場合は、マイナー アラートとこの重大度レベルを超えるすべてのアラートが転送されます。 |
    | **プロトコル** | 特定のプロトコルを選択するには、 **[固有]** を選択し、このルールが適用されるプロトコルを選択します。 既定では、すべてのプロトコルが選択されます。 |
    | **エンジン** | このルールを適用する特定のセキュリティ エンジンを選択するには、 **[Specific]\(特定\)** を選択して、エンジンを選択します。 既定では、すべてのセキュリティ エンジンが含まれます。 |
    | **システム通知** | センサーの "*オンライン*" または "*オフライン*" の状態を転送します。 このオプションは、オンプレミス管理コンソールにログインしている場合にのみ使用できます。 |

3. [アクション] セクションで、 **[FortiSIEM への送信]** を選択します。

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="転送ルールを作成し、Fortinet への送信を選択しているスクリーンショット。":::

4. FortiSIEM サーバーの詳細を入力します。

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="転送ルールに FortiSIEm の詳細を追加しているスクリーンショット。":::

    | パラメーター | 説明 |
    | --------- | ----------- |
    | **Host** | FortiSIEM サーバーの IP アドレスを入力します。 |
    | **ポート** | FortiSIEM サーバー ポートを入力します。 |
    | **タイム ゾーン** | アラート検出のタイム スタンプ。 |

5. **[Submit]\(送信\)** をクリックします。

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>Fortigate ファイアウォールを使用して悪意のあるソースをブロックする

ポリシーを設定して、Defender for IoT のアラートを使用して、FortiGate ファイアウォールで悪意のあるソースを自動的にブロックすることができます。

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="[FortiGate ファイアウォール] ウィンドウ ビューを示しているスクリーンショット。":::

たとえば、次のアラートで悪意のあるソースをブロックできます。

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="NotPetya マルウェアの疑いを示すウィンドウのスクリーンショット。":::

**悪意のあるソースをブロックする FortiGate ファイアウォール規則を設定するには**:

1. FortiGate で [API キーを作成します](#create-an-api-key-in-fortinet)。

1. Defender for IoT センサーまたは管理コンソールにサインインし、 **[転送]** を選択して、[マルウェア関連のアラートをブロックする転送ルールを設定します](#set-a-forwarding-rule-to-block-malware-related-alerts)。

1. Defender for IoT センサーまたは管理コンソールで、 **[アラート]** を選択して、[悪意のあるソースをブロック](#block-a-malicious-source-using-the-fortigate-firewall)します。

1. FortiGage の **[Administrator]\(管理者\)** ウィンドウに移動し、ブロックした悪意のあるソース アドレスを見つけます。

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="FortiGate の [Administrator]\(管理者\) ウィンドウ ビューのスクリーンショット。":::

   ブロック ポリシーが自動的に作成され、FortiGate の [IPv4 Policy]\(IPv4 ポリシー\) ウィンドウに表示されます。

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="FortiGate の [IPv4 Policy]\(IPv4 ポリシー\) ウィンドウ ビューのスクリーンショット。":::

1. ポリシーを選択し、[Enable this policy]\(このポリシーを有効にする\) がオンの位置に切り替えられていることを確認します。

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="FortiGate の [IPv4 Policy Edit]\(IPv4 ポリシーの編集\) ビューのスクリーンショット。":::

    | パラメーター | 説明|
    |--|--|
    | **名前** | ポリシーの名前。 |
    | **受信インターフェイス** | ファイアウォールのトラフィックのインバウンド インターフェイス。 |
    | **送信インターフェイス** | ファイアウォールのトラフィックのアウトバウンド インターフェイス。 |
    | **ソース** | トラフィックの送信元アドレス。 |
    | **宛先** | トラフィックの宛先アドレス。 |
    | **[スケジュール]** | 新しく定義した規則を適用するタイミング。 たとえば、「 `always` 」のように入力します。 |
    | **サービス** | プロトコル、つまりトラフィックが使用する特定のポート。 |
    | **操作** | ファイアウォールによって実行されるアクション。 |

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Fortinet 統合の使用を開始する方法を学習しました。 次に、Palo Alto の統合について学習します。

> [!div class="nextstepaction"]
> [次のステップのボタン](./tutorial-palo-alto.md)
