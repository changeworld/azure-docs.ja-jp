---
title: Microsoft AppSource のパートナー センターで Power BI ビジュアル プランの技術的な構成を設定する
description: Microsoft AppSource のパートナー センターで Power BI ビジュアル プランの技術的構成を設定する方法について説明します。
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: 94f1a884eb2fbb10ced8c343d0408845f0ff34ba
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273024"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>Power BI ビジュアル プランの技術的な構成を設定する

**[Technical configuration]\(技術的な構成\)** タブで、Power BI ビジュアル プランに必要なファイルを指定します。

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="パートナー センターの [Technical configuration]\(技術的な構成\) ページを示しています。":::

## <a name="pbiviz-package"></a>PBIVIZ パッケージ

[Power BI ビジュアル](/power-bi/developer/visuals/package-visual)を、必要なすべてのメタデータが含まれている PBIVIZ パッケージにパックします。

- ビジュアル名
- 表示名
- GUID (下記の「注」を参照)
- バージョン (下記の「注」を参照)
- Description
- 作成者名とメール アドレス

> [!NOTE]
> ビジュアルを更新または再送信する場合:
> - GUID はそのままにしておく必要があります。
> - パッケージを更新するごとにバージョン番号をインクリメントする必要があります。

## <a name="sample-pbix-report-file"></a>サンプルの PBIX レポート ファイル

ビジュアル プランを紹介するには、ユーザーがビジュアルをよく理解できるようにする必要があります。 ユーザーにとってのビジュアルの価値を強調し、使用例や書式設定オプションを示す必要があります。 ヒントと秘訣や避けるべきことを説明した "ヒント" ページを最後に追加します。 サンプルの PBIX レポート ファイルは、外部に接続していないオフラインの状態で動作する必要があります。

> [!NOTE]
> - PBIX レポートでは、PBIVIZ と同じバージョンのビジュアルを使用する必要があります。
> - PBIX レポート ファイルは、外部に接続していないオフラインの状態で動作する必要があります。

左側のナビゲーション メニューから **[Offer management]\(プランの管理\)** タブにスキップする前に、 **[下書きの保存]** を選択してください。

## <a name="next-steps"></a>次のステップ

- [Offer management (オファーの管理)](power-bi-visual-manage-names.md)
