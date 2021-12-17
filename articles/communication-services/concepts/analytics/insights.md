---
title: Azure Communication Services のインサイト機能 (プレビュー)
titleSuffix: An Azure Communication Services concept document
description: Communications Services でブックを介して使用できるデータ視覚化の説明
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: bfe0d0a6fbc47ff1cdce238d6c30c3f50c118bdd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716545"
---
# <a name="communications-services-insights-preview"></a>Communications Services Insights プレビュー

## <a name="overview"></a>概要
Communications リソースの内部で提供される **インサイト (プレビュー)** 機能を使用すると、Communications Services で監視している Azure Monitor のログとメトリックから分析情報を抽出し、さまざまなデータ視覚化の形で表示することができます。 インサイト内での視覚化には [Azure Monitor ブック](../../../azure-monitor/visualize/workbooks-overview.md)が利用されます。 ブックを利用するには、[診断設定での Azure Monitor の有効化](enable-logging.md)に関するページで説明されている手順に従います。ブックを有効にするには、[Log Analytics ワークスペース](../../../azure-monitor/logs/log-analytics-overview.md)の送信先にログを送信する必要があります。 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="Communication Services のインサイト機能":::

## <a name="accessing-azure-insights-for-communication-services"></a>Communications Services 用の Azure インサイトへのアクセス

1. **Azure portal** のホーム ページから、対象の **Communications Services** リソースを選択します。

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Azure portal ホーム":::

2. リソースに移動したら、左側のナビゲーション バーで **[監視]** カテゴリまで下にスクロールして **[インサイト]** タブをクリックします。

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="[インサイト] のナビゲーション":::

3. 対象の Communication Service リソースの **[インサイト]** ダッシュボードが表示されます。

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="Communication Services の [インサイト] タブ":::

## <a name="insights-dashboard-navigation"></a>[インサイト] ダッシュボードのナビゲーション

Communication Service の [インサイト] ダッシュボードでは、直感的でわかりやすい方法でリソースのログ データを参照できます。 **[概要]** セクションでは、すべてのモダリティにまたがるビューが提供されるため、特定の時間範囲におけるリソースのさまざまな使用状況を確認できます。

:::image type="content" source="media\workbooks\overview.png" alt-text="Insights の概要":::

上部に表示されるパラメーターを使用して、表示する時間範囲と時間の粒度を制御できます。

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="[時間範囲] パラメーター":::

これらのパラメーターはグローバルであり、ダッシュボード全体の表示データを更新します。

**[概要]** セクションには、表示する視覚化の種類を制御する追加のパラメーターがあります。

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="[プロットの種類] パラメーター":::

このパラメーターはローカルであり、このセクションのプロットにのみ影響します。

残りのタブには、特定のモダリティに関連するログ データが表示されます。

:::image type="content" source="media\workbooks\main-nav.png" alt-text="メイン ナビゲーション":::

**[音声とビデオ]** のログはその性質上、最も複雑なため、このモダリティは 4 つのサブセクションに分かれています。

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="[音声とビデオ] のナビゲーション":::

**[概要]** タブには、共有されているメディア ストリームの種類、通話に参加しているエンドポイントの種類 (VoIP、ボット、アプリケーション、PSTN、サーバーなど)、OS の使用状況、参加者の終了理由など、音声とビデオの使用状況に関する全般的な情報が表示されます。

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="[音声とビデオ] の [概要]":::

**[音声とビデオ]** モダリティの下にある **[ボリューム]** タブでは、特定の期間 ( **[時間範囲]** パラメーター) 内の通話数と参加者数が、時間ビン ( **[時間の粒度]** パラメーター) に分割して表示されます。

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="[音声とビデオ] の [ボリューム]":::

**[ボリューム]** タブの **[グループ化]** パラメーターを使用すると、通話数と参加者数を、通話の種類 (P2P か、それともグループ通話か) と相互運用機能による通話 (純粋な Azure Communication Services (ACS) か、それとも Teams の相互運用機能か) のいずれかでセグメント化して視覚化することができます。

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="[音声とビデオ] の [ボリューム] の [グループ化]":::

**[音声とビデオ]** の下にある **[品質]** タブでは、通話の品質分布を調べることができます。このダッシュボードでは、品質は 3 つのレベルで定義されます。

- 低品質のメディア ストリームの割合 ( **[ストリーム品質]** プロット)。ストリームの品質は、少なくとも 1 つの異常なテレメトリ値がある場合に低品質に分類され、異常の範囲は次のように定義されます。
  - ジッター > 30 ミリ秒
  - パケット損失率 > 10%
  - ラウンド トリップ時間 > 500 ミリ秒

- **[影響を受ける通話]** の割合。影響を受ける通話は、少なくとも 1 つの低品質なストリームがある通話として定義されます。

- **[参加者の終了理由]** 。参加者が通話を終了した理由を追跡します。 最終的な理由は [SIP コード](https://en.wikipedia.org/wiki/List_of_SIP_response_codes)で表されますが、これは、シグナリング要求の特定のステータスを説明する数値コードです。 SIP コードは、*Success* (成功)、*Client Failure* (クライアント障害)、*Server Failure* (サーバー障害)、*Global Failure* (グローバル障害)、*Redirection* (リダイレクト)、*Provisional* (暫定) の 6 つのカテゴリに分類できます。 SIP コード カテゴリの分布は左側の円グラフに表示され、参加者の終了理由に関する特定の SIP コードの一覧が右側に表示されます。

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="[音声とビデオ] の [品質]":::

通話で使用されるメディア ストリームの種類 ( **[メディアの種類]** パラメーター) によって品質をフィルター処理することもできます。たとえば、ビデオ ストリーム品質の観点から影響を受ける通話のみを取得します。

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="[音声とビデオ] の [品質] の [メディアの種類] パラメーター":::

エンドポイントの種類 ( **[エンドポイントの種類]** パラメーター) でフィルター処理することもできます。たとえば、PSTN 参加者の終了理由を取得します。 これらのフィルターでは、複数の選択が可能です。

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="[音声とビデオ] の [品質] の [エンドポイントの種類] パラメーター":::

**[詳細]** タブでは、特定の時間範囲内の **[音声とビデオ]** 通話をすばやく参照できます。日付で通話をグループ化したり、発生したすべての通話の詳細 (通話の参加者、参加者あたりの発信ストリーム数、通話時間、通話のテレメトリ値など) を表示したりできます。

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="[音声とビデオ] の [詳細]":::

通話の詳細は、初期状態では非表示になっています。 通話をクリックすると、参加者の一覧が表示されます。

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="[音声とビデオ]: 参加者の詳細":::

参加者をクリックすると、その参加者の発信ストリームの一覧が、(通話時間全体に比例した) 継続時間、およびテレメトリ値 (異常値は赤で表示) と共に表示されます。

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="[音声とビデオ]: ストリームの詳細":::

**[認証]** タブには、アクセス トークンの発行や ID の作成などの操作によって生成される認証ログが表示されます。 表示データには、実行された操作の種類や各操作の結果が含まれます。

:::image type="content" source="media\workbooks\auth.png" alt-text="[認証] タブ":::

**[チャット]** タブには、すべてのチャット関連操作とその結果タイプのデータが表示されます。

:::image type="content" source="media\workbooks\chat.png" alt-text="[チャット] タブ":::

**[SMS]** タブには、ACS リソースを通じた SMS 使用の操作および結果が表示されます (現時点では、このモダリティにはデータがありません)。

:::image type="content" source="media\workbooks\sms.png" alt-text="[SMS] タブ":::

## <a name="editing-dashboards"></a>ダッシュボードの編集

**Communication Service** リソースで提供される **[インサイト]** ダッシュボードは、上部のナビゲーション バーの **[編集]** ボタンをクリックしてカスタマイズできます。

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="ダッシュボードの編集":::

これらのダッシュボードを編集しても **[インサイト]** タブは変更されませんが、リソースの [ブック] タブからアクセスできる別のブックが作成されます。

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="[ブック] タブ":::

ブックの詳細な説明については、[Azure Monitor ブック](../../../azure-monitor/visualize/workbooks-overview.md)のドキュメントを参照してください。