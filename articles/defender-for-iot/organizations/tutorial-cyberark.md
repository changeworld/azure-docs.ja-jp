---
title: CyberArk を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Microsoft Defender for IoT と CyberArk を統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: cbfa178c2e5358503208773109fc53ab3c274be7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278381"
---
# <a name="tutorial-integrate-cyberark-with-microsoft-defender-for-iot"></a>チュートリアル: CyberArk を Microsoft Defender for IoT と統合する

このチュートリアルは、CyberArk を Microsoft Defender for IoT と統合して使用する方法を学習するために役立ちます。

Defender for IoT は、ICS のほか、ICS 対応の脅威分析と機械学習を備えた IIoT サイバーセキュリティ プラットフォームを提供します。

脅威アクターは、侵害されたリモート アクセス資格情報を使用して、リモート デスクトップおよび VPN 接続経由で重要なインフラストラクチャ ネットワークにアクセスしています。 このアプローチでは、信頼関係接続を使用して、どのような OT 境界セキュリティでも容易にバイパスします。 資格情報は通常、毎日のタスクを実行するためにリモート アクセスを必要とする制御エンジニアやパートナー メンテナンス担当者などの特権ユーザーから盗まれます。

Defender for IoT と CyberARK の統合により、次のことが可能になります。

- 認可されていないリモート アクセスからの OT リスクを軽減する

- OT のための継続的な監視および特権アクセス セキュリティを提供する

- インシデント対応、脅威ハンティング、脅威モデリングを強化する

Defender for IoT アプライアンスは、スイッチなどのネットワーク デバイス上の SPAN ポート (ミラー ポート) 経由で OT ネットワークに、また Defender for IoT アプライアンス上の専用ネットワーク インターフェイスへの一方向の (受信) 接続経由でルーターに接続されます。

専用ネットワーク インターフェイスはまた、Defender for IoT アプライアンスで一元管理と API アクセスのためにも提供されます。 このインターフェイスは、特権ユーザーを管理したり、リモート アクセス接続をセキュリティで保護したりするために組織のデータ センターにデプロイされた CyberArk PSM ソリューションとの通信にも使用されます。

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="CyberArk PSM ソリューションのデプロイ":::

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> - CyberArk で PSM を構成する
> - Defender for IoT で統合を有効にする
> - 検出を表示および管理する
> - 統合を停止する

## <a name="prerequisites"></a>前提条件

- CyberARK バージョン 2.0。

- 企業内のすべての Defender for IoT アプライアンスに CLI でアクセスできることを確認します。

- Azure アカウント。 Azure アカウントをまだお持ちではない場合は、[今すぐ Azure 無料アカウントを作成](https://azure.microsoft.com/free/)できます。

## <a name="configure-psm-cyberark"></a>PSM CyberArk を構成する

CyberArk を Defender for IoT との通信を許可するように構成する必要があります。 この通信は、PSM を構成することによって実現されます。

**PSM を構成するには**:

1. `c:\Program Files\PrivateArk\Server\dbparam.xml` ファイルを見つけて開きます。

1. 次のパラメーターを追加します:

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. ファイルを保存して閉じます。

1. Defender for IoT の syslog 構成ファイル `CyberX.xsl` を `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl` に配置します。

1. **Server Central Administration** を開きます。

1. **[信号機の停止]** を選択してサーバーを停止します。

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="Server Central Administration の [信号機の停止] のスクリーンショット。":::

1. **[信号機の開始]** を選択してサーバーを起動します。

## <a name="enable-the-integration-in-defender-for-iot"></a>Defender for IoT で統合を有効にする

統合を有効にするには、Defender for IoT 管理コンソールで Syslog サーバーを有効にする必要があります。 既定では、Syslog サーバーは、ポート 514 UDP を使用してシステムの IP アドレスをリッスンします。

**Defender for IoT を構成するには**:

1. Defender for IoT 管理コンソールで、 **[システム設定]** に移動します。

1. Syslog サーバーを **[オン]** に切り替えます。

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="[オン] に切り替えられた Syslog サーバーのスクリーンショット。":::

1. (省略可能) CLI 経由でシステムにサインインすることによってポートを変更し、`/var/cyberx/properties/syslog.properties` に移動して `listener: 514/udp` を変更します。

## <a name="view-and-manage-detections"></a>検出を表示および管理する

Microsoft Defender for IoT と CyberArk PSM の間の統合は、syslog メッセージ経由で実行されます。 これらのメッセージは、PSM ソリューションによって Defender for IoT に送信され、Defender for IoT にリモート セッションまたは検証エラーを通知します。

Defender for IoT プラットフォームは PSM からこれらのメッセージを受信すると、それをネットワークに表示されているデータに関連付けるため、ネットワークへのすべてのリモート アクセス接続が、認可されていないユーザーではなく、PSM ソリューションによって生成されたことが検証されます。

### <a name="view-alerts"></a>アラートを表示する

Defender for IoT プラットフォームは、PSM によって認可されていないリモート セッションを識別するたびに、`Unauthorized Remote Session` を発行します。 即座の調査を容易にするために、このアラートには、ソース デバイスと宛先デバイスの IP アドレスと名前も示されます。

**アラートを表示するには**:

1. 管理コンソールにサインインします。

1. 左側のパネルから **[アラート]** を選択します。

1. アラートの一覧から、 **[認可されていないリモート セッション]** というタイトルのアラートを選択します。

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="[認可されていないリモート セッション] アラート。":::

### <a name="event-timeline"></a>イベント タイムライン

PSM では、リモート接続が認可されるたびに、それが Defender for IoT の [イベント タイムライン] ページに表示されます。 [イベント タイムライン] ページには、すべてのアラートと通知のタイムラインが表示されます。

**イベント タイムラインを表示するには**:

1. Defender for IoT センサーにサインインします。

1. 左側のパネルから **[イベント タイムライン]** を選択します。

1. [PSM リモート セッション] というタイトルのイベントを見つけます。

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="[イベント ログ] 画面のビュー。":::

### <a name="auditing--forensics"></a>監査とフォレンジックス

管理者は、組み込みのデータ マイニング インターフェイスを使用して Defender for IoT プラットフォームにクエリを実行することによって、リモート アクセス セッションを監査および調査できます。 この情報を使用すると、ソースまたは宛先デバイス、プロトコル (RDP または SSH)、ソースと宛先のユーザー、タイムスタンプ、そのセッションが PSM を使用して認可されたかどうかなどのフォレンジックの詳細を含め、発生したすべてのリモート アクセス接続を識別できます。

**監査および調査するには**:

1. Defender for IoT センサーにサインインします。

1. 左側のパネルから **[データ マイニング]** を選択します。

1. **[リモート アクセス]** を選択します。

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="データ マイニング インターフェイスのビュー。":::

## <a name="stop-the-integration"></a>統合を停止する

任意の時点で、統合の通信を停止できます。

**統合を停止するには**:

1. Defender for IoT 管理コンソールで、 **[システム設定]** 画面に移動します。

1. Syslog サーバーのオプションを **[オフ]** に切り替えます。

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="サーバーの状態のビュー。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、CyberArk の統合の使用を開始する方法を学習しました。 引き続き、Forescout の統合について学習します。

> [!div class="nextstepaction"]
> [次のステップのボタン](./tutorial-forescout.md)
