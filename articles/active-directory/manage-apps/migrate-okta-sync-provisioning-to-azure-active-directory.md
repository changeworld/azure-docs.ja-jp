---
title: Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行するためのチュートリアル
titleSuffix: Active Directory
description: Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する方法について説明します
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: d70d3f32093fc2e509d351e0ce194bac1d7490b8
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2021
ms.locfileid: "129084153"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization"></a>チュートリアル: Okta 同期プロビジョニングを Azure Active Directory Connect ベースの同期に移行する

この記事では、Okta から Azure Active Directory (Azure AD) へのユーザー プロビジョニングを現在使用している組織がユーザー同期またはユニバーサル同期を Azure AD Connect に移行する方法について説明します。 これにより、Azure AD や Office 365 へのプロビジョニングをさらに強化できるようになります。

同期プラットフォームの移行は、小さな変更ではありません。 この記事で説明されているプロセスの各手順は、ステージング モードから Azure AD Connect を削除したり、Azure AD クラウド プロビジョニング エージェントを有効にしたりする前に、実際の環境に対して検証する必要があります。

## <a name="prerequisites"></a>前提条件

Okta プロビジョニングから Azure AD に切り替える場合、お客様には Azure AD Connect サーバーと Azure AD クラウド プロビジョニングの 2 つの選択肢があります。 2 つの製品の違いを理解するために、[Microsoft がまとめた比較記事](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync)を確認することをお勧めします。

Azure AD クラウド プロビジョニングは、ユニバーサル同期やユーザー同期を使用している Okta のお客様にとって最もわかりやすい移行パスです。クラウド プロビジョニング エージェントは軽量で、Okta ディレクトリ同期エージェントのようにドメイン コントローラー上かその近くにインストールできます。 両者を同一のサーバーにインストールすることはお勧めしません。

Azure AD Connect サーバーを選択しなければならないのは、ユーザーを同期するときに、お客様の組織で次のいずれかのテクノロジを利用する必要がある場合です。

- デバイスの同期 - Hybrid Azure AD Join または Hello for Business

- パススルー認証

- 150k を超えるオブジェクトのサポート

- 書き戻しのサポート

>[!NOTE]
>Azure AD Connect や Azure AD クラウド プロビジョニングをインストールする場合は、すべての前提条件を考慮する必要があります。 インストールの前に、[こちらの記事](../hybrid/how-to-connect-install-prerequisites.md)で詳細をご確認ください。  

## <a name="step-1---confirm-immutableid-attribute-synchronized-by-okta"></a>手順 1 - Okta によって同期された ImmutableID 属性を確認する

ImmutableID は、同期されるオブジェクトをオンプレミスの対応物に関連付けるために使用される主要な属性です。 オンプレミス オブジェクトの Active Directory objectGUID が Okta で受け取られると、それは Base64 でエンコードされた文字列に変換されます。 次に、既定でその文字列が Azure AD の ImmutableID フィールドにスタンプされます。

Azure AD PowerShell に接続すると、現在の ImmutableID 値を確認できます。 Azure AD PowerShell モジュールを使用したことがない場合は管理 PowerShell セッションで `Install-Module AzureAD` を実行し、それから次のコマンドを実行します。

```Powershell
Import-module AzureAD
Connect-AzureAD
```

このモジュールが既にあり、それが最新でない場合、最新バージョンに更新するように求める警告が表示されることがあります。

モジュールがインストールされたら、それをインポートし、次の手順に従って Azure AD サービスに接続します。

1. 最新の認証ウィンドウにグローバル管理者の資格情報を入力します。

   ![モジュールのインポートを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

2. テナントに接続したら、ImmutableID がどのように設定されているかを確認します。 次に示す例では、objectGUID の Okta の既定値が ImmutableID に使用されています。

   ![ImmutableID が objectGUID の Okta の既定値になっていることを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

3. オンプレミスで objectGUID から Base64 への変換を手動で確認するには、いくつかの方法があります。個々の検証では次の例を使用します。

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'

   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Okta objectGUID を ImmutableID に手動で変更する方法を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="step-2---mass-validation-methods-for-objectguid"></a>手順 2 - objectGUID の一括検証メソッド

Azure AD Connect に一括移行する前に、Azure AD の ImmutableID がオンプレミスのそれらの値と完全に一致することを検証することが重要です。

この例では、オンプレミスの AD ユーザー **すべて** を取得し、それらの objectGUID と計算済みの ImmutableID のリストを CSV ファイルにエクスポートします。

1. オンプレミスのドメイン コントローラー上の PowerShell で次のコマンドを実行します。 

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![オンプレミスのドメイン コントローラーのコマンドを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

2. Azure AD PowerShell セッションで次のコマンドを実行して、既に同期されている値を収集します。

   ```powershell

   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Azure AD PowerShell セッションを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   両方のエクスポートを取得したら、各ユーザーの ImmutableID が一致することを確認します。

   >[!IMPORTANT]
   >クラウドの ImmutableID が objectGUID の値と一致しない場合は、Okta 同期の既定値が変更されています。ImmutableID を決定するために別の属性が選択されている可能性があります。 次のセクションに進む前に、どのソース属性が ImmutableID に設定されているかを特定することが重要です。      Okta 同期を無効にする前に、Okta が同期している属性を更新する必要があります。

## <a name="step-3---install-azure-ad-connect-in-staging-mode"></a>手順 3 - ステージング モードで Azure AD Connect をインストールする

ソースと移行先ターゲットの一覧が準備できたら、Azure AD Connect サーバーをインストールします。 Azure AD Connect クラウド プロビジョニングを使用することを選択した場合は、このセクションをスキップしてください。

1. 選択したサーバーへの [Azure AD Connect のダウンロードとインストール](../hybrid/how-to-connect-install-custom.md)を続行します。 

2. **[ユーザーの識別]** ページの **[Azure AD でのユーザーの識別方法を選択します]** で、 **[特定の属性を選択します]** のラジアルを選択します。 次に、Okta の既定値を変更していない場合は、 **[mS-DS-ConsistencyGUID]** を選択します。

   >[!WARNING]
   >これは、このページで **[次へ]** を選択する前に行う最も重要な手順です。 ソース アンカーに対して選択している属性が、既存の Azure AD ユーザーに **現在** 設定されているものであることを確認する必要があります。 間違った属性を選択すると、このオプションをもう一度選択するために、Azure AD Connect をアンインストールしてから再インストールすることが必要になります。

   ![Consistency GUID を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

3. **[構成]** ページで、 **[ステージング モードを有効にする]** のチェックボックスをオンにしたことを確認してから、 **[インストール]** を選択します。

   ![[ステージング モードを有効にする] を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

4. 構成が完了したら、 **[終了]** を選択します。

ステージング モードを終了する前に、ImmutableID が正確に一致していることを確認することが重要です。

1. 同期サービスを **管理者** として開きます。

   ![同期サービスを開く様子を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

2. まず、domain.onmicrosoft.com コネクタ スペースへの [完全同期] について、 **[Connectors with Flow Updates]\(フロー更新を含むコネクタ\)** タブの下にユーザーが表示されていることを確認します。

   ![[Connector with Flow Update]\(フロー更新を含むコネクタ\) を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

3. 次に、エクスポートで保留中の削除が存在しないことを確認します。 **[コネクタ]** タブを選択してから、domain.onmicrosoft.com コネクタ スペースを強調表示します。 次に、 **[コネクタ スペースの検索]** を選択します。

   ![[コネクタ スペースの検索] を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

4. [コネクタ スペースの検索] で、[スコープ] ドロップダウンを選択し、 **[保留中のエクスポート]** を選択します。

   ![[保留中のエクスポート] を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

5. **[削除]** をオンにしてから、 **[検索]** を選択します。すべてのオブジェクトが正しく一致している場合は、[削除] に対して一致するレコードは存在しないはずです。 削除を保留しているオブジェクトとそのオンプレミスの値を記録します。

   ![削除された一致レコードを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

6. 次に、 **[削除]** をオフにし、 **[追加] と [変更]** をオンにしてから、検索を実行します。 Okta 経由で Azure AD に現在同期されているすべてのユーザーには [更新] の機能が表示されます。 現在は Okta によって同期されていないものの、Azure AD Connect のインストール時に選択された組織単位 (OU) 構造には存在する新しいオブジェクトをすべて追加します。

   ![新しいオブジェクトの追加を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

7. 更新の行をダブルクリックすると、どの Azure AD Connect が Azure AD と通信するかが表示されます。

8. Azure AD に既に存在するユーザーに対して [追加] 機能がある場合、そのユーザーのオンプレミスのアカウントがクラウド アカウントと一致しておらず、そのアカウントによって新しいオブジェクトが作成されると AD Connect が判断したことを意味します。予期されていないすべての新しい追加を記録します。 ステージング モードを終了する前に、Azure AD の ImmutableID 値を修正してください。

   この例では、オンプレミスの値が正しく入力されていなかったにもかかわらず、Okta はメール属性をユーザーのアカウントにスタンプしていました。 Azure AD Connect によって John Smith のアカウントを引き継ぐと、メール属性はこのユーザーのオブジェクトから削除されます。

   更新内容に Azure AD に必要なすべての属性が含まれていることを確認します。 複数の属性を削除する場合、ステージング モードを削除する前に、それらのオンプレミスの AD 値を手動で設定することが必要になる場合があります。

   ![オンプレミスの AD 値を設定する様子を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >次の手順に進む前に、すべてのユーザー属性が正しく同期されていることと、 **[保留中のエクスポート]** タブに想定どおりに表示されていることを確認します。 それらが削除されている場合は、それらの ImmutableID が一致していることと、同期のために選択されている OU のいずれかにそのユーザーが含まれていることを確認します。

## <a name="step-4---install-azure-ad-cloud-sync-agents"></a>手順 4 - Azure AD クラウド同期エージェントをインストールする

ソースと移行先ターゲットの一覧が準備できたら、[Azure AD クラウド同期エージェントをインストールして構成](../cloud-sync/tutorial-single-forest.md)します。 Azure AD Connect サーバーを使用することを選択した場合は、このセクションをスキップしてください。

## <a name="step-5---disable-okta-provisioning-to-azure-ad"></a>手順 5 - Azure AD への Okta プロビジョニングを無効にする

Azure AD Connect のインストールを検証し終え、保留中のエクスポートが適切な状態になったら、Azure AD への Okta プロビジョニングを無効にします。

1. Okta ポータルに移動し、 **[アプリケーション]** を選択してから、Azure AD に対してユーザーをプロビジョニングするために使用する Okta アプリを選択します。 [プロビジョニング] タブの **[統合]** セクションを開きます。

   ![Okta の [統合] セクションを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

2. **[編集]** を選択し、 **[API 統合を有効にする]** オプションをオフにして、 **[保存]** を選択します。

   ![Okta の [API 統合を有効にする] の編集を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Azure AD へのプロビジョニングを処理する Office 365 アプリが複数ある場合は、すべてがオフになっていることを確認します。

## <a name="step-6---disable-staging-mode-in-azure-ad-connect"></a>手順 6 - Azure AD Connect でステージング モードを無効にする

Okta プロビジョニングを無効にすると、Azure AD Connect サーバーでオブジェクトの同期を開始できる状態になります。 Azure AD クラウド同期エージェントを使用することを選択した場合は、このセクションをスキップしてください。

1. デスクトップからインストール ウィザードをもう一度実行し、 **[構成]** を選択します。

   ![Azure AD Connect サーバーを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

2. **[ステージング モードの構成]** を選択してから **[次へ]** を選択し、グローバル管理者の資格情報を入力します。

   ![ステージング モードの構成を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

3. **[ステージング モードを有効にする]** をオフにして、[次へ] を選択します。

   ![[ステージング モードを有効にする] をオフにする様子を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

4. **[構成]** を選択して続行します。

   ![構成の準備ができていることを示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

5. 構成が完了したら、管理者として **同期サービス** を開きます。 domain.onmicrosoft.com コネクタの [エクスポート] を確認します。 すべての追加、更新、削除が想定どおりに処理されていることを確認します。

   ![同期サービスの確認を示す画像](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

これで、Azure AD Connect サーバー ベースのプロビジョニングへの移行が正常に完了しました。 インストール ウィザードを再実行することにより、Azure AD Connect の機能セットの更新と拡張を実行できます。

## <a name="step-7---enable-cloud-sync-agents"></a>手順 7 - クラウド同期エージェントを有効にする

Okta プロビジョニングを無効にすると、Azure AD クラウド同期エージェントでオブジェクトの同期を開始できる状態になります。[Azure AD Portal](https://aad.portal.azure.com/) に戻ります。

1. **[構成プロファイル]** を **[有効]** に変更します。

2. 有効にしたら、プロビジョニング メニューに戻り、 **[ログ]** を選択します。

3. プロビジョニング コネクタによってインプレース オブジェクトが適切に更新されていることを確認します。 クラウド同期エージェントは非破壊的です。 一致が正しく処理されないと、更新に失敗します。

4. ユーザーが一致しない場合は、必要な更新を行って immutableID をバインドしてから、クラウド プロビジョニング同期を再開します。

## <a name="next-steps"></a>次のステップ

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)

- [Okta フェデレーションを Azure AD マネージド認証に移行する](migrate-okta-federation-to-azure-active-directory.md)

- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
