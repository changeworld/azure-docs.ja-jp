---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/08/2021
ms.author: kengaderdus
ms.openlocfilehash: 69695f17ca9be03d473c7b6220e557094413cc64
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2021
ms.locfileid: "131008231"
---
アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスか以前の **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](../articles/active-directory-b2c/app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。
    1. ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
    1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*ROPC_Auth_app* のようになります。
1. その他の値はそのままにして、 **[登録]** を選択します。
1. 後の手順で使用するために、**アプリケーション (クライアント) ID** を記録しておきます。
1. **[管理]** で、 **[認証]** を選択します。
1. **[新しいエクスペリエンスを試す]** (表示されている場合) を選択します。
1. **[詳細設定]** の **[次のモバイルとデスクトップのフローを有効にする]** セクションで、 **[はい]** を選択することで、アプリケーションをパブリック クライアントとして処理します。 ROPC フローには、この設定が必要となります。
1. **[保存]** を選択します。
1. 左側のメニューで、 **[マニフェスト]** を選択してマニフェスト エディターを開きます。 
1. **oauth2AllowImplicitFlow** 属性を *true* に設定します。
    ```json
    "oauth2AllowImplicitFlow": true,
    ```
1. **[保存]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご利用の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。
    1. ポータル ツールバーの **[Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** アイコンを選択します。
    1. **[ポータルの設定] | [Directories + subscriptions]\(ディレクトリ + サブスクリプション\)** ページで Azure AD B2C ディレクトリを **[ディレクトリ名]** リストで見つけ、 **[Switch]** を選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します
1. **[アプリケーション (レガシ)]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*ROPC_Auth_app* のようになります。
1. **[ネイティブ クライアント]** の場合、 **[はい]** を選択します。
1. その他の値はそのままにして、 **[作成]** を選択します。
1. 後の手順で使用するために、**アプリケーション ID** を記録しておきます。