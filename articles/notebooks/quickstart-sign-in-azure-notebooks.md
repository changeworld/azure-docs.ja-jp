---
title: Azure Notebooks にサインインする
description: Azure Notebooks にすばやくサインインし、ユーザー ID を設定します。これにより、保存済みのプロジェクトにアクセスしたり、ノートブックを他のユーザーと共有したりできるようになります。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: fb8c94b1-6d0a-4b77-8d14-ae6efcdd99f4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: f595fbb6571c3deaaa0644ba9d62f598181592e3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53106313"
---
# <a name="quickstart-sign-in-and-set-a-user-id"></a>クイック スタート:サインインとユーザー ID の設定

サインインしなくても Azure Notebooks を常に表示できますが、ノートブックを実行し、保存されたプロジェクトとノートブックにアクセスし、他のユーザーとノートブックを共有するには、サインインする必要があります。

## <a name="sign-in"></a>[サインイン]

1. [notebooks.azure.com](https://notebooks.azure.com/) の右上にある **[Sign in]\(サインイン\)** を選択します。

    ![Azure Notebooks でのサインイン コマンドの場所](media/accounts/sign-in-command.png)

1. 入力を求められたら、Microsoft アカウントか、職場または学校アカウントのメール アドレスを入力して、**[次へ]** を選択します。 アカウントの種類は、「[Your user account for Azure Notebooks (Azure Notebook のユーザー アカウント)](azure-notebooks-user-account.md)」で説明されています。 Microsoft アカウントがない場合、または Azure Notebooks 専用のアカウントにしたい場合は、**[Create one]\(作成\)** を選択します。

    ![サインイン プロンプトでの Microsoft アカウント新規作成コマンド](media/accounts/create-new-microsoft-account.png)

1. パスワードを求められたら、入力します。

1. 初めてサインインする場合は、Azure Notebooks でアカウントへのアクセス許可を求められます。 **[はい]** を選択して続行します。

    ![アカウントのアクセス許可プロンプト](media/accounts/account-permission-prompt.png)

## <a name="set-a-user-id"></a>ユーザー ID を設定する

1. 初めてサインインするときに、"anon-idrca3" のような一時的なユーザー ID が割り当てられます。 "anon-" で始まるユーザー ID を使用しているときは常に、独自の ID の作成を求めるメッセージが表示されます。 ユーザー ID はプロジェクトとノートブックを共有するために取得する URL で使用されるので、一意でわかりやすいものを選択します。

    ![Azure Notebooks 用のユーザー ID を入力するプロンプト](media/accounts/create-user-id.png)

    **[No Thanks]\(必要ありません\)** を選択すると、サインインするたびにユーザー ID を要求するメッセージが表示され続けます。 ユーザー ID は、[ユーザー プロファイル](azure-notebooks-user-profile.md)でいつでも設定できます。

1. 正常にサインインすると、Azure Notebooks のパブリック プロファイル ページに移動します。そこでは、**[Edit Profile Information]\(プロファイル情報の編集\)** を選択して、情報の残りの部分を入力できます (詳しくは、「[Your profile and user ID (プロファイルとユーザー ID)](azure-notebooks-user-profile.md)」をご覧ください)。

    ![Azure Notebooks プロファイル ページの初期表示](media/accounts/profile-page-new.png)

## <a name="sign-out"></a>サインアウトする

サインアウトするには、ページの右上のユーザー名を選択して、**[Sign out]\(サインアウト\)** を選択します。

![Azure Notebooks でのサインアウト コマンドの場所](media/accounts/sign-out-command.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クイック スタート:ノートブックを作成して共有する](quickstart-create-share-jupyter-notebook.md)
