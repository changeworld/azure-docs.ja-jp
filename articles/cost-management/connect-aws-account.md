---
title: アマゾン ウェブ サービス アカウントを Azure Cost Management に接続する | Microsoft Docs
description: アマゾン ウェブ サービス アカウントを接続して、Cost Management のレポートにコストや使用状況のデータを表示します。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: c2c7ea043d2da41442829321ac663325f30ff066
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297330"
---
# <a name="connect-an-amazon-web-services-account"></a>アマゾン ウェブ サービス アカウントの接続

アマゾン ウェブ サービス (AWS) アカウントを Azure Cost Management に接続するためのオプションは 2 つあります。 IAM ロールと接続することも、読み取り専用 IAM ユーザー アカウントと接続することもできます。 推奨されているのは IAM ロールです。これは、信頼できるエンティティへのアクセス許可が定義されているアクセスを委任できるためです。 IAM ロールの場合、長期的なアクセス キーを共有する必要はありません。 AWS アカウントを Cost Management に接続すると、Cost Management レポートにコストや使用状況のデータを表示できるようになります。 このドキュメントでは、両方のオプションについて説明します。

AWS IAM の ID の詳細については、「[ID (ユーザー、グループ、ロール)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)」を参照してください。

また、AWS 課金の詳細レポートを有効にして、AWS Simple Storage Service (S3) バケットに情報を保管します。 詳細な課金レポートには、時間単位の、タグ付きの課金請求、リソース情報が含まれます。 レポートを保管すると、Cost Management で、そうした情報をバケットから取得し、レポートに表示できます。


## <a name="aws-role-based-access"></a>AWS のロールベースのアクセス

以下のセクションでは、Cost Management へのアクセスを提供する読み取り専用の IAM ロールについて説明します。

### <a name="get-your-cost-management-account-external-id"></a>Cost Management アカウントの外部 ID を取得する

最初に、Azure Cost Management ポータルから一意の接続パスフレーズを取得します。 これは、**外部 ID** として AWS で使用されます。

1. Azure portal から Cloudyn ポータルを開くか、[https://azure.cloudyn.com](https://azure.cloudyn.com) に移動してサインインします。
2. 歯車記号をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Add AWS Account]\(AWS アカウントの追加\)** ダイアログで、**[External ID]\(外部 ID\)** をコピーし、次のセクションにある AWS ロール作成の手順のためにこの値を保存します。 外部 ID はアカウントに一意です。 次の画像では、_Contoso_ に続く数字がサンプルの外部 ID です。 ID は異なります。  
    ![外部 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS の読み取り専用のロールベースのアクセスを追加する

1. https://console.aws.amazon.com/iam/home から AWS コンソールにサインインし、**[Roles]\(ロール\)** を選択します。
2. **[Create Role]\(ロールの作成\)** をクリックし、**[Another AWS account]\(別の AWS アカウント\)** を選択します。
3. **[Account ID]\(アカウント ID\)** に `432263259397` を貼り付けます。 このアカウント ID は、AWS によって Cloudyn サービスに割り当てられた Cost Management データ コレクター アカウントです。 表示されたアカウント ID を使用します。
4. **[オプション]** の横にある **[Require external ID]\(外部 ID が必要\)** を選択します。 前に Cost Management で **[External ID]\(外部 ID\)** フィールドからコピーした一意の値を貼り付けます。 **[Next: Permissions]\(次へ: アクセス許可\)** をクリックします。  
    ![ロールの作成](./media/connect-aws-account/create-role01.png)
5. **[Attach permissions policies]\(アクセス許可ポリシーのアタッチ\)**, で、**[Policy type]\(ポリシー タイプ\)** フィルターの検索ボックスに「`ReadOnlyAccess`」と入力し、**[ReadOnlyAccess]** を選択して、**[Next: Review]\(次へ: 確認\)** をクリックします。  
    ![読み取り専用アクセス](./media/connect-aws-account/readonlyaccess.png)
6. 確認ページで、選択内容が正しいことを確認し、**ロール名**を入力します。 たとえば、*Azure-Cost-Mgt* とします。**ロールの説明**を入力します。 たとえば、_Role assignment for Azure Cost Management_ として、**[Create role]\(ロールの作成\)** をクリックします。
7. **[Roles]\(ロール\)** の一覧で、作成したロールをクリックし、[Summary]\(概要\) ページから **[Role ARN]\(ロール ARN\)** の値をコピーします。 ロール ARN (Amazon Resource Name) の値は、後で Azure Cost Management で構成を登録するときに使用します。  
    ![ロール ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Cost Management で AWS IAM ロール アクセスを構成する

1. Azure portal から Cloudyn ポータルを開くか、https://azure.cloudyn.com/ に移動してサインインします。
2. 歯車記号をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Account Name]\(アカウント名\)** に、アカウントの名前を入力します。
5. **[Access Type]\(アクセスの種類\)** の横にある **[IAM Role]\(IAM ロール\)** を選択します。
6. **[Role ARN]\(ロール ARN\)** フィールドに、以前コピーした値を貼り付け、**[Save]\(保存\)** をクリックします。  
    ![[Add AWS Account]\(新しいアカウントの追加\)](./media/connect-aws-account/add-aws-account-box.png)


AWS アカウントがアカウントの一覧に表示されます。 表示された**所有者 ID** がロール ARN の値と一致しています。 **[Account Status]\(アカウントの状態\)** に緑色のチェック マーク記号が表示されている必要があります。この記号は、Cost Management が AWS アカウントにアクセスできることを示します。 AWS 課金の詳細が有効になるまで、[Consolidation Status]\(統合の状態\)は **[Standalone]\(スタンドアロン\)** と表示されます。

![AWS アカウントの状態](./media/connect-aws-account/aws-account-status01.png)

Cost Management は、データの収集とレポートの作成を開始します。 次に、[AWS 課金の詳細を有効に](#enable-detailed-aws-billing)します。


## <a name="aws-user-based-access"></a>AWS のユーザーベースのアクセス

以下のセクションでは、Cost Management へのアクセスを提供する読み取り専用のユーザーについて説明します。

### <a name="add-aws-read-only-user-based-access"></a>AWS の読み取り専用のユーザーベースのアクセスを追加する

1. https://console.aws.amazon.com/iam/home から AWS コンソールにサインインし、**[ユーザー]** を選択します。
2. **[ユーザーの追加]** をクリックします。
3. **[User name]\(ユーザー名\)** フィールドに、ユーザー名を入力します。
4. **[Access type]\(アクセスの種類\)** で、**[Programmatic access]\(プログラムによるアクセス\)** を選択し、**[Next: Permissions]\(次へ: アクセス許可\)** をクリックします。  
    ![ユーザーの追加](./media/connect-aws-account/add-user01.png)
5. アクセス許可については、**[Attach existing policies directly]\(既存のポリシーを直接アタッチする\)** を選択します。
6. **[Attach permissions policies]\(アクセス許可ポリシーのアタッチ\)**, で、**[Policy type]\(ポリシー タイプ\)** フィルターの検索ボックスに「`ReadOnlyAccess`」と入力し、**[ReadOnlyAccess]** を選択して、**[Next: Review]\(次へ: 確認\)** をクリックします。  
    ![ユーザーのアクセス許可の設定](./media/connect-aws-account/set-permission-for-user.png)
7. 確認ページで、選択内容が正しいことを確認し、**[Create user]\(ユーザーの作成\)** をクリックします。
8. 完了ページに、アクセス キー ID とシークレット アクセス キーが表示されます。 この情報を使用して、Cost Management で登録を構成します。
9. **[Download .csv]\(.csv のダウンロード\)** をクリックして credentials.csv ファイルを安全な場所に保存します。  
    ![資格情報のダウンロード](./media/connect-aws-account/download-csv.png)

### <a name="configure-aws-iam-user-based-access-in-cost-management"></a>Cost Management で AWS IAM ユーザーベースのアクセスを構成する

1. Azure portal から Cloudyn ポータルを開くか、https://azure.cloudyn.com/ に移動してサインインします。
2. 歯車記号をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Account Name]\(アカウント名\)** では、アカウント名を入力します。
5. **[Access Type]\(アクセスの種類\)** の横にある **[IAM User]\(IAM ユーザー\)** を選択します。
6. **[Access Key]\(アクセス キー\)** に、credentials.csv ファイルの**アクセス キー ID** の値を貼り付けます。
7. **[Secret Key]\(シークレット キー\)** に、credentials.csv ファイルの**シークレット アクセス キー**の値を貼り付け、**[Save]\(保存\)** をクリックします。  

AWS アカウントがアカウントの一覧に表示されます。 **[Account Status]\(アカウントの状態\)** には、緑色のチェック マーク記号が付きます。

Cost Management は、データの収集とレポートの作成を開始します。 次に、[AWS 課金の詳細を有効に](#enable-detailed-aws-billing)します。

## <a name="enable-detailed-aws-billing"></a>AWS 課金の詳細を有効にする

AWS ロール ARN を取得するには、次の手順を実行します。 ロール ARN を使用して、請求バケットに読み取りアクセス許可を与えます。

1. https://console.aws.amazon.com から AWS コンソールにサインインし、**[Services]\(サービス\)** を選択します。
2. [Service Search] に「*IAM*」と入力し、そのオプションを選択します。
3. 左手のメニューから **[Roles]\(ロール\)** を選択します。
4. ロールの一覧で、Cloudyn アクセス用に作成したロールを選択します。
5. [Roles Summary]\(ロールの概要\) ページで、クリックして**ロール ARN** をコピーします。 ロール ARN は、後続の手順のために手元に用意しておいてください。

### <a name="create-an-s3-bucket"></a>S3 バケットの作成

課金の詳細情報を格納する S3 バケットを作成します。

1. https://console.aws.amazon.com から AWS コンソールにサインインし、**[Services]\(サービス\)** を選択します。
2. [Service Search] に「*S3*」と入力し、**[S3]** を選択します。
3. [Amazon S3] ページで、**[Create bucket]\(バケットの作成\)** をクリックします。
4. バケットの作成ウィザードで、バケットの名前と地域を選択し、**[次へ]** をクリックします。  
    ![バケットの作成](./media/connect-aws-account/create-bucket.png)
5. **[プロパティの設定]** ページで、既定値を保持し、**[次へ]** をクリックします。
6. [Review]\(レビュー\) ページで、**[Create bucket]\(バケットの作成\)** をクリックします。 バケット一覧が表示されます。
7. 作成したバケットをクリックし、**[Permissions]\(アクセス許可\)** タブを選択し、**[Bucket Policy]\(バケット ポリシー\)** を選択します。 バケット ポリシー エディターが開きます。
8. 次の JSON の例をコピーし、バケット ポリシー エディターに貼り付けます。
  - `<BillingBucketName>` を S3 バケットの名前に置き換えます。
  - `<ReadOnlyUserOrRole>` を、事前にコピーしたロールまたはユーザー ARN に置き換えます。

  ```
  {
    "Version": "2012-10-17",
    "Id": "Policy1426774604000",
    "Statement": [
        {
            "Sid": "Stmt1426774604000",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": [
                "s3:GetBucketAcl",
                "s3:GetBucketPolicy"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>"
        },
        {
            "Sid": "Stmt1426774604001",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::386209384616:root"
            },
            "Action": "s3:PutObject",
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        },
        {
            "Sid": "Stmt1426774604002",
            "Effect": "Allow",
            "Principal": {
                "AWS": "<ReadOnlyUserOrRole>"
            },
            "Action": [
                "s3:List*",
                "s3:Get*"
            ],
            "Resource": "arn:aws:s3:::<BillingBucketName>/*"
        }
    ]
  }
  ```

9. **[Save]** をクリックします。  
    ![バケット ポリシー エディター](./media/connect-aws-account/bucket-policy-editor.png)


### <a name="enable-aws-billing-reports"></a>AWS 課金レポートを有効にする

S3 バケットを作成し、設定したら、AWS コンソールの [[Billing Preferences]\(請求の設定\)](https://console.aws.amazon.com/billing/home?#/preference) に移動します。

1. [Preferences]\(設定\)ページで、**[Receive Billing Reports]\(請求レポートの受信\)** を選択します。
2. **[Receive Billing Reports]\(請求レポートの受信\)** で、作成したバケットの名前を入力し、**[Verify]\(確認\)** をクリックします。  
3. レポートの粒度のすべてのオプションを選択し、**[Save preferences]\(設定の保存\)** をクリックします。  
    ![レポートを有効にする](./media/connect-aws-account/enable-reports.png)

Cost Management では、S3 バケットから課金の詳細情報を取得して、課金の詳細が有効になった後に、レポートを追加します。 詳細な課金データが Cloudyn コンソールに表示されるまで、最大 24 時間かかることがあります。 詳細な課金データが表示されると、アカウントの統合状態が **[Consolidated]\(統合済み\)** と表示されます。 アカウントの状態は、**[Completed]\(完了\)** と表示されます。

![アカウントの統合済み状態](./media/connect-aws-account/consolidated-status.png)

最適化レポートには、正確な推奨を提供するのに適切なデータのサンプル サイズを取得するのに数日必要なレポートもあります。

## <a name="next-steps"></a>次の手順

- Azure Cost Management の詳細については、Cost Management に関する「[使用状況とコストを確認する](tutorial-review-usage.md)」チュートリアルに進んでください。
