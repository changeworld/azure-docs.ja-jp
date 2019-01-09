---
title: Azure Stack の監視データの使用 | Microsoft Docs
description: Azure Stack からの監視データを使用するためのオプションについて説明します。
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
ms.date: 12/01/2018
ms.author: mabrigg
ms.openlocfilehash: fbd4552a9e40f16a6fedec4e04be0d7d6d39351d
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2018
ms.locfileid: "52724487"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Azure Stack の監視データの使用方法

*適用対象: Azure Stack 統合システム*

グローバル Azure 上の Azure Monitor と同様に、Azure Monitor パイプラインを使用して、監視データを 1 か所で確認することができます。 ただし、グローバル Azure で使用できるすべての監視データを Azure Stack で使用できるわけではありません。 この記事では、サービスからプログラムで監視データを取り込むためのさまざまな方法の概要を説明します。
 
## <a name="options-for-data-consumption"></a>データの使用のオプション

| データ型 | Category | サポートされているサービス | アクセスの方法 |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor プラットフォーム レベルのメトリック | メトリック | [Azure Stack 上の Azure Monitor でサポートされるメトリック](azure-stack-metrics-supported.md) | REST API |
| ゲスト OS メトリック (Perf カウントなど) の計算 | メトリック | Windows および Linux Virtual Machines | ストレージ テーブルまたは BLOB:<br>Windows または Linux の Azure 診断 <br>イベント ハブ:<br>Windows の Azure 診断 |
| Storage のメトリック | メトリック | Azure Storage | ストレージ テーブル:<br>Storage Analytics |
| アクティビティ ログ | events | すべての Azure サービス | REST API:<br>Azure Monitor イベント API |
| ゲスト OS ログ (IIS、ETW、syslog など) の計算 | events | Windows および Linux Virtual Machines | ストレージ テーブルまたは BLOB:<br>Windows または Linux の Azure 診断 <br>イベント ハブ:<br>Windows の Azure 診断 |
| ストレージ ログ | events | Azure Storage | ストレージ テーブル:<br>Storage Analytics |

## <a name="next-steps"></a>次の手順

[Azure Stack 上の Azure Monitor](azure-stack-metrics-azure-data.md) について学習します。
