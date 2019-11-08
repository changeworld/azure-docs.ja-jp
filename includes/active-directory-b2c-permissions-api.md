---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 462c1fca0ecd706c1bf04ac5a0ef8561321e05bc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474832"
---
#### <a name="applicationstabapplications"></a>[アプリケーション](#tab/applications/)

1. **[アプリケーション]** を選択し、API へのアクセスを必要とする Web アプリケーションを選択します。 たとえば、*webapp1* とします。
1. **[API アクセス]** を選択し、 **[追加]** を選択します。
1. **[API の選択]** ボックスの一覧で、Web アプリケーションにアクセスを許可する API を選択します。 たとえば、*webapi1* とします。
1. **[スコープの選択]** ボックスの一覧で、先ほど定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[OK]** を選択します。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. **[アプリの登録 (プレビュー)]** を選択し、API へのアクセスを必要とする Web アプリケーションを選択します。 たとえば、*webapp1* とします。
1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の下で **[アクセス許可の追加]** を選択します。
1. **[自分の API]** タブを選択します。
1. Web アプリケーションにアクセスを許可する API を選択します。 たとえば、*webapi1* とします。
1. **[アクセス許可]** で、 **[デモ]** を展開し、前に定義したスコープを選択します。 *demo.read* や *demo.write* などです。
1. **[アクセス許可の追加]** を選択します. 指示に従って、数分待ってから次の手順に進みます。
1. **[(ご自身のテナント名) に管理者の同意を与えます]** を選択します。
1. 現在サインインしているお使いの管理者アカウントを選択するか、少なくとも*クラウド アプリケーション管理者* ロールが割り当てられているお使いの Azure AD B2C テナントのアカウントでサインインします。
1. **[Accept]\(承認\)** を選択します。
1. **[更新]** を選択し、両方のスコープの **[状態]** に、"... に付与されました" が表示されていることを確認します。 アクセス許可が反映されるまでに数分かかる場合があります。