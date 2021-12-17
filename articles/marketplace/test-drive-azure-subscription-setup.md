---
title: ホストされた体験版用に Azure Marketplace サブスクリプションを設定する
description: Dynamics 365 for Customer Engagement と Dynamics 365 for Operations の体験版用に Azure Marketplace サブスクリプションを設定する方法について説明します
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 10/01/2020
ms.openlocfilehash: 3aa0ddf4a9013d5f64584fbe93a795f6420dc410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389950"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>ホストされた体験版用に Azure Marketplace サブスクリプションを設定する

この記事では、体験版用に Azure Marketplace サブスクリプションと **Dynamics 365 for Customer Engagement & Power Apps** または **Dynamics 365 for Operations** 環境を設定する方法について説明します。

## <a name="set-up-for-dynamics-365-for-customer-engagement--power-apps"></a>Dynamics 365 for Customer Engagement & Power Apps を設定する

1. 管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
2. 右上隅のアカウント アイコンの上にカーソルを移動して、Dynamics 365 体験版インスタンスに関連付けられたテナント内にいることを確認します。 正しいテナント内でない場合は、アカウント アイコンを選択して正しいテナントに切り替えます。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="正しいテナントの選択。":::

3. **Dynamics 365 Customer Engagement Plan** ライセンスが使用可能であることを確認します。

    [![プランのライセンスの確認。](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Azure で Azure Active Directory (AD) アプリを作成します。 AppSource では、このアプリを使用して、テナントでの体験版ユーザーのプロビジョニングとプロビジョニング解除が行われます。
    1. フィルター ペインから、 **[Azure Active Directory]** を選択します。
    2. **[アプリの登録]** を選択します。

        [![アプリの登録の選択。](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. **[新規登録]** を選択します。
    4. 適切なアプリケーション名を指定します。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="アプリケーションの登録。":::

    5. [サポートされているアカウントの種類] で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント\)** を選択します。
    6. **[作成]** を選択して、アプリが作成されるまで待ちます。
    7. アプリが作成されたら、概要画面に表示される **アプリケーション ID** をメモします。 この値は、後で体験版を構成するときに必要になります。
    8. **[アプリケーションの管理]** で、 **[API のアクセス許可]** を選択します。
    9. **[アクセス許可の追加]** を選択し、次に **[Microsoft Graph API]** を選択します。
    10. **[アプリケーション]** アクセス許可カテゴリを選択し、次に **[User.ReadWrite.All]** 、 **[Directory.Read.All]** 、 **[Directory.ReadWrite.All]** のアクセス許可を選択します。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="アプリケーションのアクセス許可の設定。":::

    11. アクセス許可が追加されたら、 **[Grant admin consent for Microsoft]\(Microsoft に対する管理者の同意の付与\)** を選択します。
    12. メッセージ アラートから、 **[はい]** を選択します。

        [![アプリケーションのアクセス許可が正常に付与されたことを示しています。](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    13. Azure AD アプリのシークレットを作成するには、次のようにします。
        1. **[アプリケーションの管理]** から、 **[Certificate and secrets]\(証明書とシークレット\)** を選択します。
        2. [クライアント シークレット] で、 **[新しいクライアント シークレット]** を選択します。

             :::image type="content" source="./media/test-drive/new-client-secret.png" alt-text="新しいクライアント シークレットの追加":::

        3. 説明 ("*体験版*" など) を入力し、適切な期間を選択します。 このキーの有効期限が切れると体験版が中断され、その時点で新しいキーを生成して AppSource に提供する必要があるため、最大期間として 24 か月を使用することをお勧めします。
        4. **[追加]** を選択して、Azure アプリのシークレットを生成します。 このブレードを離れるとすぐに非表示になるため、この値をコピーします。 この値は、後で体験版を構成するときに必要になります。

            :::image type="content" source="./media/test-drive/add-client-secret-customer.png" alt-text="クライアント シークレットの追加。":::

5. アプリケーションにサービス プリンシパルの役割を追加して、Azure AD アプリで Azure テナントからユーザーを削除できるようにします。 この手順を完了するには、2 つのオプションがあります。

    **方法 1**

    1. "**Azure AD のロールと管理者**" を検索し、サービスを選択します。

        :::image type="content" source="./media/test-drive/active-ad-roles.png" alt-text="Azure AD のロールと管理者を検索する方法を示しています。":::

    2. **[すべてのロール]** ページで、"**ユーザー管理者**" のロールを検索し、 **[ユーザー管理者]** をダブルクリックします。

        :::image type="content" source="./media/test-drive/user-administrator.png" alt-text="ユーザー管理者を検索して選択する方法を示しています。":::

    3. **[割り当ての追加]** を選択します。

        :::image type="content" source="./media/test-drive/add-assignments-1.png" alt-text="[割り当ての追加] ボタンを示しています。":::

    4. 上で作成したアプリを検索して選択し、 **[追加]** をクリックします。

        :::image type="content" source="./media/test-drive/add-assignments-2.png" alt-text="アプリの割り当てが成功したことを示しています。":::

    5. サービス プリンシパルのロールがアプリケーションに正常に割り当てられていることに注意してください。

        :::image type="content" source="./media/test-drive/successful-assignment.png" alt-text="サービス プリンシパルのロールがアプリケーションに正常に割り当てられていることを示しています。":::

    **方法 2**

    1. 管理レベルの PowerShell コマンド プロンプトを開きます。
    2. Install-Module MSOnline (MSOnline がインストールされていない場合、このコマンドを実行します)。
    3. Connect-MsolService (これにより、ポップアップ ウィンドウが表示されます。新しく作成された組織テナントを使用してサインインします)。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

         :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="アカウントへのサインイン。":::

6. 新しいセキュリティ グループを作成し、キャンバス アプリ (Power Apps) に追加します。 この手順は、キャンバス アプリ (Power Apps) プランにのみ適用されます。
    1. 新しいセキュリティ グループを作成します。
        1. **[Azure Active Directory]** に移動します。
        1. **[管理]** にある **[グループ]** を選択します。
        1. **[+ 新しいグループ]** を選択します。
        1. **[セキュリティ グループ]** の種類を選択します。 
        1. **[グループ名]** として「*TestDriveSecurityGroup*」と入力します。
        1. **[セキュリティ グループ体験版]** などの説明を追加します。
        1. 他のフィールドは既定値のままにして、 **[作成]** を選択します。

            :::image type="content" source="./media/test-drive/create-new-group.png" alt-text="新しいセキュリティ グループを作成する方法を示します。":::

    1. 作成したセキュリティ グループをキャンバス アプリ (Power Apps) に追加します。
        1. **[PowerApps]** ポータル ページを開き、サインインします。
        1. **[アプリ]** を選択し、アプリの省略記号をクリックします。
        1. **[共有]** を選択します。
        1. 前の手順で作成した **TestDriveSecurityGroup** セキュリティ グループを検索します。
        1. セキュリティ グループに **[データのアクセス許可]** を追加します。
        1. **[電子メールの送信]** 招待状チェック ボックスをオフにします。
        1. **[共有]** を選択します。
    
            > [!NOTE]
            > キャンバス アプリ (Power Apps) 用の CE/Dataverse 以外のバックエンド データ ソースを使用する場合:
            > - 上記で作成したセキュリティ グループにデータ ソースへのアクセスを許可します。 たとえば、SharePoint データ ソースなどです。
            > - SharePoint を開き、データ テーブルをセキュリティ グループと共有します。

7. 作成したばかりの Azure アプリをアプリケーション ユーザーとして体験版 CRM インスタンスに追加します。 この手順は、Dynamics 365 Customer Engagement オファーにのみ適用されます。
    1. **CRM インスタンス** にサインインし、 **[設定]**  >  **[セキュリティ]**  >  **[ユーザー]** を選択します。
    2. ビューを **[アプリケーション ユーザー]** に変更します。

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="ユーザーのアカウント情報の設定。":::

    3. 新しいユーザーを追加します (フォームがアプリケーション ユーザー用であることを確認してください)。
    4. 上記で作成した **Azure ApplicationId** を **[アプリケーション ID]** に追加します。
    5. **[保存]** を選択します。
    6. **[ロールの管理]** を選択します。
    7. "*システム管理者*" など、読み取り、書き込み、およびロールの割り当ての特権を含むカスタムまたは OOB のセキュリティ ロールを割り当てます。

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="ロールの特権の選択。":::

    10. **[別のユーザーの代わりに操作します]** 特権を有効にします。
    11. 体験版用に作成したカスタム セキュリティ ロールをアプリケーション ユーザーに割り当てます。

## <a name="set-up-for-dynamics-365-for-operations"></a>Dynamics 365 for Operations の設定

1. 管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。
2. 右上隅のアカウント アイコンの上にカーソルを移動して、Dynamics 365 体験版インスタンスに関連付けられたテナント内にいることを確認します。 正しいテナント内でない場合は、アカウント アイコンを選択して正しいテナントに切り替えます。

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="正しいテナントの選択。":::

3. Azure で Azure AD アプリを作成します。 AppSource では、このアプリを使用して、テナントでの体験版ユーザーのプロビジョニングとプロビジョニング解除が行われます。
    1. フィルター ペインから、 **[Azure Active Directory]** を選択します。
    2. **[アプリの登録]** を選択します。

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="アプリの登録の選択。":::

    3. **[新規登録]** を選択します。
    4. 適切なアプリケーション名を指定します。

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="アプリケーションの登録。":::

    5. [サポートされているアカウントの種類] で、 **[Accounts in any organizational directory and personal Microsoft accounts]\(任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント\)** を選択します。
    6. **[作成]** を選択して、アプリが作成されるまで待ちます。
    7. アプリが作成されたら、概要画面に表示される **アプリケーション ID** をメモします。 この値は、後で体験版を構成するときに必要になります。
    8. **[アプリケーションの管理]** で、 **[API のアクセス許可]** を選択します。
    9. **[アクセス許可の追加]** を選択し、次に **[Microsoft Graph API]** を選択します。
    10. **[アプリケーション]** アクセス許可カテゴリを選択し、次に **[Directory.Read.All]** と **[Directory.ReadWrite.All]** のアクセス許可を選択します。

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="アプリケーションのアクセス許可の設定。":::

    11. **[アクセス許可の追加]** を選択します。
    12. アクセス許可が追加されたら、 **[Grant admin consent for Microsoft]\(Microsoft に対する管理者の同意の付与\)** を選択します。
    13. メッセージ アラートから、 **[はい]** を選択します。

        [![アプリケーションのアクセス許可が正常に付与されたことを示しています。](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Azure AD アプリのシークレットを生成するには、次のようにします。
        1. **[アプリケーションの管理]** から、 **[Certificate and secrets]\(証明書とシークレット\)** を選択します。
        2. [クライアント シークレット] で、 **[新しいクライアント シークレット]** を選択します。
        3. 説明 ("*体験版*" など) を入力し、適切な期間を選択します。 このキーの有効期限が切れると、体験版は中断されます。その時点で、新しいキーを生成して AppSource に提供する必要があります。
        4. **[追加]** を選択して、Azure アプリのシークレットを生成します。 このブレードを離れるとすぐに非表示になるため、この値をコピーします。 この値は、後で体験版を構成するときに必要になります。

            :::image type="content" source="./media/test-drive/add-client-secret-operations.png" alt-text="クライアント シークレットの追加を示します。":::

4. アプリケーションにサービス プリンシパルの役割を追加して、Azure AD アプリで Azure テナントからユーザーを削除できるようにします。
    1. 管理レベルの PowerShell コマンド プロンプトを開きます。
    2. Install-Module MSOnline (MSOnline がインストールされていない場合、このコマンドを実行します)。
    3. Connect-MsolService (これにより、ポップアップ ウィンドウが表示されます。新しく作成された組織テナントを使用してサインインします)。
    4. $applicationId = **<YOUR_APPLICATION_ID>** 。
    5. $sp = Get-MsolServicePrincipal -AppPrincipalId $applicationId。
    6. Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId $sp.ObjectId -RoleMemberType servicePrincipal。

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="アカウントへのサインイン。":::

5. 次に、上記のアプリを **Dynamics 365 for Operations** に追加して、アプリでユーザーを管理できるようにします。
    1. **Dynamics 365 for Operations** インスタンスを見つけます。
    2. 左上隅から、3 本線のメニュー (ハンバーガー) をクリックします。
    3. **[システム管理]** を選択します。
    4. **[Azure Active Directory アプリケーション]** を選択します。
    5. **[+新規]** を選択します。
    6. On-Behalf-Of アクションを実行する **Azure AD アプリのクライアント ID** を入力します。

    > [!NOTE]
    > アクションはこのユーザー ID の代わりに実行されます (通常、インスタンスのシステム管理者、または他のユーザーを追加する権限を持つユーザー)。

    [![アクションはこのユーザー ID の代わりに実行されます (通常、インスタンスのシステム管理者、または他のユーザーを追加する権限を持つユーザー)。](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
