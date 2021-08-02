---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 最適化'
description: 監査およびアクティビティ ログに加えて、Azure メトリックを使用してサーバーのパフォーマンスも監視できます。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/26/2021
ms.openlocfilehash: 982001fa8150efaa1e5a268e7044f9b50694c767
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958609"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-optimization"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 最適化

### <a name="monitoring-hardware-and-query-performance"></a>ハードウェアとクエリのパフォーマンスの監視

監査およびアクティビティ ログに加えて、[Azure メトリック](../../azure-monitor/essentials/data-platform-metrics.md)を使用してサーバーのパフォーマンスも監視できます。 Azure メトリックは 1 分間隔で提供され、そこからアラートを構成できます。 監視できるメトリックの種類の詳細については、「[Azure Database for MySQL での監視](../concepts-monitoring.md)」を参照してください。

前述のように、データベース レベルのアップグレードを決定するときに、cpu\_percent や memory\_percent などのメトリックの監視が重要になることがあります。 値が一貫して高い場合、レベルのアップグレードが必要である可能性があります。

また、CPU とメモリが問題ではないと思われる場合は、管理者は、パフォーマンスの低いクエリについてインデックス作成やクエリの変更などのデータベースベースのオプションを調べることができます。

パフォーマンスの低いクエリを見つけるには、次を実行します。

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s | top 5 by query\_time\_d desc
```

### <a name="query-performance-insight"></a>Query Performance Insight

Azure には、サーバー監視の基本的な側面に加えて、アプリケーション クエリのパフォーマンスを監視するためのツールが用意されています。 クエリを修正または改善すると、クエリのスループットが大幅に向上する可能性があります。 [Query Performance Insight ツール](../concepts-query-performance-insight.md)を使用して、実行時間の長いクエリを分析し、設定された期間内に予測可能な結果になる場合にはこれらの項目をキャッシュしたり、クエリを変更してパフォーマンスを高めることができるかどうかを判断します。

`slow\_query\_log` は、MySQL ログ ファイルの低速なクエリを表示するように設定できます (既定ではオフ)。 `long\_query\_time` サーバー パラメーターを使用すると、クエリ時間が長い (既定値は 10 秒) ユーザーに警告できます。

### <a name="upgrading-the-tier"></a>レベルのアップグレード

Azure portal は、`General Purpose` と `Memory Optimized` の間のスケーリングに使用できます。 `Basic` レベルを選択した場合、後から `General Purpose` または`Memory Optimized` にレベルをアップグレードするオプションはありません。 ただし、別の方法を使用して、新しい Azure Database for MySQL インスタンスへの移行/アップグレードを実行することもできます。

Basic から別のサーバー レベルに移行するスクリプトの例については、[Azure Database for MySQL の Basic から General Purpose または Memory Optimized レベルへのアップグレード](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)に関するページを参照してください。

### <a name="scale-the-server"></a>サーバーのスケーリング

レベル内で、そのレベルで許容される最小および最大の限度までコアおよびメモリをスケーリングすることができます。 CPU またはメモリを限度まで使用し続けていることが監視からわかった場合、手順に従って、[必要に応じてスケールアップ](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)します。

### <a name="moving-regions"></a>リージョンの移動

データベースの別の Azure リージョンへの移動は、アプローチとアーキテクチャによって異なります。 アプローチによっては、システムのダウンタイムが発生する可能性があります。

推奨のプロセスは、メンテナンス フェールオーバーに読み取りレプリカを使用する場合と同じです。 ただし、前述の計画メンテナンス方法と比較すると、フェールオーバー レイヤーがアプリケーションに実装されている場合に、フェールオーバーの速度がはるかに速くなります。 アプリケーションは、読み取りレプリカ フェールオーバー プロセス中にのみしばらくダウンします。 詳細については、「事業継続とディザスター リカバリー」のセクションを参照してください。

### <a name="wwi-scenario"></a>WWI のシナリオ

WWI のビジネス ユーザーとアプリケーション ユーザーは、データベースをオンデマンドでスケーリングする機能について非常に興奮しているようでした。 また、Query Performance Insight を使用して、実行時間の長いクエリのパフォーマンスに対処する必要があるかどうかの判断にも関心を示しました。

彼らは、潜在的なフェールオーバーや読み取り専用が必要になるあらゆるシナリオに対して、読み取りレプリカ サーバーを利用することを選択しました。

移行チームは、Azure エンジニアと一緒に、MySQL サーバーのパフォーマンスに関する潜在的な問題を監視するために KQL クエリを設定しました。 KQY クエリは、データベースおよび会議チームにイベントの問題を電子メールで送信するアラートを使用して設定されました。

彼らは、さしあたっては潜在的な問題を監視し、必要に応じて運用効率を改善するために後から Azure Automation Runbook を実装することにしました。

### <a name="optimization-checklist"></a>最適化チェックリスト

  - 低速クエリを監視します。

  - Performance Insight ダッシュボードを定期的に確認します。

  - 監視を利用して、レベルのアップグレードとスケールの決定を行います。

  - ユーザーまたはアプリケーションのニーズが変化した場合にリージョンの移動を検討します。  


> [!div class="nextstepaction"]  
> [事業継続とディザスター リカバリー (BCDR)](./business-continuity-and-disaster-recovery.md)