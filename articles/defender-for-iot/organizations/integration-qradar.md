---
title: QRadar の統合
description: Defender for IoT ソリューションと QRadar との統合を構成します。
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 26808482ad9fabd528d2ec7e0d846c301c2c2ebf
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342421"
---
# <a name="about-the-qradar-integration"></a>QRadar の統合について

Defender for IoT は、重要な国家インフラを守る実績を持つ Blue Team のエキスパートによって構築された唯一の ICS および IoT サイバーセキュリティ プラットフォームであるだけでなく、特許を取得した ICS 対応の脅威分析と機械学習を備えた唯一のプラットフォームです。

Defender for IoT では、その継続的な ICS 脅威監視プラットフォームが IBM QRadar と統合されています。 

この統合の利点のいくつかを次に示します。

- 統一された IT や、OT セキュリティの監視とガバナンスのために Azure Defender for IoT アラートを IBM QRadar に転送する機能。

- IT と OT の両方の環境にまたがる鳥瞰図を取得する機能。 これにより、IT と OT の境界にまたがる場合が多い多段階攻撃を検出し、それに対応できるようになります。

- 既存の SOC ワークフローとの統合。

## <a name="configuring-syslog-listener-for-qradar"></a>QRadar 用の Syslog リスナーの構成

QRadar と連携するように Syslog リスナーを構成するには:

1. QRadar にサインインします。

1. 左側のウィンドウから、 **[管理者]**  >  **[データ ソース]** の順に選択します。

   [:::image type="content" source="media/integration-qradar/log.png" alt-text="使用可能なオプションからログ ソースを選択する。":::](media/integration-qradar/log.png#lightbox)

1. [データ ソース] ウィンドウで、 **[ログ ソース]** を選択します。

   [:::image type="content" source="media/integration-qradar/modal.png" alt-text="Syslog を選択すると、モーダル ウィンドウが開く。":::](media/integration-qradar/modal.png#lightbox)

1. **[モーダル]** ウィンドウで、 **[追加]** を選択します。

   :::image type="content" source="media/integration-qradar/source.png" alt-text="適切なフィールドに入力することによってログ ソースを追加する。":::

1. **[ログ ソースの追加]** ダイアログ ボックスで、次のパラメーターを設定します。

   - **[ログ ソース名]** : `<XSense Name>`
   
   - **[ログ ソースの説明]** : `<XSense Name>`
   
   - **[ログ ソースの種類]** : `Universal LEEF`

   - **[プロトコル構成]** : `Syslog`
   
   - **[ログ ソース識別子]** : `<XSenseName>`
   
   > [!NOTE]
   > [ログ ソース識別子] の名前に空白を含めることはできません。 各空白文字をアンダースコアに置き換えることをお勧めします。

1. **[保存]** を選択します。

1. **[変更の配置]** を選択します。

:::image type="content" source="media/integration-qradar/deploy.png" alt-text="[変更の配置] ビューのスクリーンショット":::

## <a name="deploying-defender-for-iot-platform-qid"></a>Defender for IoT プラットフォームの QID の配置

QID は、QRadar におけるイベント識別子です。 Defenders for IoT プラットフォームのレポートはすべて、同じイベント (XSense アラート) の下にタグ付けされます。

**Xsense QID を配置するには**:

1. QRadar コンソールにサインインします。

1. `xsense_qids` という名前でファイルを作成します。

1. このファイルで、コマンド `,XSense Alert,XSense Alert Report From <XSense Name>,5,7001` を使用します。

1. `sudo /opt/qradar/bin/qidmap_cli.sh -i -f <path>/xsense_qids` を実行します。 QID が正常に配置されたことを示すメッセージが表示されます。

## <a name="setting-up-qradar-forwarding-rules"></a>QRadar 転送ルールの設定

Defender for IoT アプライアンスで、Qradar 転送ルールを構成します。 このルールをオンプレミス管理コンソールでマップします。

**Defender for IoT アプライアンスで QRadar 通知を定義するには**:

1. サイド メニューで、 **[転送]** を選択します。

   :::image type="content" source="media/integration-qradar/create.png" alt-text="転送ルールを作成する":::

1. アクションを **[QRadar]** に設定します。

1. QRadar の IP アドレスとタイム ゾーンを構成します。

1. **[送信]** を選択します。

**Central Manager で通知を QRadar にマップするには**:

1. サイド メニューから、 **[転送]** を選択します。

1. Qradar GUI で、QRadar の下にある **[ログ アクティビティ]** を選択します。

1. **[フィルターの追加]** を選択し、次のパラメーターを設定します。
   - [パラメーター]: `Log Sources [Indexed]`
   - [演算子]: `Equals`
   - [ログ ソース グループ]: `Other`
   - [ログ ソース]: `<Xsense Name>`

1. XSense から不明なレポートをダブルクリックします。

1. **[イベントのマップ]** を選択します。

1. [モーダル ログ ソース イベント] ページで、次のように選択します。
   - [高レベルのカテゴリ] - 疑わしいアクティビティ + [低レベルのカテゴリ] - 不明な疑わしいイベント + ログ
   - [ソースの種類] - 任意

1. **[Search]** を選択します。

1. 結果から、名前 XSense が現れる行を選択し、 **[OK]** を選択します。

XSense レポートはすべて、今後は XSense アラートとしてタグ付けされます。

## <a name="adding-custom-fields-to-alerts"></a>アラートへのカスタム フィールドの追加

**アラートにカスタム フィールドを追加するには**:

1. **[プロパティの抽出]** を選択します。

1. **[Regex ベース]** を選択します。

1. 次のフィールドを構成します。
   - [新しいプロパティ]: _次の一覧から選択する_
      - Xsense アラートの説明
      - Xsense アラート ID
      - Xsense アラートのスコア
      - Xsense アラートのタイトル
      - Xsense の宛先名
      - Xsense の直接リダイレクト
      - Xsense 送信者 IP
      - Xsense 送信者名
      - Xsense アラート エンジン
      - Xsense ソースデバイス名
   - **[解析の最適化]** をオンにする
   - フィールドの種類: `AlphaNumeric`
   - **[有効]** をオンにする
   - [ログ ソースの種類]: `Universal LEAF`
   - [ログ ソース]: `<Xsense Name>`
   - イベント名 (既に XSense アラートとして設定されている必要がある)
   - [キャプチャ グループ]: 1
   - [正規表現]:
      - Xsense アラートの説明の正規表現: `msg=(.*)(?=\t)`
      - Xsense アラート ID の正規表現: `alertId=(.*)(?=\t)`
      - Xsense アラートのスコアの正規表現: `Detected score=(.*)(?=\t)`
      - Xsense アラートのタイトルの正規表現: `title=(.*)(?=\t)`
      - Xsense の宛先名の正規表現: `dstName=(.*)(?=\t)`
      - Xsense の直接リダイレクトの正規表現: `rta=(.*)(?=\t)`
      - Xsense 送信者 IP の正規表現: `reporter=(.*)(?=\t)`
      - Xsense 送信者名の正規表現: `senderName=(.*)(?=\t)`
      - Xsense アラート エンジンの正規表現: `engine =(.*)(?=\t)`
      - Xsense ソースデバイス名の正規表現: `src`

## <a name="defining-defender-for-iot-appliance-name"></a>Defender for IoT アプライアンス名の定義

プラットフォームの名前はいつでも変更できます。

サイトを構築し、オンプレミス管理コンソールでアプライアンスをゾーンに割り当てたら、各アプライアンスに意味のある名前を割り当てる必要があります。 たとえば、"オートバイ PL ユニット 2" は、このアプライアンスがオートバイ生産ラインのユニット #2 を保護していることを示します。 

アプライアンスの名前はログに渡されるため、そのアプライアンスのわかりやすい名前を選択することが重要です。 ログを確認するときは、各アラートにセンサーがアタッチされます。 各アラートにどのセンサーが関連しているかは、その名前に基づいて識別できます。

**アプライアンス名を変更するには**:

1. サイド メニューで、現在のアプライアンス名を選択します。 **[管理コンソールの構成の編集]** ダイアログ ボックスが表示されます。

   :::image type="content" source="media/integration-qradar/edit-management-console.png" alt-text="コンソールの名前を変更する。":::

1. [名前] フィールドに名前を入力し、 **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

[アラート情報を転送する](how-to-forward-alert-information-to-partners.md)方法を学習します。
