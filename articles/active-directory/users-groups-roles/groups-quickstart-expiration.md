---
title: Azure Active Directory 内の Office 365 グループの有効期限ポリシーのクイック スタート | Microsoft Docs
description: Office 365 グループの有効期限 - Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: quickstart
ms.date: 08/07/2018
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 7008943e9077cbad3c58de43f64b105f35931bf3
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "40208902"
---
# <a name="quickstart-set-office-365-groups-to-expire-in-azure-active-directory"></a>クイック スタート: Azure Active Directory における Office 365 グループの有効期限を設定する

このクイック スタートでは、Office 365 グループの有効期限ポリシーを設定します。 ユーザーが独自のグループを設定できるようになっていると、未使用のグループが増えてしまうことがあります。 未使用のグループを管理する 1 つの方法は、それらのグループに有効期限を設定することです。グループを手動で削除するというメンテナンスの負担が軽減されます。

有効期限ポリシーは次のように単純なものです。

* 有効期限が迫っているグループを更新するよう、そのグループの所有者に通知します。
* 更新されないグループはすべて削除されます。
* 削除された Office 365 グループは、30 日以内であれば、グループの所有者または Azure AD 管理者が復元できます。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisite"></a>前提条件

グループの有効期限を設定するには、テナントの全体管理者またはユーザー アカウント管理者であることが必要です。

## <a name="turn-on-user-creation-for-groups"></a>ユーザーによるグループの作成を有効にする

1. ディレクトリの全体管理者アカウントまたはユーザー アカウント管理者アカウントで [Azure portal](https://portal.azure.com) にサインインします。

2. **[グループ]** を選択し、**[全般]** を選択します。
  
  ![セルフ サービスのグループ設定](./media/groups-quickstart-expiration/self-service-settings.png)

3. **[ユーザーは Office 365 グループを作成できます]** を **[はい]** に設定します。

4. 最後に **[保存]** を選択してグループの設定を保存します。

## <a name="set-group-expiration"></a>グループの有効期限の設定

1. [Azure portal](https://portal.azure.com) で **[Azure Active Directory]** > **[グループ]** > **[有効期限]** の順に選択して有効期限の設定を開きます。
  
  ![有効期限の設定](./media/groups-quickstart-expiration/expiration-settings.png)

2. 期限切れの間隔を設定します。 プリセット値を選択するか、31 日を超えるカスタム値を入力します。 

3. グループに所有者がいない場合に、有効期限の通知を送信するメール アドレスを指定します。

4. このクイック スタートでは、**[これらの Office 365 グループの有効期限を有効にする]** を **[すべて]** に設定します。

5. 最後に **[保存]** を選択して有効期限の設定を保存します。

これで完了です。 このクイック スタートを通じて、選択した Office 365 グループの有効期限ポリシーを正しく設定することができました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

**有効期限ポリシーを削除するには**

1. テナントの全体管理者アカウントで [Azure portal](https://portal.azure.com) にサインインしていることを確認します。
2. **[Azure Active Directory]** > **[グループ]** > **[有効期限]** の順に選択します。
3. **[これらの Office 365 グループの有効期限を有効にする]** を **[なし]** に設定します。

**ユーザーによるグループの作成を無効にするには**

1. **[Azure Active Directory]** > **[グループ]** > **[全般]** の順に選択します。 
2. Azure portal で **[ユーザーは Office 365 グループを作成できます]** を **[いいえ]** に設定します。

## <a name="next-steps"></a>次の手順

技術的な制約やカスタム禁止単語リストの追加、Office 365 の各アプリ間のエンド ユーザー エクスペリエンスを含む有効期限の詳細については、次の記事を参照してください。こうした有効期限ポリシーについて詳しく説明されています。

> [!div class="nextstepaction"]
> [有効期限ポリシーの詳細](groups-lifecycle.md)