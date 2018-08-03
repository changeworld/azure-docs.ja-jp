---
title: LUIS アプリケーションへのアクセスについて - Azure | Microsoft Docs
description: LUIS のオーサリングにアクセスする方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: fe0ee7d575e69c883ad7f980477e86fbd004778e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397245"
---
# <a name="authoring-and-endpoint-user-access"></a>オーサリングとエンドポイントのユーザー アクセス
オーサリング アクセスは、所有者とコラボレーターが利用できます。 プライベート アプリについては、エンドポイント アクセスは所有者とコラボレーターが利用できます。 パブリック アプリについては、エンドポイント アクセスは、自分の LUIS アカウントを持ち、パブリック アプリの ID を持つすべてのユーザーが利用できます。 

## <a name="access-to-authoring"></a>オーサリングへのアクセス
[LUIS](luis-reference-regions.md#luis-website) Web サイトまたは[オーサリング API](https://aka.ms/luis-authoring-apis) からアプリへのアクセスは、そのアプリの所有者によって制御されます。 

所有者とすべてのコラボレーターが、アプリのオーサリング アクセスを持ちます。 

|オーサリング アクセスに含まれるもの|メモ|
|--|--|
|エンドポイント キーの追加または削除||
|バージョンのエクスポート||
|エンドポイント ログのエクスポート||
|バージョンのインポート||
|アプリの公開|アプリが公開されている場合、オーサリング キーまたはエンドポイント キーを持つすべてのユーザーがアプリにクエリを実行できます。|
|モデルの変更|
|[発行]|
|[アクティブ ラーニング](luis-how-to-review-endoint-utt.md)のためのエンドポイントの発話の確認|
|トレーニング|

## <a name="access-to-endpoint"></a>エンドポイントへのアクセス
LUIS にクエリを実行するためのエンドポイントへのアクセスは、**[設定]** ページにあるアプリの **[パブリック]** 設定で制御します。 プライベート アプリのエンドポイントのクエリでは、残りのクォータのヒット数に関して、承認済みのキーがチェックされます。 パブリック アプリのエンドポイントのクエリでも、(クエリを行っているユーザーの) エンドポイント キーを提供する必要があります。この場合も、残りのクォータのヒット数がチェックされます。 

エンドポイント キーは、GET 要求のクエリ文字列または POST 要求のヘッダーで渡されます。

![アプリをパブリックに設定する](./media/luis-concept-security/set-application-as-public.png)

### <a name="private-app-endpoint-security"></a>プライベート アプリのエンドポイントのセキュリティ
プライベート アプリのエンドポイントは、次の場合のみ使用できます。

|キーとユーザー|説明|
|--|--|--|
|所有者のオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|コラボレーターのオーサリング キー| 最大で 1,000 エンドポイント ヒット|
|**[[公開]](luis-how-to-publish-app.md)** ページから追加されたエンドポイント キー|所有者とコラボレーターがエンドポイント キーを追加できる|

その他のオーサリング キーまたはエンドポイント キーにはアクセス権が**ありません**。

### <a name="public-app-endpoint-access"></a>パブリック アプリのエンドポイントへのアクセス
アプリの **[設定]** ページで **[パブリック]** としてアプリを構成します。 アプリをいったんパブリックとして構成すると、キーでエンドポイントのクォータを使い切ってない場合に限り、"_すべての_" 有効な LUIS オーサリング キーまたは LUIS エンドポイント キーでアプリにクエリを実行できます。

アプリ ID を指定した場合のみ、所有者やコラボレーター以外のユーザーがパブリック アプリにアクセスできます。 LUIS には、パブリックな "_マーケット_" やパブリック アプリを検索する他の方法がありません。  

## <a name="microsoft-user-accounts"></a>Microsoft のユーザー アカウント
作成者とコラボレーターは、[公開] ページで LUIS にキーを追加できます。 Azure portal で LUIS キーを作成している Microsoft のユーザー アカウントには、アプリの所有者またはアプリ コラボレーターが必要です。 

Active Directory ユーザー アカウントの詳細については、「[Azure Active Directory テナント ユーザー](luis-how-to-collaborate.md#azure-active-directory-tenant-user)」を参照してください。 

<!--
### Individual consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then you can provide individual consent as part of the login process. 

### Administrator consent
If the Microsoft user account is part of an Azure Active Directory (AAD), and the active directory doesn't allow users to give consent, then the administrator can give individual consent via the method discussed in this [blog](https://blogs.technet.microsoft.com/tfg/2017/10/15/english-tips-to-manage-azure-ad-users-consent-to-applications-using-azure-ad-graph-api/). 
-->
## <a name="securing-the-endpoint"></a>エンドポイントのセキュリティ保護 
サーバー間環境で呼び出すことによって、LUIS エンドポイント キーを表示できるユーザーを制御できます。 ボットから LUIS を使用している場合、このボットと LUIS の間の接続は既にセキュリティで保護されています。 LUIS エンドポイントを直接呼び出している場合は、アクセスが制御された ([AAD](https://azure.microsoft.com/services/active-directory/) など) サーバー側 API (Azure [関数](https://azure.microsoft.com/services/functions/)など) を作成する必要があります。 サーバー側 API が呼び出され、認証と承認が確認されると、LUIS への呼び出しが渡されます。 この戦略では man-in-the-middle (中間者) 攻撃を防ぐことはできませんが、ユーザーからエンドポイントを難読化し、アクセスの追跡を可能にして、エンドポイントの応答のログ ([Application Insights](https://azure.microsoft.com/services/application-insights/) など) を追加できます。  

## <a name="security-compliance"></a>キュリティ コンプライアンス
LUIS では、ISO 27001:2013 および ISO 27018:2014 監査が監査レポート内に不適合 (所見) なしで正常に完了しました。 さらに LUIS では、成熟度機能評価で考えられる最高の CSA STAR 証明書 (Gold Award) も取得しました。 Azure は、この証明書を取得した唯一の主要パブリック クラウド サービス プロバイダーです。 詳細については、[セキュリティ センター](https://www.microsoft.com/en-us/trustcenter/compliance/iso-iec-27001)の ISO ページで参照されている Azure の主要な[コンプライアンス概要](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942)ドキュメントで、更新済みのスコープ ステートメントに LUIS が記載されていることを確認できます。  

## <a name="next-steps"></a>次の手順

[ベスト プラクティス](luis-concept-best-practices.md)を参照し、意図とエンティティを使用して最適な予測を得る方法について学習します。