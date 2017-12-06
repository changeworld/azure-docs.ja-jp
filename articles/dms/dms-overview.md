---
title: "Azure Database Migration Service プレビューの概要 | Microsoft Docs"
description: "多数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を提供する、Azure Database Migration Service の概要です。"
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.topic: article
ms.date: 11/17/2017
ms.openlocfilehash: cc4053ac2be7e3a7a45da9a2a55f32f68bd9e3a0
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2017
---
# <a name="what-is-the-azure-database-migration-service-preview"></a>Azure Database Migration Service プレビューとは何ですか
Azure Database Migration Service は、複数のデータベース ソースから Azure データ プラットフォームへのシームレスな移行を最小限のダウンタイムで実現できるように設計された、完全管理型のサービスです。 このサービスは現在、パブリック プレビューとして提供されており、次の点に重点を置いて開発が進められています。

- 信頼性とパフォーマンス。
- ソースとターゲットのペアの反復的追加。
- スムーズな移行を実現するための継続的な投資。

## <a name="use-familiar-tools"></a>使い慣れたツールの使用
Azure Database Migration Service では、Microsoft の既存のツールやサービスの一部の機能が統合されています。  これにより、お客様には包括的な高可用性ソリューションが提供されます。 このサービスでは、[Data Migration Assistant](http://aka.ms/dma) を使用して評価レポートを生成します。評価レポートには、移行を実行する前に必要な変更について推奨される手順が記載されています。 必要な修正を実行するかどうかは、お客様の判断に委ねられます。 移行プロセスを開始する準備ができたら、Azure Database Migration Service によって、関連するすべての手順が実行されます。 プロセスは Microsoft によって決定されたベスト プラクティスを利用して実行されるので、お客様は安心して移行プロジェクトの完了を待つことができます。

## <a name="regional-availabilty-during-public-preview"></a>パブリック プレビュー段階で使用できる地域
現在、Azure Database Migration Service のパブリック プレビュー リリースは次のリージョンで使用できます。
- 米国東部
- 米国中南部
- 米国西部
- ブラジル南部
- 西ヨーロッパ
- 北ヨーロッパ

## <a name="next-steps"></a>次のステップ
- [Azure Portal を使用して Azure Database Migration Service のインスタンスを作成します](quickstart-create-data-migration-service-portal.md)。
