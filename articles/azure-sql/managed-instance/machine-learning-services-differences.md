---
title: Machine Learning Services の主な違い
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
ms.date: 03/17/2021
ms.openlocfilehash: b5ad439a8e10fa9aa44e477ca35f45d65ae40803
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104599546"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-managed-instance-and-sql-server"></a>Azure SQL Managed Instance と SQL Server の Machine Learning Services の主な違い

この記事では、[Azure SQL Managed Instance の Machine Learning Services](machine-learning-services-overview.md) と [SQL Server Machine の Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) の主な違いについて説明します。

## <a name="language-support"></a>言語のサポート

SQL Managed Instance と SQL Server の Machine Learning Services はどちらも、Python と R の[拡張機能フレームワーク](/sql/machine-learning/concepts/extensibility-framework)をサポートしています。 SQL Managed Instance の主な相違点は次のとおりです。

- Python と R のみがサポートされています。 Java などの外部言語を追加することはできません。

- 初期バージョンの Python と R が異なります。

  | プラットフォーム                   | Python ランタイムのバージョン           | R ランタイムのバージョン                   |
  |----------------------------|----------------------------------|--------------------------------------|
  | Azure SQL Managed Instance | 3.7.2                            | 3.5.2                                |
  | SQL Server 2019            | 3.7.1                            | 3.5.2                                |
  | SQL Server 2017            | 3.5.2 および 3.7.2 (CU22 以降) | 3.3.3 および 3.5.2 (CU22 以降)     |
  | SQL Server 2016            | 使用できません                    | 3.2.2 および 3.5.2 (SP2 CU14 以降) |

## <a name="python-and-r-packages"></a>Python および R のパッケージ

SQL Managed Instance では、外部ランタイム (Java など) に依存するか、インストールまたは使用のための OS API へのアクセスが必要なパッケージはサポートされません。

Python および R パッケージの管理の詳細については、以下を参照してください。

- [Python パッケージ情報の取得](https://docs.microsoft.com/sql/machine-learning/package-management/python-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)
- [R パッケージ情報の取得](https://docs.microsoft.com/sql/machine-learning/package-management/r-package-information?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)

## <a name="resource-governance"></a>リソース管理

SQL Managed Instance では、[Resource Governor](/sql/relational-databases/resource-governor/resource-governor?view=azuresqldb-mi-current&preserve-view=true) よって R リソースを制限することはできません。また、外部リソース プールはサポートされていません。

既定では、機能拡張が有効になっている場合、R リソースは使用可能な SQL Managed Instance リソースの最大 20% に設定されます。 この既定の割合を変更するには、[https://azure.microsoft.com/support/create-ticket/](https://azure.microsoft.com/support/create-ticket/) で Azure サポート チケットを作成します。

次の SQL コマンドを使用して拡張機能が有効になります (SQL Managed Instance が再起動され、数秒間使用できなくなります)。

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

拡張機能を無効にし、メモリと CPU リソースの 100% を SQL Server に戻すには、次のコマンドを使用します。

```sql
sp_configure 'external scripts enabled', 0;
RECONFIGURE WITH OVERRIDE;
```

SQL Managed Instance で使用できるリソースの合計は、選択したサービス レベルによって異なります。 詳細については、「[Azure SQL Database purchasing models](/azure/sql-database/sql-database-service-tiers)」 (Azure SQL Database の購入モデル) を参照してください。

### <a name="insufficient-memory-error"></a>メモリ不足エラー

メモリ使用量は、R スクリプトでの使用量と、実行されている並列クエリの数によって決まります。 R に使用できるメモリが不足している場合は、エラー メッセージが表示されます。 一般的なエラー メッセージは次のとおりです。

- `Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime`
- `'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"`
- `An external script error occurred: Error: cannot allocate vector of size.`

これらのエラーのいずれかが発生した場合は、データベースをより高いサービス レベルにスケーリングすることで解決できます。

## <a name="sql-managed-instance-pools"></a>SQL Managed Instance のプール

現在、Machine Learning Services は [Azure SQL Managed Instance プール (プレビュー)](instance-pools-overview.md)ではサポートされていません。

## <a name="next-steps"></a>次のステップ

- 詳細については、「[Azure SQL Managed Instance の Machine Learning Services](machine-learning-services-overview.md)」を参照してください。
- Machine Learning Services で Python を使用する方法については、[Python スクリプトの実行](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。
- Machine Learning Services で R を使用する方法については、[R スクリプトの実行](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)に関するページを参照してください。
