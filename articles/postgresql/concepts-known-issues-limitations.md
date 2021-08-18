---
title: 既知の問題と制限事項 - Azure Database for PostgreSQL - シングル サーバーとフレキシブル サーバー (プレビュー)
description: お客様に認識していただく必要がある既知の問題の一覧を示します。
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 0806ef1a191759e769271a97fb1809298cde10db
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725272"
---
# <a name="azure-database-for-postgresql---known-issues-and-limitations"></a>Azure Database for PostgreSQL - 既知の問題と制限事項

このページでは、お使いのアプリケーションに影響を与える可能性がある Azure Database for PostgreSQL の既知の問題の一覧を示します。 また、問題を回避するための軽減策と推奨事項も記載しています。

## <a name="intelligent-performance---query-store"></a>インテリジェントなパフォーマンス - クエリ ストア

Azure Database for PostgreSQL - シングル サーバーに適用されます。

| 適用できます | 原因 | 修復|
| ----- | ------ | ---- | 
| PostgreSQL 9.6、10、11 | サーバー パラメーター `pg_qs.replace_parameter_placeholders` をオンにすると、一部のまれなシナリオでサーバーがシャットダウンすることがあります。 | Azure portal の [サーバー パラメーター] セクションで、パラメーター `pg_qs.replace_parameter_placeholders` 値を `OFF` にして保存します。   | 


## <a name="next-steps"></a>次のステップ
- クエリ ストアの[ベスト プラクティス](./concepts-query-store-best-practices.md)に関する記事を参照してください。
