<properties
   pageTitle="Azure AD Connect: 自動アップグレード | Microsoft Azure"
   description="このトピックでは、Azure AD Connect 同期の組み込みの自動アップグレード機能について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Azure AD Connect: 自動アップグレード
この機能は、ビルド 1.1.105.0 (2016 年 2 月リリース) で導入されました。

## 概要
Azure AD Connect のインストールを常に最新の状態に保つことは、**自動アップグレード**機能によって、これまでよりも簡単になりました。この機能は、高速インストールでは既定で有効であり、新しいバージョンがリリースされると、インストールが自動的にアップグレードされます。

自動アップグレードは、次の場合に既定で有効です。

- 簡単設定を使用したインストール
- SQL Express LocalDB を使用 (簡単設定で常に使用されます)
- AD アカウントが、簡単設定によって作成された既定の MSOL\_ アカウント
- メタバース内のオブジェクトが 100,000 未満

自動アップグレードの現在の状態は、PowerShell コマンドレット `Get-ADSyncAutoUpgrade` で表示できます。次のような状態があります。

| State | コメント |
| ---- | ---- |
| 有効 | 自動アップグレードが有効です。 |
| Suspended | システムによる設定だけが可能です。システムは自動アップグレードを受け付けることができなくなりました。 |
| 無効 | 自動アップグレードが無効です。 |

`Set-ADSyncAutoUpgrade` を使用して、**有効**と**無効**を切り替えることができます。システムだけが、状態を**保留**に設定することができます。

自動アップグレードは、アップグレード インフラストラクチャとして、Azure AD Connect Health を使用しています。自動アップグレードを正しく動作させるには、「[Office 365 の URL と IP アドレスの範囲](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)」に記載されているように、**Azure AD Connect Health** のプロキシの URL を開いておく必要があります。

**Synchronization Service Manager** UI がサーバーで実行されている場合は、UI が閉じられるまで、アップグレードが中断されます。

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0218_2016-->