---
title: Azure Stack の監視データの使用 | Microsoft Docs
description: Azure Stack の監視データを使用するオプションについて学習します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: mabrigg
ms.openlocfilehash: 949dee06089ed381010a03ac2fae73aaf4a00c54
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889033"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Azure Stack の監視データの使用方法

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

グローバル Azure 上の Azure Monitor と同様に、Azure Monitor パイプラインを使用して、監視データを 1 か所で確認することができます。 ただし、グローバル Azure で使用できるすべての監視データを Azure Stack で使用できるわけではありません。 この記事では、サービスからプログラムで監視データを取り込むためのさまざまな方法の概要を説明します。
 
## <a name="options-for-data-consumption"></a>データの使用のオプション

| データ型 | Category | サポートされているサービス | アクセスの方法 |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor プラットフォーム レベルのメトリック | メトリック | [Azure Stack 上の Azure Monitor でサポートされるメトリック](azure-stack-metrics-supported.md) | REST API |
| ゲスト OS メトリック (Perf カウントなど) の計算 | メトリック | Windows および Linux Virtual Machines | ストレージ テーブルまたは BLOB:<br>Windows または Linux の Azure 診断 <br>イベント ハブ:<br>Windows の Azure 診断 |
| Storage のメトリック | メトリック | Azure Storage | ストレージ テーブル:<br>Storage Analytics |
| アクティビティ ログ | events | すべての Azure サービス | REST API:<br>Azure Monitor イベント API |
| ゲスト OS ログ (IIS、ETW、syslog など) の計算 | events | Windows および Linux Virtual Machines | ストレージ テーブルまたは BLOB:<br>Windows または Linux の Azure 診断 <br>イベント ハブ:<br>Windows の Azure 診断 |
| ストレージ ログ | events | Azure Storage | ストレージ テーブル:<br>Storage Analytics<br>`Vita: how about hybrid OMS/AppInsights, shall we mention?` |

## <a name="next-steps"></a>次の手順

[Azure Stack 上の Azure Monitor](azure-stack-metrics-azure-data.md) について学習します。