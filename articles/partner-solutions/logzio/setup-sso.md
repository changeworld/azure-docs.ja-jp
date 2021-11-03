---
title: Azure と Logz.io の統合に対するシングル サインオン - Azure パートナー ソリューション
description: Azure と Logz.io の統合にシングル サインオンをセットアップする方法について説明します。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 10/25/2021
ms.author: tomfitz
ms.openlocfilehash: 86ef7f781e3093b2daa78a393963b9053c9f5982
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057573"
---
# <a name="set-up-logzio-single-sign-on"></a>Logz.io のシングル サインオンをセットアップする

この記事では、Azure Active Directory でシングル サインオン (SSO) を設定する方法について説明します。 Logz.io の統合への SSO はオプションです。

## <a name="configure-single-sign-on"></a>Configure single sign-on

Logz.io リソース内で Security Assertion Markup Language (SAML) の SSO 機能を使用するには、エンタープライズ アプリケーションを設定する必要があります。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル メニューから **[Azure Active Directory]** を選択するか、_Azure Active Directory_ を検索します。
1. **[管理]**  >  **[エンタープライズ アプリケーション]** に移動し、 **[新しいアプリケーション]** ボタンを選択します。
1. _Logz.io_ を検索し、**Logz.io - Azure AD Integration** という名前の SAML アプリケーションを選択して、 **[作成]** を選択します。

   :::image type="content" source="./media/sso-setup/gallery.png" alt-text="Azure Active Directory ギャラリーを参照します。":::

1. **[概要]** から、SSO アプリケーションの **[アプリケーション ID]** をコピーします。

   SAML を構成する後のステップで使用するので、"_アプリケーション ID_" を記録しておきます。

   :::image type="content" source="./media/sso-setup/app-id.png" alt-text="アプリケーション ID をコピーします。":::

1. **[2. シングル サインオンの設定]** という名前のタイルを選択します。

   :::image type="content" source="./media/sso-setup/setup.png" alt-text="シングル サインオンを設定します。":::

1. シングル サインオンの方法として、 **[SAML]** プロトコルを選択します。

   :::image type="content" source="./media/sso-setup/saml.png" alt-text="SAML シングル サインオン。":::

1. SAML の構成の必須フィールド **[識別子]** および **[応答 URL]** で、既存の値を選択するか、テキスト ボックスを使用して入力します。 新しいエントリを作成するには、前のステップでコピーした "_アプリケーション ID_" を使用します。

   新しい値を追加するには、次のパターンを使用します。

   - **[識別子]** : `urn:auth0:logzio:<Application ID>`
   - **[応答 URL]** : `https://logzio.auth0.com/login/callback?connection=<Application ID>`

   :::image type="content" source="./media/sso-setup/saml-config.png" alt-text="基本的な SAML の構成。":::

1. **[Save single sign-on settings]\(シングル サインオン設定を保存する\)** というプロンプトで、 **[はい]** を選択して、 **[保存]** を選択します。

   SSO のプロンプトで、 **[いいえ、後でテストします]** を選択します。

1. SAML の構成が保存されたら、 **[管理]**  >  **[プロパティ]** に移動し、 **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** に設定します。 **[保存]** を選択します。

   :::image type="content" source="./media/sso-setup/properties.png" alt-text="シングル サインオンのプロパティ。":::

## <a name="next-steps"></a>次の手順

- SSO に関する問題を解決するには、[トラブルシューティング](troubleshoot.md)に関する記事を参照してください。
- Logz.ioの 統合を作成するために、「[クイック スタート: Azure portal で Logz.io リソースを作成する](create.md)」を参照してください。
