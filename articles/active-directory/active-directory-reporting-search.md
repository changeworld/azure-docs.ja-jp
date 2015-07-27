<properties
	pageTitle="Azure Active Directory のレポート検索"
	description="Azure Active Directory のセキュリティ、アクティビティ、および監査レポートを検索する方法"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/07/2015"
	ms.author="kenhoff"/>

# Azure Active Directory のレポート検索

Azure Active Directory には、ディレクトリ管理者向けに、複数のレポートにわたってユーザーのセキュリティ、アクティビティ、および監査イベントを検索する機能が用意されています。

検索パネルを表示するには、**Azure 管理ポータル、Azure Active Directory、[レポート]** の順に移動します。 パネルは、レポートの一覧の上部にあります。

特定のユーザーのアクティビティまたは監査イベントを検索するには、[開始] および [終了] フィールドで日付範囲を選択し、ユーザーの UPN または表示名を入力した後、チェックマーク ボタンをクリックします。

## 検索に含まれるレポート

検索結果にすべてのレポートが含まれるわけではありません。レポートが検索結果に含まれるかどうかを次の表に示します。

|	レポート |	あり |
|	------												|	--------			|
|	不明なソースからのサインイン |	いいえ |
|	複数のエラー後のサインイン |	いいえ |
|	複数の地域からのサインイン |	いいえ |
|	不審なアクティビティのある IP アドレスからのサインイン |	いいえ |
|	感染している可能性があるデバイスからのサインイン |	いいえ |
|	不規則なサインイン アクティビティ |	いいえ |
|	異常なサインイン アクティビティがあるユーザー |	いいえ |
|	資格情報が漏洩したユーザー |	いいえ |
|	監査レポート |	あり |
|	パスワード リセット アクティビティ |	あり |
|	パスワード リセット登録アクティビティ |	あり |
|	セルフ サービス グループ アクティビティ |	あり |
|	アプリケーションの使用状況 |	いいえ |
|	アカウント プロビジョニングのアクティビティ |	あり |
|	パスワード ロールオーバーの状態 |	いいえ |
|	アカウント プロビジョニング エラー |	いいえ |
|	RMS の利用状況 |	いいえ |
|	最もアクティブな RMS ユーザー |	いいえ |
|	RMS デバイスの利用状況 |	いいえ |

## 詳細情報

 - [Azure Active Directory レポート](active-directory-view-access-usage-reports.md)
 - [Azure Active Directory レポートの監査イベント](active-directory-reporting-audit-events.md)

<!---HONumber=July15_HO3-->