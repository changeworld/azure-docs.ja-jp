---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: 6cb0ef848bdeab35c971ebd1a0b14eca1dfe3001
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523218"
---
#### <a name="applicationstabapplications"></a>[アプリケーション](#tab/applications/)

1. **[登録済みのアプリ]** 概要ページで、 **[設定]** を選択します。
1. **[API アクセス]** の下の、 **[必要なアクセス許可]** を選択します。
1. **[Windows Azure Active Directory]** を選択します。
1. **[アプリケーションのアクセス許可]** で **[ディレクトリ データの読み取りと書き込み]** を選択します。
1. **[保存]** を選択します。
1. **[アクセス許可の付与]** を選択し、 **[はい]** を選択します。 アクセス許可が完全に反映されるまでに数分かかる場合があります。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. **[管理]** の下にある **[API のアクセス許可]** を選択します。
1. **[構成されたアクセス許可]** の **[アクセス許可の追加]** を選択します。
1. **[Azure Active Directory Graph]** を選択します。
1. **[アプリケーションのアクセス許可]** を選択します。
1. **[ディレクトリ]** を展開し、 **[Directory.ReadWrite.All]** チェック ボックスをオンにします。
1. **[アクセス許可の追加]** を選択します. 指示に従って、数分待ってから次の手順に進みます。
1. **[(ご自身のテナント名) に管理者の同意を与えます]** を選択します。
1. テナント管理者のアカウントを選択します。
1. **[Accept]\(承認\)** を選択します。
1. **[更新]** を選択し、 **[状態]** に、"... に付与されました" が表示されていることを確認します。 アクセス許可が反映されるまでに数分かかる場合があります。