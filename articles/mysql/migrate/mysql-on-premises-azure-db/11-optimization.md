---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 最適化'
description: サーバーのパフォーマンス監視には、監査ログとアクティビティ ログに加えて、Azure メトリックスを使用することもできます。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: c207e4981adc64d92804c97a69225eacb89e2fac
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082899"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-optimization"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 最適化

## <a name="prerequisites"></a>前提条件

[移行後の管理](10-post-migration-management.md)

## <a name="monitoring-hardware-and-query-performance"></a>ハードウェアとクエリのパフォーマンスの監視

サーバーのパフォーマンス監視には、監査ログとアクティビティ ログに加えて、[Azure メトリックス](/azure/azure-monitor/platform/data-platform-metrics)を使用することもできます。 Azure メトリックスは 1 分に 1 回の頻度で提供され、それに基づいたアラートを構成できます。 監視できるメトリックの種類の詳細については、「[Azure Database for MySQL での監視](/azure/mysql/concepts-monitoring)」を参照してください。

前述のとおり、cpu\_percent や memory\_percent などのメトリックを監視することは、データベース レベルのアップグレードを決定する際に重要になる可能性があります。 値が一貫して高い場合、レベルのアップグレードが必要であることを示している可能性があります。

また、CPU とメモリが問題ではないと思われる場合、管理者は、パフォーマンスの低いクエリについてインデックス作成やクエリの変更などのデータベースベースのオプションを調べることができます。

パフォーマンスの低いクエリを検出するには、次を実行します。

```
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DBFORMYSQL"
| where Category == 'MySqlSlowLogs'
| project TimeGenerated, LogicalServerName\_s, 
event\_class\_s, start\_time\_t , q uery\_time\_d, 
sql\_text\_s | top 5 by query\_time\_d desc
```

## <a name="query-performance-insight"></a>Query Performance Insight

Azure には、サーバー監視の基本的な側面に加えて、アプリケーションのクエリ パフォーマンスを監視するためのツールが用意されています。 クエリを修正または改善すると、クエリのスループットが大幅に向上する可能性があります。 [Query Performance Insight ツール](/azure/mysql/concepts-query-performance-insight)を使用して、実行時間の最も長いクエリを分析し、これらの項目をキャッシュできるかどうか (設定された期間内に確定される場合)、またはクエリを変更してパフォーマンスを向上できるかどうかを決定します。

`slow\_query\_log` を、MySQL ログ ファイルの低速なクエリを表示するように設定できます (既定ではオフ)。 `long\_query\_time` サーバー パラメーターを使用すると、クエリ時間が長い (既定値は 10 秒) ことをユーザーに警告できます。

## <a name="upgrading-the-tier"></a>レベルのアップグレード

Azure portal は、`General Purpose` と `Memory Optimized` の間のスケーリングに使用できます。 `Basic` レベルを選択した場合、後でレベルを `General Purpose` または`Memory Optimized` にアップグレードするオプションはありません。 ただし、別の手法を使用して、新しい Azure Database for MySQL インスタンスへの移行またはアップグレードを実行することはできます。

Basic から別のサーバー レベルに移行するスクリプトの例については、「[Azure Database for MySQL で Basic から General Purpose または Memory Optimized レベルへのアップグレード](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)」を参照してください。

## <a name="scale-the-server"></a>サーバーのスケーリング

レベル内で、そのレベルで許容される最小および最大の限度までコアおよびメモリをスケーリングすることができます。 監視によって、CPU またはメモリが継続的に限界に達していることが判明した場合、手順に従って[スケールアップし、需要に対応します。](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/upgrade-from-basic-to-general-purpose-or-memory-optimized-tiers/ba-p/830404)

## <a name="moving-regions"></a>リージョンの移動

別の Azure リージョンへのデータベースの移動は、アプローチとアーキテクチャによって異なります。 アプローチによっては、システムのダウンタイムが発生する可能性があります。

推奨されるプロセスは、メンテナンス フェールオーバーに読み取りレプリカを使用する場合と同じです。 ただし、前述の計画メンテナンス方法と比較すると、フェールオーバー レイヤーがアプリケーションに実装されている場合に、フェールオーバーの速度がはるかに速くなります。 アプリケーションは、読み取りレプリカのフェールオーバー プロセス中、少しの間ダウンするだけです。 詳細については、「事業継続とディザスター リカバリー」のセクションを参照してください。

## <a name="wwi-scenario"></a>WWI のシナリオ

WWI ビジネスおよびアプリケーション ユーザーは、データベースをオンデマンドでスケーリングする機能に非常に興奮していました。 また、Query Performance Insight を使用して、実行時間の長いクエリのパフォーマンスに対処する必要があるかどうかを判断することにも関心を示しました。

彼らは、潜在的なフェールオーバーや読み取り専用が必要になるあらゆるシナリオに対して、読み取りレプリカ サーバーを利用することを選択しました。

移行チームは、Azure エンジニアと協力して、MySQL サーバーのパフォーマンスに関する潜在的な問題を監視するために KQL クエリを設定しました。 KQY クエリは、データベースおよび会議チームにイベントの問題を電子メールで送信するアラートを使用して設定されました。

彼らは、現時点では潜在的な問題を監視し、運用効率を向上させるために必要に応じて後で Azure Automation Runbook を実装することを選択しました。

## <a name="optimization-checklist"></a>最適化チェック リスト

  - 速度の遅いクエリを監視します。

  - Performance Insight ダッシュボードを定期的に確認します。

  - 監視を利用して、レベルのアップグレードとスケールの決定を行います。

  - ユーザーまたはアプリケーションのニーズが変化した場合にリージョンの移動を検討します。  


> [!div class="nextstepaction"]
> [ビジネス継続性とディザスター リカバリー (BCDR)](./12-business-continuity-and-disaster-recovery.md)