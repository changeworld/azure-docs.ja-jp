---
title: AWS アカウントを Microsoft Defender for Cloud に接続する
description: Microsoft Defender for Cloud を使用して AWS リソースを保護する
author: memildin
ms.author: memildin
ms.date: 11/09/2021
ms.topic: quickstart
ms.service: defender-for-cloud
manager: rkarlin
zone_pivot_groups: connect-aws-accounts
ms.openlocfilehash: 37352dcf8ae68a107fd10dc76a7ccce5fbab95d0
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525564"
---
#  <a name="connect-your-aws-accounts-to-microsoft-defender-for-cloud"></a>AWS アカウントを Microsoft Defender for Cloud に接続する

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

通常、クラウド ワークロードは複数のクラウド プラットフォームにまたがるため、クラウド セキュリティサービスもそうである必要があります。

Microsoft Defender for Cloud により、Azure、アマゾン ウェブ サービス (AWS)、および Google Cloud Platform (GCP) のワークロードが保護されます。

AWS ベースのリソースを保護するために、次の 2 つのメカニズムのいずれかを使用してアカウントを接続できます。

- **クラシック クラウド コネクタ エクスペリエンス** - 最初のマルチクラウド サービスの一環として、AWS および GCP のアカウントを接続する手段としてこれらのクラウド コネクタが導入されました。 クラシック クラウド コネクタ エクスペリエンスを使用して既に AWS コネクタを構成している場合は、この新しいメカニズムを使用してアカウントを接続し直すことをお勧めします。 [環境設定] ページでアカウントを追加したら、古いコネクタを削除して、重複する推奨事項が表示されないようにします。

- **[環境設定] ページ (プレビュー)** (推奨) - このプレビュー ページには、大幅に改善された、簡単なオンボード エクスペリエンス (自動プロビジョニングを含む) が用意されています。 また、このメカニズムによって、Defender for Cloud の強化されたセキュリティ機能が AWS リソースにも拡張されます。

    - **Defender for Cloud の CSPM 機能** が、AWS リソースまで拡張されています。 このエージェントレスプランでは、AWS 固有のセキュリティの推奨事項に従って AWS リソースを評価します。これらは、セキュリティで保護されたスコアに含まれています。 これらのリソースは、AWS (AWS CI、AWS PCI DSS、および AWS の基本的なセキュリティのベスト プラクティス) に固有の組み込み標準に対するコンプライアンスを評価されます。 Defender for Cloud の[資産インベントリ ページ](asset-inventory.md)は、Azure リソースと AWS リソースを共に管理するのに役立つマルチクラウド対応機能です。
    - **Microsoft Defender For Kubernetes** は、そのコンテナーの脅威検出と高度な防御を **Amazon eks Linux クラスター** に拡張します。
    - **Microsoft Defender for servers** を使用すると、お使いの Windows インスタンスと Linux EC2 インスタンスを対象とした脅威検出および高度な防御が追加されます。 このプランには、Microsoft Defender for Endpoint の統合ライセンス、セキュリティ ベースラインと OS レベルの評価、脆弱性評価スキャン、適応型アプリケーション制御 (AAC)、ファイルの整合性の監視 (FIM) などが含まれます。

こちらのスクリーンショットでは、Defender for Cloud の[概要ダッシュボード](overview-page.md)に AWS アカウントが表示されています。

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="4 つの AWS プロジェクトが Defender for Cloud の概要ダッシュボードに表示されている" lightbox="./media/quickstart-onboard-aws/aws-account-in-overview.png":::


::: zone pivot="env-settings"

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|価格:|CSPM プランは無料です。<br>Defender for Kubernetes プランは、プレビュー期間中は無料でご利用いただけます。 その後は、Azure リソースの Defender for Kubernetes プランと同じ価格で課金されます。<br>[Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用して Azure に接続される AWS マシンごとに、Defender for servers プランの料金が、Azure マシン用の Defender for servers プランと同じ価格で課金されます。 AWS EC2 に Azure Arc エージェントがデプロイされていない場合、そのマシンに対して課金されることはありません。|
|必要なロールとアクセス許可:|関連する Azure サブスクリプションの **所有者**<br>所有者が (Defender for servers プランに必要となる) サービス プリンシパルの詳細を提供している場合、**共同作成者** も AWS アカウントに接続できます|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||

## <a name="prerequisites"></a>前提条件

- AWS アカウントを Azure サブスクリプションに接続するには、言うまでもなく、AWS アカウントにアクセスする必要があります。

- **Defender For Kubernetes プランを有効にする** には、次のものが必要です。
    - EKS K8s API サーバーにアクセスする権限を持つ、少なくとも 1 つの Amazon EKS クラスター。 新しい EKS クラスターを作成する必要がある場合は、「[Amazon EKS の開始方法– eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)」の手順に従ってください。
    - 新しい SQS キュー、Kinesis Firehose 配信ストリーム、および S3 バケットをクラスターのリージョンに作成するためのリソース容量。

- **Defender for servers プランを有効にする** には、次のものが必要です。
    - Microsoft Defender for servers を有効にする (「[クイック スタート: 強化されたセキュリティ機能を有効にする](enable-enhanced-security.md)」を参照)。
    - AWS Systems Manager (SSM) によって管理され、SSM エージェントを使用している EC2 インスタンスがあるアクティブな AWS アカウント。 一部の Amazon マシン イメージ (AMI) には SSM エージェントがプレインストールされています。該当する AMI の一覧が、「[AMIs がプリインストールされた SSM Agent](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent-technical-details.html#ami-preinstalled-agent)」に記載されています。 お使いの EC2 インスタンスに SSM エージェントがない場合は、Amazon の関連する指示に従ってください。
        - [ハイブリッド環境に SSM エージェントをインストールする (Windows)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-win.html)
        - [ハイブリッド環境に SSM エージェントをインストールする (Linux)](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-managed-linux.html)


## <a name="connect-your-aws-account"></a>AWS アカウントを接続する

以下の手順に従って AWS クラウド コネクタを作成します。 

1. [Defender for Cloud] メニューで、 **[環境設定]** を開きます。
1. **[環境の追加]**  >  **[アマゾン ウェブ サービス]** を選択します。

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-environment-settings.png" alt-text="AWS アカウントを Azure サブスクリプションに接続します。":::

1. AWS アカウントの詳細 (コネクタ ソースを格納する場所を含む) を入力し、 **[Next: Select plans]\(次: プランの選択\)** を選択します。

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-details.png" alt-text="AWS アカウントの追加ウィザードのステップ 1: アカウントの詳細を入力します。":::

1. [Select plans]\(プランの選択\) タブで、この AWS アカウントに対して有効にする Defender for Cloud 機能を選択します。 

    > [!NOTE]
    > 各機能には、アクセス許可に関する独自の要件があり、料金が発生する可能性があります。

    :::image type="content" source="media/quickstart-onboard-aws/add-aws-account-plans-selection.png" alt-text="[Select plans]\(プランの選択\) タブで、この AWS アカウントに対して有効にする Defender for Cloud 機能を選択します。":::

    > [!IMPORTANT]
    > 推奨事項の現在の状態を提示するために、CSPM プランでは AWS リソース API が 1 日に数回クエリされます。 これらの読み取り専用 API 呼び出しでは料金は発生しませんが、読み取りイベントの記録を有効にした場合は CloudTrail に登録されます *。* [AWS のドキュメント](https://aws.amazon.com/cloudtrail/pricing/)で説明されているように、記録を 1 つ維持するのに追加料金は発生しません。 AWS からデータをエクスポートしている場合 (たとえば、外部 SIEM に)、このような呼び出しの量が増加すると、取り込みのコストも増加する可能性があります。 このような場合は、Defender for Cloud ユーザーまたはロール ARN: arn:aws:iam::[accountId]:role/CspmMonitorAws (これは既定のロール名です。アカウントに設定されているロール名を確認してください) から読み取り専用呼び出しを除外することをお勧めします。

    - Defender for Servers の対象を AWS EC2 まで拡張するには、 **[サーバー]** プランを **[オン]** に設定し、必要に応じて構成を編集します。 

    - Defender for Kubernetes の対象を AWS EKS Linux クラスター まで拡張するには、 **[コンテナー]** プランを **[オン]** に設定し、必要に応じて構成を編集します。

1. セットアップを完了します。
    1. **[次: アクセスの構成]** を選択します。
    1. CloudFormation テンプレートをダウンロードします。
    1. ダウンロードした CloudFormation テンプレートを使用して、画面上の指示に従って AWS にスタックを作成します。
    1. **[Next : Review and update]\(次: 確認と生成\)** を選択します。
    1. **［作成］** を選択します

すぐに AWS リソースのスキャンが開始され、数時間以内にセキュリティに関する推奨事項が表示されます。

::: zone-end


::: zone pivot="classic-connector"

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|一般公開 (GA)|
|価格:|[Microsoft Defender for servers](defender-for-servers-introduction.md) が必要|
|必要なロールとアクセス許可:|関連する Azure サブスクリプションの **所有者**<br>所有者がサービス プリンシパルの詳細を提供する場合は、**共同作成者** も AWS アカウントに接続できます|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: 各国 (Azure Government、Azure China 21Vianet)|
|||


## <a name="connect-your-aws-account"></a>AWS アカウントを接続する

以下の手順に従って AWS クラウド コネクタを作成します。 

### <a name="step-1-set-up-aws-security-hub"></a>手順 1. AWS Security Hub を設定する:

1. 複数のリージョンのセキュリティに関する推奨事項を表示するには、関連するリージョンごとに次の手順を繰り返します。

    > [!IMPORTANT]
    > AWS 管理アカウントを使用している場合は、これらの手順を繰り返して、関連するすべてのリージョンで、管理アカウントと、接続されたすべてのメンバー アカウントを構成します

    1. [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html) を有効にします。
    1. [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html) を有効にします。
    1. データが Security Hub に送信されていることを確認します。 Security Hub を初めて有効にするときは、データが使用可能になるまで数時間かかることがあります。

### <a name="step-2-set-up-authentication-for-defender-for-cloud-in-aws"></a>手順 2. AWS で Defender for Cloud の認証を設定する

Defender for Cloud で AWS に対する認証を許可するには、次の 2 つの方法があります。

- **Defender for Cloud 用の IAM ロールを作成する** (推奨) - 最も安全な方法です
- **Defender for Cloud 用の AWS ユーザー** - IAM が有効になっていない場合の安全性が低いオプション

#### <a name="create-an-iam-role-for-defender-for-cloud"></a>Defender for Cloud 用の IAM ロールを作成する
1. アマゾン ウェブ サービス コンソールの **[セキュリティ、アイデンティティ、コンプライアンス]** の **[IAM]** を選択します。
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS サービス。":::

1. **[ロール]** 、 **[ロールの作成]** の順に選択します。
1. **[別の AWS アカウント]** を選択します。
1. 次の詳細を入力します。

    - **アカウント ID** - Defender for Cloud の AWS コネクタ ページに示されているように、Microsoft アカウント ID (**158177204117**) を入力します。
    - **外部 ID が必要** - 選択する必要があります
    - **外部 ID** - Defender for Cloud の AWS コネクタ ページに示されているように、サブスクリプション ID を入力します 

1. **[次へ]** を選択します。
1. **[Attach permission policies]\(アクセス許可ポリシーをアタッチ\)** セクションで、次の [AWS マネージド ポリシー](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_job-functions.html)を選択します。

    - SecurityAudit (`arn:aws:iam::aws:policy/SecurityAudit`)
    - AmazonSSMAutomationRole (`arn:aws:iam::aws:policy/service-role/AmazonSSMAutomationRole`)
    - AWSSecurityHubReadOnlyAccess (`arn:aws:iam::aws:policy/AWSSecurityHubReadOnlyAccess`)

1. 必要に応じて、タグを追加します。 ユーザーにタグを追加しても、接続には影響しません。
1. **[次へ]** を選択します。

1. ロールの一覧で、作成したロールを選択します

1. 後で使用できるように Amazon Resource Name (ARN) を保存します。 

#### <a name="create-an-aws-user-for-defender-for-cloud"></a>Defender for Cloud 用の AWS ユーザーを作成する 
1. **[ユーザー]** タブを開き、 **[ユーザーの追加]** を選択します。
1. **[詳細]** ステップで Defender for Cloud のユーザー名を入力し、AWS アクセスの種類として **[プログラムによるアクセス]** が選択されていることを確認します。 
1. **[次のステップ: アクセス権限]** を選択します。
1. **[Attach existing policies directly]\(既存のポリシーを直接アタッチ\)** を選択し、次のポリシーを適用します。
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. **タグ** を選択します。 必要に応じて、タグを追加します。 ユーザーにタグを追加しても、接続には影響しません。
1. **[レビュー]** を選択します。
1. 後で使用するために、自動的に生成された **アクセス キー ID** と **シークレット アクセス キー** の CSV ファイルを保存します。
1. 概要を確認し、 **[ユーザーの作成]** を選択します。


### <a name="step-3-configure-the-ssm-agent"></a>手順 3. SSM エージェントを構成する

お使いの AWS リソース全体でタスクを自動化するには、AWS Systems Manager が必要です。 お使いの EC2 インスタンスに SSM エージェントがない場合は、Amazon の関連する指示に従ってください。

- [Windows インスタンスでの SSM エージェントのインストールと構成](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Amazon EC2 Linux インスタンスでの SSM エージェントのインストールと構成](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>手順 4. Azure Arc の前提条件を満たす
1. 適切な [Azure リソース プロバイダー](../azure-arc/servers/agent-overview.md#register-azure-resource-providers)が登録されていることを確認します。
    - Microsoft.HybridCompute
    - Microsoft.GuestConfiguration

1. 大規模なオンボーディング用にサービス プリンシパルを作成します。 「[大規模なオンボーディング用にサービス プリンシパルを作成する](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」で説明されているように、オンボードに使用するサブスクリプションの **所有者** として、Azure Arc オンボード用サービス プリンシパルを作成します。


### <a name="step-5-connect-aws-to-defender-for-cloud"></a>手順 5. AWS から Defender for Cloud に接続する

1. Defender for Cloud のメニューから **[環境設定]** を開き、クラシック コネクタ エクスペリエンスに切り替えるオプションを選択します。

    :::image type="content" source="media/quickstart-onboard-gcp/classic-connectors-experience.png" alt-text="Defender for Cloud のクラシック クラウド コネクタ エクスペリエンスに切り替える。":::

1. **[Add AWS account]\(AWS アカウントの追加\)** を選択します。
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Defender for Cloud のマルチ クラウド コネクタ ページの [Add AWS account]\(AWS アカウントの追加\) ボタン":::
1. **[AWS authentication]\(AWS 認証\)** タブでオプションを構成します。
    1. コネクタの **表示名** を入力します。
    1. サブスクリプションが正しいことを確認します。 これは、コネクタと AWS Security Hub の推奨事項が追加されるサブスクリプションです。
    1. 「[手順 2. AWS で Defender for Cloud の認証を設定する](#step-2-set-up-authentication-for-defender-for-cloud-in-aws)」で選択した認証オプションに応じて、次の操作を行います。
        - **[Assume Role]\(ロールを想定\)** を選択し、「[Defender for Cloud 用の IAM ロールを作成する](#create-an-iam-role-for-defender-for-cloud)」から ARN を貼り付けます。

            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Azure portal で AWS 接続ウィザードの関連フィールドに ARN ファイルを貼り付ける。":::

            OR

        - **資格情報** を選択し、「[Defender for Cloud 用の AWS ユーザーを作成する](#create-an-aws-user-for-defender-for-cloud)」で保存した .csv ファイルから **アクセス キー** と **秘密鍵** を貼り付けます。
1. **[次へ]** を選択します。
1. **[Azure Arc Configuration]\(Azure Arc 構成\)** タブでオプションを構成します。

    接続されている AWS アカウントの EC2 インスタンスが Defender for Cloud によって検出され、SSM を使用して Azure Arc にオンボードされます。 

    > [!TIP]
    > サポートされるオペレーティング システムの一覧については、FAQ の「[自身の EC2 インスタンスではどのオペレーティングシステムがサポートされていますか?](#what-operating-systems-for-my-ec2-instances-are-supported)」を参照してください。

    1. 選択したサブスクリプションで、検出された AWS EC2 がオンボードされる **リソース グループ** と **Azure リージョン** を選択します。
    1. こちらの「[大規模なオンボーディング用にサービス プリンシパルを作成する](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)」で説明されているように、Azure Arc の **サービス プリンシパル ID** と **サービス プリンシパルのクライアント シークレット** を入力します
    1. マシンがプロキシ サーバー経由でインターネットに接続している場合は、プロキシ サーバーの IP アドレスか、マシンがプロキシ サーバーとの通信に使用する名前とポート番号を指定します。 ```http://<proxyURL>:<proxyport>``` の形式で値を入力します。
    1. **[Review + create]\(レビュー + 作成\)** を選択します。

        概要情報をレビューする

        タグ セクションには、Azure で簡単に認識できるように、オンボード EC2 ごとに自動的に作成されるすべての Azure タグと、そのタグに関連する独自の詳細情報が記載されています。 

        Azure タグの詳細については、「[タグを使用して Azure リソースと管理階層を整理する](../azure-resource-manager/management/tag-resources.md)」を参照してください。

### <a name="step-6-confirmation"></a>手順 6. 確認

コネクタが正常に作成され、AWS Security Hub が正しく構成されたら、次のようになります。

- Defender for Cloud では、環境をスキャンして AWS EC2 インスタンスを探し、Azure Arc にそれらをオンボードし、Log Analytics エージェントのインストールを可能にするほか、脅威の防止とセキュリティに関する推奨事項が提供されます。 
- Defender for Cloud サービスでは、新しい AWS EC2 インスタンスが 6 時間ごとにスキャンされ、構成に従ってそれらがオンボードされます。
- Defender for Cloud の規制コンプライアンス ダッシュボードに AWS CIS 標準が表示されます。
- Security Hub ポリシーが有効になっている場合、オンボードが完了してから 5 分から 10 分後に、推奨事項が Defender for Cloud ポータルと規制コンプライアンス ダッシュボードに表示されます。


::: zone-end

:::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="Defender for Cloud の推奨事項ページの AWS リソースと推奨事項" lightbox="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png":::


## <a name="monitoring-your-aws-resources"></a>AWS リソースの監視

前のスクリーンショットでわかるように、Defender for Cloud のセキュリティに関する推奨事項のページには AWS リソースが表示されます。 環境フィルターを使用して、Defender for Cloud のマルチクラウド機能を利用できます。Azure、AWS、および GCP リソースの推奨事項がまとめて表示されます。

リソースの種類別に、リソースのアクティブな推奨事項をすべて表示するには、Defender for Cloud の資産インベントリ ページを使用し、関心のある AWS リソースの種類にフィルターを適用します。

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="AWS オプションを示す資産インベントリ ページのリソースの種類のフィルター"::: 


## <a name="faq---aws-in-defender-for-cloud"></a>FAQ - Defender for Cloud の AWS

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>自身の EC2 インスタンスのではどのオペレーティングシステムがサポートされていますか?

AWS マシン向け Azure Arc への自動オンボードのためにサポートされている OS

- Ubuntu 16.04 - SSM エージェントは既定でプレインストールされています
- Ubuntu 18.04 - SSM エージェントは既定でプレインストールされています
- Windows Server - SSM エージェントは既定でプレインストールされています
- CentOS Linux 7 - SSM は手動でインストールするか、個別にオンボードする必要があります
- SUSE Linux Enterprise Server (SLES) 15 (x64) - SSM は手動でインストールするか、個別にオンボードする必要があります
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM は手動でインストールするか、個別にオンボードする必要があります


## <a name="next-steps"></a>次の手順

AWS アカウントの接続は、Microsoft Defender for Cloud で利用できるマルチクラウド エクスペリエンスの一部です。 関連情報については、次のページを参照してください。

- [GCP アカウントを Microsoft Defender for Cloud に接続する](quickstart-onboard-gcp.md)
