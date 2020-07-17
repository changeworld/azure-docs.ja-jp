---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184355"
---
アプリケーションを Azure AD B2C テナントに登録するには、現在の**アプリケーション** エクスペリエンス、または新しく統合された**アプリの登録 (プレビュー)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[アプリケーション](#tab/applications/)

1. [Azure portal](https://portal.azure.com) にサインインする
1. ポータルツールバーの **[Directory + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントが含まれているディレクトリを選択します。
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
1. **[管理]** の **[アプリの登録 (レガシ)]** を選択します。
1. **[新しいアプリケーションの登録]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*managementapp1* と入力します。
1. **[アプリケーションの種類]** で、 **[Web アプリ / API]** を選択します。
1. **[サインオン URL]** に、有効な URL を入力します。 たとえば、「 `https://localhost` 」のように入力します。 エンドポイントを到達可能にする必要はありませんが、有効な URL にする必要があります。
1. **作成** を選択します。
1. **[登録済みのアプリケーション]** の概要ページに表示される **[アプリケーション ID]** を記録します。 この値は、後の手順で使用します。

#### <a name="app-registrations-preview"></a>[アプリの登録 (プレビュー)](#tab/app-reg-preview/)

1. [Azure portal](https://portal.azure.com) にサインインする
1. ポータルツールバーの **[Directory + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントが含まれているディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録 (プレビュー)]** 、 **[新規登録]** の順に選択します。
1. アプリケーションの**名前**を入力します。 たとえば、*managementapp1* と入力します。
1. **[この組織のディレクトリ内のアカウントのみ]** を選択します。
1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオフにします。
1. **[登録]** を選択します。
1. アプリケーションの概要ページに表示されている **[アプリケーション (クライアント) ID]** を記録します。 この値は、後の手順で使用します。