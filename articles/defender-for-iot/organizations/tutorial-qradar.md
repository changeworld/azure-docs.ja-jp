---
title: Qradar を Microsoft Defender for IoT と統合する
description: このチュートリアルでは、Qradar を Microsoft Defender for IoT と統合する方法について説明します。
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: 3c6a1cfab17b2355f5f5501cd7fc2e944c74556f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278362"
---
# <a name="tutorial-integrate-qradar-with-microsoft-defender-for-iot"></a>チュートリアル: Qradar を Microsoft Defender for IoT と統合する

このチュートリアルは、Qradar を Microsoft Defender for IoT と統合して使用する方法を学ぶのに役立ちます。

Defender for IoT は、唯一の ICS と、特許を取得した ICS 対応の脅威分析と機械学習を備えた IoT サイバーセキュリティ プラットフォームを提供します。

Defender for IoT では、その継続的な ICS 脅威監視プラットフォームが IBM QRadar と統合されています。

この統合の利点のいくつかを次に示します。

- 統一された IT や、OT セキュリティの監視とガバナンスのために Microsoft Defender for IoT アラートを IBM QRadar に転送する機能。

- IT と OT の両方の環境に関する概要を取得する機能。 IT と OT の境界にまたがる場合が多い多段階攻撃を検出し、それに対応することが可能になります。

- 既存の SOC ワークフローとの統合。

このチュートリアルでは、次の作業を行う方法について説明します。

> [!div class="checklist"]
> * QRadar 用に Syslog リスナーを構成する
> * Defender for IoT プラットフォームの QID を配置する
> * QRadar 転送ルールを設定する
> * 管理コンソールで QRadar に通知をマップする
> * アラートにカスタム フィールドを追加する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルに前提条件はありません。

## <a name="configure-syslog-listener-for-qradar"></a>QRadar 用に Syslog リスナーを構成する

**QRadar と連携するように Syslog リスナーを構成するには**:

1. QRadar にサインインします。

1. 左側のウィンドウから、 **[管理者]**  >  **[データ ソース]** の順に選択します。

1. [データ ソース] ウィンドウで、 **[ログ ソース]** を選択します。

   [:::image type="content" source="media/tutorial-qradar/log.png" alt-text="使用可能なオプションからログ ソースを選択しているスクリーンショット。":::](media/tutorial-qradar/log.png#lightbox)

1. **[モーダル]** ウィンドウで、 **[追加]** を選択します。

    [:::image type="content" source="media/tutorial-qradar/modal.png" alt-text="Syslog の選択後にモーダル ウィンドウが開いているスクリーンショット。":::](media/tutorial-qradar/modal.png#lightbox)

1. **[ログ ソースの追加]** ダイアログ ボックスで、次のパラメーターを設定します。

    :::image type="content" source="media/tutorial-qradar/source.png" alt-text="適切なフィールドへの入力によるログ ソースの追加のスクリーンショット。":::

   - **[ログ ソース名]** : `<Sensor name>`

   - **[ログ ソースの説明]** : `<Sensor name>`

   - **[ログ ソースの種類]** : `Universal LEEF`

   - **[プロトコル構成]** : `Syslog`

   - **[ログ ソース識別子]** : `<Sensor name>`

   > [!NOTE]
   > [ログ ソース識別子] の名前に空白を含めることはできません。 各空白文字をアンダースコアに置き換えることをお勧めします。

1. **[保存]** を選択します。

1. **[変更の配置]** を選択します。

   :::image type="content" source="media/tutorial-qradar/deploy.png" alt-text="[変更の配置] ビューのスクリーンショット":::

## <a name="deploy-defender-for-iot-platform-qid"></a>Defender for IoT プラットフォームの QID を配置する

QID は、QRadar におけるイベント識別子です。 Defenders for IoT プラットフォームのレポートはすべて、同じイベント (センサー アラート) の下にタグ付けされます。

**Defender for IoT プラットフォームの QID を配置するには**:

1. QRadar コンソールにサインインします。

1. `xsense_qids` という名前でファイルを作成します。

1. このファイルで、コマンド `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001` を使用します。

1. `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids` を実行します。 QID が正常に配置されたことを示すメッセージが表示されます。

## <a name="setup-qradar-forwarding-rules"></a>QRadar 転送ルールを設定する

統合を機能させるには、Defender for IoT アプライアンスで Qradar 転送ルールを設定する必要があります。

**Defender for IoT アプライアンスで QRadar 通知を定義するには**:

1. サイド メニューで、 **[転送]** を選択します。

1. **[転送ルールの作成]** を選択し ます。

1. アクションを **[QRadar]** に設定します。

    :::image type="content" source="media/tutorial-qradar/create.png" alt-text="転送ルールの作成ウィンドウのスクリーンショット。":::

1. QRadar の IP アドレスとタイム ゾーンを構成します。

1. **[送信]** を選択します。

## <a name="map-notifications-to-qradar"></a>QRadar に通知をマップする

次に、オンプレミスの管理コンソールでルールをマップする必要があります。

**QRadar に通知をマップするには**:

1. 管理コンソールにサインインします。

1. 左側のペインで、 **[転送中]** を選択します。

1. Qradar GUI で、QRadar の下にある **[ログ アクティビティ]** を選択します。

1. **[フィルターの追加]** を選択し、次のパラメーターを設定します。
   - [パラメーター]: `Log Sources [Indexed]`
   - [演算子]: `Equals`
   - [ログ ソース グループ]: `Other`
   - [ログ ソース]: `<Xsense Name>`

1. センサーから不明なレポートをダブルクリックします。

1. **[イベントのマップ]** を選択します。

1. [モーダル ログ ソース イベント] ページで、次のように選択します。
   - [高レベルのカテゴリ] - 疑わしいアクティビティ + [低レベルのカテゴリ] - 不明な疑わしいイベント + ログ
   - [ソースの種類] - 任意

1. **[Search]** を選択します。

1. 結果から、名前 XSense が現れる行を選択し、 **[OK]** を選択します。

すべてのセンサー レポートは今後、センサー アラートとしてタグ付けされます。

## <a name="add-custom-fields-to-the-alerts"></a>アラートにカスタム フィールドを追加する

**アラートにカスタム フィールドを追加するには**:

1. **[プロパティの抽出]** を選択します。

1. **[Regex ベース]** を選択します。

1. 次のフィールドを構成します。
   - [新しいプロパティ]: _次の一覧から選択する_
      - センサー アラートの説明
      - センサー アラート ID
      - センサー アラートのスコア
      - センサー アラートのタイトル
      - センサーの宛先名
      - センサーの直接リダイレクト
      - センサーの送信者 IP
      - センサーの送信者名
      - センサー アラート エンジン
      - センサー ソース デバイス名
   - **[解析の最適化]** をオンにする
   - フィールドの種類: `AlphaNumeric`
   - **[有効]** をオンにする
   - [ログ ソースの種類]: `Universal LEAF`
   - [ログ ソース]: `<Sensor Name>`
   - イベント名 (センサー アラートとして既に設定されている必要がある)
   - [キャプチャ グループ]: 1
   - [正規表現]:
      - センサー アラートの説明の正規表現: `msg=(.*)(?=\t)`
      - センサー アラート ID の正規表現: `alertId=(.*)(?=\t)`
      - センサー アラートのスコアの正規表現: `Detected score=(.*)(?=\t)`
      - センサー アラートのタイトルの正規表現: `title=(.*)(?=\t)`
      - センサーの宛先名の正規表現: `dstName=(.*)(?=\t)`
      - センサーの直接リダイレクトの正規表現: `rta=(.*)(?=\t)`
      - センサーの送信者 IP の正規表現: `reporter=(.*)(?=\t)`
      - センサーの送信者名の正規表現: `senderName=(.*)(?=\t)`
      - センサー アラート エンジンの正規表現: `engine =(.*)(?=\t)`
      - センサー ソース デバイス名の正規表現: `src`

## <a name="clean-up-resources"></a>リソースをクリーンアップする

クリーンアップするリソースがありません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、QRadar 統合の使用を開始する方法を学習しました。 次に、[ServiceNow と Microsoft Defender for IoT を統合する](tutorial-servicenow.md)方法の学習に進みます。

> [!div class="nextstepaction"]
> [ServiceNow を Microsoft Defender for IoT と統合する](tutorial-servicenow.md)
