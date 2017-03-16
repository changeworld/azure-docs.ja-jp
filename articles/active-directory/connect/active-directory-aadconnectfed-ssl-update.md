---
title: "Azure AD Connect: Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書の更新 | Microsoft Docs"
description: "このドキュメントでは、Azure AD Connect を使って AD FS ファームの SSL 証明書を更新する手順について詳しく説明します。"
services: active-directory
keywords: "Azure AD Connect, AD FS SSL の更新, AD FS 証明書の更新, 変更、AD FS 証明書, 新しい AD FS 証明書, AD FS 証明書, 更新、AD FS SSL 証明書, 更新、SSL 証明書、AD FS, 構成、AD FS SSL 証明書, AD FS, SSL, 証明書, AD FS サービス通信証明書, 更新、フェデレーション, 構成、フェデレーション, AAD Connect"
authors: anandyadavmsft
manager: femila
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2016
ms.author: anandy
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: d8f4181d7b51109cc5a2e205a40225a7763d0be4
ms.lasthandoff: 03/07/2017


---    

# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Active Directory フェデレーション サービス (AD FS) ファームの SSL 証明書の更新

## <a name="overview"></a>概要
この記事では、Azure AD Connect を使って Active Directory Federation Services (AD FS) ファームの SSL 証明書を更新する方法について説明します。  Azure AD Connect のサインオン方法が AD FS に設定されている場合、Azure AD Connect ツールを使って AD FS ファーム用の SSL 証明書を簡単に更新できます。すべてのフェデレーション サーバーと WAP サーバーを簡単な 3 手順で更新できます。

![3 つの手順](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>AD FS によって使われる証明書について詳しくは、「[AD FS で使用される証明書とは](https://technet.microsoft.com/library/cc730660.aspx)」をご覧ください

##<a name="pre-requisites"></a>前提条件

* **AD FS ファーム**: AD FS ファームは 2012R2 以降である必要があります。
* **Azure AD Connect**: Azure AD Connect のバージョンが 1.1.443.0 以降であることを確認します。 このバージョンであれば、"AD FS SSL 証明書を更新します" タスクが見つかります。

![SSL の更新タスク](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

##<a name="step-1-provide-ad-fs-farm-information"></a>手順 1: AD FS ファームの情報を提供する

AAD Connect は、次の方法で自動的に AD FS ファームに関する情報の取得を試みます。
1. AD FS (2016 以降) でファームの情報を照会します
2. 以前の実行の情報を参照します (Azure AD Connect でローカルに保存されたもの) 

表示されるサーバーの一覧にサーバーを追加したり、一覧からサーバーを削除したりして、AD FS ファームの現在の構成を反映できます。 サーバーの情報が提供されるとすぐに、Azure AD Connect は接続の状態と現在の SSL 証明書の状態を表示します。

![AD FS サーバーの情報](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

AD FS ファームの一部ではなくなったサーバーが一覧に含まれる場合は、[削除] をクリックして、AD FS ファームのサーバーの一覧からそのサーバーを削除します。 

![一覧でのオフライン サーバー](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)
 
>[!NOTE] 
> Azure AD Connect での AD FS ファームのサーバー一覧からの削除はローカルな操作であり、Azure AD Connect がローカルに保持する AD FS ファームの情報が更新されます。 Azure AD Connect が変更を反映するために AD FS の構成を変更することはありません。    

##<a name="step-2-provide-new-ssl-certificate"></a>手順 2: 新しい SSL 証明書を提供する

AD FS ファームのサーバーに関する情報の確認が済むと、Azure AD Connect は新しい SSL 証明書を要求します。 パスワードで保護された PFX 証明書を提供して、インストールを続けます。 

![SSL 証明書](./media/active-directory-aadconnectfed-ssl-update/certificate.png)
 
証明書を提供すると、Azure AD Connect は証明書について一連の前提条件チェックを行い、証明書が AD FS ファームに対して正しいことを確認します。
1.    証明書のサブジェクト名 (SN)/サブジェクト代替名 (SAN) がフェデレーション サービス名と同じであるか、またはワイルドカード証明書であること
2.    証明書の有効期間が 30 日より長いこと
3.    証明書の信頼チェーンが有効であること 
4.    証明書がパスワードで保護されていること

##<a name="step-3-select-servers-for-update"></a>手順 3: 更新するサーバーを選ぶ

次の手順では、SSL 証明書を更新する必要があるサーバーを選びます。 オフラインになっているサーバーを更新対象に選ぶことはできません。 

![更新するサーバーを選ぶ](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

構成が完了すると、更新の状態を示すメッセージと、AD FS ログインを確認するためのオプションが表示されます。

![構成の完了](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

##<a name="faqs"></a>FAQ

* **新しい AD FS SSL 証明書のサブジェクト名はどのようなものにする必要がありますか。**

    Azure AD Connect は、証明書のサブジェクト名と代替サブジェクト名にフェデレーション サービスの名前が含まれるかどうかを確認します。 たとえば、フェデレーション サービス名が fs.contoso.com である場合、サブジェクト名と代替サブジェクト名も fs.contoso.com である必要があります。  ワイルドカード証明書も受け付けられます。 

* **WAP サーバー ページで資格情報を再度要求されるのはなぜですか。**

    AD FS サーバー接続用に提供された資格情報に、WAP サーバーを管理するための権限がない場合、Azure AD Connect は WAP サーバーの管理権限のある資格情報を要求します。

* **サーバーがオフラインとして表示される場合は、どうすればよいでしょうか。**

    サーバーがオフラインの場合、Azure AD Connect はどのような操作も実行できません。 サーバーが AD FS ファームの一部である場合は、サーバーの接続状態を確認し、問題を解決した後、更新アイコンをクリックしてウィザードに表示される状態を更新します。 以前はファームの一部であったサーバーが存在しなくなっている場合は、[削除] をクリックして、Azure AD Connect が保持するサーバーの一覧から削除します。  Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成自体は変更されません。AD FS 2016 以降を使っている場合は、サーバーは構成の設定に残っており、次にタスクを実行すると再び表示されます。

* **新しい SSL 証明書でファーム サーバーのサブセットを更新できますか。**

    はい。 いつでも、"SSL 証明書の更新" タスクを再び実行して、残りのサーバーを更新できます。 [SSL 証明書の更新を実行するサーバーを選択します] ページでは、[SSL の有効期限] でサーバーの一覧を並べ替えて、まだ更新されていないサーバーに簡単にアクセスできます。 

* **以前の実行でサーバーを削除しましたが、[AD FS サーバー] ページの一覧にオフラインとしてまだ表示されます。削除後にオフライン サーバーがまだ表示されるのはなぜですか。**

    Azure AD Connect の一覧からサーバーを削除しても、AD FS の構成では削除されません。 Azure AD Connect は AD FS (2016 以降) でもファームに関する情報を参照し、AD FS の構成にサーバーがまだ存在する場合は、一覧に再び表示します。  

## <a name="next-steps"></a>次のステップ

[Azure AD Connect とフェデレーション](active-directory-aadconnectfed-whatis.md)
[Azure AD Connect による Active Directory フェデレーション サービスの管理とカスタマイズ](active-directory-aadconnect-federation-management.md)

