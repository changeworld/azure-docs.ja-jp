---
title: Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行するためのチュートリアル
titleSuffix: Active Directory
description: このチュートリアルでは、Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する方法について説明します。
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/01/2021
ms.author: gasinh
ms.subservice: app-mgmt
ms.openlocfilehash: 0902fbd761cb66eb5dbee4a5e3406ecba688c0b7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451485"
---
# <a name="tutorial-migrate-okta-sync-provisioning-to-azure-ad-connect-based-synchronization"></a>チュートリアル: Okta 同期プロビジョニングを Azure AD Connect ベースの同期に移行する

このチュートリアルでは、現時点で組織が Okta から Azure Active Directory (Azure AD) にユーザー プロビジョニングを移行し、ユーザー同期またはユニバーサル同期を Azure AD Connect に移行するための方法について説明します。 この機能により、Azure AD や Office 365 へのプロビジョニングをさらに強化できるようになります。

同期プラットフォームの移行は、小さな変更ではありません。 この記事で説明されているプロセスの各手順は、ステージング モードから Azure AD Connect を削除したり、Azure AD クラウド プロビジョニング エージェントを有効にしたりする前に、実際の環境に対して検証する必要があります。

## <a name="prerequisites"></a>前提条件

Okta プロビジョニングから Azure AD に切り替える場合は、2 つの選択肢があります。 Azure AD Connect サーバーまたは Azure AD クラウド プロビジョニングを使用できます。 2 つの違いを理解するためには、[Microsoft がまとめた比較記事](../cloud-sync/what-is-cloud-sync.md#comparison-between-azure-ad-connect-and-cloud-sync)をご覧ください。

Azure AD クラウド プロビジョニングは、ユニバーサル同期やユーザー同期を使用している Okta のお客様にとって最もわかりやすい移行パスです。クラウド プロビジョニング エージェントは軽量です。 Okta ディレクトリ同期エージェントのようにドメイン コントローラー上かその近くにインストールできます。 これらを同じサーバーにはインストールしないでください。

ユーザーを同期するときに、お客様の組織で次のいずれかのテクノロジを利用する必要がある場合は、Azure AD Connect サーバーを使用してください。

- デバイスの同期: Hybrid Azure AD Join または Hello for Business
- パススルー認証
- 150,000 を超えるオブジェクトのサポート
- 書き戻しのサポート

>[!NOTE]
>Azure AD Connect や Azure AD クラウド プロビジョニングをインストールする場合は、すべての前提条件を考慮する必要があります。 インストールを続行する前に、「[Azure AD Connect の前提条件](../hybrid/how-to-connect-install-prerequisites.md)」で詳細を確認してください。

## <a name="confirm-immutableid-attribute-synchronized-by-okta"></a>Okta によって同期された ImmutableID 属性を確認する

ImmutableID は、同期されるオブジェクトをオンプレミスの対応物に関連付けるために使用される主要な属性です。 オンプレミス オブジェクトの Active Directory objectGUID が Okta で受け取られると、それは Base64 でエンコードされた文字列に変換されます。 次に、既定でその文字列が Azure AD の ImmutableID フィールドにスタンプされます。

Azure AD PowerShell に接続すると、現在の ImmutableID 値を確認できます。 Azure AD PowerShell モジュールを使用したことがない場合は管理 PowerShell セッションで `Install-Module AzureAD` を実行し、それから次のコマンドを実行します。

```Powershell
Import-module AzureAD
Connect-AzureAD
```

このモジュールが既にあり、それが最新でない場合、最新バージョンに更新するように求める警告が表示されることがあります。

モジュールがインストールされたら、それをインポートし、次の手順に従って Azure AD サービスに接続します。

1. 最新の認証ウィンドウにグローバル管理者の資格情報を入力します。

   ![import-module を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/import-module.png)

1. テナントに接続した後、ImmutableID 値の設定を確認します。 次に示す例では、objectGUID の Okta の既定値が ImmutableID に使用されています。

   ![ImmutableID が objectGUID の Okta の既定値になっていることを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/okta-default-objectid.png)

1. オンプレミスで objectGUID から Base64 への変換を手動で確認するには、いくつかの方法があります。 個々の検証では次の例を使用します。

   ```PowerShell
   Get-ADUser onpremupn | fl objectguid
   $objectguid = 'your-guid-here-1010'
   [system.convert]::ToBase64String(([GUID]$objectGUID).ToByteArray())
   ```

   ![Okta objectGUID を ImmutableID に手動で変更する方法を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/manual-objectguid.png)

## <a name="mass-validation-methods-for-objectguid"></a>objectGUID の一括検証メソッド

Azure AD Connect に一括移行する前に、Azure AD の ImmutableID 値がオンプレミスのそれらの値と完全に一致することを検証することが重要です。

この例では、オンプレミスの Azure AD ユーザー *すべて* を取得し、それらの objectGUID 値と計算済みの ImmutableID 値のリストを CSV ファイルにエクスポートします。

1. オンプレミスのドメイン コントローラー上の PowerShell で次のコマンドを実行します。

   ```PowerShell
   Get-ADUser -Filter * -Properties objectGUID | Select-Object
   UserPrincipalName, Name, objectGUID, @{Name = 'ImmutableID';
   Expression = {
   [system.convert\]::ToBase64String(([GUID\]\$_.objectGUID).ToByteArray())
   } } | export-csv C:\\Temp\\OnPremIDs.csv
   ```

   ![オンプレミスのドメイン コントローラーのコマンドを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/domain-controller.png)

1. Azure AD PowerShell セッションで次のコマンドを実行して、既に同期されている値を収集します。

   ```powershell
   Get-AzureADUser -all $true | Where-Object {$_.dirsyncenabled -like
   "true"} | Select-Object UserPrincipalName, @{Name = 'objectGUID';
   Expression = {
   [GUID][System.Convert]::FromBase64String($_.ImmutableID) } },
   ImmutableID | export-csv C:\\temp\\AzureADSyncedIDS.csv
   ```

   ![Azure AD PowerShell セッションを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-powershell.png)

   両方のエクスポートを取得したら、各ユーザーの ImmutableID が一致することを確認します。

   >[!IMPORTANT]
   >クラウドの ImmutableID 値が objectGUID 値と一致しない場合は、Okta 同期の既定値が変更されています。ImmutableID 値を決定するために別の属性が選択されている可能性があります。 次のセクションに進む前に、どのソース属性が ImmutableID 値に設定されているかを特定することが重要です。 Okta 同期を無効にする前に、Okta が同期している属性を更新する必要があります。

## <a name="install-azure-ad-connect-in-staging-mode"></a>ステージング モードで Azure AD Connect をインストールする

ソースと移行先ターゲットの一覧が準備できたら、Azure AD Connect サーバーをインストールします。 Azure AD Connect クラウド プロビジョニングを使用することを選択した場合は、このセクションをスキップしてください。

1. 選択したサーバーへの [Azure AD Connect のダウンロードとインストール](../hybrid/how-to-connect-install-custom.md)を続行します。

1. **[ユーザーの識別]** ページの **[Azure AD でのユーザーの識別方法を選択します]** で、 **[特定の属性を選択します]** オプションを選択します。 次に、Okta の既定値を変更していない場合は、 **[mS-DS-ConsistencyGUID]** を選択します。

   >[!WARNING]
   >これは、このページで最も重要な手順です。 **[次へ]** を選択する前に、ソース アンカーに対して選択している属性が、既存の Azure AD ユーザーに "*現在*" 設定されているものであることを確認する必要があります。 間違った属性を選択すると、このオプションをもう一度選択するために、Azure AD Connect をアンインストールしてから再インストールすることが必要になります。
   
   ![mS-DS-ConsistencyGuid を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/consistency-guid.png)

1. **[構成]** ページで、 **[ステージング モードを有効にする]** のチェックボックスをオンにしたことを確認します。 その後、 **[インストール]** を選択します。

   ![[ステージング モードを有効にする] チェック ボックスを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/enable-staging-mode.png)

1. 構成が完了したら、 **[終了]** を選択します。

   ステージング モードを終了する前に、ImmutableID 値が適切に一致することを確認します。

1. 管理者として **Synchronization Service** を開きます。

   ![Synchronization Service を開いていることを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/open-sync-service.png)

1. domain.onmicrosoft.com コネクタ スペースへの **[完全同期]** について、 **[フロー更新を含むコネクタ]** タブの下にユーザーが表示されていることを確認します。

   ![[フロー更新を含むコネクタ] タブを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/connector-flow-update.png)

1. エクスポートで保留中の削除が存在しないことを確認します。 **[コネクタ]** タブを選択してから、domain.onmicrosoft.com コネクタ スペースを強調表示します。 次に、 **[コネクタ スペースの検索]** を選択します。

   ![[コネクタ スペースの検索] アクションを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/search-connector-space.png)

1. **[コネクタ スペースの検索]** ダイアログで、 **[スコープ]** ドロップダウンを選択し、 **[保留中のエクスポート]** を選択します。

   ![[保留中のエクスポート] を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/pending-export.png)

1. **[削除]** を選択してから、 **[検索]** を選択します。 すべてのオブジェクトが適切に一致している場合は、 **[削除]** に対して一致するレコードは存在しないはずです。 削除を保留しているオブジェクトとそのオンプレミスの値を記録します。

   ![削除された一致レコードを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/delete-matching-records.png)

1. **[削除]** をオフにし、 **[追加]** と **[変更]** をオンにしてから、検索を実行します。 Okta 経由で Azure AD に現在同期されているすべてのユーザーには [更新] の機能が表示されます。 現在は Okta によって同期されていないものの、Azure AD Connect のインストール時に選択された組織単位 (OU) 構造には存在する新しいオブジェクトをすべて追加します。

   ![新しいオブジェクトの追加を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/add-new-object.png)

1. 更新の行をダブルクリックすると、どの Azure AD Connect が Azure AD と通信するかが表示されます。

1. Azure AD に既に存在するユーザーに対して "**追加**" 機能がある場合、そのオンプレミスのアカウントはクラウド アカウントと一致しません。 AD Connect では、それによって新しいオブジェクトが作成され、予期されていないすべての新しい追加が記録されると判断しています。 ステージング モードを終了する前に、Azure AD の ImmutableID 値を修正してください。

   この例では、オンプレミスの値が正しく入力されていなかったにもかかわらず、Okta は "**メール**" 属性をユーザーのアカウントにスタンプしました。 Azure AD Connect によって John Smith のアカウントを引き継ぐと、"**メール**" 属性はこのユーザーのオブジェクトから削除されます。

   更新内容に Azure AD に必要なすべての属性が含まれていることを確認します。 複数の属性を削除する場合、ステージング モードを削除する前に、それらのオンプレミスの AD 値を手動で設定することが必要になる場合があります。

   ![オンプレミスの追加の値の設定を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/on-premises-ad-values.png)

   >[!NOTE]
   >次の手順に進む前に、すべてのユーザー属性が正しく同期されていることと、 **[保留中のエクスポート]** タブに想定どおりに表示されることを確認します。 それらが削除されている場合は、それらの ImmutableID の値が一致していることと、同期のために選択されている OU のいずれかにそのユーザーが含まれていることを確認します。

## <a name="install-azure-ad-cloud-sync-agents"></a>Azure AD クラウド同期エージェントをインストールする

ソースと移行先ターゲットの一覧が準備できたら、[Azure AD クラウド同期エージェントをインストールして構成](../cloud-sync/tutorial-single-forest.md)します。 Azure AD Connect サーバーを使用することを選択した場合は、このセクションをスキップしてください。

## <a name="disable-okta-provisioning-to-azure-ad"></a>Azure AD への Okta プロビジョニングを無効にする

Azure AD Connect のインストールを検証し終え、保留中のエクスポートが適切な状態になったら、Azure AD への Okta プロビジョニングを無効にします。

1. Okta ポータルに移動し、 **[アプリケーション]** を選択してから、Azure AD に対してユーザーをプロビジョニングするために使用する Okta アプリを選択します。 **[プロビジョニング]** タブを開いて、 **[統合]** セクションを選択します。

   ![Okta の [統合] セクションを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/integration-section.png)

1. **[編集]** を選択し、 **[API 統合を有効にする]** オプションをオフにして、 **[保存]** を選択します。

   ![Okta の [API 統合を有効にする] の編集を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/edit-api-integration.png)

   >[!NOTE]
   >Azure AD へのプロビジョニングを処理する Office 365 アプリが複数ある場合は、すべてがオフになっていることを確認します。

## <a name="disable-staging-mode-in-azure-ad-connect"></a>Azure AD Connect でステージング モードを無効にする

Okta プロビジョニングを無効にすると、Azure AD Connect サーバーでオブジェクトの同期を開始できる状態になります。 Azure AD クラウド同期エージェントを使用することを選択した場合は、このセクションをスキップしてください。

1. デスクトップからインストール ウィザードをもう一度実行し、 **[構成]** を選択します。

   ![Azure AD Connect サーバーを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/azure-ad-connect-server.png)

1. **[ステージング モードの構成]** を選択してから、 **[次へ]** を選択します。 グローバル管理者の資格情報を入力します。

   ![[ステージング モードの構成] オプションを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/configure-staging-mode.png)

1. **[ステージング モードを有効にする]** オプションをオフにし、 **[次へ]** を選択します。

   ![オフになっている [ステージング モードを有効にする] オプションを示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/uncheck-enable-staging-mode.png)

1. **[構成]** を選択して続行します。

   ![[構成] ボタンの選択を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/ready-to-configure.png)

1. 構成が完了したら、管理者として **同期サービス** を開きます。 domain.onmicrosoft.com コネクタの **[エクスポート]** を確認します。 すべての追加、更新、削除が想定どおりに処理されていることを確認します。

   ![同期サービスの確認を示すスクリーンショット。](./media/migrate-okta-sync-provisioning-to-azure-active-directory-connect-based-synchronization/verify-sync-service.png)

これで、Azure AD Connect サーバーベースのプロビジョニングへの移行が正常に完了しました。 インストール ウィザードを再実行することにより、Azure AD Connect の機能セットの更新と拡張を実行できます。

## <a name="enable-cloud-sync-agents"></a>クラウド同期エージェントを有効にする

Okta プロビジョニングを無効にすると、Azure AD クラウド同期エージェントでオブジェクトの同期を開始できる状態になります。 [Azure AD ポータル](https://aad.portal.azure.com/)に戻ります。

1. **[構成プロファイル]** を **[有効]** に変更します。

1. プロビジョニング メニューに戻り、 **[ログ]** を選択します。

1. プロビジョニング コネクタによってインプレース オブジェクトが適切に更新されていることを確認します。 クラウド同期エージェントは非破壊的です。 一致が正しく処理されないと、更新に失敗します。

1. ユーザーが一致しない場合は、必要な更新を行って immutableID 値をバインドします。 その後、クラウド プロビジョニング同期を再開します。

## <a name="next-steps"></a>次のステップ

Okta から Azure AD への移行の詳細については、以下を参照してください。

- [Okta から Azure AD にアプリケーションを移行する](migrate-applications-from-okta-to-azure-active-directory.md)
- [Okta フェデレーションを Azure AD マネージド認証に移行する](migrate-okta-federation-to-azure-active-directory.md)
- [Okta サインオン ポリシーを Azure AD の条件付きアクセスに移行する](migrate-okta-sign-on-policies-to-azure-active-directory-conditional-access.md)
