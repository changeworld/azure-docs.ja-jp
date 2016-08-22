<properties
	pageTitle="O365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する | Microsoft Azure"
	description="このページは、O365 でフェデレーション信頼の SHA アルゴリズムを変更するガイドラインについて説明します。"
    keywords="SHA1、SHA256、O365、フェデレーション、aadconnect、adfs、ad fs、sha の変更、フェデレーション信頼、証明書利用者信頼"
	services="active-directory"
	documentationCenter=""
	authors="anandyadavmsft"
	manager="samueld"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="anandy"/>

#O365 証明書利用者信頼の署名ハッシュ アルゴリズムを変更する

##Overview

AD FS はAzure Active Directory にそのトークンを署名し、改ざんできないようにします。この署名には、SHA1 または SHA256 を使用できます。Microsoft Azure Active Directory は SHA256 アルゴリズムで署名されたトークンをサポートするようになり、最上位レベルのセキュリティ確保のためトークン署名アルゴリズムを SHA256 に設定することを推奨します。この記事では、トークン署名アルゴリズムをより安全性の高い SHA 256 レベルに設定するステップについて説明します。

##トークン署名アルゴリズムを変更する

以下のステップに示すように署名アルゴリズムを設定すると、AD FS は O365 証明書利用者信頼のトークンを SHA-256 で署名することを開始します。追加の構成変更は不要で、この変更を加えても Office 365 やその他 Azure AD アプリケーションにアクセスするエンド ユーザーには影響しません。

###管理コンソールを使用する

* プライマリ AD FS サーバーで AD FS 管理コンソールを開きます。
* AD FS ノードを展開し、[証明書利用者信頼] をクリックします。
* O365/Azure 証明書利用者信頼を右クリックし、[プロパティ] を選択します。
* [詳細設定] タブを選択し、セキュア ハッシュ アルゴリズムとして SHA 256 を選択します。
* [OK] をクリックします。

![SHA256 署名アルゴリズム - MMC](./media/active-directory-aadconnectfed-sha256guidance/mmc.png)

###AD FS PowerShell コマンドレットを使用する

* すべての AD FS サーバーでは、管理者特権で PowerShell を開きます。
* Set-AdfsRelyingPartyTrust コマンドレットを使用してセキュア ハッシュ アルゴリズムを設定します。

 <code>Set-AdfsRelyingPartyTrust -TargetName 'Microsoft Office 365 Identity Platform' -SignatureAlgorithm 'http://www.w3.org/2001/04/xmldsig-more#rsa-sha256'</code>

##追加情報

* [AAD Connect で O365 信頼を修復する](./active-directory-aadconnect-federation-management.md#repairing-the-trust)

<!---HONumber=AcomDC_0810_2016-->