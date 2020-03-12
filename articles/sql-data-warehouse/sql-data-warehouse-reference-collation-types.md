---
title: データ ウェアハウスの照合順序の種類
description: Azure Synapse Analytics SQL プールでサポートされている照合順序の種類。
services: sql-data-warehouse
author: antvgski
manager: igorstan
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 67627e4157c85853cf05dd6b24ced968a9654e62
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198441"
---
# <a name="database-collation-support-for-azure-synapse-analytics-sql-pool"></a>Azure Synapse Analytics SQL プールのデータベースの照合順序のサポート

新しい Azure Synapse SQL プール データベースを作成するときに、Azure portal から既定のデータベースの照合順序を変更できます。 この機能により、サポートされる 3800 個のデータベースの照合順序のいずれかを使用して、新しいデータベースをさらに簡単に作成できるようになりました。 

照合順序では、文字ベースのデータ型に対するロケール、コード ページ、並べ替え順序、および大文字と小文字の区別の規則を指定します。 選択されると、照合順序情報を必要とするすべての列と式が、選択された照合順序をデータベース設定から継承します。 既定の継承は、文字ベースのデータ型に対する別の照合順序を明示的に宣言することによって上書きできます。

## <a name="changing-collation"></a>照合順序の変更
既定の照合順序を変更するには、プロビジョニング エクスペリエンスで [Collation] フィールドを更新します。

たとえば、既定の照合順序を大文字と小文字が区別されるように変更する場合は、単純に [Collation] の名前を SQL_Latin1_General_CP1_CI_AS から SQL_Latin1_General_CP1_CS_AS に変更します。 

## <a name="list-of-unsupported-collation-types"></a>サポートされていない照合順序の種類の一覧
*   Japanese_Bushu_Kakusu_140_BIN
*   Japanese_Bushu_Kakusu_140_BIN2
*   Japanese_Bushu_Kakusu_140_CI_AI_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*   Japanese_Bushu_Kakusu_140_CI_AI
*   Japanese_Bushu_Kakusu_140_CI_AI_WS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS
*   Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS
*   Japanese_Bushu_Kakusu_140_CI_AS_WS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS
*   Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AI
*   Japanese_Bushu_Kakusu_140_CS_AI_WS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS
*   Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS
*   Japanese_Bushu_Kakusu_140_CS_AS_WS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS
*   Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*   Japanese_XJIS_140_BIN
*   Japanese_XJIS_140_BIN2
*   Japanese_XJIS_140_CI_AI_VSS
*   Japanese_XJIS_140_CI_AI_WS_VSS
*   Japanese_XJIS_140_CI_AI_KS_VSS
*   Japanese_XJIS_140_CI_AI_KS_WS_VSS
*   Japanese_XJIS_140_CI_AS_VSS
*   Japanese_XJIS_140_CI_AS_WS_VSS
*   Japanese_XJIS_140_CI_AS_KS_VSS
*   Japanese_XJIS_140_CI_AS_KS_WS_VSS
*   Japanese_XJIS_140_CS_AI_VSS
*   Japanese_XJIS_140_CS_AI_WS_VSS
*   Japanese_XJIS_140_CS_AI_KS_VSS
*   Japanese_XJIS_140_CS_AI_KS_WS_VSS
*   Japanese_XJIS_140_CS_AS_VSS
*   Japanese_XJIS_140_CS_AS_WS_VSS
*   Japanese_XJIS_140_CS_AS_KS_VSS
*   Japanese_XJIS_140_CS_AS_KS_WS_VSS
*   Japanese_XJIS_140_CI_AI
*   Japanese_XJIS_140_CI_AI_WS
*   Japanese_XJIS_140_CI_AI_KS
*   Japanese_XJIS_140_CI_AI_KS_WS
*   Japanese_XJIS_140_CI_AS
*   Japanese_XJIS_140_CI_AS_WS
*   Japanese_XJIS_140_CI_AS_KS
*   Japanese_XJIS_140_CI_AS_KS_WS
*   Japanese_XJIS_140_CS_AI
*   Japanese_XJIS_140_CS_AI_WS
*   Japanese_XJIS_140_CS_AI_KS
*   Japanese_XJIS_140_CS_AI_KS_WS
*   Japanese_XJIS_140_CS_AS
*   Japanese_XJIS_140_CS_AS_WS
*   Japanese_XJIS_140_CS_AS_KS
*   Japanese_XJIS_140_CS_AS_KS_WS
*   SQL_EBCDIC1141_CP1_CS_AS
*   SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>現在の照合順序の確認

データベースの現在の照合順序を確認するには、次の T-SQL スニペットを実行できます。

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```

プロパティのパラメーターとして "Collation" が渡された場合、DatabasePropertyEx 関数から現在データベースで指定されている照合順序が返されます。 詳細については、「[DatabasePropertyEx](/sql/t-sql/functions/databasepropertyex-transact-sql)」を参照してください。

