---
title: Azure Active Directory で LinkedIn 接続の統合を有効にする | Microsoft Docs
description: Azure Active Directory で Microsoft のアプリに対する LinkedIn アカウント接続を有効または無効にする方法について説明します
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 09/11/2018
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.openlocfilehash: 0eaa2656313ecd9b64503051265dc16285f0a1f3
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44492874"
---
# <a name="linkedin-account-connections"></a>LinkedIn アカウントの接続

この記事では、Azure Active Directory (Azure AD) 管理センターで、テナントに対する LinkedIn アカウント接続を有効または無効にする方法を説明します。

> [!IMPORTANT]
> LinkedIn アカウント接続の設定は、現在 Azure AD テナントにロールアウト中です。 テナントにロールアウトされると、既定で有効になります。 
> 
> 例外:
> * 設定は、米国政府機関用 Microsoft Cloud、Microsoft Cloud Germany、または中国の 21 vianet によって運用される Azure および Office 365 を使用しているお客様はご利用いただけません。
> * 設定は、ドイツでプロビジョニングされたテナントの既定によりオフになります。 Microsoft Cloud Germany を使用しているお客様は設定を使用できませんのでご注意ください。
> * 設定は、フランスでプロビジョニングされたテナントの既定によりオフになります。

> 統合は、それを有効にさせている場合 *、および*ユーザーに代わって会社のデータにアクセスするアプリへの同意をユーザーに許可している場合のみ動作します。 同意設定については、[アプリケーションへのユーザーのアクセスを削除する方法](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)を参照してください。

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-in-the-azure-portal"></a>Azure Portal でテナントに対する LinkedIn アカウント接続を有効または無効にする

テナント全体またはテナント内の選択したユーザーに対してのみ LinkedIn アカウント接続を有効または無効にできます。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com/)に、Azure AD テナントの全体管理者のアカウントでサインインします。
2. **[ユーザー]** を選択します。
3. **[ユーザー]** ウィンドウで、**[ユーザー設定]** を選択します。
4. **[LinkedIn アカウント接続]** で次の操作を行います。
  * テナント内のすべてのユーザーに対する LinkedIn アカウント接続を有効にするには、**[はい]** を選択します。
  * テナントの選択したユーザーのみに対する LinkedIn アカウント接続を有効にするには、**[選択済み]** を選択します。
  * すべてのユーザーの LinkedIn アカウント接続を無効にするようには、**[いいえ]** を選択します。![LinkedIn アカウント接続の有効化](./media/linkedin-integration/linkedin-integration.png)
5. **[保存]** を選択して完了し、設定を保存する。

## <a name="enable-or-disable-linkedin-account-connections-for-your-tenant-using-group-policy"></a>グループ ポリシー でテナントに対する LinkedIn アカウント接続を有効または無効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
2. **ADMX** ファイルを抽出して中央のストアにコピーします。
3. [グループ ポリシーの管理] を開きます。
4. **[ユーザーの構成]** > **[管理用テンプレート]** > **[Microsoft Office 2016]** > **[その他]** > **[Show LinkedIn features in Office applications]\(Office アプリケーションに LinkedIn の機能を表示する\)** の設定でグループ ポリシー オブジェクトを作成します。
5. **[有効]** または **[無効]** を選択します。
  
 State | 効果
------ | ------
**有効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が有効になります。 組織内のユーザーがその Office アプリケーションで LinkedIn の機能を使用することはできません。
 **無効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が無効になり、エンドユーザーはこの設定を変更できません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Office 365 全体でプロフィール カードに LinkedIn の機能が表示されます。

## <a name="learn-more"></a>詳細情報

* [組織の LinkedIn を統合します](linkedin-user-consent.md)

* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>次の手順
現在の LinkedIn アカウント接続設定を Azure Portal で確認するには、次のリンクをクリックします。

[LinkedIn アカウント接続を管理する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings) 