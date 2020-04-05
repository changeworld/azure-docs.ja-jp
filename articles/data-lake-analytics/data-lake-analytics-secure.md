---
title: 複数ユーザーのための Azure Data Lake Analytics のセキュリティ保護
description: Azure Data Lake Analytics でジョブの実行に複数のユーザーを構成する方法を説明します。
ms.service: data-lake-analytics
services: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 9fbc94259d6fdfb6758204efd6e6f0a346dc58da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "60813372"
---
# <a name="configure-user-access-to-job-information-to-job-information-in-azure-data-lake-analytics"></a>Azure Data Lake Analytics のジョブ情報へのユーザー アクセスを構成します。 

Azure Data Lake Analytics では、複数のユーザー アカウントまたはサービス プリンシパルを使用できます。 

それらの同じユーザーに詳細なジョブ情報を表示するには、ユーザーはジョブ フォルダーの内容を読み取ることができる必要があります。 ジョブ フォルダーは `/system/` ディレクトリにあります。 

必要なアクセス許可が構成されていない場合、ユーザーにエラーが表示されます。 `Graph data not available - You don't have permissions to access the graph data.` 

## <a name="configure-user-access-to-job-information"></a>ジョブ情報へのユーザー アクセスを構成します

**ユーザー追加ウィザード**を使用して、フォルダーに ACL を構成することができます。 詳細については、「[新しいユーザーの追加](data-lake-analytics-manage-use-portal.md#add-a-new-user)」を参照してください。

さらにきめ細かく制御する必要がある場合、またはアクセス許可のスクリプトを作成する必要がある場合、次のようにしてフォルダーをセキュリティ保護します。

1. ルート フォルダーに (アクセス ACL を介して) **[実行]** アクセス許可を付与します。
   - /
   
2. ジョブ フォルダーを含むフォルダーに (アクセス ACL および既定の ACL を介して) **[実行]** および **[読み取り]** アクセス許可を付与します 。 たとえば、2018 年 5 月 25 日に実行された特定のジョブについて、次のフォルダーにアクセスする必要があります。
   - /system
   - /system/jobservice
   - /system/jobservice/jobs
   - /system/jobservice/jobs/Usql
   - /system/jobservice/jobs/Usql/2018
   - /system/jobservice/jobs/Usql/2018/05
   - /system/jobservice/jobs/Usql/2018/05/25
   - /system/jobservice/jobs/Usql/2018/05/25/11
   - /system/jobservice/jobs/Usql/2018/05/25/11/01
   - /system/jobservice/jobs/Usql/2018/05/25/11/01/b074bd7a-1448-d879-9d75-f562b101bd3d

## <a name="next-steps"></a>次のステップ
[新しいユーザーの追加](data-lake-analytics-manage-use-portal.md#add-a-new-user)
