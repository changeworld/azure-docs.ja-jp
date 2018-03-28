---
title: 'Azure AD Connect: シームレス シングル サインオン - GDPR コンプライアンス | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) シームレス SSO および GDPR コンプライアンスについて説明します。
services: active-directory
keywords: Azure AD Connect とは, GDPR, Azure AD に必要なコンポーネント, SSO, シングル サインオン
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: billmath
ms.openlocfilehash: 0c7ed376accb1eed01106358491e925d3b8126c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-ad-seamless-single-sign-on-gdpr-compliance"></a>Azure AD シームレス シングル サインオン: GDPR コンプライアンス

## <a name="overview"></a>概要

2018 年 5 月に、欧州のプライバシー保護法である、[一般データ保護規則 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) が施行されます。 GDPR は、欧州連合 (EU) 内で人々に製品やサービスを提供したり、EU 居住者に関連するデータを収集および分析したりする企業、政府機関、非営利組織やその他の組織を対象とする新しいルールです。 GDPR は、組織の所在地に関係なく適用されます。 

Microsoft では、GDPR の要件を満たすのに役立つ製品やサービスを提供しています。 Microsoft のプライバシー ポリシーについては、[セキュリティ センター](https://www.microsoft.com/trustcenter)をご覧ください。

Azure AD シームレス SSO では、EUII を含めることができる次の種類のログが作成されます。

- Azure AD Connect トレース ログ ファイル。

シームレス SSO の GDPR コンプライアンスには、次の 2 つの方法で準拠できます。

1.  要請を受けた際、個人のデータを抽出し、その個人のデータを環境から削除する
2.  すべてのデータが 48 時間しか保持されないようにする。

実装や保守がより簡単なので、2 番目の方法を強くお勧めします。 ログの種類ごとの以下の手順を確認してください。

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect トレース ログ ファイルを削除する

**%ProgramData%\AADConnect** フォルダーの内容を確認して、Azure AD Connect のインストールまたはアップグレード、あるいはシームレス SSO の構成の変更から 48 時間以内のこのフォルダーのトレース ログ コンテンツ (**trace-\*.log** ファイル) を削除します。このアクションによって GDPR が適用されるデータが作成されるためです。

>[!IMPORTANT]
>このフォルダー内にある **PersistedState.xml** ファイルは削除しないでください。このファイルは Azure AD Connect の以前のインストールの状態を保持するために使用され、さらには、アップグレードのインストールが完了された場合にも使用されるためです。 このファイルが個人に関するデータを含むことはないため、絶対に削除しないでください。

これらのトレース ログ ファイルの確認と削除には Windows エクスプ ローラーを使用することもできますし、次のような PowerShell スクリプトを使用して、必要なアクションを実行することもできます。

```
$Files = ((Get-Item -Path "$env:programdata\aadconnect\trace-*.log").VersionInfo).FileName 
 
Foreach ($file in $Files) { 
    {Remove-Item -Path $File -Force} 
}
```

拡張子が ".PS1" のファイルにスクリプトを保存します。 必要に応じて、このスクリプトを実行してください。

関連する Azure AD Connect の GDPR 要件の詳細については、[こちらの記事](active-directory-aadconnect-gdpr.md)をご覧ください。

### <a name="note-about-domain-controller-logs"></a>ドメイン コントローラー ログに関する注意事項

監査ログが有効になっている場合、この製品では、お使いのドメイン コント ローラーのセキュリティ ログを生成できます。 監査ポリシーの構成に関する詳細については、[こちらの記事](https://technet.microsoft.com/library/dd277403.aspx)をご覧ください。

## <a name="next-steps"></a>次の手順

- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-sso.md) - この機能に関する一般的な問題を解決する方法を確認します。
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 新しい機能の要求を提出します。
