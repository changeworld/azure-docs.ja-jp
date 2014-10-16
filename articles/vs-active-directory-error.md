<properties title="Error During Authentication Detection" pageTitle="Error During Authentication Detection" metaKeywords="" description="" services="active-directory" documentationCenter="" authors="ghogen, kempb" />

<tags ms.service="active-directory" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/8/2014" ms.author="ghogen, kempb"></tags>

#### 認証の検出時のエラー

ウィザードが以前の認証コードを検出するときに、互換性のない認証の種類が検出されました。

###### 確認内容

ウィザードは、Visual Studio の以前のバージョンで構成された認証コードのバージョンを検出しようと試みます。このエラーが発生した場合、互換性のない認証の種類が検出されたことを表します。ウィザードは、Visual Studio の以前のバージョンに対して次の種類の認証を検出します。

-   Windows 認証
-   個々のユーザー アカウント
-   組織アカウント

詳細については、「[Azure AD の認証シナリオ][Azure AD の認証シナリオ]」を参照してください。

  [Azure AD の認証シナリオ]: http://msdn.microsoft.com/library/azure/dn499820.aspx
