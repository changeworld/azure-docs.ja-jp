---
title: "Log Analytics の新しいログ検索についてよく寄せられる質問 | Microsoft Docs"
description: "この記事では、よく寄せられる新しいクエリ言語への Log Analytics のアップグレードに関する質問について説明します。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: 2de9a87390da11a034c6cebaa37d4cc89edf3cad
ms.contentlocale: ja-jp
ms.lasthandoff: 08/09/2017

---

# <a name="log-analytics-new-log-search-frequently-asked-questions"></a>Log Analytics の新しいログ検索についてよく寄せられる質問

この記事では、よく寄せられる[新しいクエリ言語への Log Analytics](log-analytics-log-search-upgrade.md) のアップグレードに関する質問について説明します。



## <a name="upgrade-process"></a>アップグレード プロセス

### <a name="i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>ワークスペースが複数あります。 すべてのワークスペースを同時にアップグレードできますか。  
A: いいえ。  アップグレードはワークスペースごとに 1 つずつ適用されます。 現時点では、多数のワークスペースを一度にアップグレードすることはできません。 ワークスペースをアップグレードすると、そのワークスペースの他のユーザーも影響を受けることに注意してください。  

### <a name="will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>アップグレードすると、ワークスペースで収集された既存のログ データは変更されますか。  
いいえ。 ワークスペース検索で使用できるログ データは、アップグレードの影響を受けません。 保存した検索、警告、およびビューは、新しい検索言語に自動的に変換されます。  

### <a name="what-happens-if-i-dont-upgrade-my-workspace"></a>ワークスペースをアップグレードしないとどうなりますか。  
従来のログ検索は、数か月後に廃止される予定です。 その時点でアップグレードされていないワークスペースは、自動的にアップグレードされます。

### <a name="i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>アップグレードするように選択しなかったにもかかわらず、ワークスペースがアップグレードされました。 なぜでしょうか。  
このワークスペースの他の管理者が、ワークスペースをアップグレードした可能性があります。 新しい言語が一般公開された時点で、すべてのワークスペースが自動的にアップグレードされることに注意してください。  

### <a name="i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>誤ってアップグレードしてしまいました。アップグレードをキャンセルして、すべてを復元する必要があります。 どうすればよいですか。  
ご安心ください。  アップグレード前のワークスペースのスナップショットが作成されているため、復元できます。 ただし、アップグレード後に保存した検索、警告、またはビューは失われることに注意してください。  ワークスペース環境を復元するには、「[アップグレード後の復元](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)」の手順に従ってください。



## <a name="log-searches"></a>ログ検索

### <a name="i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>アップグレードされたワークスペース以外で検索を保存しました。 その検索を、新しい検索言語に自動変換できますか。
ログ検索ページで言語コンバーター ツールを使用して、1 つずつ変換できます。  ワークスペースをアップグレードしないで、複数の検索を自動的に変換することはできません。


## <a name="alerts"></a>アラート

### <a name="i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>警告ルールが多数あります。 アップグレード後、そのルールを新しい言語で作成し直す必要がありますか。  
いいえ。アップグレード中、警告ルールは新しい検索言語に自動変換されます。  

## <a name="power-bi"></a>Power BI

### <a name="does-anything-change-with-powerbi-integration"></a>PowerBI 統合で変更はありますか。
はい。  ワークスペースのアップグレードが完了すると、Power BI への Log Analytics データのエクスポート プロセスは動作しなくなります。  また、アップグレード前に作成した既存のスケジュールすべてが無効になります。  アップグレード後、Azure Log Analytics では Application Insights と同じプラットフォームが使用されるため、[Application Insights クエリを Power BI にエクスポートするプロセス](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)と同じプロセスを使用して、Power BI に Log Analytics クエリをエクスポートします。

## <a name="dashboards"></a>ダッシュボード

### <a name="can-i-still-use-dashboards"></a>ダッシュボードは引き続き使用できますか。
ワークスペースがアップグレードされる前に作成したダッシュボードは引き続き使用できますが、それらのダッシュボードを編集したり、新しいダッシュボードを作成したりすることはできません。  ビューの作成と編集は引き続き[ビュー デザイナー](log-analytics-view-designer.md)で行うことができます。 

## <a name="next-steps"></a>次のステップ

- [新しい Log Analytics クエリ言語へのワークスペースのアップグレード](log-analytics-log-search-upgrade.md)について確認します。

