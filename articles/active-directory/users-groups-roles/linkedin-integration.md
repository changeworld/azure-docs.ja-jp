---
title: 組織の LinkedIn サービスへの同意 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory で Microsoft アプリの LinkedIn の統合を有効または無効にする方法について説明します。
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: abcb1696efe44293d01153aa37a9835ba5f43370
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58199700"
---
# <a name="consent-to-linkedin-integration-for-your-azure-active-directory-organization"></a>Azure Active Directory 組織の LinkedIn の統合に同意する

この記事では、Azure Active Directory (Azure AD) 管理センターで組織の LinkedIn の統合を有効または無効にする方法について説明します。

> [!IMPORTANT]
> LinkedIn の統合設定は、現在 Azure AD 組織にロールアウト中です。 組織にロールアウトされると、既定で有効になります。
> 
> 例外:
> * 設定は、米国政府機関用 Microsoft Cloud、Microsoft Cloud Germany、または中国の 21 vianet によって運用される Azure および Office 365 を使用しているお客様はご利用いただけません。
> * 設定は、ドイツでプロビジョニングされたテナントの既定によりオフになります。 Microsoft Cloud Germany を使用しているお客様は設定を使用できませんのでご注意ください。
> * 設定は、フランスでプロビジョニングされたテナントの既定によりオフになります。
>
> この統合が機能するのは、その機能を有効にしている場合で、*かつ*アプリがユーザーの代わりに会社のデータにアクセスすることにユーザーが同意した後になります。 ユーザーの同意設定については、「[アプリケーションへのユーザー アクセスの削除方法](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment)」を参照してください。

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-the-azure-portal"></a>ユーザーに対する LinkedIn の統合を Azure portal で有効または無効にする

テナント全体またはテナント内の選択したユーザーに対してのみ LinkedIn の統合を有効または無効にできます。

1. [Azure Active Directory 管理センター](https://aad.portal.azure.com/)に、Azure AD テナントの全体管理者のアカウントでサインインします。
2. **[ユーザー]** を選択します。
3. **[ユーザー]** ウィンドウで、**[ユーザー設定]** を選択します。
4. **[LinkedIn アカウント接続]** で次の操作を行います。

   * 組織内のすべてのユーザーが各自のアカウントを接続して一部の Microsoft アプリ内の LinkeId 連絡先へのアクセス権限を取得することに同意するには、**[はい]** を選択してください。
   * 組織内の選択されたユーザーだけが各自のアカウントを接続して一部の Microsoft アプリ内の LinkeId 連絡先へのアクセス権限を取得することに同意するには、**[選択]** (Selected) を選択してください。
   * 組織内のユーザーが各自のアカウントを接続して一部の Microsoft アプリ内の LinkeId 連絡先へのアクセス権限を取得するという同意を取り消すには、**[いいえ]** を選択してください。
    ![組織内で LinkedIn の統合を有効にする](./media/linkedin-integration/linkedin-integration.png)
5. **[保存]** を選択して完了し、設定を保存する。

## <a name="enable-or-disable-linkedin-integration-for-your-users-in-group-policy"></a>ユーザーに対する LinkedIn の統合をグループ ポリシーで有効または無効にする

1. [Office 2016 管理用テンプレート ファイル (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) をダウンロードします。
2. **ADMX** ファイルを抽出して中央のストアにコピーします。
3. [グループ ポリシーの管理] を開きます。
4. 次の設定を使用してグループ ポリシー オブジェクトを作成します。**[ユーザーの構成]** > **[管理用テンプレート]** > **[Microsoft Office 2016]** > **[その他]** > **[LinkedIn の機能を Office アプリケーションで表示]**。
5. **[有効]** または **[無効]** を選択します。
  
   状態 | 効果
   ------ | ------
   **有効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が有効になります。 組織内のユーザーがその Office アプリケーションで LinkedIn の機能を使用することはできません。
   **無効** | Office 2016 オプションで **Office アプリケーションでの LinkedIn の表示機能** 設定が無効になり、エンドユーザーはこの設定を変更できません。 組織内のユーザーがその Office 2016 アプリケーションで LinkedIn の機能を使用することはできません。

このグループ ポリシーが適用されるのは、ローカル コンピューターの Office 2016 アプリだけです。 ユーザーが各自の Office 2016 アプリで LinkedIn を無効にした場合でも、Office 365 全体でプロフィール カードに LinkedIn の機能が表示されます。

## <a name="learn-more"></a>詳細情報

* [組織の LinkedIn を統合します](linkedin-user-consent.md)

* [Microsoft アプリでの LinkedIn の情報と機能](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn ヘルプ センター](https://www.linkedin.com/help/linkedin)

## <a name="next-steps"></a>次の手順

次のリンクをクリックすると、現在の LinkedIn の統合設定を Azure Portal で確認できます。

[Azure portal で現在の LinkedIn の統合設定を確認する](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
