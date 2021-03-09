---
title: Machine Learning Services (プレビュー) の主な違い
description: この記事では、Azure SQL Managed Instance の Machine Learning Services と SQL Server Machine Learning Services の主な違いについて説明します。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 10/26/2020
ms.openlocfilehash: c806c0a13f9f5f13588b780054d1f285beb44802
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324535"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance と SQL Server の Machine Learning Services の主な違い

[Azure SQL Managed Instance (プレビュー) の Machine Learning Services](machine-learning-services-overview.md) の機能は、[SQL Server Machine Learning Services](/sql/advanced-analytics/what-is-sql-server-machine-learning) とほぼ同じです。 次に主な違いをいくつか示します。

> [!IMPORTANT]
> Azure SQL Managed Instance の Machine Learning Services は現在パブリック プレビュー段階です。 サインアップするには、「[プレビューのサインアップ](machine-learning-services-overview.md#signup)」を参照してください。

## <a name="preview-limitations"></a>プレビューの制限事項

プレビュー段階では、サービスには次の制限があります。

- ループバック接続が機能しません (「[Python または R スクリプトからの SQL Server へのループバック接続](/sql/machine-learning/connect/loopback-connection)」を参照してください)。
- 外部リソース プールはサポートされていません。
- Python と R のみがサポートされています。 Java などの外部言語を追加することはできません。
- [メッセージ パッシング インターフェイス (MPI)](/message-passing-interface/microsoft-mpi) を使用するシナリオはサポートされていません。

サービス レベル目標 (SLO) の更新の場合は、SLO を更新し、サポート チケットを作成し、R または Python 専用のリソース制限を再び有効にしてください。

## <a name="language-support"></a>言語のサポート

SQL Managed Instance と SQL Server の Machine Learning Services は、Python と R の両方の[拡張機能フレームワーク](/sql/advanced-analytics/concepts/extensibility-framework)をサポートしています。 主な違いは次のとおりです。

- Python と R の初期バージョンは、SQL Managed Instance と SQL Server の Machine Learning Services で異なります。

  | システム               | Python | R     |
  |----------------------|--------|-------|
  | SQL Managed Instance | 3.7.1  | 3.5.2 |
  | SQL Server           | 3.5.2  | 3.3.3 |

- `sp_configure` で `external scripts enabled` を構成する必要はありません。 プレビューに[サインアップ](machine-learning-services-overview.md#signup)すると、Azure SQL Managed Instance に対する機械学習が有効になります。

## <a name="packages"></a>パッケージ

Python および R のパッケージ管理は、SQL Managed Instance と SQL Server で動作が異なります。 これらの違いは次のとおりです。

- 外部ランタイム (Java など) に依存するか、インストールまたは使用のための OS API へのアクセスが必要なパッケージはサポートされません。
- パッケージを使用すると、送信ネットワーク呼び出しを実行できます (以前のプレビューから変更)。 [ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md) レベルで適切な送信セキュリティ規則を設定して、送信ネットワーク呼び出しを有効にすることができます。

Python および R パッケージの管理の詳細については、以下を参照してください。

- [Python パッケージ情報の取得](/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R パッケージ情報の取得](/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>リソース管理

[Resource Governor](/sql/relational-databases/resource-governor/resource-governor) と外部リソース プールを使用して、R リソースを制限することはできません。

パブリック プレビューの期間中は、R リソースは最大で SQL Managed Instance リソースの 20% に設定され、選択するサービス レベルによって異なります。 詳細については、「[Azure SQL Database purchasing models](../database/purchasing-models.md)」 (Azure SQL Database の購入モデル) を参照してください。

### <a name="insufficient-memory-error"></a>メモリ不足エラー

R に使用できるメモリが不足している場合は、エラー メッセージが表示されます。 一般的なエラー メッセージは次のとおりです。

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

メモリ使用量は、R スクリプトでの使用量と、実行されている並列クエリの数によって決まります。 上記のエラーが発生した場合は、問題を解決するために、データベースを上位のサービス レベルにスケーリングすることができます。

## <a name="next-steps"></a>次のステップ

- 詳細については、「[Azure SQL Managed Instance の Machine Learning Services](machine-learning-services-overview.md)」を参照してください。
- Machine Learning Services で Python を使用する方法については、[Python スクリプトの実行](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。
- Machine Learning Services で R を使用する方法については、[R スクリプトの実行](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。