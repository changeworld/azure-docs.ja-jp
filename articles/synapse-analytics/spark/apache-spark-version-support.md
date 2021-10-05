---
title: Apache Spark バージョンのサポート
description: Spark、Scala、Python、.NET のサポートされているバージョン
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/22/2021
ms.author: midesa
ms.openlocfilehash: 9f13a0797caa13f0d4367357308809c97bde0b17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577772"
---
# <a name="azure-synapse-runtimes"></a>Azure Synapse ランタイム
Azure Synapse の Apache Spark プールでは、ランタイムを使用して、必須コンポーネントのバージョン、Azure Synapse の最適化、パッケージ、およびコネクタを特定の Apache Spark のバージョンに結び付けます。 これらのランタイムは、新しい改良、機能、パッチを含むように定期的にアップグレードされます。 

サーバーレスの Apache Spark プールを作成する場合は、対応する Apache Spark のバージョンを選択するオプションがあります。 これに基づき、プールには関連するランタイム コンポーネントとパッケージがプレインストールされます。 これらのランタイムには、次の利点があります。

- セッションの起動時間の短縮
- 特定の Apache Spark のバージョンとの互換性をテスト済み
- 互換性のある一般的なコネクタとオープンソース パッケージへのアクセス

> [!NOTE]
> - メンテナンスの更新プログラムは、指定されたサーバーレス Apache Spark プールの新しいセッションに自動的に適用されます。 
> - 新しいランタイム バージョンを使用する場合は、アプリケーションが正常に動作するかどうかをテストして検証する必要があります。

## <a name="supported-azure-synapse-runtime-releases"></a>サポートされている Azure Synapse ランタイム リリース 
次の表は、サポートされている Azure Synapse ランタイムのリリースのランタイム名、Apache Spark のバージョン、リリース日をまとめたものです。

|  ランタイム名  | リリース日 |  リリース ステージ |
| ----- | ----- | ----- |
| [Azure Synapse Runtime for Apache Spark 2.4](./apache-spark-24-runtime.md) | 2020 年 12 月 15 日 | GA|
| [Azure Synapse Runtime for Apache Spark 3.1](./apache-spark-3-runtime.md) | 2021 年 5 月 26 日 | GA |

## <a name="runtime-release-stages"></a>ランタイム リリース ステージ

## <a name="preview-runtimes"></a>プレビュー ランタイム
Azure Synapse Analytics では、一般提供 (GA) を開始する前に機能を評価し、フィードバックを共有する機会を提供するために、プレビューを提供しています。 ランタイムがプレビューで提供されている間は、新しい依存関係やコンポーネントのバージョンが導入される可能性があります。 サポート SLA は、プレビュー ランタイムには適用されません。 

## <a name="generally-available-runtimes"></a>一般提供のランタイム
一般提供 (GA) のランタイムはすべてのお客様を対象としており、運用環境での使用に対応しています。 ランタイムが一般提供されると、セキュリティ修正と安定性の向上が移植になる可能性があります。 また、新しいコンポーネントは、基になる依存関係やコンポーネントのバージョンを変えることが無い場合にのみ導入されます。 
