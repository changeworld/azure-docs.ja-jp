---
title: "Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化 | Microsoft Docs"
description: "Azure AD アプリケーション プロキシ コネクタの基本について説明します。"
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 8436238546515b66b58b31673d54586e4a20f50f
ms.openlocfilehash: 86f10c04368d1c382939b418c6909ca807b3bf5a


---

# <a name="enable-remote-access-to-sharepoint-with-azure-ad-app-proxy"></a>Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化

この記事では、オンプレミスの SharePoint サーバと Azure AD アプリケーション プロキシを統合する方法について説明します。

> [!NOTE]
> アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「 [Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
> 

##<a name="prerequisites"></a>前提条件

この記事では、SharePoint 2013 以降が既にセットアップされ、環境内で実行されていることを前提にしています。 さらに、次の前提条件を検討してください。

* SharePoint では、ネイティブの Kerberos がサポートされています。 そのため、Azure AD アプリケーション プロキシを経由して内部サイトにリモートでアクセスするユーザーは、シームレスなシングル サインオン エクスペリエンスを想定できます。

* SharePoint サーバーに、いくつかの構成変更を加える必要があります。 ステージング環境を使用することをお勧めします。 これにより、ステージング サーバーを最初に更新し、実稼働に移行する前のテスト サイクルを容易にすることができます。

* SharePoint では公開 URL で SSL が必要なため、SharePoint 用の SSL を既に設定していると想定しています。 内部のサイトで SSL を有効にして、リンクの送信およびマッピングが適切であることを確認する必要があります。 SSL を構成していない場合は、「[Configure SSL for SharePoint 2013 (SharePoint 2013 用の SSL の構成)](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013)」で、SSL の設定手順をご覧ください。 また、コネクタ コンピューターがユーザー発行の証明書を信頼することを確認します  (この証明書は、パブリックに発行されたものである必要はありません)。

##<a name="steps-to-set-up-sharepoint"></a>SharePoint をセットアップする手順

Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスを有効にするには、以下の手順に従います。

**パート 1: シングル サインオン (SSO) の構成**

  * 手順 A. SharePoint サーバーがサービス アカウントとして実行されていることを確認する。
  * 手順 B. Kerberos 用に SharePoint を構成する。
  * 手順 C. SharePoint に割り当てられているサービス アカウントのサービス プリンシパル名 (SPN) を設定する。
  * 手順 D. コネクタが信頼され、SharePoint に委任するように設定されていることを確認する。

**パート 2: セキュリティで保護されたリモート アクセスの有効化**

 * SharePoint ファームを Azure AD アプリケーション プロキシに発行する。

**パート 3: SharePoint による外部 URL の把握**

 * SharePoint で代替アクセス マッピングを設定する。

### <a name="part-1-set-up-single-sign-on-sso-to-sharepoint"></a>パート 1: SharePoint へのシングル サインオン (SSO) の設定

お客様は、バックエンド アプリケーション (この場合は SharePoint サーバー) での最良の SSO エクスペリエンスを求めています。 この一般的な Azure AD シナリオでは、ユーザーは&1; 回のみ認証され、再度認証を求められることはありません。

Windows 認証を必要とする、または使用するオンプレミス アプリケーションの場合は、Kerberos 認証プロトコルと、Kerberos の制約付き委任 (KCD) と呼ばれる機能を使用してこれを実現できます。 KCD を構成すると、ある特定のユーザーが Windows に直接ログインしていない場合でも、アプリケーション プロキシ コネクタは、そのユーザーの Windows チケット/トークンを取得できます。 KCD の詳細については、「[Kerberos の制約付き委任の概要](https://technet.microsoft.com/en-us/library/jj553400.aspx)」をご覧ください。

SharePoint サーバーにこの設定を行うには、以下の手順に従います。

**手順 A: SharePoint がローカル システム、ローカル サービス、ネットワーク サービスのいずれでもなく、サービス アカウントで実行されていることを確認する**

まず、SharePoint が定義済みのサービス アカウントで実行されていることを確認する必要があります。 これを行って、有効なアカウントにサービス プリンシパル名 (SPN) をアタッチできるようにする必要があります。 SPN は、Kerberos プロトコルがさまざまなサービスを特定する方法です。 そして、KCD を構成するために後で必要になります。

サイトが定義済みのサービス アカウントで実行されていることを確認するには、以下の手順に従います。

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[セキュリティ]** に移動して、**[サービス アカウントの構成]** を選択します。
3. **[Web アプリケーション プール - SharePoint - 80]** を選択します。 Web プールの名前に応じて、オプションが若干異なることがあります。既定で SSL を使用している場合も同様です。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-web-application.png)

4. **[このコンポーネントのアカウントの選択]** が **[ローカル サービス]** または **[ネットワーク サービス]** の場合は、アカウントを作成する必要があります。 アカウントがそのどちらでもない場合、すべて完了し、次の手順に進むことができます。 
5. **[新しい管理アカウントの登録]** を選択します。 アカウントを作成したら、アカウントを使用する前に **Web アプリケーション プール**を設定する必要があります。

> [!NOTE]
サービス用に事前に作成した AD アカウントが必要です。 パスワードの自動変更を許可することをお勧めします。 問題のトラブルシューティングの完全な手順の詳細については、「[SharePoint 2013 でのパスワードの自動変更の構成](https://technet.microsoft.com/EN-US/library/ff724280.aspx)」をご覧ください。 

**手順 B: Kerberos 用に SharePoint を構成する**

KCD を使用して、SharePoint サーバーへのシングル サインオン (SSO) を実行します。これは、Kerberos でのみ動作します。 

Kerberos 認証用 SharePoint サイトを構成するには

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[アプリケーション構成の管理]** に移動し、**[Web アプリケーションの管理]** を選択して、SharePoint サイトを選択します。 この例では、**[SharePoint - 80]** です。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-manage-web-applications.png)
  
3. ツール バーの **[認証プロバイダー]** をクリックします。
4. **[認証プロバイダー]** ボックスで、**[既定のゾーン]** をクリックして設定を表示します。
5. **[認証の編集]** ボックスで、**[クレーム認証の種類]** が表示されるまで下へスクロールし、**[Windows 認証の有効化]** と **[統合 Windows 認証]** の両方がオンになっていることを確認します。 
6. ドロップダウン ボックスで、**[ネゴシエート (Kerberos)]** が選択されていることを確認します。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-service-edit-authentication.png)

7. **[認証の編集]** ボックスの最下部で、**[保存]** をクリックします。

**手順 C: SharePoint サービス アカウントの SPN を設定する**

KCD を構成する前に、上記で構成した、サービス アカウントとして実行されている SharePoint サービスを特定する必要があります。 そのために、サービス プリンシパル名 (SPN) を設定します。 詳細については、「[Service Principal Names (サービス プリンシパル名)](https://technet.microsoft.com/en-us/library/cc961723.aspx)」をご覧ください。

SPN の形式は以下のとおりです。

```
<service class>/<host>:<port>
```

_service class_ は、サービスの一意の名前です。 SharePoint では、HTTP を使用します。

_host_ は、サービスが実行されているホストの完全修飾ドメイン名または Netbios 名です。 SharePoint サイトの場合、使用している IIS のバージョンによっては、サイトの URL であることが必要となる可能性があります。

_port_ は省略可能です。 SharePoint サーバーの FQDN が以下である場合、

```
sharepoint.demo.o365identity.us
```

SPN は以下のようになります。 

```
HTTP/ sharepoint.demo.o365identity.us demo
```

さらに、サーバー上の特定のサイトの SPN も設定することが必要な場合があります。 詳細については、[Kerberos 認証の構成](https://technet.microsoft.com/en-us/library/cc263449(v=office.12).aspx)に関する記事をご覧ください。 「Kerberos 認証を使用して Web アプリケーションのサービス プリンシパル名を作成する」セクションに特に注意を払ってください。 

これを行う最も簡単な方法は、サイトに既に存在する可能性がある SPN の形式に従うことです。 その SPN をコピーし、サービス アカウントに対して SPN を登録します。 これを行うには、次の手順を実行します。

1. 別のコンピューターから、SPN を使用してサイトを参照します。 
 これを行うと、関連する一連の Kerberos チケットがコンピューターにキャッシュされます。 これらのチケットには、参照した対象サイトの SPN が含まれます。 [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html) と呼ばれるツールを使用して、そのサイトの SPN を取り出すことができます。
 
2. ブラウザーでサイトにアクセスしたユーザーと同じコンテキストで実行されているコマンド ウィンドウで、次のコマンドを実行します。 
```
Klist
```
3. 対象とするサービスの SPN のセットが返されます。 この例では、強調表示されている値が、必要な SPN です。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)
  
4. これで SPN がわかったため、先ほど Web アプリケーション用に設定したサービス アカウントで SPN が正しく構成されていることを確認する必要があります。 次のセクションの手順に従います。

**SPN の設定**

SPN を設定するには、ドメインの管理者として、コマンド プロンプトから次のコマンドを実行します。

```
setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
```

このコマンドにより、_demo\sp_svc_ として実行されている SharePoint サービス アカウントの SPN が設定されます。

_http/sharepoint.demo.o365identity.us_ をサーバーの SPN に、_demo\sp_svc_ を環境でのサービス アカウントに置き換えるのを忘れないでください。 setspn コマンドでは、追加前にその SPN が検索されます。 この場合、**重複している SPN 値**のエラーが表示されることがあります。 このエラーが表示された場合、SPN 値がサービス アカウントに関連付けられていることを確認してください。

-l オプションを使用して Setspn コマンドを実行すると、SPN が追加されたことを確認できます。 Setspn ツールの詳細については、「[Setspn](https://technet.microsoft.com/en-us/library/cc731241.aspx)」をご覧ください。

**手順 D: コネクタが信頼され、SharePointに委任されていることを確認する**

この手順では、KCD を構成して、Azure AD アプリケーション プロキシ サービスがユーザー ID を SharePoint サービスに委任できるようにします。 これを行うには、アプリケーション プロキシ コネクタを有効にして、Azure AD で認証されているユーザーの Kerberos チケットを取得できるようにします。 その後、そのサーバーは、コンテキストを対象アプリケーション (この場合は SharePoint) に渡します。 

KCD を構成するには、コネクタ コンピューターごとに以下の手順を繰り返す必要があります。

1. ドメイン管理者として DC にログインし、**[Active Directory ユーザーとコンピューター]** を開きます。
2. コネクタが実行されているコンピューターを見つけます。 この例では、同じサーバー SharePoint です。
3. そのコンピューターをダブルクリックし、**[委任]** タブをクリックします。
4. 委任設定が **[指定されたサービスへの委任でのみこのコンピューターを信頼する]** に設定されていることを確認し、**[任意の認証プロトコルを使う]** を選択します。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-delegation-box.png)
  
5. ここで、先ほどサービス アカウント用に作成した SPN を追加する必要があります。 **[追加]** ボタンをクリックし、**[ユーザーまたはコンピューター]** をクリックして、先ほど作成したアカウントを見つけます。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-users-computers.png)

 選択対象の SPN の一覧が表示されます。 上記で設定した SPN を追加する必要があります。 
6. その項目を選択して、**[OK]** をクリックします。 もう一度 **[OK]** をクリックして変更を保存します。

### <a name="part-2-enable-remote-access-to-sharepoint"></a>パート 2: SharePoint へのリモート アクセスの有効化

Kerberos 用の SharePoint を有効にし、KCD を構成したことから、これで SharePoint へのシングル サインオン (SSO) を設定する準備ができました。 そして、Azure AD アプリケーション プロキシ経由のリモート アクセスのため、コネクタから SharePoint を公開できます。

**Azure AD アプリケーション プロキシによる SharePoint の公開**

以下の手順を実行するには、組織の Azure Active Directory アカウント内のグローバル管理者ロールのメンバーである必要があります。

1. Microsoft Azure 管理ポータル (https://manage.windowsazure.com) にログインし、Azure AD テナントを見つけます。
2. **[アプリケーション]** をクリックし、**[追加]** をクリックします。
3. **[ネットワーク外部からアクセスできるアプリケーションを発行します]**を選択します。 このオプションが表示されない場合は、テナントで Azure AD Basic または Azure AD Premium が設定されていることを確認します。
4. 表示されるボックスで、各オプションを次のように入力します。 
 * **名前**: 必要な任意の値 (例: _SharePoint_)。
 * **内部 URL**: SharePoint サイトの内部的な URL (例: https://SharePoint/)。 この例では、必ず https を使用します。
 * **事前認証方法**: _[Azure Active Directory]_ を選択します。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-add-application.png)

5. アプリケーションを発行したら、**[構成]** タブをクリックします。
6. オプション **[ヘッダーの URL を変換する]** まで下にスクロールします。 既定値は _[はい]_ です。この値を _[いいえ]_ に変更します。 

 SharePoint では、_ホスト ヘッダー_値を使用してサイトが検索され、この値に基づいてリンクも生成されます。 この設定の実際の効果は、SharePoint で生成されたリンクはどれも公開 URL であり、外部 URL を使用するように適切に設定されていることを確実にするというものです。 この値を _[はい]_ に設定すると、コネクタが要求をバックエンド アプリケーションに転送できるようにもなります。 しかし、これを _[いいえ]_ に設定すると、コネクタは内部ホスト名を送信せずに、ホスト ヘッダーを公開 URL としてバックエンド アプリケーションに送信することになります。

 また、SharePoint がこの URL を受け入れるようにするには、SharePoint サーバーでもう&1; つの構成を完了する必要があります。 これは、次のセクションで行います。

7. **[内部認証方法]** を _[Windows 統合]_ に変更します。 Azure AD テナントにおいて、オンプレミスとクラウドで異なる UPN を使用している場合は、**[委任されたログイン ID]** も忘れずに更新します。
8. **[内部アプリケーション SPN]** を上記で設定した値に設定します。 たとえば、_http/sharepoint.demo.o365identity.us_ とします。
9. これで、アプリケーションを対象ユーザーに割り当てることができます。

アプリケーションの表示は次のようになります。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-internal-application-spn.png)

###<a name="part-3-ensure-sharepoint-knows-about-the-external-url"></a>パート 3: SharePoint による外部 URL の把握

必要な最終手順は、SharePoint が外部 URL に基づいてサイトを見つけることができるようにして、その外部 URL に基づいたリンクが表示されるようにすることです。 これを行うには、SharePoint の代替アクセス マッピングを構成します。

**SharePoint サイトの代替名の構成**

1. **SharePoint 2013 の全体管理**サイトを開きます。
2. **[システム設定]** で、**[代替アクセス マッピングの構成]** を選択します。 

 これにより、**[代替アクセス マッピング]** ボックスが開きます。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access1.png)

3. **[代替アクセス マッピング コレクション]** の横にあるドロップダウン リストで、**[Change Alternative Access Mapping Collection] (代替アクセス マッピング コレクションの変更)** を選択します。
4. サイト (例: **[SharePoint - 80]**) を選択します。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access2.png)

5. 内部 URL またはパブリック URL のいずれかとして、公開 URL を追加できます。 この例では、**パブリック URL** をエクストラネットとして使用します。
6. **[エクストラネット]** パスで **[パブリック URL の編集]** をクリックし、前の手順のように、アプリケーションを発行するパスを入力します。 たとえば、「_https://sharepoint-iddemo.msappproxy.net_」と入力します。

  ![Azure AD アプリケーション プロキシ コネクタ](./media/application-proxy-remote-sharepoint/remote-sharepoint-alternate-access3.png)

7. **[保存]**をクリックします。 

 これで、Azure AD アプリケーション プロキシ経由で外部から SharePoint サイトにアクセスできるようになりました。

##<a name="next-steps"></a>次のステップ

[オンプレミス アプリケーションへの安全なリモート アクセスを実現する方法](active-directory-application-proxy-get-started.md)<br>
[Understand Azure AD Application Proxy Connectors (Azure AD アプリケーション プロキシ コネクタを理解する)](application-proxy-understand-connectors.md)<br>
[Publishing SharePoint 2016 and Office Online Server with Azure AD Application Proxy (Azure AD アプリケーション プロキシによる SharePoint 2016 および Office Online Server の公開)](https://blogs.technet.microsoft.com/dawiese/2016/06/09/publishing-sharepoint-2016-and-office-online-server-with-azure-ad-application-proxy/)









<!--HONumber=Feb17_HO1-->


