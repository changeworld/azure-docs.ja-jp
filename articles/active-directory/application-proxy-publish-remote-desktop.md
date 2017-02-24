---
title: "Azure Active Directory アプリケーション プロキシを使用したリモート デスクトップの公開 | Microsoft Docs"
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
ms.sourcegitcommit: 40ddf7d6648561eab855b41e76a5ca509c6ca4ef
ms.openlocfilehash: 05130d728c579e0b778bdc8ec49e01f2223c2bba


---

# <a name="publish-remote-desktop-with-azure-active-directory-application-proxy"></a>Azure Active Directory アプリケーション プロキシを使用したリモート デスクトップの公開

この記事では、リモート ユーザーがリモート デスクトップのデプロイメントにアクセスできるようにする方法について説明します。 このようなリモート デスクトップのデプロイメントは、オンプレミスまたはプライベート ネットワーク (laaS デプロイメントなど) に配置できます。 

> [!NOTE]
> アプリケーション プロキシは、Azure Active Directory の Premium または Basic エディションにアップグレードしている場合にのみ利用できる機能です。 詳細については、「[Azure Active Directory のエディション](active-directory-editions.md)」をご覧ください。
> 
 

リモート デスクトップ プロトコルのトラフィックは、パススルー プロキシ アプリケーションとしてアプリケーション プロキシ経由で公開できます。 このソリューションは、接続の問題を解決し、ネットワーク バッファリング、セキュリティを強化したインターネットのフロント エンド、DDoS 保護などの基本的なセキュリティ保護を提供します。 

##<a name="remote-desktop-deployment"></a>リモート デスクトップのデプロイメント

リモート デスクトップのデプロイメント内で、リモート デスクトップ ゲートウェイを公開すると、HTTPS トラフィック経由の RPC を、UDP トラフィック経由の RDP に変換できます。

MSTSC.exe などのリモート デスクトップ クライアントを使用して、Azure AD アプリケーション プロキシにアクセスするようにクライアントを構成できます。 これにより、コネクタを使用してリモート デスクトップ ゲートウェイへの新しい HTTPS 接続を作成できます。 これで、リモート デスクトップ ゲートウェイはインターネットに直接公開されなくなり、すべての HTTPS 要求はまずクラウドで終了します。 

次の図にトポロジを示します。

 ![AzureAD サービスのローカル](./media/application-proxy-publish-remote-desktop/remote-desktop-topology.png)

## <a name="configure-the-remote-desktop-gateway-url"></a>リモート デスクトップ ゲートウェイの URL の構成

ユーザーがリモート デスクトップ ゲートウェイの URL を構成していれば、通常通りに RDP トラフィックをトリガーすると、ファイルとその他のメソッドにアクセスできます。

アプリケーション プロキシ (msappproxy.net) で指定されるドメイン名、または Azure AD で構成されているカスタム ドメイン名 (rdg.contoso.com など) を使用して発行できます。 

クライアント デバイスと RDP ファイルにすでにリモート デスクトップ ゲートウェイの URL が構成されている場合は、同じドメイン名を使用して変更を回避することもできます。 その場合は、アプリケーション プロキシがこのドメインを対象とする証明書を提供し、その CRL にインターネットからアクセスできる必要があります。

リモート デスクトップ ゲートウェイの URL が構成されていない場合は、ユーザーまたは管理者が、下記のようにリモート デスクトップ クライアント (MSTSC) でリモート デスクトップ接続ボックスを使用して指定できます。

 ![AzureAD サービスのローカル](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-advanced.png)

**[詳細]** で **[設定]** をクリックすると、[接続設定] ボックスが表示されます。

 ![AzureAD サービスのローカル](./media/application-proxy-publish-remote-desktop/remote-desktop-connection-settings.png)

## <a name="remote-desktop-web-access"></a>リモート デスクトップの Web アクセス

リモート デスクトップ Web アクセス (RDWA) ポータルを使用している組織の場合は、Azure AD アプリケーション プロキシを使用して公開することもできます。 事前認証とシングル サインオン (SSO) を使用してこのポータルに公開できます。

次の図にこの場合のトポロジを示します。

 ![AzureAD サービスのローカル](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal1.png)

上記の場合は、ユーザーは RDWA にアクセスする前に Azure AD で認証されます。 Azure AD で既に認証されている場合 (Office 365 を使用している場合など) は、RDWA のために再認証する必要はありません。

ユーザーが RDP セッションを開始するときには、RDP チャネル経由で再認証する必要があります。 これは、RDWA からリモート デスクトップ ゲートウェイへの SSO が、ActiveX を使用してクライアントにエンドユーザーの資格情報を格納するためです。 RDWA フォームベースの認証から、このプロセスがトリガーされます。 RDWA 認証が Kerberos を使用しているときにはフォーム ベース認証が行われず、RDWA から RDP への SSO は機能しません。

RDWA で RDP トラフィックへの SSO が必要な場合、または RDWA フォームベース認証が大きくカスタマイズされている場合は、事前認証なしで RDWA を発行できます。

次の図にこの場合のトポロジを示します。

 ![AzureAD サービスのローカル](./media/application-proxy-publish-remote-desktop/remote-desktop-web-access-portal2.png)

上記の場合は、ユーザーがフォームベース認証を使用して RDWA を認証する必要がありますが、RDP 経由で認証する必要はありません。 

どちらの場合も、RDP トラフィックで事前認証が必要ではないことに注意する必要があります。 そのため、ユーザーは最初に RDWA を経由せずにアクセスできます。

##<a name="next-steps"></a>次のステップ

[Azure AD アプリケーション プロキシによる SharePoint へのリモート アクセスの有効化](application-proxy-enable-remote-access-sharepoint.md)<br>
[Azure Portal でアプリケーション プロキシを有効にする](https://github.com/Microsoft/azure-docs-pr/blob/master/articles/active-directory/active-directory-application-proxy-enable.md)



<!--HONumber=Feb17_HO1-->


