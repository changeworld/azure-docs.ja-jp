---
title: Palo Alto を Microsoft Defender for IoT と統合する
description: Defender for IoT は、その継続的な ICS 脅威監視プラットフォームが Palo Alto の次世代ファイアウォールと統合され、重大な脅威をより迅速かつ効率的にブロックできるようになりました。
ms.date: 11/09/2021
ms.topic: tutorial
ms.openlocfilehash: 5beec75d23fb940f35ded84f2a938dae7e3dd9bc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301459"
---
# <a name="tutorial-integrate-palo-alto-with-microsoft-defender-for-iot"></a>チュートリアル: Palo-Alto を Microsoft Defender for IoT と統合する

このチュートリアルは、Palo Alto と Microsoft Defender for IoT を統合し、使用する方法を学ぶのに役立ちます。

Defender for IoT は、その継続的な ICS 脅威監視プラットフォームが Palo Alto の次世代ファイアウォールと統合され、重大な脅威をより迅速かつ効率的にブロックできるようになりました。

利用できる統合の種類は次のとおりです。

- 自動ブロック オプション: Defender for IoT と Palo Alto の直接統合。

- ブロックに関する推奨事項を中央管理システムに送信する: Defender for IoT と Panorama の統合。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> - 指定された Palo Alto ファイアウォールによる即時ブロックを構成する
> - Defender for IoT で Panorama ブロック ポリシーを作成する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

### <a name="panorama-permissions"></a>Panorama のアクセス許可

- Panorama の管理者による自動ブロック許可の確認。

## <a name="configure-immediate-blocking-by-a-specified-palo-alto-firewall"></a>指定された Palo Alto ファイアウォールによる即時ブロックを構成する

マルウェア関連のアラートなどのケースでは、自動ブロックを有効にすることができます。 Defender for IoT の転送ルールは、特定の Palo Alto ファイアウォールにブロック コマンドを直接送信するために使用されます。

Defender for IoT では、重大な脅威を識別するときに、感染したソースをブロックするオプションを含むアラートを送信します。 アラートの詳細で **[ソースのブロック]** を選択すると、指定された Palo Alto ファイアウォールにブロック コマンドを送信する転送ルールがアクティブ化されます。

**即時ブロックを構成するには**:

1. 左側のウィンドウで、 **[転送]** を選択します。

1. **[転送ルールの作成]** を選択し ます。

    :::image type="content" source="media/tutorial-palo-alto/forwarding.png" alt-text="転送中のアラート画面のスクリーンショット。":::

1. [アクション] ドロップダウンメニューから、 **[Send To Palo Alto NGFW]\(Palo Alto NGFW に送信\)** を選択します。

   :::image type="content" source="media/tutorial-palo-alto/forward-rule.png" alt-text="[転送ルールの作成] 画面のスクリーンショット。":::

1. [アクション] ウィンドウで、次のパラメーターを設定します。

   - **[Host]\(ホスト\)** : NGFW サーバーの IP アドレスを入力します。
   - **ポート**:NGFW サーバーのポートを入力します。
   - **[ユーザー名]** : NGFW サーバーのユーザー名を入力します。
   - **パスワード**:NGFW サーバーのパスワードを入力します。
   - **構成する**:次のオプションを設定して、Palo Alto ファイアウォールによる疑わしいソースのブロックを許可します。
     - **[無効な関数コードをブロックする]** :プロトコル違反 - ICS プロトコル仕様に違反する無効なフィールド値 (潜在的な悪用)。
     - **[不正な PLC プログラミング/ファームウェア更新のブロック]** :不正な PLC 変更。
     - **[承認されていない PLC 停止をブロックする]** :PLC 停止 (ダウンタイム)。
     - **[マルウェア関連のアラートをブロックする]** :産業用マルウェアの実行のブロック (TRITON、NotPetya など)。 **[自動ブロック]** のオプションを選択できます。 その場合、ブロックは直ちに自動的に実行されます。
     - **[承認されていないスキャンをブロックする]** :不正なスキャン (潜在的な偵察)。

    :::image type="content" source="media/tutorial-palo-alto/edit.png" alt-text="転送ルールの編集画面のスクリーンショット。":::

1. **[送信]** を選択します。

その後、疑わしいソースをブロックする必要があります。

**疑わしいソースをブロックするには**:

1. **[アラート]** ペインに移動し、Palo Alto 統合に関連するアラートを選択します。

1. 疑わしいソースを自動的にブロックするには、 **[ソースのブロック]** を選択します。 **[確認してください]** ダイアログ ボックスが表示されます。

    :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="承認されていないソースをブロックするための [ソースのブロック] ボタンのスクリーンショット。":::

1. **[OK]** を選択します。

これで、疑わしいソースが Palo Alto ファイアウォールによってブロックされます。

## <a name="create-panorama-blocking-policies-in-defender-for-iot"></a>Defender for IoT で Panorama ブロック ポリシーを作成する

Defender for IoT と Palo Alto Networks の統合によって、Palo Alto Networks の NMS と Panorama に自動的に新しいポリシーが作成されます。

次の表は、この統合が対象とするインシデントを示しています。

| インシデントの種類 | 説明 |
|--|--|
|**承認されていない PLC の変更** | デバイスのラダー ロジックまたはファームウェアの更新。 これは、正当なアクティビティを表すことも、デバイスを侵害しようとする試みを表すこともあります。 たとえば、リモート アクセス型トロイの木馬 (RAT) などの悪意のあるコードや、回転するタービンなどの物理プロセスを危険な方法で作動させるパラメーターなどです。 |
|**プロトコル違反** | プロトコル仕様に違反するパケット構造またはフィールド値。 これは、誤って構成されたアプリケーションを表すことも、デバイスを侵害しようとする悪意のある試みを表すこともあります。 たとえば、ターゲット デバイスでバッファー オーバーフロー状態を発生させたりします。 |
|**PLC 停止** | デバイスの機能を停止させるコマンド。これにより、PLC によって制御されている物理プロセスが危険にさらされます。 |
|**ICS ネットワークで検出された産業用マルウェア** | 自身のネイティブ プロトコルを使用して ICS デバイスを操作するマルウェア (TRITON や Industroyer など)。 Defender for IoT では、ICS および SCADA 環境に水平移動した IT マルウェアも検出します。 たとえば、Conficker、WannaCry、NotPetya などです。 |
|**スキャン型マルウェア** | 攻撃の前段階でシステム構成に関するデータを収集する偵察ツール。 たとえば、トロイの木馬 Havex では、産業用ネットワークをスキャンして、Windows ベースの SCADA システムが ICS デバイスと通信するときに使用する標準プロトコルである OPC を使用しているデバイスを検索します。 |

事前構成済みのユース ケースが Defender for IoT によって検出されると、アラートに **[ソースのブロック]** ボタンが追加されます。 その後、CyberX ユーザーが **[ソースのブロック]** ボタンを選択すると、定義済みの転送ルールが Defender for IoT によって送信され、Panorama 上にポリシーが作成されます。

このポリシーは、Panorama 管理者がネットワーク内の関連する NGFW にポリシーをプッシュする場合にのみ適用されます。

IT ネットワークでは、動的 IP アドレスが使用されている場合があります。 そのため、それらのサブネットでは、IP アドレスではなく FQDN (DNS 名) に基づいてポリシーを作成する必要があります。 Defender for IoT では、構成された時間間隔で逆引き参照を実行し、動的 IP アドレスを持つデバイスを FQDN (DNS 名) と照合します。

さらに、Defender for IoT では、関連する Panorama ユーザーにメールを送信し、Defender for IoT によって作成された新しいポリシーが承認を待機していることを通知します。 次の図は、Defender for IoT と Panorama の統合アーキテクチャを示しています。

:::image type="content" source="media/tutorial-palo-alto/structure.png" alt-text="CyberX-Panorama 統合アーキテクチャのスクリーンショット。":::

Defender for IoT で Panorama ブロック ポリシーを作成するための最初のステップは、DNS 参照を構成することです。

**DNS 参照を構成するには**:

1. 左側のウィンドウで、 **[システム設定]** を選択します。

1. **[DNS 設定]** :::image type="icon" source="media/tutorial-palo-alto/settings.png"::: ボタンを選択します。

1. **[DNS 設定の編集]** ダイアログ ボックスで、次のパラメーターを設定します。

   - **状態**: DNS リゾルバーの状態。

   - **[DNS サーバー アドレス]** ネットワーク DNS サーバーの IP アドレスまたは FQDN を入力します。
   - **[DNS サーバー ポート]** :DNS サーバーの照会に使用されるポートを入力します。
   - **サブネット**:動的 IP アドレスのサブネット範囲を設定します。 Defender for IoT によって DNS サーバーの IP アドレスの逆引き参照が実行され、現在の FQDN 名と照合される範囲です。
   - **[逆引き参照のスケジュール]** :スケジュールのオプションを次のように定義します。
     - [特定時刻]:日単位で逆引き参照を実行するタイミングを指定します。
     - [一定間隔 (時間単位)]:逆引き参照を実行する頻度を設定します。
   - **[ラベル数]** :ネットワーク IP アドレスをデバイスの FQDN に自動的に解決するように Defender for IoT に指示します。 <br />DNS の FQDN 解決を構成するには、表示するドメイン ラベルの数を追加します。 最大 30 文字が左から右に表示されます。

    :::image type="content" source="media/tutorial-palo-alto/configuration.png" alt-text="DNS 設定の構成画面のスクリーンショット。":::

1. **[SAVE]\(保存\)** を選択します。

DNS 設定が正しいことを確認するには、 **[参照テスト]** を選択します。 このテストでは、DNS サーバーの IP アドレスと DNS サーバーのポートが正しく設定されていることが確認されます。

## <a name="block-suspicious-traffic-with-the-palo-alto-firewall"></a>Palo Alto ファイアウォールを使用して疑わしいトラフィックをブロックする

疑わしいトラフィックは、Palo Alto ファイアウォールでブロックする必要があります。 Defender for IoT の転送ルールを使用して、疑わしいトラフィックをブロックできます。

**Defender for IoT 転送ルールを使用して Palo Alto ファイアウォールで疑わしいトラフィックをブロックするには**:

1. 左側のウィンドウで、 **[転送]** を選択します。

1. **[転送ルールの作成]** を選択し ます。

1. **[アクション]** ドロップダウン メニューから、 **[Send To Palo Alto Panorama]\(Palo Alto Panorama に送信\)** を選択します。

1. [アクション] ウィンドウで、次のパラメーターを設定します。

   - **[Host]\(ホスト\)** : Panorama サーバーの IP アドレスを入力します。

   - **ポート**:Panorama サーバーのポートを入力します。

   - **[ユーザー名]** : Panorama サーバーのユーザー名を入力します。

   - **パスワード**:Panorama サーバーのパスワードを入力します。

   - **[アドレスのレポート]** :ブロックの実行方法を次のように定義します。

     - **[IP アドレスごと]** :常に IP アドレスに基づいて Panorama 上にブロック ポリシーを作成します。

     - **[FQDN または IP アドレスごと]** :FQDN が存在する場合は FQDN、それ以外の場合は IP アドレスに基づいて、Panorama 上にブロック ポリシーを作成します。

   - **電子メール**: ポリシー通知メール用のメール アドレスを設定します

    > [!NOTE]
    > 必ず Defender for IoT でメール サーバーを構成してください。 メール アドレスが入力されていない場合は、Defender for IoT から通知メールが送信されません。

   - **アラート検出時に DNS 参照を実行する (チェックボックス)** : [レポート アドレス] で [FQDN] または [IP アドレス] オプションが設定されている場合。 このチェック ボックスは既定でオンになっています。 IP アドレスのみが設定されている場合、このオプションは無効になります。

   - **構成する**:Palo Alto Panorama による疑わしいソースのブロックを許可するには、次のオプションを設定します。

     - **[無効な関数コードをブロックする]** :プロトコル違反 - ICS プロトコル仕様に違反する無効なフィールド値 (潜在的な悪用)。

     - **[不正な PLC プログラミング/ファームウェア更新のブロック]** :不正な PLC 変更。

     - **[承認されていない PLC 停止をブロックする]** :PLC 停止 (ダウンタイム)。

     - **[マルウェア関連のアラートをブロックする]** :産業用マルウェアの実行のブロック (TRITON、NotPetya など)。 **[自動ブロック]** のオプションを選択できます。 その場合、ブロックは直ちに自動的に実行されます。

     - **[承認されていないスキャンをブロックする]** :不正なスキャン (潜在的な偵察)。

    :::image type="content" source="media/tutorial-palo-alto/details.png" alt-text="アクションの選択画面のスクリーンショット。":::

1. **[送信]** を選択します。

その後、疑わしいソースをブロックする必要があります。

**疑わしいソースをブロックするには**:

1. **[アラート]** ペインで、Palo Alto 統合に関連するアラートを選択します。 **[アラートの詳細]** ダイアログ ボックスが表示されます。

  :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="アラート画面のスクリーンショット。Palo Alto に関連するものを選択し、[ソースのブロック] を選択します。":::

1. 疑わしいソースを自動的にブロックするには、 **[ソースのブロック]** を選択します。

1. **[OK]** を選択します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-step"></a>次のステップ

このチュートリアルでは、Palo Alto 統合の使用を開始する方法を学習しました。

> [!div class="nextstepaction"]
> [次のステップのボタン](tutorial-splunk.md)
