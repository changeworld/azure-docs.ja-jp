---
title: "Azure AD Connect と一般データ保護規則 | Microsoft Docs"
description: "このドキュメントでは、Azure AD Connect で GDPR コンプライアンスを取得する方法について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: billmath
ms.openlocfilehash: c3956dd379961b119f65bdebe1f5a8038c4fa8f0
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="gdpr-compliance-and-azure-ad-connect"></a>GDPR コンプライアンスと Azure AD Connect 

2018 年 5 月に、欧州のプライバシー保護法である、[一般データ保護規則 (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) が施行されます。 GDPR は、欧州連合 (EU) 内で人々に製品やサービスを提供したり、EU 居住者に関連するデータを収集および分析したりする企業、政府機関、非営利組織やその他の組織を対象とする新しいルールです。 GDPR は、組織の所在地に関係なく適用されます。 

Microsoft では、GDPR の要件を満たすのに役立つ製品やサービスを提供しています。 Microsoft のプライバシー ポリシーについては、[セキュリティ センター](https://www.microsoft.com/trustcenter)をご覧ください

>[!NOTE] 
>この記事では、Azure AD Connect と GDPR コンプライアンスについて説明します。  Azure AD Connect Health と GDPR コンプライアンスについては、[こちら](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)の記事をご覧ください。

Azure AD Connect では、次の 2 つの方法で一般データ保護規則へのコンプライアンスを達成できます。

1.  要請を受けた際、個人のデータを抽出し、その個人のデータを環境から削除する
2.  すべてのデータが 48 時間しか保持されないようにする。

Azure AD Connect チームは、実装と保守がはるかに簡単な 2 番目のオプションをお勧めします。

Azure AD Connect の同期サーバーには、GDPR コンプライアンスの対象となる次のデータが格納されます。
1.  個人に関するデータ (**Azure AD Connect データベース**に格納されます)
2.  **Windows イベント ログ** ファイル内のデータ (個人に関する情報が含まれる可能性のあります)
3.  **Azure AD Connect のインストール ログ ファイル**内のデータ (個人に関するデータが含まれる可能性があります)

Azure AD Connect のお客様は、GDPR に準拠するために次のガイドラインに従ってください。
1.  Azure AD Connect のインストール ログ ファイルが置かれているフォルダーの内容を、定期的に (少なくとも 48 時間おきに) 削除する
2.  この製品では、イベント ログが作成されることもあります。  イベント ログについて詳しくは、[こちらのドキュメント](https://msdn.microsoft.com/library/windows/desktop/aa385780.aspx)をご覧ください。

個人に関するデータは、その個人のデータが元々置かれていたソース システムから削除されると、Azure AD Connect データベースからも自動的に削除されます。 GDPR に準拠するために、管理者が特定の操作を行う必要はありません。  ただし、Azure AD Connect のデータは、少なくとも 2 日おきにデータ ソースと同期される必要があります。

## <a name="delete-the-azure-ad-connect-installation-log-file-folder-contents"></a>Azure AD Connect のインストール ログ ファイル フォルダーの内容を削除する
**c:\programdata\aadconnect** フォルダーの内容は定期的にチェックし、削除するようにしてください。ただし、**PersistedState.Xml** ファイルは例外です。 このファイルには Azure AD Connect の以前のインストールの状態が保持されるため、アップグレード インストールの実行時に必要となります。 このファイルには個人に関するデータは含まれないので、削除しないでください。

>[!IMPORTANT]
>PersistedState.xml ファイルは削除しないでください。  このファイルにはユーザー情報は含まれず、以前のインストール状態が保持されます。

これらのファイルの確認と削除には Windows エクスプ ローラーを使用することもできますし、次のようなスクリプトを使用して、必要なアクションを実行することもできます。


```
$Files = ((Get-childitem -Path "$env:programdata\aadconnect" -Recurse).VersionInfo).FileName
Foreach ($file in $files) {
If ($File.ToUpper() -ne "$env:programdata\aadconnect\PERSISTEDSTATE.XML".toupper()) # Do not delete this file
    {Remove-Item -Path $File -Force}
    } 
```

### <a name="schedule-this-script-to-run-every-48-hours"></a>このスクリプトを 48 時間おきに実行するようにスケジュールする
スクリプトを 48 時間おきに実行するようスケジュールするには、次の手順に従います。

1.  スクリプトを **&#46;PS1** という拡張子のファイルに保存した後、コントロール パネルを開き、**[システムとセキュリティ]** をクリックします。
    ![システム](media\active-directory-aadconnect-gdpr\gdpr2.png)

2.  [管理ツール] で、**[タスクのスケジュール]** をクリックします。
    ![タスク](media\active-directory-aadconnect-gdpr\gdpr3.png)
3.  タスク スケジューラで、**[Task Schedule Library]\(タスク スケジュール ライブラリ\)** を右クリックし、**[基本タスクの作成...]** をクリックします
4.  新しいタスクの名前を入力し、**[次へ]** をクリックします。
5.  タスク トリガーとして **[毎日]** を選択し、**[次へ]** をクリックします。
6.  繰り返しを **[2 日]** に設定し、**[次へ]** をクリックします。
7.  アクションとして **[プログラムを起動する]** を選択し、**[次へ]** をクリックします。
8.  プログラム/スクリプトのボックスに「**PowerShell**」と入力し、**[引数の追加 (オプション)]** というラベルの付いたボックスに、先ほど作成したスクリプトへの完全なパスを入力して、**[次へ]** をクリックします。
9.  次の画面に、作成しようとしているタスクの概要が表示されます。 値を確認し、**[完了]** をクリックしてタスクを作成します。



## <a name="next-steps"></a>次の手順
- [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)
- [Azure AD Connect Health と AD DS](../../active-directory/connect-health/active-directory-aadconnect-health-gdpr.md)
