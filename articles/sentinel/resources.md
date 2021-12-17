---
title: Microsoft Azure Sentinel の操作に便利なリソース
description: このドキュメントでは、Microsoft Azure Sentinel の操作時に便利なリソースの一覧を示します。
services: sentinel
author: yelevin
manager: rkarlin
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6ad57bdde0ce86f69747e3d29258bc128888b212
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522481"
---
# <a name="useful-resources-for-working-with-microsoft-sentinel"></a>Microsoft Azure Sentinel の操作に便利なリソース

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Azure Sentinel の操作の詳細を入手するのに役立つリソースを一覧で示します。

## <a name="learn-more-about-creating-queries"></a>クエリの作成に関する詳細

Microsoft Azure Sentinel では、Azure Monitor Log Analytics の Kusto クエリ言語 (KQL) を使用してクエリを作成します。 詳細については、次を参照してください。

- [KQL の概念](/azure/data-explorer/kusto/concepts/)
- [KQL のクエリ](/azure/data-explorer/kusto/query/)
- [KQL のクイック リファレンス ガイド](/azure/data-explorer/kql-quick-reference)。
- [KQL クエリを始める](../azure-monitor/logs/get-started-queries.md)

## <a name="learn-more-about-creating-automation"></a>オートメーション作成の詳細

Azure Logic Apps と増え続ける組み込みプレイブック ギャラリーを利用し、Microsoft Azure Sentinel でオートメーションを作成します。 

詳細については、[Azure Logic Apps コネクタ](/connectors/)に関するページを参照してください。

## <a name="compare-playbooks-workbooks-and-notebooks"></a>プレイブック、ブック、ノートブックを比較する

次の表では、Microsoft Azure Sentinel でのプレイブック、ブック、ノートブックの違いを説明します。

| カテゴリ |プレイブック  |ブック  |ノートブック  |
|---------|---------|---------|---------|
|**ペルソナ**     |   <ul><li>SOC エンジニア</li><li>すべてのレベルのアナリスト</li></ul>      | <ul><li> SOC エンジニア</li><li>すべてのレベルのアナリスト</li></ul>       | <ul><li>脅威の検出者と Tier-2/Tier-3 アナリスト</li><li>インシデント調査担当者</li><li>データ サイエンティスト</li><li>セキュリティ研究者</li></ul>       |
|**用途**     | 単純で反復可能なタスクの自動化:<ul><li>外部データの取り込み </li><li>TI や GeoIP 検索などを使用したデータ エンリッチメント </li><li> 調査 </li><li>修復 </li></ul>       | <ul><li>グラフ</li></ul>        |   <ul><li>Microsoft Azure Sentinel データおよび外部データに対するクエリ </li><li>TI、GeoIP 検索、WhoIs 検索などを使用したデータ エンリッチメント </li><li> 調査 </li><li> グラフ </li><li> 検出 </li><li>機械学習とビッグ データ分析 </li></ul>      |
|**長所**     |<ul><li> 単一の反復可能なタスクに最適 </li><li>コーディングのスキルは不要  </li></ul>      |<ul><li>Microsoft Azure Sentinel データの概要に最適 </li><li>コーディングのスキルは不要</li></ul>       | <ul><li>反復可能なタスクの複雑なチェーンに最適 </li><li>アドホックでより手続き型の制御</li><li>対話機能を使用したピボットがさらに容易に </li><li>データの操作および視覚化のための Python ライブラリが豊富 </li><li>機械学習とカスタム分析 </li><li>分析証拠の文書化および共有が簡単 </li></ul>       |
|**課題**     | <ul><li>アドホックで複雑なタスク チェーンには適していない </li><li>証拠の文書化と共有には適していない</li></ul>        |   <ul><li>外部データとの統合はできない </li></ul>     |    <ul><li> 高い学習曲線、コーディングに関する知識が必要 </li></ul>   |
|  **詳細情報**   | [Microsoft Sentinel のプレイブックを使用して脅威への対応を自動化する](automate-responses-with-playbooks.md)        | [収集されたデータを視覚化する](get-visibility.md)        | [Jupyter のノートブックを使用してセキュリティの脅威を検出する](notebooks.md)        |
|     |         |         |         |

## <a name="comment-on-our-blogs-and-forums"></a>ブログとフォーラムでコメントしてください

ユーザーの方のご意見をお待ちしています。

Microsoft Azure Sentinel の技術者コミュニティ スペースでは

- [最近のブログ投稿を見て、コメントできます](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog)
- [Microsoft Azure Sentinel に関する独自の質問を投稿できます](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel)

[User Voice](https://feedback.azure.com/d365community/forum/37638d17-0625-ec11-b6e6-000d3a4f07b8) プログラムから改善案を送信し、改善にご協力いただけます。

## <a name="join-the-microsoft-sentinel-github-community"></a>Microsoft Azure Sentinel GitHub コミュニティに参加する

[Microsoft Azure Sentinel GitHub リポジトリ](https://github.com/Azure/Azure-Sentinel)は、脅威の検出とオートメーションのための強力なリソースです。 

Microsoft のセキュリティ アナリストは、新しいブック、プレイブック、捜索クエリなどを常に作成して追加し、お客様の環境で使用できるようにそれらをコミュニティに投稿しています。 

プライベート コミュニティの GitHub リポジトリからサンプル コンテンツをダウンロードして、Microsoft Azure Sentinel 用のカスタム ブック、捜索クエリ、ノートブック、プレイブックを作成します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [認定を受ける](/learn/paths/security-ops-sentinel/)

> [!div class="nextstepaction"]
> [お客様のユース ケース ストーリーを読む](https://customers.microsoft.com/en-us/search?sq=%22Azure%20Sentinel%20%22&ff=&p=0&so=story_publish_date%20desc)
