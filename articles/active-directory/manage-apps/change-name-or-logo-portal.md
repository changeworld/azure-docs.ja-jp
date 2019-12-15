---
title: Azure AD で エンタープライズ アプリケーションの名前またはロゴを変更する
description: Azure Active Directory でカスタム エンタープライズ アプリケーションの名前またはロゴを変更する方法
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3dfe0f8788275dd4403b6c9cad99a8eb09e479b
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274713"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory でカスタム エンタープライズ アプリケーションの名前またはロゴを変更する

Azure Active Directory (Azure AD) で、カスタム エンタープライズ アプリの名前またはロゴを簡単に変更できます。 これらを変更するには、適切なアクセス許可が必要であり、カスタム アプリケーションの作成者である必要があります。

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>エンタープライズ アプリケーションの名前またはロゴを変更する方法

1. [Azure Active Directory ポータル](https://aad.portal.azure.com/)に、ディレクトリの全体管理者のアカウントでサインインします。 **Azure Active Directory 管理センター**のページが表示されます。
2. 左側のウィンドウで、 **[エンタープライズ アプリケーション]** を選択します。 エンタープライズ アプリケーションの一覧が表示されます。
3. アプリケーションを選択します。 アプリケーションの概要ページが表示されます。
4. アプリケーションの概要ウィンドウの **[管理]** 見出しで、 **[プロパティ]** を選択します。 **[プロパティ]** ページが表示されます。
5. 名前を変更する場合は、 **[名前]** ボックスを選択し、新しい名前を入力して **Enter** キーを押します。
6. ロゴを変更する場合、 **[ロゴ]** フィールドで、アプリケーションの現在のロゴ画像の下にある **[ファイルを選択]** ボックスの横のフォルダー アイコンを選択します。

   ![[プロパティ] を選択する](./media/change-name-or-logo-portal/change-logo.png)

   または、ロゴを変更しない場合、手順 8 に移動します。
7. ファイル ピッカーで、新しいロゴに指定したいファイルを選択します。 現在のロゴ画像の下にあるボックスにファイル名が表示されます。

   > [!NOTE]
   > Azure で指定できるロゴ画像は PNG ファイルで、幅、高さ、ファイル サイズに制限があります。
8. **[保存]** を選択します。 新しいロゴを選択した場合、 **[ロゴ]** フィールドの画像が新しいロゴ ファイルに変化します。

## <a name="next-steps"></a>次の手順

* [クイック スタート:Azure Active Directory で組織のグループとメンバーを表示する](../fundamentals/active-directory-groups-view-azure-portal.md)
* [エンタープライズ アプリケーションにユーザーまたはグループを割り当てる](assign-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションからユーザーまたはグループの割り当てを削除する](remove-user-or-group-access-portal.md)
* [エンタープライズ アプリケーションのユーザー サインインを無効にする](disable-user-sign-in-portal.md)
