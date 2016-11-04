---
title: アプリケーション プロキシで要求に対応するアプリケーションを利用する
description: Azure AD アプリケーション プロキシをセットアップする方法について説明します。
services: active-directory
documentationcenter: ''
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2016
ms.author: kgremban

---
# アプリケーション プロキシで要求に対応するアプリケーションを利用する
要求に対応するアプリケーションは、Security Token Service (STS) へのリダイレクトを行います。STS は、ユーザーをアプリケーションにリダイレクトする前に、ユーザーにトークンの代わりの資格情報を要求します。これらのリダイレクトで機能するようにアプリケーション プロキシを有効にするには、次の手順を実行する必要があります。

## 前提条件
この手順を実行する前に、要求に対応するアプリのリダイレクト先の STS が、オンプレミス ネットワークの外部でも使用できるようになっていることを確認してください。

## Azure クラシック ポータルの構成
1. 「[アプリケーション プロキシを使用したアプリケーションの発行](active-directory-application-proxy-publish.md)」で説明されている手順に従って、アプリケーションを発行します。
2. アプリケーションの一覧で、要求に対応するアプリケーションを選択し、**[構成]** をクリックします。
3. **[事前認証方法]** で **[パススルー]** を選択した場合は、**[外部 URL]** スキームとして **[HTTPS]** を選択してください。
4. **[事前認証方法]** として **Azure Active Directory** を選択した場合は、**[内部認証方法]** として **[なし]** を選択してください。

## ADFS 構成
1. [ADFS 管理] を開きます。
2. **[証明書利用者信頼]** に移動し、アプリケーション プロキシで発行しているアプリを右クリックして、**[プロパティ]** を選択します。![[証明書利用者信頼] でアプリ名を右クリックしているスクリーンショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. **[エンドポイント]** タブの **[エンドポイントの種類]** で、**[WS-Federation]** を選択します。
4. **[信頼された URL]** で、アプリケーション プロキシの **[外部 URL]** で入力した URL を入力し、**[OK]** をクリックします。![エンドポイントを追加し、信頼された URL 値を設定しているスクリーン ショット](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## 関連項目
* [アプリケーション プロキシを使用してアプリケーションを発行する](active-directory-application-proxy-publish.md)
* [シングル サインオンを有効にする](active-directory-application-proxy-sso-using-kcd.md)
* [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](active-directory-application-proxy-troubleshoot.md)
* [ネイティブ クライアント アプリケーションからプロキシ アプリケーションを操作できるようにする](active-directory-application-proxy-native-client.md)

最新のニュースと更新情報については、[アプリケーション プロキシに関するブログ](http://blogs.technet.com/b/applicationproxyblog/)をご覧ください。

<!---HONumber=AcomDC_0622_2016-->