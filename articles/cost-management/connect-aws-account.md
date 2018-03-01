---
title: "アマゾン ウェブ サービス アカウントを Azure Cost Management に接続する | Microsoft Docs"
description: "アマゾン ウェブ サービス アカウントを接続して、Cost Management のレポートにコストや使用状況のデータを表示します。"
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/08/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: a82d803b51859f809ca5a39ce177697a1f66a008
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2018
---
# <a name="connect-an-amazon-web-services-account"></a>アマゾン ウェブ サービス アカウントの接続

アマゾン ウェブ サービス (AWS) アカウントを Azure Cost Management に接続するためのオプションは 2 つあります。 IAM ロールと接続することも、読み取り専用 IAM ユーザー アカウントと接続することもできます。 推奨されているのは IAM ロールです。これは、信頼できるエンティティへのアクセス許可が定義されているアクセスを委任できるためです。 IAM ロールの場合、長期的なアクセス キーを共有する必要はありません。 AWS アカウントを Cost Management に接続すると、Cost Management レポートにコストや使用状況のデータを表示できるようになります。 このドキュメントでは、両方のオプションについて説明します。

AWS IAM の ID の詳細については、「[ID (ユーザー、グループ、ロール)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html)」を参照してください。

## <a name="aws-role-based-access"></a>AWS のロールベースのアクセス

以下のセクションでは、Cost Management へのアクセスを提供する読み取り専用の IAM ロールについて説明します。

### <a name="get-your-cost-management-account-external-id"></a>Cost Management アカウントの外部 ID を取得する

最初に、Azure Cost Management ポータルから一意の接続パスフレーズを取得します。 これは、**外部 ID** として AWS で使用されます。

1. Azure Portal から Cloudyn ポータルを開くか [https://azure.cloudyn.com](https://azure.cloudyn.com) に移動し、ログインします。
2. **[Settings]\(設定\)** (歯車アイコン) をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Add AWS Account]\(AWS アカウントの追加\)** ダイアログで、**[External ID]\(外部 ID\)** をコピーし、次のセクションにある AWS ロール作成の手順のためにこの値を保存します。 次の画像では、ID の例は _Cloudyn_ です。 ID は異なります。  
    ![外部 ID](./media/connect-aws-account/external-id.png)

### <a name="add-aws-read-only-role-based-access"></a>AWS の読み取り専用のロールベースのアクセスを追加する

1. https://console.aws.amazon.com/iam/home で AWS コンソールにサインインし、**[ロール]** を選択します。
2. **[Create Role]\(ロールの作成\)** をクリックし、**[Another AWS account]\(別の AWS アカウント\)** を選択します。
3. **[Account ID]\(アカウント ID\)** フィールドに ID `432263259397` を貼り付けます。 このアカウント ID は、使用する必要がある Cost Management データ コレクター アカウントです。
4. **[Options]\(オプション\)** の横にある **[Require external ID]\(外部 ID が必要\)** を選択し、前にコピーした値を **[External ID]\(外部 ID\)** フィールドに貼り付けて、**[Next: Permissions]\(次へ: アクセス許可\)** をクリックします。  
    ![ロールの作成](./media/connect-aws-account/create-role01.png)
5. **[Attach permissions policies]\(アクセス許可ポリシーのアタッチ\)**, で、**[Policy type]\(ポリシー タイプ\)** フィルターの検索ボックスに「`ReadOnlyAccess`」と入力し、**[ReadOnlyAccess]** を選択して、**[Next: Review]\(次へ: 確認\)** をクリックします。  
    ![読み取り専用アクセス](./media/connect-aws-account/readonlyaccess.png)
6. 確認ページで、選択内容が正しいことを確認し、**ロール名**を入力します。 たとえば、*Azure-Cost-Mgt* とします。**ロールの説明**を入力します。 たとえば、_Role assignment for Azure Cost Management_ として、**[Create role]\(ロールの作成\)** をクリックします。
7. **[Roles]\(ロール\)** の一覧で、作成したロールをクリックし、[Summary]\(概要\) ページから **[Role ARN]\(ロール ARN\)** の値をコピーします。 [Role ARN]\(ロール ARN\) の値は、後で Azure Cost Management で構成を登録するときに使用します。  
    ![ロール ARN](./media/connect-aws-account/role-arn.png)

### <a name="configure-aws-iam-role-access-in-cost-management"></a>Cost Management で AWS IAM ロール アクセスを構成する

1. Azure Portal から Cloudyn ポータルを開くか、https://azure.cloudyn.com/ に移動してログインします。
2. **[Settings]\(設定\)** (歯車アイコン) をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Account Name]\(アカウント名\)** に、アカウントの名前を入力します。
5. **[Access Type]\(アクセスの種類\)** の横にある **[IAM Role]\(IAM ロール\)** を選択します。
6. **[Role ARN]\(ロール ARN\)** フィールドに、以前コピーした値を貼り付け、**[Save]\(保存\)** をクリックします。  
    ![AWS アカウントの状態](./media/connect-aws-account/aws-account-status01.png)

AWS アカウントがアカウントの一覧に表示されます。 表示された**所有者 ID** がロール ARN の値と一致しています。 **[Account Status]\(アカウントの状態\)** には、緑色のチェック マーク記号が付きます。

Cost Management は、データの収集とレポートの作成を開始します。 ただし、一部の最適化レポートでは、正確な推奨事項が生成されるまでの数日間のデータが必要になる場合があります。

## <a name="aws-user-based-access"></a>AWS のユーザーベースのアクセス

以下のセクションでは、Cost Management へのアクセスを提供する読み取り専用のユーザーについて説明します。

### <a name="add-aws-read-only-user-based-access"></a>AWS の読み取り専用のユーザーベースのアクセスを追加する

1. https://console.aws.amazon.com/iam/home で AWS コンソールにサインインし、**[ユーザー]** を選択します。
2. **[ユーザーの追加]**をクリックします。
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

1. Azure Portal から Cloudyn ポータルを開くか、https://azure.cloudyn.com/ に移動してログインします。
2. **[Settings]\(設定\)** (歯車アイコン) をクリックし、**[Cloud Accounts]\(クラウド アカウント\)** を選択します。
3. [Accounts Management]\(アカウント管理\) で **[AWS Accounts]\(AWS アカウント\)** タブを選択し、**[Add new +]\(新規追加 +\)** をクリックします。
4. **[Account Name]\(アカウント名\)** では、アカウント名を入力します。
5. **[Access Type]\(アクセスの種類\)** の横にある **[IAM User]\(IAM ユーザー\)** を選択します。
6. **[Access Key]\(アクセス キー\)** に、credentials.csv ファイルの**アクセス キー ID** の値を貼り付けます。
7. **[Secret Key]\(シークレット キー\)** に、credentials.csv ファイルの**シークレット アクセス キー**の値を貼り付け、**[Save]\(保存\)** をクリックします。  
    ![AWS ユーザー アカウントの状態](./media/connect-aws-account/aws-user-account-status.png)

AWS アカウントがアカウントの一覧に表示されます。 **[Account Status]\(アカウントの状態\)** には、緑色のチェック マーク記号が付きます。

Cost Management は、データの収集とレポートの作成を開始します。 ただし、一部の最適化レポートでは、正確な推奨事項が生成されるまでの数日間のデータが必要になる場合があります。

## <a name="next-steps"></a>次の手順

- Cloudyn の Azure Cost Management の詳細については、引き続き Cost Management の「[使用状況とコストを確認する](tutorial-review-usage.md)」チュートリアルを参照してください。
