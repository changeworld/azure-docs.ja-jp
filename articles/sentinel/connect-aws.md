---
title: AWS CloudTrail を Microsoft Sentinel に接続する | Microsoft Docs
description: AWS コネクタを使用して Microsoft Sentinel へのアクセスを AWS リソース ログに委任することで、AWS CloudTrail と Microsoft Sentinel の間の信頼関係が確立されます。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: eff347499e045c35ecd2e08185e09fede68a1a42
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132707331"
---
# <a name="connect-aws-cloudtrail-to-microsoft-sentinel"></a>AWS CloudTrail を Microsoft Sentinel に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

AWS コネクタを使用して、AWS CloudTrail 管理イベントを Microsoft Sentinel にストリーミングします。 この接続プロセスにより、Microsoft Sentinel へのアクセスが AWS リソース ログに委任され、AWS CloudTrail と Microsoft Sentinel の間の信頼関係が確立されます。 これを行うには、Microsoft Sentinel にアクセス許可を付与するロールを AWS で作成し、AWS ログにアクセスできるようにします。

> [!NOTE]
> AWS CloudTrail には、LookupEvents API に[組み込まれている制限](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html)があります。 アカウントごとに 2 つ以上のトランザクション (TPS) が許可され、各クエリでは最大 50 のレコードを返すことができます。 したがって、単一のテナントで、1 つのリージョン内で秒あたり 100 を超えるレコードが継続的に生成される場合、データ インジェストでバックログと遅延が発生します。
> 現在、Microsoft Sentinel に接続できるのは AWS Commercial CloudTrail のみであり、AWS GovCloud CloudTrail は接続できません。

## <a name="prerequisites"></a>前提条件

Microsoft Sentinel ワークスペースへの書き込みアクセス許可が必要です。

> [!NOTE]
> Microsoft Sentinel によって、すべてのリージョンから CloudTrail 管理イベントが収集されます。 あるリージョンから別のリージョンにイベントをストリーミングしないことをお勧めします。

## <a name="connect-aws"></a>AWS の接続 


1. Microsoft Sentinel で、 **[データ コネクタ]** を選択し、テーブルで **[アマゾン ウェブ サービス]** 行を選択し、右側の [AWS] ペインで **[コネクタ ページを開く]** を選択します。

1. 次の手順を使用して、 **[Configuration]\(構成\)** の下の指示に従います。
 
1.  アマゾン ウェブ サービス コンソールの **[Security, Identity & Compliance]\(セキュリティ、ID、およびコンプライアンス\)** で **[IAM]** を選択します。

    ![AWS1](./media/connect-aws/aws-1.png)

1.  **Roles\(ロール\)** を選択し、**Create role\(ロールの作成\)** を選択します。

    ![AWS2](./media/connect-aws/aws-2.png)

1.  **[別の AWS アカウント]** を選択します。 **[アカウント ID]** フィールドに、**Microsoft アカウント ID** (**123412341234**) を入力します。この ID は、Microsoft Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS3](./media/connect-aws/aws-3.png)

1.  必ず **[Require External ID]\(外部 ID が必要\)** を選択し、外部 ID (ワークスペース ID) を入力します。この ID は、Microsoft Sentinel ポータルの AWS コネクタ ページで確認できます。

    ![AWS4](./media/connect-aws/aws-4.png)

1.  **[Attach permissions policy]\(アクセス許可ポリシーのアタッチ\)** で **[AWSCloudTrailReadOnlyAccess]** を選択します。

    ![AWS5](./media/connect-aws/aws-5.png)

1.  (省略可能) タグを入力します。

    ![AWS6](./media/connect-aws/aws-6.png)

1.  次に、**ロール名** を入力し、 **[ロールの作成]** を選択します。

    ![AWS7](./media/connect-aws/aws-7.png)

1.  ロールの一覧で、作成したロールを選択します。

    ![AWS8](./media/connect-aws/aws-8.png)

1.  **Role ARN** をコピーします。 Microsoft Sentinel ポータルのアマゾン ウェブ サービス コネクタ画面の **[追加するロール]** フィールドにこれを貼り付け、 **[追加]** を選択します。

    ![AWS9](./media/connect-aws/aws-9.png)

1. Log Analytics で AWS イベントに関連するスキーマを使用するには、**AWSCloudTrail** を検索します。

    > [!IMPORTANT]
    > 2020 年 12 月 1 日以降では、**AwsRequestId** フィールドは **AwsRequestId_** フィールドに置き換えられています (アンダースコアが追加されていることに注意してください)。 以前の **AwsRequestId** フィールドのデータは、お客様が指定したデータ保有期間が終了するまで保持されます。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、AWS CloudTrail を Microsoft Sentinel に接続する方法について学習しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
