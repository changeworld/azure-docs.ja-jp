---
title: VM の体験版を構成する
description: パートナー センターで VM の体験版を構成します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2021
ms.openlocfilehash: 74f9017829789f073f8efc5850755e611eb4b788
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725461"
---
# <a name="configure-a-vm-test-drive"></a>VM の体験版を構成する

体験版を使用すると、顧客は事前構成済みの環境に決められた時間だけアクセスできるようになり、購入前にオファーを試すことができます。これにより、リードの見込みが高くなり、コンバージョン率が向上します。

VM プランの場合、使用可能な体験版のオプションは Azure Resource Manager (ARM) のデプロイ **のみ** です。 デプロイ テンプレートには、ソリューションを構成しているすべての Azure リソースが含まれている必要があります。

左側のナビゲーション メニューに **[体験版]** タブを表示するには、[[オファーのセットアップ]](azure-vm-offer-setup.md#test-drive-optional) ページで **[体験版]** チェック ボックスをオンにして、CRM システムに接続します。 **[保存]** を選択すると、 **[体験版]** タブと次の 2 つのサブタブが表示されます。

- **[[技術的な構成]](#technical-configuration)** – 体験版を構成し、ARM テンプレートを指定します (次のセクション)。
- **[[Marketplace の登録情報]](#marketplace-listing)** – ユーザー マニュアルやビデオなど、顧客向けの登録情報と補足リソースの詳細を指定します。

## <a name="technical-configuration"></a>技術的な構成

### <a name="regions"></a>リージョン

**[リージョンの編集]** を選択し、体験版を使用する各リージョンのチェック ボックスをオンにします。 または、右上にある **[すべて選択]** または **[すべて選択解除]** リンクを必要に応じて使用します。 最適なパフォーマンスにするには、最も多くの顧客が配置されることが予想されるリージョンのみを選択し、サブスクリプションで必要なすべてのリソースをそこにデプロイできるようにします。 リージョンの選択が完了したら、 **[保存]** を選択します。

### <a name="instances"></a>インスタンス

ボックスに 0 から 99 の値を入力して、リージョンごとに使用できるホット インスタンス、ウォーム インスタンス、またはコールド インスタンスの数を示します。 指定した各インスタンスの種類の数が、オファーで使用可能なリージョンの数で乗算されます。

- **ホット** – デプロイを待機する必要なく、常に実行されていて、顧客がすぐにアクセスできるように準備ができている (取得時間は 10 秒未満) 事前デプロイ済みのインスタンス。 ほとんどの顧客は完全なデプロイを待ちたくないので、少なくとも 1 つのホット インスタンスを持つことをお勧めします。そうしないと、顧客の使用量が減る可能性があります。 ホット インスタンスは常に Azure サブスクリプションで実行されるため、アップタイム コストが大きくなります。
- **ウォーム** – 事前にデプロイされたインスタンス。その後、ストレージに配置されています。 ホット インスタンスよりもコストが低く、顧客のために迅速に再起動されます (取得時間は 3 分から 10 分)。
- **コールド** – 各顧客が要求したときに、体験版の ARM テンプレートをデプロイする必要があるインスタンス。 コールド インスタンスは、ホット インスタンスやウォーム インスタンスと比べて読み込み速度が非常に遅くなります。 待機時間は、必要なリソースによって大きく異なります (最大 1.5 時間)。 ホット インスタンスは Azure サブスクリプションで常に実行されますが、コールド インスタンスでは、コストは体験版の期間だけであるため、コストに対する効果がより高くなります。

### <a name="technical-configuration-of-arm-template"></a>ARM テンプレートの技術的な構成

体験版の ARM テンプレートは、ソリューションを構成するすべての Azure リソースのコード化されたコンテナーです。 体験版に必要な ARM デプロイ テンプレートを作成するには、「[Azure Resource Manager の体験版](azure-resource-manager-test-drive.md#write-the-test-drive-template)」を参照してください。 テンプレートが完成したら、ここに戻り、ARM テンプレートをアップロードして構成を完了する方法を確認します。

正常に公開するには、ARM テンプレートのフォーマットを検証することが重要です。 これを行うには、[オンライン API ツール](/rest/api/resources/deployments/validate)を使用するか、[テスト配置](/azure/azure-resource-manager/templates/deploy-portal)を使用するという 2 つの方法があります。 テンプレートをアップロードする準備ができたら、示されている領域に .zip ファイルをドラッグするか、ファイルを **参照** します。

**[体験版の期間]** を時間単位で入力します。 これは、体験版がアクティブな状態である時間です。 この期間が終わると、体験版は自動的に終了します。

### <a name="deployment-subscription-details"></a>デプロイ サブスクリプションの詳細

ユーザーに代わって Microsoft によって体験版がデプロイされるようにするには、以下の手順を完了して Azure サブスクリプションと Azure Active Directory (AAD) に接続し、 **[下書きの保存]** を選択します。

1. **[Azure サブスクリプション ID]** - Azure サービスと Azure portal へのアクセス権を付与します。 このサブスクリプションで、リソースの使用状況が報告され、サービスが課金されます。 まだお持ちでない場合、体験版に使用するために[別個の Azure サブスクリプション](/azure/cost-management-billing/manage/create-subscription)の作成を検討することをお勧めします。 Azure サブスクリプション ID を見つけるには、Azure portal にサインインし、検索バーで "サブスクリプション" を検索します。
2. **[Azure AD のテナント ID]** – Azure portal 内で **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** に移動し、Azure Active Directory (AD) のテナント ID を入力します。 テナント ID を持っていない場合は、Azure Active Directory で新しく作成します。 テナントの設定については、「[クイック スタート: テナントを設定する](/azure/active-directory/develop/quickstart-create-new-tenant?branch=main)」を参照してください。
3. 他のフィールドに進む前に、Microsoft Test-Drive アプリケーションをテナントにプロビジョニングします。 このアプリケーションを使用して、体験版リソースでの操作を実行します。
    1. [Azure Az PowerShell モジュール](/powershell/azure/install-az-ps?branch=main&view=azps-6.6.0)をまだお持ちではない場合はインストールしてください。
    2. Microsoft Test-Drive アプリケーションのサービス プリンシパルを追加します。
        1. `Connect-AzAccount` を実行して、Azure アカウントにサインインするための資格情報を指定します。これには、Azure Active Directory の **グローバル管理者**[組み込みロール](/azure/active-directory/roles/permissions-reference?branch=main)が必要です。
        2. 新しいサービス プリンシパルを作成します: `New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment`。
        3. サービス プリンシパルが作成されたことを確認します: `Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'`。
            :::image type="content" source="media/test-drive/commands-to-verify-service-principal.png" alt-text="プリンシパルが作成されたことを確認する方法を示しています。":::
1. **[Azure AD アプリ ID]** - Microsoft Test-Drive アプリケーションをテナントにプロビジョニングしたら、アプリケーション ID (`d7e39695-0b24-441c-a140-047800a05ede`) を貼り付けます。  
1. **[Azure AD アプリ クライアントのシークレット]** – シークレットは必要ありません。 "no-secret" などのダミー シークレットを挿入します。
1. アプリケーションを使用してサブスクリプションにデプロイするので、サブスクリプションで共同作成者としてアプリケーションを追加する必要があります。 この設定は、Azure Portal または PowerShell で行います。

    **方法 1: Azure portal**

    1. 体験版に使用しているサブスクリプションを選択します。
    2. **[アクセス制御 (IAM)]** を選択します。
    3. メイン ウィンドウ内で **[ロールの割り当て]** タブを選択し、 **[追加]** を選択して、ドロップダウン メニューから **[ロールの割り当ての追加]** を選択します。
    4. 次の Azure AD アプリケーション名を入力します: `Microsoft TestDrive`。 **共同作成者** ロールを割り当てるアプリケーションを選択します。
    5. **[保存]** を選択します。

    **方法 2: PowerShell**

    1. 次を実行して、ServicePrincipal object-id を取得します: `(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id`。
    2. その ObjectId とサブスクリプション ID を指定して、次を実行します: `New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>`。

左側のナビゲーション メニューの次の **[体験版]** タブである **[Marketplace の登録情報]** に進み、体験版のソリューションを完成します。

## <a name="marketplace-listing"></a>Marketplace の登録情報

顧客向けの登録情報とリソースの追加の詳細を指定します。

**[説明]** – 体験版、デモの内容、体験する機能、ユーザーが試す目的、オファーが適切かどうかを判断するのに役立つその他の関連情報について説明します (最大 3,000 文字)。

**[アクセス情報]** – 顧客が体験版全体の機能にアクセスして使用するために知る必要があることをシナリオに沿って説明します (最大 10,000 文字)。

**[ユーザー マニュアル]** – 体験版のエクスペリエンスについて詳しく説明します。 このマニュアルは、体験版のエクスペリエンスによって顧客が得ることができる内容をカバーし、疑問がある場合にリファレンスとして使用できる必要があります。 PDF 形式で、名前は 255 文字未満である必要があります。

**[体験版のデモ ビデオ]** (省略可能) – リンクとサムネイル画像を使用して、他の場所でホストされているビデオを参照します。 ビデオは、オファーの機能を正しく使用する方法やそれらのメリットが際立つシナリオを把握する方法など、顧客が体験版への理解を深めるのに役立つ方法です。 **[ビデオを追加]** を選択して、次の情報を含めます。

- **名前**
- **[URL]** – YouTube または Vimeo のみ
- **[サムネイル]** – 画像は PNG 形式で、533 x 324 ピクセルである必要があります。

以下の「**次のステップ**」に進む前に、 **[下書きの保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [プランをレビューして公開する](review-publish-offer.md)
