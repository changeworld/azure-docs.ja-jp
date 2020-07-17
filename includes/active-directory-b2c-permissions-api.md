---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875685"
---
#### <a name="applications"></a>[アプリケーション](#tab/applications/)

1. **[アプリケーション]** を選択し、API へのアクセスを必要とする Web アプリケーションを選択します。 たとえば、*webapp1* とします。
1. **[API アクセス]** を選択し、 **[追加]** を選択します。
1. **[API の選択]** ボックスの一覧で、Web アプリケーションにアクセスを許可する API を選択します。 たとえば、*webapi1* とします。
1. **[スコープの選択]** ボックスの一覧で、先ほど定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[OK]** を選択します。

#### <a name="app-registrations-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. **[アプリの登録 (プレビュー)]** を選択し、API へのアクセスを必要とする Web アプリケーションを選択します。 たとえば、*webapp1* とします。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. Web アプリケーションにアクセスを許可する API を選択します。 たとえば、*webapi1* とします。
1. **[アクセス許可]** で、 **[デモ]** を展開し、前に定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[アクセス許可の追加]** を選択します.
1. **[<テナント名> に管理者の同意を与えます]** を選択します。
1. アカウントを選択するよう求めるメッセージが表示されたら、現在サインインしている管理者アカウントを選択するか、少なくとも "*クラウド アプリケーション管理者*" ロールが割り当てられている Azure AD B2C テナントのアカウントでサインインします。
1. **[はい]** を選択します。
1. **[更新]** を選択し、両方のスコープの **[状態]** に、"... に付与されました" が表示されていることを確認します。
