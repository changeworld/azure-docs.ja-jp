---
title: Azure Active Directory Connect クラウド同期パスワード ライトバックを有効にする
description: このチュートリアルでは、Azure AD Connect クラウド同期を使用して Azure AD セルフサービス パスワード リセットのライトバックを有効にし、変更をオンプレミスの Active Directory Domain Services 環境に同期する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 10/25/2021
ms.author: justinha
author: justinha
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4, ignite-fall-2021
ms.openlocfilehash: 17bc0c700aee1a351371f35e7c84e95312214088
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132550308"
---
# <a name="tutorial-enable-cloud-sync-self-service-password-reset-writeback-to-an-on-premises-environment-preview"></a>チュートリアル: クラウド同期セルフサービス パスワード リセットのオンプレミス環境へのライトバックを有効にする (プレビュー)

Azure Active Directory Connect クラウド同期を使用すると、Azure AD パスワードの変更を、切断されているオンプレミスの Active Directory Domain Services (AD DS) ドメインのユーザー間でリアルタイムに同期できます。 Azure AD Connect クラウド同期のパブリック プレビューは、ドメイン レベルで [Azure Active Directory Connect](tutorial-enable-sspr-writeback.md) と side-by-side で実行して、追加のシナリオ (たとえば、会社が分割または合併されたためにユーザーが切断されたドメインに含まれているなど) でのパスワード ライトバックを簡略化できます。 さまざまなドメイン内の各サービスを、ニーズに応じてさまざまなユーザー セットが対象になるように構成できます。 Azure Active Directory Connect クラウド同期では、軽量の Azure AD クラウド プロビジョニング エージェントを使用してセルフサービス パスワード リセット (SSPR) のライトバックのセットアップを簡略化し、クラウド内のパスワード変更をオンプレミスのディレクトリに安全に送信できます。 

Azure Active Directory Connect クラウド同期のセルフサービス パスワード リセットのライトバックは、パブリック プレビューの一環としてサポートされています。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="prerequisites"></a>前提条件 

- 少なくとも Azure AD Premium P1 または試用版ライセンスが有効になっている Azure AD テナント。 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- 次のいずれかを含むアカウント:
  - [認証ポリシー管理者](../roles/permissions-reference.md#authentication-policy-administrator)および[ハイブリッド ID 管理者](../roles/permissions-reference.md#hybrid-identity-administrator)のロール
  - [全体管理者](../roles/permissions-reference.md#global-administrator)ロール 
- セルフサービス パスワード リセット用に構成された Azure AD。 必要に応じて、このチュートリアルを完了して Azure AD SSPR を有効にします。 
- Azure AD Connect クラウド同期のバージョン 1.1.587 以降を使用して構成されたオンプレミスの AD DS 環境。 必要に応じて、[こちらのチュートリアル](tutorial-enable-sspr.md)を使用して Azure AD Connect クラウド同期を構成します。 
- Azure AD Connect クラウド同期でパスワード ライトバックを有効にするには、署名済みの PowerShell スクリプトを実行する必要があります。
  - PowerShell 実行ポリシーでスクリプトの実行が許可されていることを確認します。 
  - インストール中に推奨される実行ポリシーは "RemoteSigned" です。 
  - PowerShell 実行ポリシーの設定の詳細については、「[Set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy)」を参照してください。 


## <a name="deployment-steps"></a>デプロイメントの手順

1. [Azure AD Connect クラウド同期サービス アカウントのアクセス許可を構成する](#configure-azure-ad-connect-cloud-sync-service-account-permissions)
1. [Azure AD Connect クラウド同期でパスワード ライトバックを有効にする](#enable-password-writeback-in-azure-ad-connect-cloud-sync)
1. [SSPR のパスワード ライトバックを有効にする](#enable-password-writeback-for-sspr)
 
### <a name="configure-azure-ad-connect-cloud-sync-service-account-permissions"></a>Azure AD Connect クラウド同期サービス アカウントのアクセス許可を構成する 

クラウド同期のアクセス許可は、既定では構成済みになっています。 アクセス許可をリセットする必要がある場合は、「[トラブルシューティング](#troubleshooting)」を参照して、パスワード ライトバックに必要な特定のアクセス許可と PowerShell を使用してそれらを設定する方法の詳細を確認してください。 

### <a name="enable-password-writeback-in-azure-ad-connect-cloud-sync"></a>Azure AD Connect クラウド同期でパスワード ライトバックを有効にする

パブリック プレビューの場合は、Set-AADCloudSyncPasswordWritebackConfiguration コマンドレットとテナントの全体管理者の資格情報を使用して、Azure AD Connect クラウド同期でパスワード ライトバックを有効にする必要があります。 

```powershell
Import-Module 'C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll' 
Set-AADCloudSyncPasswordWritebackConfiguration -Enable $true -Credential $(Get-Credential)
``` 

### <a name="enable-password-writeback-for-sspr"></a>SSPR のパスワード ライトバックを有効にする 

Azure AD Connect クラウド同期でパスワード ライトバックを有効にしたら、Azure AD セルフサービス パスワード リセット (SSPR) をパスワード ライトバック用に検証して構成します。 SSPR でパスワード ライトバックを使用できるようにすると、自分のパスワードを変更またはリセットするユーザーは、その更新したパスワードがオンプレミスの AD DS 環境にも同期されるようになります。 

SSPR でパスワード ライトバックを検証して有効にするには、次の手順を実行します。 

1. 全体管理者アカウントを使用して、Azure portal にサインインします。 
1. [Azure Active Directory] に移動し、 **[パスワード リセット]** を選択してから、 **[オンプレミスの統合]** を選択します。 
1. Azure AD Connect クラウド同期エージェントのセットアップが完了したことを確認します。
1. **[オンプレミスのディレクトリにパスワードをライトバックしますか?]** を **[はい]** に設定します。 
1. **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可しますか?]** を **[はい]** に設定します。
   
   ![ライトバックを有効にする方法を示すスクリーンショット。](media/tutorial-enable-sspr-cloud-sync-writeback/writeback.png)

1. 準備ができたら、 **[保存]** を選択します。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このドキュメントの一環として構成した SSPR パスワード ライトバック機能をもう使用しない場合は、次の手順を実行します。 

1. 全体管理者アカウントを使用して、Azure portal にサインインします。 
1. [Azure Active Directory] を検索して選択し、 **[パスワード リセット]** を選択してから、 **[オンプレミスの統合]** を選択します。 
1. **[オンプレミスのディレクトリにパスワードをライトバックしますか?]** を **[いいえ]** に設定します。 
1. **[パスワードをリセットせずにアカウントのロックを解除することをユーザーに許可しますか?]** を **[いいえ]** に設定します。 

Azure AD Connect クラウド同期サーバーから、全体管理者の資格情報を使用して `Set-AADCloudSyncPasswordWritebackConfiguration` を実行し、Azure AD Connect クラウド同期でのパスワード ライトバックを無効にします。 

```powershell
Import-Module ‘C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll’ 
Set-AADCloudSyncPasswordWritebackConfiguration -Enable $false -Credential $(Get-Credential)
```

## <a name="supported-operations"></a>サポート対象の操作

パスワード ライトバックは、エンドユーザーと管理者について次の状況で実行されます。


| Account        | サポート対象の操作 | 
|----------------|------------------------|
| エンド ユーザー      |  エンド ユーザーの自発的なパスワード変更操作。<br>エンドユーザーによる強制的なパスワード変更 (パスワードの期限切れなど)。<br>エンドユーザーにより、パスワード リセット ポータルから実行されたセルフサービス パスワード リセット。 |
| 管理者 |  管理者による自発的なパスワード変更。<br>管理者による強制的なパスワード変更 (パスワードの期限切れなど)。<br>管理者によりパスワード リセット ポータルから実行された管理者によるセルフサービス パスワード リセット。<br> Azure portal から管理者が開始したエンドユーザーのパスワードのリセット。<br>Microsoft Graph API から管理者が開始したエンドユーザーのパスワードのリセット。                       |

## <a name="unsupported-operations"></a>サポートされていない操作

パスワード ライトバックは、次の状況では実行されません。

| Account        | サポートされていない操作 | 
|----------------|------------------------|
| エンド ユーザー      | PowerShell コマンドレットまたは Microsoft Graph API を使った、エンド ユーザーによる自身のパスワードのリセット。                        |
| 管理者 | PowerShell コマンドレットを使って管理者が開始したエンド ユーザーのパスワードのリセット。<br>Microsoft 365 管理センターから管理者が開始したエンドユーザーのパスワードのリセット。<br>パスワード ライトバックの場合、管理者、または Azure AD の他のどの管理者も、パスワード リセット ツールを使用して自身のパスワードをリセットすることはできません。                        |

## <a name="validation-scenarios"></a>検証シナリオ

パスワード ライトバックを使用してシナリオを検証する場合は、次の操作を行ってみてください。 すべての検証シナリオでは、クラウド同期がインストールされていて、ユーザーがパスワード ライトバックのスコープに含まれている必要があります。  


|シナリオ|詳細 |
|--------|--------|
| ログイン ページからパスワードをリセットする | 切断されているドメインとフォレストの 2 人のユーザーが SSPR を実行するようにします。 また、Azure AD Connect とクラウド同期を side-by-side でデプロイし、クラウド同期構成のスコープ内に 1 人のユーザー、Azure AD Connect のスコープ内にもう 1 人を用意し、それらのユーザーが自身のパスワードをリセットすることもできます。 |
| 期限切れのパスワードの変更を強制する | 切断されているドメインとフォレストの 2 人のユーザーが期限切れのパスワードを変更するようにします。 また、Azure AD Connect とクラウド同期を side-by-side でデプロイし、クラウド同期構成のスコープ内に 1 人のユーザー、Azure AD Connect のスコープ内にもう 1 人を用意することもできます。 |
| 通常のパスワード変更 | 切断されているドメインとフォレストの 2 人のユーザーが通常のパスワード変更を行うようにします。 また、Azure AD Connect とクラウド同期を side-by-side にし、クラウド同期構成のスコープ内に 1 人のユーザー、Azure AD Connect のスコープ内にもう 1 人を用意することもできます。  |
| ユーザー パスワードの管理者リセット | 切断されているドメインとフォレストの 2 人のユーザーが、Azure 管理ポータルまたはフロントライン ワーカー ポータルから自身のパスワードをリセットするようにします。 また、Azure AD Connect とクラウド同期を side-by-side にし、クラウド同期構成のスコープ内に 1 人のユーザー、Azure AD Connect のスコープ内にもう 1 人を用意することもできます  |
| セルフサービス アカウントのロック解除 | 切断されているドメインとフォレストの 2 人のユーザーが、SSPR ポータルでアカウントのロックを解除してパスワードをリセットするようにします。 また、Azure AD Connect とクラウド同期を side-by-side にし、クラウド同期構成のスコープ内に 1 人のユーザー、Azure AD Connect のスコープ内にもう 1 人を用意することもできます。 |

## <a name="troubleshooting"></a>トラブルシューティング

Azure AD Connect クラウド同期グループのマネージド サービス アカウントには、既定では、パスワード ライトバックを行うために次のアクセス許可が設定されている必要があります。 

- [パスワードのリセット]
- lockoutTime に対する書き込みアクセス許可
- pwdLastSet に対する書き込みアクセス許可
- まだ設定して場合は、そのフォレスト内の "各ドメイン" のルート オブジェクトに対する "期限切れではないパスワード" の拡張権利。 

これらのアクセス許可が設定されていない場合は、Set-AADCloudSyncPermissions コマンドレットおよびオンプレミスのエンタープライズ管理者の資格情報を使用して、サービス アカウントに対する PasswordWriteBack アクセス許可を設定できます。 

```powershell
Import-Module ‘C:\\Program Files\\Microsoft Azure AD Connect Provisioning Agent\\Microsoft.CloudSync.Powershell.dll’ 
Set-AADCloudSyncPermissions -PermissionType PasswordWriteBack -EACredential $(Get-Credential)
```

アクセス許可を更新した後、ディレクトリ内のすべてのオブジェクトにこれらのアクセス許可がレプリケートされるまで最大で 1 時間以上かかる場合があります。 

これらのアクセス許可を割り当てないと、ライトバックが正しく構成されているように見えても、ユーザーがクラウドからオンプレミスのパスワードを更新するときにエラーが発生することがあります。 "期限切れではないパスワード" が表示されるためには、[このオブジェクトとすべての子オブジェクト] にアクセス許可が割り当てられている必要があります。 

一部のユーザー アカウントのパスワードがオンプレミスのディレクトリに書き戻されない場合は、オンプレミスの AD DS 環境でそのアカウントの継承が無効になっていないことを確認してください。 この機能を正常に動作させるには、パスワードの書き込みアクセス許可を子孫オブジェクトに適用する必要があります。 

オンプレミスの AD DS 環境のパスワード ポリシーによって、パスワードのリセットが正しく処理されない場合があります。 この機能をテストしていて、ユーザーのパスワードを 1 日に複数回リセットする場合は、[パスワードの変更禁止期間] のグループ ポリシーを 0 に設定する必要があります。 この設定は、[コンピューターの構成] > [ポリシー] > [Windows の設定] > [セキュリティの設定] > [アカウント ポリシー] の下の gpmc.msc にあります。 

グループ ポリシーを更新する場合は、更新されたポリシーがレプリケートされるまで待つか、gpupdate /force コマンドを使用します。 

パスワードがすぐに変更されるようにするには、 [パスワードの変更禁止期間] を 0 に設定する必要があります。 ただし、ユーザーがオンプレミスのポリシーに準拠していて、[パスワードの変更禁止期間] が 0 より大きい値に設定されている場合は、オンプレミスのポリシーが評価された後にパスワード ライトバックが機能しません。 

適切なアクセス許可を検証または設定する方法の詳細については、「[Azure AD Connect に対するアカウントのアクセス許可を構成する](tutorial-enable-sspr-writeback.md#configure-account-permissions-for-azure-ad-connect)」を参照してください。 

## <a name="next-steps"></a>次のステップ

- クラウド同期、および Azure AD Connect とクラウド同期の比較の詳細については、「[Azure AD Connect クラウド同期とは](../cloud-sync/what-is-cloud-sync.md)」を参照してください。
- Azure AD Connect を使用したパスワード ライトバックの設定に関するチュートリアルについては、「[チュートリアル: Azure Active Directory セルフサービス パスワード リセットのオンプレミス環境へのライトバックを有効にする](tutorial-enable-sspr-writeback.md)」を参照してください。
