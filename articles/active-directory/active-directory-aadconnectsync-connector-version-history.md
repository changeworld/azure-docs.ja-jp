<properties
   pageTitle="Azure AD Connect 同期: コネクタ バージョンのリリース履歴 | Microsoft Azure"
   description="このトピックでは、Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタの全リリース一覧を提供します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/24/2016"
   ms.author="andkjell"/>

# Azure AD Connect 同期: コネクタ バージョンのリリース履歴
Forefront Identity Manager (FIM) と Microsoft Identity Manager (MIM) のコネクタは頻繁に更新されます。

この記事は、リリースされたバージョンを追跡し、最新バージョンに更新する必要があるかどうかを判断できるようにするためのものです。

関連リンク:

- [最新コネクタのダウンロード](http://go.microsoft.com/fwlink/?LinkId=717495)
- [Generic LDAP コネクタ](active-directory-aadconnectsync-connector-genericldap.md) リファレンス ドキュメント
- [Generic SQL コネクタ](active-directory-aadconnectsync-connector-genericsql.md) リファレンス ドキュメント
- [Web サービス コネクタ](http://go.microsoft.com/fwlink/?LinkID=226245) リファレンス ドキュメント
- [PowerShell コネクタ](active-directory-aadconnectsync-connector-powershell.md) リファレンス ドキュメント
- [Lotus Domino コネクタ](active-directory-aadconnectsync-connector-domino.md) リファレンス ドキュメント

## 1\.1.117.0
リリース日: 2016 年 3

**新しいコネクタ** [Generic SQL コネクタ](active-directory-aadconnectsync-connector-genericsql.md)の初回リリース

**新機能:**

- Generic LDAP コネクタ:
    - Isode によるデルタ インポート対応になりました。
- Web サービス コネクタ:
    - csEntryChangeResult アクティビティと setImportErrorCode アクティビティが更新され、オブジェクト レベルのエラーを同期エンジンに返すことができるようになりました。
    - SAP6 テンプレートと SAP6User テンプレートが更新され、新しいオブジェクト レベルのエラー機能を利用できるようになりました。
- Lotus Domino コネクタ:
    - エクスポートするとき、アドレス帳ごとに証明が 1 つ必要になります。すべての証明に同じパスワードを利用できるようになり、管理が簡単になりました。

**修正された問題:**

- Generic LDAP コネクタ:
    - IBM Tivoli DS の場合、一部の参照属性が正しく検出されませんでした。
    - Open LDAP の場合、デルタ インポートのとき、文字列の始めと終わりの空白文字が切り詰められました。
    - Novell と NetIQ では、両方が OU/コンテナー間でオブジェクトを動かし、同時にオブジェクトの名前を変更した場合、エクスポートに失敗しました。
- Web サービス コネクタ:
    - Web サービスで同じバインディングに対して複数のエンドポイントが存在するとき、コネクタはそのようなエンドポイントを正しく検出しませんでした。
- Lotus Domino コネクタ:
    - fullName 属性をメールイン データベースにエクスポートできませんでした。
    - 追加されたメンバーと削除されたメンバーの両方をグループからエクスポートすると、追加されたメンバーだけがエクスポートされました。
    - Notes ドキュメントが無効の場合 (属性 isValid が false に設定されている場合)、コネクタが失敗します。

## 以前のリリース
2016 年 3 月より前は、コネクタはサポート トピックとしてリリースされていました。

**Generic LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597、2015 年 9 月
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549、2015 年 3 月
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534、2015 年 1 月
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419、2014 年 9 月
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082、2014 年 3 月

**Web サービス**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419、2014 年 9 月

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419、2014 年 9 月

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597、2015 年 9 月
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549、2015 年 3 月
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712、2014 年 8 月
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003、2014 年 2 月  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721、2013 年 10 月
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534、2013 年 8 月

## 次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0525_2016-->