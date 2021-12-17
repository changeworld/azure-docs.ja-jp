---
title: Microsoft Azure Maps の認証の詳細を表示する
description: Azure portal を使用して Azure Maps の認証の詳細を表示します。
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/21/2021
ms.topic: include
ms.service: azure-maps
services: azure-maps
custom.ms: subject-rbac-steps
ms.openlocfilehash: ccc0e8c43001f161648032be7fbb83aec43cc49f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803067"
---
Azure portal で Azure Maps アカウントの認証の詳細を表示するには:

1. [Azure portal](https://portal.azure.com) にサインインします。

2. Azure portal メニューに移動します。 **[すべてのリソース]** を選択し、Azure Maps アカウントを選択します。

3. 左側のペインの **[設定]** で、 **[認証]** を選択します。

    :::image type="content" border="true" source="../media/how-to-manage-authentication/view-authentication-keys.png" alt-text="認証の詳細。":::

Azure Maps アカウントの作成時に、3 つの値が作成されます。 それらは、Azure Maps で次の 2 種類の認証をサポートするために使用されます。
- **Azure Active Directory 認証**: `Client ID` は、REST API 要求に使用されるアカウントを表します。 `Client ID` 値は、アプリケーション構成に格納し、その後 Azure AD 認証を使用する Azure Maps HTTP 要求を行う前に、取得する必要があります。
- **共有キー認証**: `Primary Key` および `Secondary Key` は、共有キー認証のサブスクリプション キーとして使用されます。 共有キー認証は、Azure Maps への各要求で、Azure Maps アカウントによって生成されたキーを渡す処理に依存しています。 キーを定期的に再生成することをお勧めします。 再生成中に現在の接続を維持するために、2 つのキーが用意されています。 1 つのキーを、もう一方を再生成しているときに使用できます。 キーを再生成したら、新しいキーを使用するように、このアカウントにアクセスするすべてのアプリケーションを更新する必要があります。 詳細については、「[Azure Maps による認証](../azure-maps-authentication.md)」を参照してください
