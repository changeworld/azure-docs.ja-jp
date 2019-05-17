---
title: Azure Cost Management で AWS のコストと使用状況レポートの統合を設定して構成する
description: この記事では、Azure Cost Management で AWS のコストと使用状況レポートの統合を設定して構成する方法を説明します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: ormaoz
ms.custom: ''
ms.openlocfilehash: e87e95ec9e4e20ee4785c2b1f448a7ca5f442b8a
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409207"
---
# <a name="set-up-and-configure-aws-cost-and-usage-report-integration"></a>AWS のコストと使用状況レポートの統合を設定して構成する

Amazon Web サービスのコストと使用状況レポートの統合では、Azure Cost Management で AWS 支出を監視して制御することができます。 統合により、Azure portal の単一の場所で、Azure と AWS の両方での支出を監視して制御できるようになります。 この記事では、統合を設定し、Cost Management 機能を使用して、コストを分析し、予算を確認できるように構成する方法を説明します。

Cost Management では、レポート定義を取得し、レポートの GZIP CSV ファイルをダウンロードするために AWS アクセス資格情報を使用して、S3 バケットに格納されている AWS のコストと使用状況レポートを処理します。

## <a name="create-a-cost-and-usage-report-in-aws"></a>AWS でコストと使用状況レポートを作成する

コストと使用状況レポートの使用は、AWS のコストを収集して処理するための AWS で推奨される方法です。 詳細については、「[AWS Cost and Usage Report](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/billing-reports-costusage.html)」 (AWS のコストと使用状況レポート) というドキュメント記事を参照してください。

AWS の Billing and Cost Management コンソールの **[レポート]** ページを使用して、以下の手順でコストと使用状況レポートを作成します。

1. https://console.aws.amazon.com/billing で、AWS Management コンソールにサインインし、[Billing and Cost Management]\(課金とコスト管理\) コンソールを開きます。
2. ナビゲーション ウィンドウで **[レポート]** をクリックします。
3. **[レポートの作成]** をクリックします。
4. **[レポート名]** には、ご利用のレポートの名前を入力します。
5. **[時間単位]** では、**[毎時間]** を選択します。
6. **[含める]** では、レポート内の各リソースの ID を追加し、**[リソース ID]** を選択します。
7. **[Enable support for]\(サポートを有効にする対象\)** では、選択する必要はありません。
8. **[Data refresh settings]\(データ更新の設定\)** では、**[Automatically refresh your Cost &amp; Usage Report when charges are detected for previous months with closed bills]\(請求書がクローズされた前の月の料金が検出された場合に、のコストと使用状況レポートを自動的に更新する\)** を選択します。
9. **[次へ]** をクリックします。
10. **[Amazon S3 bucket]\(Amazon S3 バケット\)** では、レポートの配信先となる Amazon S3 バケットの名前を入力し、**[確認]** をクリックします。 バケットの適切なアクセス許可を有効にする必要があります。 バケットへのアクセス許可の追加について詳しくは、「[バケットとオブジェクトのアクセス許可の設定](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/set-permissions.html)」を参照してください。
11. **[Report path prefix]\(レポート パス プレフィックス\)** では、レポートの名前に追加するレポート パス プレフィックスを入力します。
12. **[圧縮]** では、**[GZIP]** を選択します。
13. **[次へ]** をクリックします。
14. レポートの設定を確認した後、**[Review and Complete]\(確認して完了\)** をクリックします。
    **レポート名**をメモしておきます。 これは後の手順で使用します。

AWS で Amazon S3 バケットへのレポートの配信が開始されるまで、最大で 24 時間かかる場合があります。 配信が開始された後、AWS では、少なくとも 1 日 1 回は AWS のコストと使用状況レポート ファイルが更新されます。 配信の開始を待たずに、AWS 環境の構成を続行することができます。

## <a name="create-a-role-and-policy-in-aws"></a>AWS でロールとポリシーを作成する

Azure Cost Management では、1 日数回、コストと使用状況レポートが配置されている S3 バケットにアクセスします。 Cost Management では、新しいデータを確認するために資格情報へのアクセスが必要です。 Cost Management によるアクセスを許可するには、AWS でロールとポリシーを作成します。

Azure Cost Management で AWS アカウントへのロールベースのアクセスを有効にするために、AWS コンソールでロールが作成されます。 AWS コンソールからの_ロール ARN_ と_外部 ID_ が必要です。 後で、Azure Cost Management の [AWS コネクタの作成] ページでこれらを使用します。

[新しいロールの作成] ウィザードを使用します。

1. AWS コンソールにサインインし、**[サービス]** を選択します。
2. サービスの一覧で **[IAM]** を選択します。
3. **[ロール]** を選択してから **[ロールの作成]** をクリックします。
4. 次のページで、**[Another AWS account]\(別の AWS アカウント\)** を選択します。
5. **[アカウント ID]** に、「_432263259397_」と入力します。
6. **[オプション]** で、**[Require external ID (Best practice when a third party will assume this role)]\(外部 ID が必要 (サード パーティでこのロールを想定する場合のベスト プラクティス)\)** を選択します。
7. **[外部 ID]** に、外部 ID を入力します。 外部 ID は、AWS ロールと Azure Cost Management の間で共有されるパスコードです。 Cost Management の [新しいコネクタ] ページでも同じ外部 ID が使用されます。 たとえば、外部 ID は _Companyname1234567890123_ のようになります。
    **[MFA の要求]** の選択は変更しないでください。 クリアされたままにしておく必要があります。
8. **[次へ: Permissions]\(次へ: アクセス許可\)** をクリックします。
9. **[Create policy]\(ポリシーの作成\)** をクリックします。 新しいブラウザー タブが開きます。ここで新しいポリシーを作成します。
10. **[サービスの選択]** をクリックします。

コストと使用状況レポートのアクセス許可を構成します。

1. **コストと使用状況レポート**を入力します。
2. **[アクセス レベル]**、**[読み取り]** > **[DescribeReportDefinitions]** の順に選択します。 これにより、Cost Management では、定義されている CUR レポートを読み取り、レポート定義の前提条件が一致するかどうかを判断できます。
3. **[Add additional permissions]\(さらにアクセス許可を追加\)** をクリックします。

S3 バケットとオブジェクト アクセス許可を構成します。

1. **[サービスの選択]** をクリックします。
2. 「_S3_」と入力します。
3. **[アクセス レベル]**、**[一覧]** > **[ListBucket]** の順に選択します。 このアクションでは、S3 バケット内のオブジェクトの一覧を取得します。
4. **[アクセス レベル]**、**[読み取り]** > **[GetObject]** の順に選択します。 このアクションでは、課金ファイルのダウンロードを許可します。
5. **[リソース]** を選択します。
6. **[bucket – Add ARN]\(バケット – ARN の追加\)** を選択します。
7. **[バケット名]** に、CUR ファイルを格納するために使用されるバケットを入力します。
8. **[object – Add ARN]\(オブジェクト - ARN の追加\)** を選択します。
9. **[バケット名]** に、CUR ファイルを格納するために使用されるバケットを入力します。
10. **[オブジェクト名]** で、**[任意]** を選択します。
11. **[Add additional permissions]\(さらにアクセス許可を追加\)** をクリックします。

Cost エクスプローラーのアクセス許可を構成します。

1. **[サービスの選択]** をクリックします。
2. 「_Cost エクスプローラー サービス_」と入力します。
3. **[All Cost Explorer Service actions (ce:\*)]\(すべての Cost エクスプローラー サービス アクション (ce:*)\)** を選択します。 このアクションでは、コレクションが正しいことを検証します。
4. **[Add additional permissions]\(さらにアクセス許可を追加\)** をクリックします。

組織のアクセス許可を追加する:

1. **組織**を入力します。
2. **[アクセス レベル]、[一覧]** > **[ListAccounts]** の順に選択します。 このアクションでは、アカウントの名前を取得します。
3. **[ポリシーの確認]** に、新しいポリシーの名前を入力します。 正しい情報を入力したことを確認してから、**[ポリシーの作成]** をクリックします。
4. 前のタブに戻り、ブラウザーの Web ページを更新します。 検索バーで、新しいポリシーを検索します。
5. **[Next:Review]\(次へ:確認\)** を選択します。
6. 新しいロールの名前を入力します。 正しい情報を入力したことを確認してから、**[ロールの作成]** をクリックします。
    ロールの作成時に前の手順で使用された**ロール ARN** と**外部 ID** をメモしておいてください。 これらは後で Azure Cost Management コネクタを設定するときに使用します。

ポリシー JSON は次の例のようになるはずです。 _bucketname_ を S3 バケットの名前に置き換えます。

```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
"organizations:ListAccounts",
            "ce:*",
            "cur:DescribeReportDefinitions"
            ],
            "Resource": "*"
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": [
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::bucketname",
                "arn:aws:s3:::bucketname/*"
            ]
        }
    ]
}
```

## <a name="set-up-a-new-aws-connector-in-azure"></a>Azure で新しい AWS コネクタを設定する

新しい AWS コネクタを作成し、AWS コストの監視を開始するには、次の情報を使用します。

1. https://portal.azure.com で Azure portal にサインインします。
2. **[コストの管理と請求]** > **[コスト管理]** の順に移動します。
3. **[設定]** で、**[クラウド コネクタ (プレビュー)]** をクリックします。  
    ![クラウド コネクタ (プレビュー設定) を示す例](./media/aws-integration-setup-configure/cloud-connectors-preview01.png)。
4. ページの上部にある **[+ 追加]** をクリックし、新しいコネクタを作成します。
5. [AWS コネクタの作成] ページで、**表示名**を入力してコネクタに名前を付けます。  
    ![[AWS コネクタの作成] ページの例](./media/aws-integration-setup-configure/create-aws-connector01.png)
6. 必要に応じて、既定の管理グループを選択します。 検出されたすべてのリンクされたアカウントが格納されます。 これは後で設定できます。
7. **[課金]** セクションで、プレビューの有効期限が切れたときに継続的に操作できるようにする場合は、**[一般提供時に 1% を自動請求する]** を選択します。 自動オプションを選択した場合は、課金**サブスクリプション**を選択する必要があります。
8. **ロール ARN** を入力します。 これは、AWS でのロールの設定時に使用した値です。
9. **外部 ID** を入力します。 これは、AWS でのロールの設定時に使用した値です。
10. AWS で作成した**レポート名**を入力します。
11. **[次へ]** をクリックしてから、**[作成]** をクリックします。

新しい AWS スコープ、AWS 統合アカウントと AWS のリンクされたアカウント、およびそれらのコスト データが表示されるまで、数時間かかる場合があります。

コネクタを作成した後、コネクタにアクセス制御を割り当てることをお勧めします。 ユーザーには、新しく検出されたスコープへのアクセス許可が割り当てられます。AWS 統合アカウントと AWS のリンクされたアカウントです。 コネクタを作成するユーザーは、コネクタ、統合アカウント、およびすべてのリンクされたアカウントの所有者です。

検出後にユーザーにコネクタのアクセス許可を割り当てても、既存の AWS スコープにアクセス許可は割り当てられません。 代わりに、新しいリンクされたアカウントにのみ、アクセス許可が割り当てられます。

## <a name="additional-steps"></a>追加の手順

- まだ設定していない場合は、[管理グループを設定](../governance/management-groups/index.md#initial-setup-of-management-groups)します。
- スコープ ピッカーに新しいスコープが追加されたことを確認します。 必ず、**[更新]** をクリックして最新データを表示してください。
- [クラウド コネクタ] ページで、コネクタを選択し、**[請求先アカウントに移動する]** をクリックして、管理グループにリンクされたアカウントを割り当てます。

## <a name="manage-cloud-connectors"></a>クラウド コネクタを管理する

[クラウド コネクタ] ページでコネクタを選択した場合は、次の操作を行うことができます。

- **[請求先アカウントに移動する]** をクリックして、AWS 統合アカウント情報を表示します。
- **[アクセスの制御]** をクリックして、コネクタのロール割り当てを管理します。
- **[編集]** をクリックして、コネクタを更新します。 AWS アカウント番号を変更することはできません。これは、[ロール ARN] に表示されます。 ただし、新しいコネクタを作成することはできます。
- **[確認]** をクリックして、確認テストを再実行し、Cost Management でコネクタ設定を使用して、データを収集できることを確認します。

![作成された AWS コネクタの一覧を示す例](./media/aws-integration-setup-configure/list-aws-connectors.png)

## <a name="role-of-azure-management-groups"></a>Azure 管理グループのロール

クロスクラウド プロバイダーの情報を表示する単一の場所を作成するには、Azure サブスクリプションと AWS のリンクされたアカウントを同じ管理グループに配置する必要があります。 管理グループを使用して Azure 環境をまだ構成していない場合は、「[管理グループの初期セットアップ](../governance/management-groups/index.md#initial-setup-of-management-groups)」を参照してください。

コストを分ける場合は、AWS のリンクされたアカウントのみを保持する管理グループを作成できます。

## <a name="aws-consolidated-account"></a>AWS 統合アカウント

AWS 統合アカウントは、複数の AWS アカウントの請求と支払いを統合するために使用されます。 AWS 統合アカウントは、AWS のリンクされたアカウントとしても機能します。

![AWS 統合アカウントの詳細の例](./media/aws-integration-setup-configure/aws-consolidated-account01.png)

ページからは、次のことを行うことができます。

- **[更新]** をクリックし、管理グループとの AWS のリンクされたアカウントの関連付けを一括更新します。
- **[アクセスの制御]** をクリックして、スコープのロール割り当てを設定します。

### <a name="aws-consolidated-account-permissions"></a>AWS 統合アカウントのアクセス許可

既定では、AWS 統合アカウントのアクセス許可は、AWS コネクタのアクセス許可に基づいて、作成時に設定されます。 コネクタの作成者は所有者です。

AWS 統合アカウントの [アクセス レベル] ページを使用して、アクセス レベルを管理します。 しかし、AWS 統合アカウントへのアクセス許可は、AWS のリンクされたアカウントによって継承されません。

## <a name="aws-linked-account"></a>AWS のリンクされたアカウント

AWS のリンクされたアカウントは、AWS リソースが作成され、管理される場所です。 リンクされたアカウントは、セキュリティ境界としても機能します。

このページからは、次のことを行うことができます。

- **[更新]** をクリックし、管理グループとの AWS のリンクされたアカウントの関連付けを更新します。
- **[アクセスの制御]** をクリックして、スコープのロール割り当てを設定します。

![AWS のリンクされたアカウント ページの例](./media/aws-integration-setup-configure/aws-linked-account01.png)

### <a name="aws-linked-account-permissions"></a>AWS のリンクされたアカウントのアクセス許可

既定では、AWS のリンクされたアカウントのアクセス許可は、AWS コネクタのアクセス許可に基づいて、作成時に設定されます。 コネクタの作成者は所有者です。 AWS のリンクされたアカウントの [アクセス レベル] ページを使用して、アクセス レベルを管理します。 AWS 統合アカウントのアクセス許可は、AWS のリンクされたアカウントによって継承されません。

AWS のリンクされたアカウントでは常に、所属する管理グループからのアクセス許可が継承されます。

## <a name="next-steps"></a>次の手順

- これで、AWS のコストと使用状況レポートの統合を設定して構成したので、[AWS のコストと使用状況の管理](aws-integration-manage.md)に進みます。
- コスト分析に慣れていない場合は、[コスト分析を使用するコストの調査と分析](quick-acm-cost-analysis.md)に関するクイックスタートを参照してください。
- Azure の予算に慣れている場合は、[Azure の予算の作成と管理](tutorial-acm-create-budgets.md)に関するチュートリアルを参照してください。
