---
title: アプリの登録を作成する (ポータル)
titleSuffix: Azure Digital Twins
description: Azure portal を使用して、クライアント アプリ向けの認証オプションとして Azure AD アプリの登録を作成する方法について説明します。
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8350ff149b54ef8cf8f4061abd64df73dc688962
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128653117"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-portal"></a>Azure Digital Twins で使用するアプリ登録を作成する (ポータル)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Azure Digital Twins インスタンスを使用する場合、「[クライアント アプリをコーディングする](tutorial-code.md)」で作成されたカスタム クライアント アプリなどのクライアント アプリケーションを使用して、そのインスタンスを操作することが一般的です。 これらのアプリケーションを操作するには、Azure Digital Twins で認証する必要があります。また、アプリで使用できる [認証メカニズム](how-to-authenticate-client.md)には、[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) の **アプリの登録** が関与します。

アプリの登録は、認証シナリオによっては必要ありません。 ただし、アプリの登録が必要な認証方法またはコード サンプルを使用している場合は、この記事で [Azure portal](https://portal.azure.com) を使用した設定方法を確認できます。 また、アプリの登録を使用して認証するために必要な[重要な値を収集](#collect-important-values)する方法についても説明しています。

## <a name="azure-ad-app-registrations"></a>Azure AD アプリの登録

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 Azure AD で **アプリの登録** を設定することは、クライアント アプリに Azure Digital Twins へのアクセスを許可する方法の 1 つです。

このアプリの登録では、[Azure Digital Twins API](concepts-apis-sdks.md) に対するアクセス許可を構成します。 後で、クライアント アプリは登録の **クライアントおよびテナント ID 値** を使用してこのアプリ登録に対して認証できます。結果として API への構成済みのアクセス許可が付与されます。

>[!TIP]
> 新しいアプリ登録を必要となるたびに設定することもできます。*または*、1 回だけ行い、1 つのアプリ登録を確立してそれを必要とするすべてのシナリオで共有することもできます。

## <a name="create-the-registration"></a>登録を作成する

まず、Azure portal 上で [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) に移動します (このリンクを使用するか、ポータルの検索バーを使って検索できます)。 サービス メニューから *[アプリの登録]* 、 *[+ 新しい登録]* の順に選択します。

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Azure portal の Azure AD サービス ページのスクリーンショット。[アプリの登録] ページで新しい登録を作成する手順が示されています。":::

以下の *[アプリケーションの登録]* ページで、要求される次の値を入力します。
* **Name**:登録に関連付ける Azure AD アプリケーションの表示名
* **サポートされているアカウントの種類**: *[この組織ディレクトリのみに含まれるアカウント (既定のディレクトリのみ - シングル テナント)]* を選択します
* **リダイレクト URI**:Azure AD アプリケーションの "*Azure AD アプリケーション応答 URL*"。 `http://localhost` の *パブリック クライアント/ネイティブ (モバイルとデスクトップ)* URI を追加します。

完了したら、 *[登録]* ボタンを選択します。

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="記述された値が入力されている Azure portal の [アプリケーションの登録] ページのスクリーンショット。":::

登録の設定が完了したら、ポータルによって詳細ページにリダイレクトされます。

## <a name="collect-important-values"></a>重要な値を収集する

次に、アプリの登録に関する重要な値を収集します。これらは、アプリの登録を使用してクライアント アプリケーションを認証するのに必要となります。 これらの値には次の値が含まれます。
* **リソース名**
* **クライアント ID**
* **テナント ID**
* **クライアント シークレット**

Azure Digital Twins に使用する場合、**リソース名** は `http://digitaltwins.azure.net` になります。

次のセクションでは、他の値を見つける方法について説明します。

### <a name="collect-client-id-and-tenant-id"></a>クライアント ID とテナント ID を収集する

**クライアント ID** と **テナント ID** の値は、Azure portal のアプリ登録の詳細ページから収集できます。

:::image type="content" source="media/how-to-create-app-registration/client-id-tenant-id.png" alt-text="アプリ登録の重要な値が示されている Azure portal のスクリーンショット。"  lightbox="media/how-to-create-app-registration/client-id-tenant-id.png":::

**実際の** ページに表示される、"**アプリケーション (クライアント) ID**" と "**ディレクトリ (テナント) ID**" をメモしておきます。

### <a name="collect-client-secret"></a>クライアント シークレットを収集する

アプリ登録用の **クライアント シークレット** を設定するには、Azure portal のアプリ登録ページで開始します。 

1. 登録のメニューから **[証明書とシークレット]** を選択して、 **[+ 新しいクライアント シークレット]** を選択します。

    :::image type="content" source="media/how-to-create-app-registration/client-secret.png" alt-text="Azure AD アプリの登録が表示され、[新しいクライアント シークレット] が強調表示されている Azure portal のスクリーンショット。":::

1. [説明] と [有効期限] に必要な値を入力して、 **[追加]** を選択します。

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-create-app-registration/add-client-secret.png" alt-text="クライアント シークレットを追加しているときの Azure portal のスクリーンショット。":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

1. **[証明書とシークレット]** ページの [有効期限] および [値] フィールドにクライアント シークレットが表示されていることを確認します。 

1. 後で使用するので **[シークレット ID]** と **[値]** を記録しておきます ([コピー] アイコンを使用してクリップボードにコピーすることもできます)。

    :::image type="content" source="media/how-to-create-app-registration/client-secret-value.png" alt-text="クライアント シークレットの値をコピーする方法を示す Azure portal のスクリーンショット。":::

>[!IMPORTANT]
>値をコピーして安全な場所に保存してください。再び取得することはできません。 後でこれらを見つけることができない場合は、新しいシークレットを作成する必要があります。

## <a name="provide-azure-digital-twins-api-permission"></a>Azure Digital Twins API のアクセス許可を指定する

次に、Azure Digital Twins API へのベースライン アクセス許可によって、作成したアプリ登録を構成します。

アプリ登録のポータル ページで、メニューから *[API のアクセス許可]* を選択します。 以下のアクセス許可ページで、 *[+ アクセス許可の追加]* ボタンを選択します。

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="[API のアクセス許可] メニュー オプションと [アクセス許可の追加] ボタンが強調表示されている Azure portal のアプリ登録のスクリーンショット。":::

次の *[API アクセス許可の要求]* ページで、 *[所属する組織で使用している API]* タブに切り替えて、"*Azure digital twins*" を検索します。 検索結果から _**Azure Digital Twins**_ を選択して、Azure Digital Twins API に対するアクセス許可の割り当てを続けます。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Azure Digital Twins が表示された Azure portal の [API アクセス許可の要求] ページの検索結果のスクリーンショット。":::

>[!NOTE]
> サービスの以前の (2020 年 7 月より前の) パブリック プレビューで作成された既存の Azure Digital Twins インスタンスがお使いのサブスクリプションにまだある場合は、代わりに "_**Azure Smart Spaces Service**_" を検索して選択する必要があります。 これは、同じ API セットの古い名前です ("*アプリケーション (クライアント) ID*" が上記のスクリーンショットと同じであることに注意してください)。この手順の他に、操作手順に変更はありません。
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Azure portal での Azure Smart Spaces Service が表示された [API アクセス許可の要求] ページの検索結果のスクリーンショット。":::

次に、これらの API に対して付与するアクセス許可を選択します。 **[Read (1)]\(読み取り (1)\)** アクセス許可を展開して、 *[Read.Write]\(読み取り.書き込み\)* と示されたチェック ボックスをオンにし、このアプリ登録に読み取りおよび書き込みのアクセス許可を付与します。

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Azure portal での Azure Digital Twins API の 'Read.Write' アクセス許可が選択された [API アクセス許可の要求] ページのスクリーンショット。":::

完了したら、 *[アクセス許可の追加]* を選択します。

### <a name="verify-success"></a>成功を確認する

*[API のアクセス許可]* ページで、読み取りおよび書き込みのアクセス許可が反映された Azure Digital Twins のエントリがあることを確認します。

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Azure Digital Twins に対して 'Read/Write Access' が表示されている、Azure portal の Azure AD アプリ登録の API のアクセス許可のスクリーンショット。":::

また、アプリ登録の *manifest.json* 内で Azure Digital Twins への接続を検証できます。これは、API のアクセス許可を追加したときに、Azure Digital Twins 情報によって自動的に更新されました。

これを行うには、メニューから **[マニフェスト]** を選択して、アプリ登録のマニフェスト コードを表示します。 コード ウィンドウの一番下までスクロールし、`requiredResourceAccess` の下の次のフィールドと値を探します。 
* `"resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0"`
* `"resourceAccess"` > `"id": "4589bd03-58cb-4e6c-b17f-b580e39652f8"`

これらの値については、下のスクリーンショットをご覧ください。

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Azure portal の Azure AD アプリ登録のマニフェストのスクリーンショット。":::

これらの値がない場合は、[API アクセス許可の追加に関するセクション](#provide-azure-digital-twins-api-permission)の手順を再試行してください。

## <a name="other-possible-steps-for-your-organization"></a>組織でのその他の考えられる手順

アプリの登録を正常に設定するには、サブスクリプションの所有者または管理者からの追加のアクションが組織で必要になる可能性があります。 必要な手順は、組織の具体的な設定によって異なることがあります。

サブスクリプションの所有者または管理者による実行が必要になる可能性がある一般的な潜在的なアクティビティのいくつかを次に示します。 これらの操作は、Azure portal の [Azure AD アプリ登録](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)のページから実行できます。
* アプリ登録に対する管理者の同意を付与する。 組織では、サブスクリプション内のすべてのアプリ登録について、Azure AD で *[管理者の同意が必要]* がグローバルに有効になっている可能性があります。 その場合は、有効にするアプリ登録について、アプリ登録の *[API のアクセス許可]* ページで所有者/管理者がユーザーの会社に対してこのボタンを選択する必要があります。

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="API のアクセス許可の下に [管理者の同意の付与] ボタンが表示されている Azure portal のスクリーンショット。":::
  - 同意が正常に付与された場合は、Azure Digital Twins のエントリに _[ **(ユーザーの会社)** に付与されました]_ の *[状態]* 値が表示されます。
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="API のアクセス許可の下に、会社に付与された管理者の同意が表示されている Azure portal のスクリーンショット。":::
* パブリック クライアント アクセスをアクティブ化する
* Web およびデスクトップへのアクセスに特定の応答 URL を設定する
* 暗黙の OAuth2 認証フローを許可する

アプリ登録とそのさまざまな設定オプションの詳細については、「[Microsoft ID プラットフォームにアプリケーションを登録する](/graph/auth-register-app-v2)」を参照してください。

## <a name="next-steps"></a>次の手順

この記事では、クライアント アプリケーションを Azure Digital Twins API で認証するために使用できる Azure AD アプリの登録を設定しました。

次に、認証メカニズムについて、アプリの登録を使用するものと、使用しないものについて確認します。
* [アプリ認証コードを作成する](how-to-authenticate-client.md)