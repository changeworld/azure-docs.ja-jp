---
title: Azure Red Hat OpenShift の Azure AD アプリ登録とユーザーを作成する | Microsoft Docs
description: サービス プリンシパルの作成、クライアント シークレットと認証コールバック URL の生成、Microsoft Azure Red Hat OpenShift クラスター上でアプリをテストするための Active Directory ユーザーの新規作成の方法について説明します。
author: tylermsft
ms.author: twhitney
ms.service: openshift
manager: jeconnoc
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/06/2019
ms.openlocfilehash: de3f3c30848d26ea399bcccc29a6149a149f6a55
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080585"
---
# <a name="create-an-azure-ad-app-registration-and-user-for-azure-red-hat-openshift"></a>Azure Red Hat OpenShift の Azure AD アプリ登録とユーザーを作成する

Microsoft Azure Red Hat OpenShift には、クラスターに代わってタスクを実行するためのアクセス許可が必要です。 サービス プリンシパルとして使用する Azure Active Directory (Azure AD) アプリの登録が組織にまだない場合は、次の手順に従って作成します。

このトピックでは、Azure Red Hat OpenShift クラスター上で実行されているアプリにアクセスするために必要な、新しい Azure AD ユーザーを作成する手順について説明します。

Azure AD テナントをまだ作成していない場合は、これらの手順を続行する前に [Azure Red Hat OpenShift 用の Azure AD テナントの作成](howto-create-tenant.md)に関する記事の手順を実行してください。

## <a name="create-a-new-app-registration"></a>新しいアプリ登録の作成

アプリケーションで Azure AD の機能を使用するには、まず Azure AD テナントにそのアプリケーションを登録する必要があります。 この登録プロセスでは、アプリが配置されている URL、ユーザーが認証された後の応答の送信先となる URL、アプリを識別する URI など、アプリケーションの詳細を Azure AD に提供します。

1. [Azure portal](https://portal.azure.com) で、ポータルの右上のユーザー名の下にテナントが表示されていることを確認します。

    ![右上にテナントが表示されているポータルのスクリーンショット][tenantcallout] 不適切なテナントが表示される場合は、右上の自分のユーザー名をクリックし、**[ディレクトリの切り替え]** をクリックして **[すべてのディレクトリ]** リストから正しいテナントを選択します。

2. [[アプリの登録]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) ブレードを開き、**[新しいアプリケーションの登録]** をクリックします。
3. **[作成]** ウィンドウにアプリケーション オブジェクトのフレンドリ名を入力します。
4. **[アプリケーションの種類]** が *[Web アプリ/API]* に設定されていることを確認します。
5. 次のパターンを使用して **[サインオン URL]** を作成します。

    ```
    https://<cluster-name>.<azure-region>.cloudapp.azure.com
    ```

    。 . 。 この `<cluster-name>` は Azure Red Hat OpenShift クラスターに指定する名前、`<azure-region>` は [Azure Red Hat OpenShift クラスターをホストしている Azure リージョン](supported-resources.md#azure-regions)です。 たとえば、クラスター名を `contoso` にして、`eastus` リージョンに作成する場合は、**サインオン URL** に入力する完全修飾ドメイン名 (FQDN) は `https://contoso.eastus.cloudapp.azure.com` になります

    > [!IMPORTANT]
    > クラスター名はすべて小文字にして、FQDN URL は一意にする必要があります。
    > クラスターには十分に特徴的な名前を選択します。

    クラスター名とサインオン URL をメモします。後で、クラスター上で実行中のアプリにアクセスするために必要になります。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、クラスター名を `CLUSTER_NAME`、このサインオン URL を `FQDN` と呼びます。

6. **サインオン URL** の値が緑色のチェック マークで検証されていることを確認します (Tab キーを押して *[サインオン URL]* フィールドからフォーカスを外し、検証チェックを実行します)。
7. **[作成]** ボタンをクリックして Azure AD アプリケーション オブジェクトを作成します。
8. 表示される **[登録済みのアプリケーション]** ページで、**[アプリケーション ID]** をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `APPID` と呼びます。

    ![アプリケーション ID テキストボックスのスクリーンショット][appidimage]
    
Azure アプリケーション オブジェクトの詳細については、「[Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)」を参照してください。

新しい Azure AD アプリケーションの作成の詳細については、[Azure Active Directory v1.0 エンドポイントへのアプリの登録](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)に関する記事を参照してください。

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

これで、Azure Active Directory に対してアプリを認証するためのクライアント シークレットを生成する準備が整いました。

1. **[登録済みのアプリケーション]** ページから **[設定]** をクリックして登録済みのアプリの設定を開きます。
2. 表示される **[設定]** ウィンドウで、**[キー]** をクリックします。  **[キー]** ウィンドウが表示されます。
![ポータルのキーの作成ページのスクリーンショット][createkeyimage]
3. キーの **[説明]** を入力します。
4. **[有効期限]** に値 (たとえば *[2 年]*) を設定します。
5. **[保存]** をクリックすると、キー値が表示されます。
6. キー値をコピーします。 [Azure Red Hat OpenShift クラスターの作成](tutorial-create-cluster.md)のチュートリアルでは、この値を `SECRET` と呼びます。

### <a name="create-a-reply-url"></a>応答 URL を作成する

Azure AD は、アプリを承認するときに使用するコールバックを指定するためにアプリ オブジェクトの "*応答 URL*" を使用します。 Web API または Web アプリケーションの場合、応答 URL は Azure AD が認証の応答 (認証が成功した場合のトークンなど) を送信する場所です。

最小の不一致 (末尾のスラッシュの欠落、大文字と小文字の区別の違い) でも、トークン発行操作が失敗し、サインインできなくなります。

1. **[設定]** ウィンドウに戻り (ポータルの上部にある階層リンクの **[設定]** をクリックします)、右側の **[応答 URL]** をクリックします。  **[応答 URL]** ウィンドウが表示されます。
2. リストの最初の応答 URL は、「[新しいアプリ登録の作成](#create-a-new-app-registration)」に関する記事の手順 6 の `FQDN` 値になります。 それを編集して `/oauth2callback/Azure%20AD` を追加します。  たとえば、応答 URL は `https://mycluster.eastus.cloudapp.azure.com/oauth2callback/Azure%20AD` のようになります
3. **[保存]** をクリックして応答 URL を保存します。

## <a name="create-a-new-active-directory-user"></a>新しい Active Directory ユーザーを作成します。

Azure Red Hat OpenShift クラスター上で実行されているアプリにサインインするために使用する新しいユーザーを Active Directory に作成します。

1. [[ユーザー - すべてのユーザー]](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers) ブレードに移動します。
2. **[+ 新しいユーザー]** をクリックします。 **[ユーザー]** ウィンドウが表示されます。
3. このユーザーに使用する **[名前]** を入力します。
4. 末尾に `.onmicrosoft.com` を追加して作成したテナントの名前に基づいて **[ユーザー名]** を作成します。 たとえば、「 `yourUserName@yourTenantName.onmicrosoft.com` 」のように入力します。 このユーザー名をメモします。 クラスター上でアプリを使用するにはサインインする必要があります。
5. **[ディレクトリ ロール]** をクリックし、**[グローバル管理者]** を選択してから、ウィンドウの下部にある **[OK]** をクリックします。
6. **[ユーザー]** ウィンドウの中央にある **[パスワードを表示]** をクリックして一時パスワードをメモします。 初めてサインインした後は、リセットするように求められます。
7. ウィンドウの下部にある **[作成]** をクリックしてユーザーを作成します。

## <a name="resources"></a>リソース

* [Azure Active Directory のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals)  
* [クイック スタート:Azure Active Directory v1.0 エンドポイントを使用してアプリを登録する](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)  

[appidimage]: ./media/howto-create-tenant/get-app-id.png
[createkeyimage]: ./media/howto-create-tenant/create-key.png
[tenantcallout]: ./media/howto-create-tenant/tenant-callout.png

## <a name="next-steps"></a>次の手順

[Azure Red Hat OpenShift の前提条件](howto-setup-environment.md)をすべて満たすと、最初のクラスターを作成する準備は完了です。

次のチュートリアルを試してください。
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)
