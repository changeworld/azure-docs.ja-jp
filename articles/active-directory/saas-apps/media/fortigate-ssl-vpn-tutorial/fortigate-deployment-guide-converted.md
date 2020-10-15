---
title: FortiGate デプロイ ガイド | Microsoft Docs
description: 次世代のファイアウォール製品である Fortinet FortiGate を設定して使用します。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 18a3d9d5-d81c-478c-be7e-ef38b574cb88
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 357eb0a60e6246996de9ab75337ecc213d845ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273332"
---
# <a name="fortigate-deployment-guide"></a>FortiGate デプロイ ガイド

このデプロイ ガイドを使用して、次世代のファイアウォール製品である Fortinet FortiGate を設定して使用する方法を理解してください。

## <a name="redeem-the-fortigate-license"></a>FortiGate ライセンスを引き替える

Fortinet FortiGate は次世代のファイアウォール製品であり、Azure IaaS (サービスとしてのインフラストラクチャ) の仮想マシンとして使用できます。 この仮想マシンには、従量課金制とライセンス持ち込みの 2 種類のライセンス モデルがあります。

Fortinet によって、Azure Active Directory (Azure AD) の "運用環境の安全なハイブリッド アクセス (SHA) の実現" チームのメンバーにライセンスが提供される場合があります。 ライセンスが提供されていない場合は、従量課金制デプロイも使用できます。

ライセンスが発行されている場合、Fortinet によりオンラインで引き替える必要がある登録コードが提供されます。

![FortiGate SSL VPN 登録コードのスクリーンショット。](registration-code.png)

1. https://support.fortinet.com/ で登録します。
2. 登録後、 https://support.fortinet.com/ でサインインします。
3. **[Asset]\(アセット\)**  >  **[Register/Activate]\(登録/アクティブ化\)** に移動します。
4. Fortinet によって提供された登録コードを入力します。
5. 登録コードを指定し、 **[The product will be used by a non-government user]\(製品を政府以外のユーザーが使用する\)** を選択して、 **[Next]\(次へ\)** を選択します。
6. 製品の説明 (例: FortiGate) を入力し、[Fortinet partner]\(Fortinet パートナー\) を **[Other]\(その他\)**  >  **[Microsoft]** と設定して、 **[Next]\(次へ\)** を選択します。
7. **[Fortinet Product Registration Agreement]\(Fortinet 製品登録契約\)** に同意し、 **[Next]\(次へ\)** を選択します。
8. **[Terms]\(使用条件\)** に同意して、 **[Confirm]\(確認\)** を選択します。
9. **[License File Download]\(ライセンス ファイルのダウンロード\)** を選択し、後で使用するためにライセンスを保存します。


## <a name="download-firmware"></a>ファームウェアをダウンロードする

現在、Fortinet FortiGate Azure VM には、SAML 認証に必要なファームウェア バージョンは付属していません。 最新バージョンを Fortinet から取得する必要があります。

1. [https://ml.azure.com](https://support.fortinet.com/ ) でサインインします。
2. **[Download]\(ダウンロード\)**  >  **[Firmware Images]\(ファームウェア イメージ\)** に移動します。
3. **[Release Notes]\(リリース ノート\)** の右側にある **[Download]\(ダウンロード\)** を選択します。
4. **v6.** > **6.** > **6.4.** を選択します。
5. 同じ行の **[HTTPS]** リンクを選択して、**FGT_VM64_AZURE-v6-build1637-FORTINET.out** をダウンロードします。
6. 後で使用するためにファイルを保存します。


## <a name="deploy-the-fortigate-vm"></a>FortiGate VM をデプロイする

1. https://portal.azure.com に移動し、FortiGate 仮想マシンをデプロイするサブスクリプションにサインインします。
2. 新しいリソース グループを作成するか、FortiGate 仮想マシンをデプロイするリソース グループを開きます。
3. **[追加]** を選択します。
4. **[Marketplace を検索]** に「*Forti*」と入力します。 **[Fortinet FortiGate Next-Generation Firewall]\(Fortinet FortiGate 次世代ファイアウォール\)** を選択します。
5. ソフトウェア プランを選択します (ライセンスを持っている場合はライセンス持ち込み、それ以外の場合は従量課金制)。 **［作成］** を選択します
6. VM の構成を設定します。

    ![[仮想マシンの作成] のスクリーンショット。](virtual-machine.png)

7. **[認証の種類]** を **[パスワード]** に設定し、VM の管理者資格情報を指定します。
8. **[確認および作成]** > **[作成]** の順に選択します。
9. VM のデプロイが完了するまで待ちます。


### <a name="set-a-static-public-ip-address-and-assign-a-fully-qualified-domain-name"></a>静的パブリック IP アドレスを設定して完全修飾ドメイン名を割り当てる

一貫したユーザー エクスペリエンスにするには、FortiGate VM に割り当てられるパブリック IP アドレスを静的に割り当てるように設定します。 さらに、それを完全修飾ドメイン名 (FQDN) にマップします。

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます。
2. **[概要]** 画面で、パブリック IP アドレスを選択します。

    ![FortiGate SSL VPN のスクリーンショット。](public-ip-address.png)

3. **[静的]**  >  **[保存]** を選択します。

FortiGate VM がデプロイされる環境に対してパブリックにルーティング可能なドメイン名を所有している場合は、VM のホスト (A) レコードを作成します。 このレコードは、静的に割り当てられた前のパブリック IP アドレスにマップされます。

### <a name="create-a-new-inbound-network-security-group-rule-for-tcp-port"></a>TCP ポートの新しい受信ネットワーク セキュリティ グループ規則を作成する

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[ネットワーク]** を選択します。 ネットワーク インターフェイスの一覧が表示され、受信ポート規則が示されます。
3. **[受信ポートの規則を追加する]** を選択します。
4. TCP 8443 に対する新しい受信ポート規則を作成します。

    ![[受信セキュリティ規則の追加] のスクリーンショット。](port-rule.png)

5. **[追加]** を選択します。


## <a name="create-a-custom-azure-app-for-fortigate"></a>FortiGate 用のカスタム Azure アプリを作成する

1. https://portal.azure.com に移動し、FortiGate サインイン用の ID が提供されているテナントの Azure AD ペインを開きます。
2. 左側のメニューで、 **[エンタープライズ アプリケーション]** を選択します。
3. **[新しいアプリケーション]**  >  **[ギャラリー以外のアプリケーション]** を選択します。
4. 名前 (FortiGate など) を入力し、 **[追加]** を選択します。
5. 左側のメニューで **[ユーザーとグループ]** を選択します。
6. サインインできるユーザーを追加して、 **[割り当て]** を選択します。
7. 左側のメニューで、 **[シングル サインオン]** を選択します。
8. **[SAML]** を選択します。
9. **[基本的な SAML 構成]** で、鉛筆アイコンを選択して構成を編集します。
10. 次を構成します。
    - **[識別子 (エンティティ ID)]** : `https://<address>/remote/saml/metadata`。
    - **[応答 URL (Assertion Consumer Service URL)]** : `https://<address>/remote/saml/login`。
    - **[ログアウト URL]** : `https://<address>/remote/saml/logout`。

    `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

11. 次の各 URL は後で使用するので記録しておきます: エンティティ ID、応答 URL、ログアウト URL。
12. **[保存]** を選択し、 **[基本的な SAML 構成]** を閉じます。
13. **[3 - SAML 署名証明書]** の下で、 **[証明書 (Base64)]** をダウンロードし、後で使用するために保存します。
14. **[4 - 設定 (アプリ名)]** で、Azure ログイン URL、Azure AD 識別子、Azure ログアウト URL をコピーし、後で使用するために保存します。
15. **[2 - ユーザー属性と要求]** で、鉛筆アイコンを選択して構成を編集します。
16. **[新しい要求の追加]** を選択し、名前を **username** に設定します。
17. ソース属性を **user.userprincipalname** に設定します。
18. **[保存]**  >  **[グループ要求を追加する]**  >  **[すべてのグループ]** を選択します。
19. **[グループ要求の名前をカスタマイズする]** を選択し、名前を **group** に設定します。
20. **[保存]** を選択します。


## <a name="prepare-for-group-matching"></a>グループ マッチングを準備する

FortiGate では、グループ メンバーシップに基づいて、サインイン後に異なるユーザーのポータル エクスペリエンスを使用できます。 たとえば、マーケティング グループ用に 1 つのエクスペリエンスがあり、財務グループ用に別のエクスペリエンスがある場合もあります。 ユーザーのグループを作成するには次のようにします。

1. https://portal.azure.com に移動し、FortiGate サインイン用の ID が提供されているテナントの Azure AD ペインを開きます。
2. **[グループ]**  >  **[新しいグループ]** を選択します。
3. 次の詳細を使用してグループを作成します。
    - グループの種類 = セキュリティ
    - グループ名 = `a meaningful name`
    - グループの説明 = `a meaningful description for the group`
    - メンバーシップの種類 = 割り当て済み
    - メンバー = `users for the user experience that will map to this group`
4. 追加のユーザー エクスペリエンスごとにステップ 3 と 4 を繰り返します。
5. グループを作成した後、各グループを選択し、それぞれの **[オブジェクト ID]** を記録します。
6. これらのオブジェクト ID とグループ名を後で使用するために保存します。


## <a name="configure-the-fortigate-vm"></a>FortiGate VM を構成する

以下のセクションでは、FortiGate VM を設定する方法について説明します。

### <a name="install-the-license"></a>ライセンスをインストールする

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. デプロイでライセンス持ち込みモデルが使用されている場合は、ライセンスのアップロードを求めるメッセージが表示されます。 前に作成したライセンス ファイルを選択してアップロードします。 **[OK]** を選択し、FortiGate VM を再起動します。

    ![FortiGate VM ライセンスのスクリーンショット。](license.png)

5. 再起動後に、ライセンスを検証するために管理者の資格情報を使用してもう一度サインインします。

### <a name="update-firmware"></a>ファームウェアを更新する

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Firmware]\(ファームウェア\)** を選択します。
5. **[Firmware Management]\(ファームウェアの管理\)** で **[Browse]\(参照\)** を選択して、先ほどダウンロードしたファームウェア ファイルを選択します。
6. 警告を無視し、 **[Backup config and upgrade]\(構成をバックアップしてアップグレード\)** を選択します。

    ![ファームウェアの管理のスクリーンショット。](backup-configure-upgrade.png)

7. **[続行]** をクリックします。
8. FortiGate の構成を (.conf ファイルとして) 保存するように求められたら、 **[Save]\(保存\)** を選択します。
9. ファームウェアがアップロードされて適用されるまで待ちます。 FortiGate VM が再起動されるまで待ちます。
10. FortiGate VM が再起動した後、管理者の資格情報を使用してもう一度サインインします。
11. ダッシュボードの設定を求めるメッセージが表示されたら、 **[Later]\(後で\)** を選択します。
12. チュートリアル ビデオが開始したら、 **[OK]** を選択します。

### <a name="change-the-management-port-to-tcp"></a>管理ポートを TCP に変更する

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)** を選択します。
5. **[Administration Settings]\(管理設定\)** で HTTPS ポートを **8443** に変更し、 **[Apply]\(適用\)** を選択します。
6. 変更が適用されると、ブラウザーで管理ページの再読み込みが試みられますが、失敗します。 これ以降、管理ページのアドレスは `https://<address>` になります。

    ![リモート証明書アップロードのスクリーンショット。](certificate.png)

### <a name="upload-the-azure-ad-saml-signing-certificate"></a>Azure AD SAML 署名証明書をアップロードする

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)** を選択します。
5. **[Import]\(インポート\)**  >  **[Remote Certificate]\(リモート証明書\)** の順に選択します。
6. Azure テナントの FortiGate カスタム アプリのデプロイからダウンロードした証明書を参照します。 それを選択して、 **[OK]** を選択します。

### <a name="upload-and-configure-a-custom-ssl-certificate"></a>カスタム SSL 証明書をアップロードして構成する

使用している FQDN をサポートする独自の SSL 証明書を使用して FortiGate VM を構成することもできます。 .PFX 形式の秘密キーでパッケージ化された SSL 証明書にアクセスできる場合、この目的でこれを使用できます。

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. 証明書エラーを無視して続行します。
3. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
4. 左側のメニューで、 **[System]\(システム\)**  >  **[Certificates]\(証明書\)** を選択します。
5. **[Import]\(インポート\)**  >  **[Local Certificate]\(ローカル証明書\)**  >  **[PKCS #12 Certificate]\(PKCS #12 証明書\)** を選択します。
6. SSL 証明書と秘密キーが含まれる .PFX ファイルを参照します。
7. .PFX パスワードと、証明書のわかりやすい名前を指定します。 **[OK]** をクリックします。
8. 左側のメニューで、 **[System]\(システム\)**  >  **[Settings]\(設定\)** を選択します。
9. **[Administration Settings]\(管理設定\)** で、 **[HTTPS server certificate]\(HTTPS サーバー証明書\)** の隣にある一覧を展開し、前にインポートした SSL 証明書を選択します。
10. **[適用]** を選択します。
11. ブラウザー ウィンドウを閉じて、`https://<address>` に移動します。
12. FortiGate 管理者の資格情報でサインインします。 正しい SSL 証明書が使用されていることがわかります。


### <a name="perform-command-line-configuration"></a>コマンド ラインの構成を実行する

以下のセクションでは、コマンド ラインを使用してさまざまな構成を行う方法について説明します。

#### <a name="for-saml-authentication"></a>SAML 認証の場合

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[シリアル コンソール]** を選択します。
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします。 次のステップでは、前に記録した URL が必要です。
    - エンティティ ID
    - [応答 URL]
    - ログアウト URL
    - Azure ログイン URL
    - Azure AD 識別子
    - Azure ログアウト URL
4. シリアル コンソールで、次のコマンドを実行します。

    ```
    config user saml
    edit azure
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert REMOTE_Cert_
    set user-name username
    set group-name group
    end
    ```
    > [!NOTE]
    > Azure ログアウト URL には `?` 文字が含まれています。 これは、FortiGate シリアル コンソールに正しく提供されるために、特殊なキー シーケンスを必要とします。 通常、URL は `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1` のようになります。 シリアル コンソールでこれを指定するには、次のように入力します。
        ```
        set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation
        ```.
    次に、Ctrl + V キーを押して残りの URL を貼り付け、行を完成させます。 
        ```
        set idp-single-logout-url
        https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.
        ```

5. 構成を確認するには、次のように実行します。

    ```
    show user saml
    ```

#### <a name="for-group-matching"></a>グループ マッチングの場合

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[シリアル コンソール]** を選択します。
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします。
4. シリアル コンソールで、次のコマンドを実行します。

    ```
    config user group
    edit <group 1 name>
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <group 1 Object Id>
    next
    end
    next
    ```

    FortiGate でポータル エクスペリエンスが異なる追加のグループごとに、これらのコマンドを繰り返します (コードの 2 行目から始めます)。

#### <a name="for-authentication-timeout"></a>認証タイムアウトの場合

1. https://portal.azure.com に移動し、FortiGate VM の設定を開きます。
2. 左側のメニューで **[シリアル コンソール]** を選択します。
3. FortiGate VM 管理者の資格情報を使用してシリアル コンソールにサインインします。
4. シリアル コンソールで、次のコマンドを実行します。

    ```
    config system global
    set remoteauthtimeout 60
    end
    ```
### <a name="create-vpn-portals-and-firewall-policy"></a>VPN ポータルとファイアウォール ポリシーを作成する

1. `https://<address>` にアクセスします。 `<address>` は、FortiGate VM に割り当てられている FQDN またはパブリック IP アドレスです。

2. FortiGate VM のデプロイ中に指定した管理者資格情報を使用してサインインします。
3. 左側のメニューで、 **[VPN]**  >  **[SSL-VPN Portals]\(SSL-VPN ポータル\)**  >  **[Create New]\(新規作成\)** を選択します。
6. 名前を指定します (通常は、カスタム ポータル エクスペリエンスを提供するために使用される Azure グループと一致させます)。
7. **[Source IP Pools]\(ソース IP プール\)** の横にあるプラス記号 **[+]** を選択し、既定のプールを選択して、 **[Close]\(閉じる\)** を選択します。
8. このグループのエクスペリエンスをカスタマイズします。 テストの場合、ポータルのメッセージとテーマをカスタマイズできます。 ここでは、ユーザーを内部リソースに誘導するカスタム ブックマークを作成することもできます。
9. **[OK]** を選択します。
10. カスタムのポータル エクスペリエンスを持つことになる Azure グループごとに、ステップ 5 から 9 を繰り返します。
11. [VPN] で **[SSL-VPN Settings]\(SSL-VPN 設定\)** を選択します。
12. **[Listen on Interfaces]\(リッスンするインターフェイス\)** の隣のプラス記号 **[+]** を選択し、 **[Port1]** を選択して、 **[Close]\(閉じる\)** を選択します。
14. 前にカスタム SSL 証明書をインストールした場合は、ドロップダウン メニューでカスタム SSL 証明書を使用するように **[Server Certificate]\(サーバー証明書\)** を変更します。
15. **[Authentication/Portal Mapping]\(認証/ポータル マッピング\)** で、 **[Create New]\(新規作成\)** を選択します。 最初の Azure グループを選択し、これを同じ名前のポータルと一致させます。 **[OK]** をクリックします。
18. Azure グループとポータルのペアごとに、ステップ 15 から 17 を繰り返します。
19. **[Authentication/Portal Mapping]\(認証/ポータル マッピング\)** で、 **[All Other Users/Groups]\(他のすべてのユーザー/グループ\)** を編集します。
20. ポータルを **[full-access]\(フルアクセス\)** に設定し、 **[OK]**  >  **[Apply]\(適用\)** を選択します。
23. **[SSL-VPN Setting]\(SSL-VPN 設定\)** ページの一番上までスクロールし、 **[No SSL-VPN policies exist. Click here to create a new SSL-VPN policy using these settings]\(SSL-VPN ポリシーが存在しません。ここをクリックしてこれらの設定を使用して新しい SSL-VPN ポリシーを作成します\)** を選択します。
24. 名前 (**VPN Grp** など) を指定します。 次に、 **[Outgoing Interface]\(送信インターフェイス\)** を **[port]\(ポート\)** に設定し、 **[Source]\(ソース\)** を選択します。
27. **[Address]\(アドレス\)** で **[all]\(すべて\)** を選択します。
28. **[User]\(ユーザー\)** で、最初の Azure グループを選択します。
29. **[Close]\(閉じる\)**  >  **[Destination]\(ターゲット\)** を選択します。 **[Address]\(アドレス\)** では、通常これは内部ネットワークです。 テスト用に **login.microsoft.com** を選択します。
32. **[Close]\(閉じる\)**  >  **[Service]\(サービス\)**  >  **[All]\(すべて\)** を選択します。 **[Close]\(閉じる\)**  >  **[OK]** を選択します。
37. 左側のメニューで、 **[Policy & Objects]\(ポリシーとオブジェクト\)**  >  **[Firewall Policy]\(ファイアウォール ポリシー\)** を選択します。
39. **[SSL-VPN tunnel interface (ssl.root)]\(SSL-VPN トンネル インターフェイス (ssl.root)\)**  >  **[port]\(ポート\)** を展開します。
40. 先ほど作成した VPN ポリシー (**VPN Grp 1**) を右クリックし、 **[Copy]\(コピー\)** を選択します。
41. VPN ポリシーの下で右クリックし、 **[Paste]\(貼り付け\)**  >  **[Below]\(下\)** を選択します。
42. 新しいポリシーを編集し、別の名前 (**VPN Grp2** など) を指定します。 また、適用先のグループを (別の Azure グループに) 変更します。
43. 新しいポリシーを右クリックし、状態を **[Enabled]\(有効\)** に設定します。


## <a name="test-sign-in-by-using-azure"></a>Azure を使用してサインインをテストする

1. プライベート ブラウザー セッションを使用して、`https://<address>` に移動します。  
2. サインインによって、サインイン用の Azure AD にリダイレクトされます。
3. Azure テナントで FortiGate アプリに割り当てられているユーザーの資格情報を指定すると、適切なユーザー ポータルが表示されます。

    ![FortiGate SSL VPN のスクリーンショット](test-sign-in.png)
