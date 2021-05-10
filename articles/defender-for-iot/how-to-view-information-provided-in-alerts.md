---
title: アラート メッセージについて
description: '[アラート] ウィンドウでアラートを選択して、詳細を確認します。'
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781485"
---
# <a name="about-alert-messages"></a>アラート メッセージについて

**[アラート]** ウィンドウでアラートを選択して、アラートの詳細を確認します。 詳細には次の情報が含まれます。

- アラート メタデータ

- トラフィック、デバイス、およびイベントに関する情報

- デバイス マップ内の接続されているデバイスへのリンク

- セキュリティ アナリストおよび管理者によって定義されたコメント

- イベントの調査に関する推奨事項

## <a name="alert-metadata"></a>アラート メタデータ

アラートの詳細には、次のアラート メタデータが含まれます。

  - アラート ID

  - アラートをトリガーしたポリシー エンジン

  - アラートがトリガーされた日付と時刻

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="不正なインターネット接続が検出されました。":::

## <a name="information-about-devices-traffic-and-the-event"></a>デバイス、トラフィック、およびイベントに関する情報

アラート メッセージでは、以下に関する情報が提供されます。

  - 検出されたデバイス。

  - プロトコルや関数コードなど、デバイス間で検出されたトラフィック。

  - イベントの影響についての分析情報。

この情報は、アラート イベントの管理方法を決定するときに使用できます。

## <a name="links-to-connected-devices-in-the-device-map"></a>デバイス マップ内の接続されているデバイスへのリンク

検出されたデバイスに接続されているデバイスの詳細については、アラート内でデバイス イメージを選択し、接続されているデバイスをマップに表示することができます。

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="RCP 操作が失敗しました。":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="デバイスのスクリーンショット。":::

マップにより、選択したデバイスと、それに接続されている他のデバイスにフィルターが適用されます。 マップには、アラートで検出されたデバイスの **[Quick Properties]\(クイック プロパティ\)** ダイアログ ボックスも表示されます。

## <a name="comments-defined-by-security-analysts-and-administrators"></a>セキュリティ アナリストおよび管理者によって定義されたコメント 

アラートには、定義済みのコメントの一覧が含まれている場合があります。 たとえば、コメントには、実行する軽減措置に関する指示や、イベントに関して連絡する担当者の名前を記述できます。

アラート イベントを管理している場合は、イベントの状態、またはアラートを調査するために実行したステップが最もよく反映されたコメントを選択できます。

選択したコメントはアラート メッセージに保存されます。 コメントを使用して、アラート イベントの調査中に個人とチーム間のコミュニケーションを改善できます。 その結果、コメントによってインシデント対応時間を短縮できます。

管理者またはセキュリティ アナリストは、コメントを事前に定義します。 選択したコメントは、転送ルールで定義されているパートナー システムに転送されません。

アラート内の情報を確認した後、さまざまなフォレンジック ステップを実行して、アラート イベントの管理を案内することができます。 例:

- 最近のデバイス アクティビティを分析します (データマイニング レポート)。 

- 同時に発生した他のイベントを分析します (イベント タイムライン)。 

- 包括的なイベント トラフィック (PCAP ファイル) を分析します。

## <a name="pcap-files"></a>PCAP ファイル

場合によっては、アラート メッセージから PCAP ファイルにアクセスできます。 これは、関連するネットワーク トラフィックに関する詳細情報が必要な場合に便利です。

PCAP ファイルをダウンロードするには、:::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="ダウンロード アイコン"::: を選択します。 これは **[アラートの詳細]** ダイアログ ボックスの右上にあります。

## <a name="recommendations-for-investigating-an-event"></a>イベントの調査に関する推奨事項 

アラートの **[Recommendation]\(推奨事項\)** 領域には、イベントの理解を深めるのに役立つ可能性のある情報が表示されます。 アラート イベントを管理したり、デバイスまたはネットワークに対してアクションを実行したりする前に、この情報を確認してください。

## <a name="see-also"></a>関連項目

[アラート ワークフローの高速化](how-to-accelerate-alert-incident-response.md)

[アラート イベントの管理](how-to-manage-the-alert-event.md)
