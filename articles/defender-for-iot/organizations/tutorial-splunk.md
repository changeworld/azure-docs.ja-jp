---
title: Splunk を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Splunk を Microsoft Defender for IoT と統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: e4b0f321741d6f07f2ac636d57de31bf02503877
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283583"
---
# <a name="tutorial-integrate-splunk-with-microsoft-defender-for-iot"></a>チュートリアル: Splunk を Microsoft Defender for IoT と統合する

このチュートリアルは、Splunk を Microsoft Defender for IoT と統合して使用する方法を学ぶのに役立ちます。

Defender for IoT により、IIoT、ICS、SCADA のリスクが軽減されます。それには、エージェント、ルール、署名、専門的なスキル、または以前の環境の知識に依存することなく、1 イメージ時間未満で、ICS デバイス、脆弱性、脅威についての即座の分析情報を提供する、特許取得済みの ICS 対応自己学習エンジンが使用されます。

OT ネットワークのセキュリティと回復性への可視性がないことに対処するため、Defender for IoT により、Defender for IoT、IIoT、および Splunk 用 ICS 脅威監視アプリケーションが開発されました。これは、IT と OT セキュリティに対する統一されたアプローチを可能にする、Defender for IoT と Splunk の間のネイティブ統合です。

このアプリケーションにより、SOC アナリストに対して、産業環境に展開された特殊な OT プロトコルと IIoT デバイスに関する多次元の可視性と、疑わしい動作や異常な動作を迅速に検出するための ICS 対応の行動分析が提供されます。 また、このアプリケーションを使用すると、1 つの企業 SOC 内から、IT と OT 両方のインシデントに対応できます。 これは、スマート コンピューターやリアルタイム インテリジェンスなどの新しい IIoT イニシアティブをサポートするために IT と OT が継続的に収束される重要な進化です。

Splunk アプリケーションは、ローカル環境にインストールすることも、クラウドで実行することもできます。 Splunk を Defender for IoT と統合することにより、両方のデプロイがサポートされます。

> [!Note]
> CyberX について記載があった場合、これは Microsoft Defender for IoT を指しています。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * Splunk で Defender for IoT アプリケーションをダウンロードする
> * Defender for IoT のアラートを Splunk に送信する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

### <a name="version-requirements"></a>バージョンの要件

このアプリケーションを実行するには、次のバージョンが必要になります。

- Defender for IoT バージョン 2.4 以降。

- Splunkbase バージョン 11 以降。

- Splunk Enterprise バージョン 7.2 以降。

### <a name="splunk-permission-requirements"></a>Splunk のアクセス許可要件

Splunk で次のアクセス許可が必要です。

- "*管理者*" レベルのユーザー ロール持つユーザー。

## <a name="download-the-defender-for-iot-application-in-splunk"></a>Splunk で Defender for IoT アプリケーションをダウンロードする

Splunk 内で Defender for IoT アプリケーションにアクセスするには、Splunkbase アプリケーション ストアからアプリケーションをダウンロードする必要があります。

**Splunk で Defender for IoT アプリケーションにアクセスするには**、次の手順を実行します。

1. [Splunkbase](https://splunkbase.splunk.com/) アプリケーション ストアに移動します。

1. `CyberX ICS Threat Monitoring for Splunk` を検索します。

1. CyberX ICS Threat Monitoring for Splunk アプリケーションを選択します。

1. **[LOGIN TO DOWNLOAD BUTTON]\(ログインしてダウンロード ボタン\)** を選択します。

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Defender for IoT のアラートを Splunk に送信する

Defender for IoT のアラートからは、広範なセキュリティ イベントに関する情報が提供されます。 イベントには次のものがあります。

- 学習されたベースライン ネットワーク アクティビティからの逸脱。

- マルウェアの検出。

- 疑わしい操作の変更に基づく検出。

- ネットワークの異常。

- プロトコル仕様からのプロトコルの逸脱。

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="検出画面。":::

Splunk サーバーにアラートを送信するように、Defender for IoT を構成することもできます。アラート情報はそこで、Splunk Enterprise のダッシュボードに表示されます。

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="すべてのアラートとその詳細を表示する。" lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

Defender for IoT から Splunk サーバーにアラート情報を送信するには、転送ルールを作成する必要があります。

**転送ルールを作成するには**、次の手順を実行します。

1. センサーにサインインして、左側のペインで **[転送]** を選択します。

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="青い [Create Forwarding Rules]\(転送ルールの作成\) ボタンを選択する。":::

1. **[Create Forwarding Rules]\(転送ルールの作成\)** を選択します。

1. **[Create Forwarding Rule]\(転送ルールの作成\)** ウィンドウで、ルールのパラメーターを定義します。

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="転送ルールのルールを作成する。" lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | パラメーター | 説明 |
    |--|--|
    | **名前** | 転送ルールの名前。 |
    | **重大度の選択** | 転送するインシデントの最小セキュリティ レベル。 たとえば、マイナーを選択すると、マイナー アラートおよびこの重大度レベルよりも上のすべてのアラートが転送されます。 |
    | **プロトコル** | 既定では、すべてのプロトコルが選択されます。 特定のプロトコルを選択するには、 **[Specific]\(特定\)** を選択し、このルールを適用するプロトコルを選択します。 |
    | **エンジン** | 既定では、すべてのセキュリティ エンジンが含まれます。 このルールを適用する特定のセキュリティ エンジンを選択するには、 **[Specific]\(特定\)** を選択して、エンジンを選択します。 |
    | **システム通知** | センサーのオンラインまたはオフラインの状態を転送します。 このオプションは、Central Manager にログインしている場合にのみ使用できます。 |

1. **[アクション]** を選択して、 **[Send to Splunk Server]\(Splunk Server に送信する\)** を選択します。

1. 次の Splunk パラメーターを入力します。

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="この画面で入力する必要がある Splunk パラメーター。" lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | パラメーター | 説明 |
    |--|--|
    | **Host** | Splunk サーバーのアドレス |
    | **[ポート]** | 8089 |
    | **ユーザー名** | Splunk サーバーのユーザー名 |
    | **パスワード** | Splunk サーバーのパスワード |

1. **[送信]** を選びます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Splunk 統合の使用を開始する方法を学習しました。 次に、[ServiceNow と Microsoft Defender for IoT を統合する](tutorial-servicenow.md)方法の学習に進みます。

> [!div class="nextstepaction"]
> [ServiceNow を Microsoft Defender for IoT と統合する](tutorial-servicenow.md)
