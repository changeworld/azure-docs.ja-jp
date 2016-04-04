<properties
	pageTitle="Azure Active Directory B2C プレビュー: サービス正常性 | Microsoft Azure"
	description="Azure Active Directory B2C の軽微な問題、状態、対応策についての通知"
	services="active-directory-b2c"
	documentationCenter=""
	authors="swkrish"
	manager="msmbaldwin"
	editor="bryanla"/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/22/2016"
	ms.author="swkrish"/>

# Azure Active Directory B2C プレビュー: サービス正常性

このページでは、サービスに関する軽微な問題、状態、対応策についての通知について説明します。後で参照するときのために、このページをブックマークできます。[Azure の状態ダッシュボード](https://azure.microsoft.com/status/)も継続的にチェックしてください。

### 2016 年 3 月 22 日: B2C テナントに対する SSO バグ

SSO (シングル サインオン) のバグは、2016 年 3 月 17 日、午後 1 時 (太平洋標準時) に発表され、2016 年 3 月 18 日、午前 10 (太平洋標準時) に対応策が講じられました。この期間中に影響を受けたのは 100 人未満です。これについては厳密な監視を継続しています。このバグは、次のような状況で発生します。

1. ID プロバイダーが 1 つだけ構成された "ローカル アカウント" でサインイン ポリシーをセットアップした。
2. コンシューマーは Azure AD B2C に初めて正常にサインインした。
3. コンシューマーはサインインを再度試したが、SSO は取得されず、エラー メッセージが表示された。

コンシューマーにとっての唯一の回避策 (手順 3 の後での) は、ブラウザーをいったん閉じてから再度開いて再認証することでした。

<!---HONumber=AcomDC_0323_2016-->