---
title: 'Azure AD Connect: パススルー GDPR コンプライアンス | Microsoft Docs'
description: この記事では、Azure Active Directory (Azure AD) パススルー認証および GDPR コンプライアンスについて説明します。
services: active-directory
keywords: Azure AD Connect パススルー認証, GDPR, Azure AD に必要なコンポーネント, SSO, シングル サインオン
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
ms.custom: seohack1
ms.openlocfilehash: 21874c961163e3efba45c2ee8557c03135987f95
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-active-directory-pass-through-authentication-gdpr-compliance"></a>Azure Active Directory パススルー認証: GDPR コンプライアンス

## <a name="overview"></a>概要

2018 年 5 月に、欧州のプライバシー保護法である、[一般データ保護規則 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) が施行されます。 GDPR は、欧州連合 (EU) 内で人々に製品やサービスを提供したり、EU 居住者に関連するデータを収集および分析したりする企業、政府機関、非営利組織やその他の組織を対象とする新しいルールです。 GDPR は、組織の所在地に関係なく適用されます。 

Microsoft では、GDPR の要件を満たすのに役立つ製品やサービスを提供しています。 Microsoft のプライバシー ポリシーについては、[セキュリティ センター](https://www.microsoft.com/trustcenter)をご覧ください。

Azure AD パススルー認証では、EUII を含めることができる次の種類のログが作成されます。

- Azure AD Connect トレース ログ ファイル。
- 認証エージェント トレース ログ ファイル。
- Windows イベント ログ ファイル。

パススルー認証の GDPR コンプライアンスには、次の 2 つの方法で準拠できます。

1.  要請を受けた際、個人のデータを抽出し、その個人のデータを環境から削除する
2.  すべてのデータが 48 時間しか保持されないようにする。

実装や保守がより簡単なので、2 番目の方法を強くお勧めします。 以下に、ログの種類ごとの手順を示します。

### <a name="delete-azure-ad-connect-trace-log-files"></a>Azure AD Connect トレース ログ ファイルを削除する

**%ProgramData%\AADConnect** フォルダーの内容を確認して、Azure AD Connect のインストールまたはアップグレード、あるいはパススルー認証の構成の変更から 48 時間以内のこのフォルダーのトレース ログ コンテンツ (**trace-\*.log** ファイル) を削除します。このアクションによって GDPR が適用されるデータが作成されるためです。

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

### <a name="delete-authentication-agent-event-logs"></a>認証エージェントのイベント ログを削除する

この製品では、**Windows イベント ログ**が作成されることもあります。 詳細については、[こちらの記事](https://msdn.microsoft.com/library/windows/desktop/aa385780(v=vs.85).aspx)をご覧ください。

パススルー認証エージェントに関するログを表示するには、サーバーで**イベント ビューアー** アプリケーションを開き、**アプリケーションとサービス ログ\Microsoft\AzureAdConnect\AuthenticationAgent\Admin** の下を調べます。

### <a name="delete-authentication-agent-trace-log-files"></a>認証エージェント トレース ログ ファイルを削除する

**%ProgramData%\Microsoft\Azure AD Connect Authentication Agent\Trace\** の内容を定期的に確認して、48 時間ごとにこのフォルダーの内容を削除する必要があります。 

>[!IMPORTANT]
>認証エージェント サービスが実行中の場合は、フォルダー内の現在のログ ファイルを削除できません。 サービスを停止してから、再試行してください。 ユーザーのサインイン エラーを回避するには、[高可用性](active-directory-aadconnect-pass-through-authentication-quick-start.md#step-5-ensure-high-availability)に対応するパススルー認証を既に構成している必要があります。

これらのファイルの確認と削除には Windows エクスプ ローラーを使用することもできますし、次のようなスクリプトを使用して、必要なアクションを実行することもできます。

```
$Files = ((Get-childitem -Path "$env:programdata\microsoft\azure ad connect authentication agent\trace" -Recurse).VersionInfo).FileName 
 
Foreach ($file in $files) { 
    {Remove-Item -Path $File -Force} 
}
```

このスクリプトを 48 時間ごとに実行するようにスケジュールするには、次の手順に従います。

1.  拡張子が ".PS1" のファイルにスクリプトを保存します。
2.  **コントロール パネル**を開き、**[システムとセキュリティ]** をクリックします。
3.  **[管理ツール]** で、**[タスクのスケジュール]** をクリックします。
4.  **タスク スケジューラ**で、**[Task Schedule Library]\(タスク スケジュール ライブラリ\)** を右クリックし、**[基本タスクの作成...]** をクリックします
5.  新しいタスクの名前を入力し、**[次へ]** をクリックします。
6.  **タスク トリガー**として **[毎日]** を選択し、**[次へ]** をクリックします。
7.  繰り返しを [2 日] に設定し、**[次へ]** をクリックします。
8.  アクションとして **[プログラムを起動する]** を選択し、**[次へ]** をクリックします。
9.  プログラム/スクリプトのボックスに「**PowerShell**」と入力し、**[引数の追加 (オプション)]** というラベルの付いたボックスに、先ほど作成したスクリプトへの完全なパスを入力して、**[次へ]** をクリックします。
10. 次の画面に、作成しようとしているタスクの概要が表示されます。 値を確認し、**[完了]** をクリックしてタスクを作成します。
 
### <a name="note-about-domain-controller-logs"></a>ドメイン コントローラー ログに関する注意事項

監査ログが有効になっている場合、この製品では、お使いのドメイン コント ローラーのセキュリティ ログを生成できます。 監査ポリシーの構成に関する詳細については、[こちらの記事](https://technet.microsoft.com/library/dd277403.aspx)をご覧ください。

## <a name="next-steps"></a>次の手順
- [**トラブルシューティング**](active-directory-aadconnect-troubleshoot-pass-through-authentication.md) - この機能に関する一般的な問題を解決する方法を確認します。
