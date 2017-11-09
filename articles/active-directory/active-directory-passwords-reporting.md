---
title: "レポート: Azure AD SSPR | Microsoft Docs"
description: "Azure AD のセルフサービスによるパスワード リセット イベントのレポート"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 1e715b54c78d42d88d2082e9b0e9f942bec72abd
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2017
---
# <a name="reporting-options-for-azure-ad-password-management"></a>Azure AD のパスワード管理に関するレポート オプション

デプロイ後、多くの組織が SSRP が本当に使用されているかどうか、およびどのように使用されているかを把握することを望んでいます。 Azure AD には、既定のセットを使用して質問に回答するのに役立つレポート機能が用意されています。また、適切なライセンスが付与されている場合は、カスタム クエリを作成できます。

![レポート][Reporting]

次の質問は、[Azure Portal] (https://portal.azure.com/) に存在するレポートによって回答できます。

> [!NOTE]
> [グローバル管理者](active-directory-assign-admin-roles.md)であり、かつレポート タブや監査ログを 1 回以上訪問して、組織の代わりにこのデータを収集できるようにオプトインする必要があります。 これを行うまでは、組織のデータが収集されません。

* パスワード リセットを登録した人数
* パスワード リセットを登録したユーザー
* ユーザーが登録しているデータ
* 過去 7 日間で自分のパスワードをリセットしたユーザー数
* パスワードをリセットするためにユーザーまたは管理者が使用する一般的な方法
* パスワード リセットを試みる場合に、ユーザーまたは管理者が直面する一般的な問題
* 自らのパスワードを頻繁にリセットしている管理者
* パスワード リセットに関する不審なアクティビティの有無

## <a name="power-bi-content-pack"></a>Power BI コンテンツ パック

Power BI ユーザーには、使いやすい SSPR レポートを含む Azure AD 用のコンテンツ パックが用意されています。 コンテンツ パックを使用およびデプロイする方法の詳細については、「[Azure Active Directory Power BI コンテンツ パックの使用方法](active-directory-reporting-power-bi-content-pack-how-to.md)」を参照してください。 その後、独自のダッシュボードを作成して、組織内で共有することができます。

## <a name="how-to-view-password-management-reports-in-the-azure-portal"></a>Azure Portal でパスワード管理レポートを表示する方法

Azure Portal エクスペリエンスでは、パスワード リセットおよびパスワード リセット登録アクティビティの表示方法が改良されています。  パスワード リセットおよびパスワード リセット登録イベントを表示するには、次の手順に従います。

1. [**portal.azure.com**](https://portal.azure.com) に移動します。
2. Azure Portal メインの左側のウィンドウで、**[その他のサービス]** メニューをクリックします。
3. サービスの一覧で、**Azure Active Directory** を検索して選択します。
4. Azure Active Directory のナビゲーション メニューで **[ユーザーとグループ]** をクリックします。
5. [ユーザーとグループ] のナビゲーション メニューで、**[監査ログ]** ナビゲーション項目をクリックします。 これにより、ディレクトリ内のすべてのユーザーに対して発生しているすべての監査イベントが表示されます。 このビューをフィルター処理して、すべてのパスワード関連イベントを表示することもできます。
6. パスワード リセット イベントのみが表示されるようにこのビューをフィルター処理するには、ブレードの上部にある **[フィルター]** ボタンをクリックします。
7. **[フィルター]** メニューから **[カテゴリ]** ドロップダウンを選択し、カテゴリの種類を **[Self-service Password Management (セルフサービスのパスワード管理)]** に変更します。
8. 必要な場合は特定の **[アクティビティ]** を選択して、さらにフィルター処理します。

## <a name="how-to-retrieve-password-management-events-from-the-azure-ad-reports-and-events-api"></a>Azure AD レポートおよびイベント API からパスワード管理イベントを取得する方法

Azure AD レポートおよびイベント API で、パスワード リセットおよびパスワード リセット登録レポートに含まれるすべての情報の取得がサポートされるようになりました。 この API を使用して、任意のレポート テクノロジと統合するために個別のパスワード リセットおよびパスワード リセット登録イベントをダウンロードできます。

### <a name="how-to-get-started-with-the-reporting-api"></a>レポート API の使用を開始する方法

このデータにアクセスするには、サーバーからデータを取得するための小規模なアプリケーションまたはスクリプトを作成する必要があります。 [Azure AD Reporting API の使用を開始する方法](active-directory-reporting-api-getting-started.md)

作業用スクリプトの準備ができたら、シナリオの条件を満たすために取得可能なパスワード リセットおよび登録イベントを調査します。

* [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): パスワード リセット イベントで利用可能な列を一覧します。
* [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): パスワード リセット登録イベントで利用可能な列を一覧します。

### <a name="reporting-api-data-retrieval-limitations"></a>レポート API によるデータ取得の制限事項

現時点では、Azure AD レポートおよびイベント API で取得できるのは**過去 30 日間**の [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) および [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent) タイプの最大 **75,000 個のイベント**です。

この枠を超えてデータを取得および保存する必要がある場合は、データを外部データベースに保存し、結果として作成されたデルタを API を使用してクエリすることをお勧めします。 マイクロソフトは、組織内で SSPR を使用を開始したときにこのデータの取得を開始し、データを外部に永続的に保存してからそれ以降のデルタを追跡し続けることをお勧めします。

## <a name="description-of-report-columns-in-azure-portal"></a>Azure Portal のレポートの列の説明

次の一覧では、レポートの各列について詳細に説明します。

* **ユーザー** – パスワード リセット登録操作を試みたユーザー。
* **ロール** – ディレクトリ内のユーザーの役割。
* **日付と時刻** – 試行の日付と時刻。
* **登録データ** – パスワード リセット登録中にユーザーが提供した認証データ。

## <a name="description-of-report-values-in-azure-portal"></a>Azure Portal のレポートの値の説明

次の表には、各列で使用できるさまざまな値について説明します。

| 分割 | 使用できる値とその意味 |
| --- | --- |
| 登録データ |**連絡用電子メール** – ユーザーが、連絡用電子メールまたは認証用電子メールを認証のために使用しました。<p><p>**会社電話** – ユーザーが、会社電話を認証のために使用しました。<p>**携帯電話** – ユーザーが、携帯電話または認証用の電話を認証のために使用しました。<p>**秘密の質問** – ユーザーが、秘密の質問を認証のために使用しました。<p>**上の任意の組み合わせ (連絡用電子メール + 携帯電話など)** – 2 ゲート ポリシーを指定した場合に発生し、パスワード リセット要求を認証するためにユーザーがいずれの方法を使用したかを示します。 |

## <a name="self-service-password-management-activity-types"></a>[Self-service Password Management (セルフサービスのパスワード管理)] のアクティビティの種類

**[Self-Service Password Management (セルフサービスのパスワード管理)]** 監査イベント カテゴリには、次のアクティビティの種類が表示されます。  次の一覧の後に、各種類の説明を示します。

* [**Blocked from self-service password reset (セルフサービスのパスワード リセットのブロック)**](#activity-type-blocked-from-self-service-password-reset) - ユーザーが 24 時間以内に合計 5 回以上、パスワードのリセット、特定のゲートの使用、または電話番号の確認を試行したことを示します。
* [**Change password (self-service) (パスワードの変更 (セルフサービス))**](#activity-type-change-password-self-service) - ユーザーが自発的または (期限切れにより) 強制的にパスワードの変更を実行したことを示します。
* [**Reset password (by admin) (パスワードのリセット (管理者))**](#activity-type-reset-password-by-admin) - 管理者が Azure Portal からユーザーに代わってパスワードのリセットを実行したことを示します。
* [**Reset password (self-service) (パスワードのリセット (セルフサービス))**](#activity-type-reset-password-self-service) - ユーザーが[Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から自分のパスワードのリセットに成功したことを示します。
* [**Self-service password reset flow activity progress (セルフサービスのパスワード リセット フロー アクティビティの進行状況)**](#activity-type-self-serve-password-reset-flow-activity-progress) - ユーザーがパスワード リセット プロセスの一環として実行する個々の特定の手順 (特定のパスワード リセット認証ゲートの通過など) を示します。
* [**Unlock user account (self-service) (ユーザー アカウントのロック解除 (セルフサービス))**](#activity-type-unlock-user-account-self-service) - ユーザーが [Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)からリセットなしの AD アカウントのロック解除機能を使用して、自分のパスワードをリセットせずに自分の Active Directory アカウントのロック解除に成功したことを示します。
* [**User registered for self-service password reset (セルフサービスのパスワード リセットを登録したユーザー)**](#activity-type-user-registered-for-self-service-password-reset) - ユーザーが、現在指定されているテナント パスワード リセット ポリシーに従って、自分のパスワードをリセットするために必要なすべての情報を登録したことを示します。

### <a name="activity-type-blocked-from-self-service-password-reset"></a>アクティビティの種類: Blocked from self-service password reset (セルフサービスのパスワード リセットのブロック)

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが 24 時間以内に合計 5 回以上、パスワードのリセット、特定のゲートの使用、または電話番号の確認を試行したことを示します。
* **アクティビティのアクター** - 追加のリセット操作の実行を禁止されたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 追加のリセット操作の実行を禁止されたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーが24 時間以内の追加のリセットの実行、追加の認証方法の試行、または追加の電話番号の確認を禁止されたことを示します。
* **アクティビティの状態が失敗になった理由** - 該当なし

### <a name="activity-type-change-password-self-service"></a>アクティビティの種類: Change password (self-service) (パスワードの変更 (セルフサービス))

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが自発的または (期限切れにより) 強制的にパスワードの変更を実行したことを示します。
* **アクティビティのアクター** - 自分のパスワードを変更したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードを変更したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーが自分のパスワードの変更に成功したことを示します。
  * _失敗_ - ユーザーが自分のパスワードの変更に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **アクティビティの状態が失敗になった理由** - 
  * _FuzzyPolicyViolationInvalidPassword_ - ユーザーが選択したパスワードが、マイクロソフトの禁止されたパスワードの検出機能によって一般的すぎるパスワードまたは特に脆弱なパスワードと見なされたため、自動的に禁止されました。

### <a name="activity-type-reset-password-by-admin"></a>アクティビティの種類: Reset password (by admin) (パスワードのリセット (管理者))

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - 管理者が Azure Portal からユーザーに代わってパスワードのリセットを実行したことを示します。
* **アクティビティのアクター** - 別のエンドユーザーまたは管理者に代わってリセットを実行した管理者。 グローバル管理者、パスワード管理者、ユーザー管理者、ヘルプデスク管理者のいずれかである可能性があります。
* **アクティビティのターゲット** - 自分のパスワードがリセットされたユーザー。 エンドユーザーまたは別の管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - 管理者がユーザーのパスワードのリセットに成功したことを示します。
  * _失敗_ - 管理者がユーザーのパスワードのリセットに失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。

### <a name="activity-type-reset-password-self-service"></a>アクティビティの種類: Reset password (self-service) (パスワードのリセット (セルフサービス))

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが[Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)から自分のパスワードのリセットに成功したことを示します。
* **アクティビティのアクター** - 自分のパスワードをリセットしたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードをリセットしたユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーが自分のパスワードのリセットに成功したことを示します。
  * _失敗_ - ユーザーが自分のパスワードのリセットに失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **アクティビティの状態が失敗になった理由** -
  * _FuzzyPolicyViolationInvalidPassword_ - 管理者が選択したパスワードが、マイクロソフトの禁止されたパスワードの検出機能によって一般的すぎるパスワードまたは特に脆弱なパスワードと見なされたため、自動的に禁止されました。

### <a name="activity-type-self-serve-password-reset-flow-activity-progress"></a>アクティビティの種類: Self serve password reset flow activity progress (セルフサービスのパスワード リセット フロー アクティビティの進行状況)

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーがパスワード リセット プロセスの一環として実行する個々の特定の手順 (特定のパスワード リセット認証ゲートの通過など) を示します。
* **アクティビティのアクター** - パスワード リセット フローの一部を実行したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - パスワード リセット フローの一部を実行したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーがパスワード リセット フローの特定の手順に成功したことを示します。
  * _失敗_ - パスワード リセットフローの特定の手順が失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。
* **使用可能なアクティビティの状態の理由**
  * [使用可能なすべてのリセット アクティビティの状態の理由](#allowed-values-for-details-column)については、下の表をご覧ください。

### <a name="activity-type-unlock-user-account-self-service"></a>アクティビティの種類: Unlock user account (self-service) (ユーザー アカウントのロック解除 (セルフサービス))

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが [Azure AD のパスワード リセット ポータル](https://passwordreset.microsoftonline.com)からリセットなしの AD アカウントのロック解除機能を使用して、自分のパスワードをリセットせずに自分の Active Directory アカウントのロック解除に成功したことを示します。
* **アクティビティのアクター** - 自分のパスワードをリセットせずに自分のアカウントをロック解除したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - 自分のパスワードをリセットせずに自分のアカウントをロック解除したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーが自分のアカウントのロック解除に成功したことを示します。
  * _失敗_ - ユーザーが自分のアカウントのロック解除に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。

### <a name="activity-type-user-registered-for-self-service-password-reset"></a>アクティビティの種類: User registered for self-service password reset (セルフサービスのパスワード リセットを登録したユーザー)

このアクティビティの詳しい説明は次のとおりです。

* **アクティビティの説明** - ユーザーが、現在指定されているテナント パスワード リセット ポリシーに従って、自分のパスワードをリセットするために必要なすべての情報を登録したことを示します。 
* **アクティビティのアクター** - パスワード リセットに登録したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **アクティビティのターゲット** - パスワード リセットに登録したユーザー。 エンドユーザーまたは管理者である可能性があります。
* **使用できるアクティビティの状態**
  * _成功_ - ユーザーが現在のポリシーに従ってパスワード リセットの登録に成功したことを示します。 
  * _失敗_ - ユーザーがパスワード リセットの登録に失敗したことを示します。 行をクリックすると **[Activity Status Reason (アクティビティの状態の理由)]** カテゴリが表示され、失敗の原因について詳しく知ることができます。 注 - これはユーザーが自分のパスワードをリセットできないということではなく、単に登録プロセスを完了しなかったことを意味します。 ユーザーのアカウントに未確認の正しいデータ (未確認の電話番号など) がある場合、そのデータを確認していなくても、パスワードのリセットに使用することができます。 詳細については、「[ユーザーの登録時に発生すること](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-learn-more#what-happens-when-a-user-registers)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問。](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [パスワード ライトバックと、それが必要な理由。](active-directory-passwords-writeback.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Reporting]: ./media/active-directory-passwords-reporting/sspr-reporting.png "Azure AD の SSPR アクティビティ監査ログの例"
