---
title: "Azure Active Directory ポータルの監査アクティビティ レポート - プレビュー | Microsoft Docs"
description: "Azure Active Directory ポータル プレビューの監査アクティビティ レポートの概要"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: d88f8bed0cbd14ee49986d6749396731a810034b
ms.lasthandoff: 04/19/2017


---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal---preview"></a>Azure Active Directory ポータルの監査アクティビティ レポート - プレビュー

Azure Active Directory [プレビュー](active-directory-preview-explainer.md)のレポートでは、環境の動作状況を判断するために必要なすべての情報を取得できます。

Azure Active Directory のレポート アーキテクチャは、次のコンポーネントで構成されます。

- **アクティビティ** 
    - **サインイン アクティビティ** – マネージ アプリケーションの使用状況とユーザー サインイン アクティビティに関する情報
    - **監査ログ** - ユーザーとグループの管理、マネージ アプリケーション、およびディレクトリのアクティビティに関するシステム アクティビティ情報です。
- **セキュリティ** 
    - **リスクの高いサインイン** - リスクの高いサインインは、ユーザー アカウントの正当な所有者ではない人によって行われた可能性があるサインイン試行の指標です。 詳細については、「Risky sign-ins (リスクの高いサインイン)」を参照してください。
    - **リスクのフラグ付きユーザー** - リスクの高いユーザーは、侵害された可能性があるユーザー アカウントの指標です。 詳細については、「Users flagged for risk (リスクのフラグ付きユーザー)」を参照してください。

このトピックでは、監査アクティビティの概要を説明します。
 


## <a name="audit-logs"></a>監査ログ

Azure Active Directory の監査ログは、コンプライアンスのためにシステム アクティビティのレコードを提供します。  
すべての監査データへの最初のエントリ ポイントは、**[Azure Active Directory]** の **[アクティビティ]** セクションの **[監査ログ]** です。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/61.png "監査ログ")

監査ログには、次のものを示す既定のリスト ビューがあります。

- 発生の日付と時刻
- アクティビティの開始者またはアクター ("*だれが*" を指す) 
- アクティビティ ("*どうした*" を指す) 
- ターゲット

![監査ログ](./media/active-directory-reporting-activity-audit-logs/18.png "監査ログ")

リスト ビューをカスタマイズするには、ツール バーの **[列]** をクリックします。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/19.png "監査ログ")

これで、追加のフィールドの表示または既に表示されているフィールドの削除ができます。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/21.png "監査ログ")


リスト ビュー内の項目をクリックすると、その項目に関する確認可能な詳細が表示されます。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/22.png "監査ログ")


## <a name="filtering-audit-logs"></a>監査ログのフィルター処理

報告されるデータを有用なものだけに絞り込むために、次のフィールドを使用して監査データをフィルター処理できます。

- 期間
- 開始者 (アクター)
- カテゴリ
- アクティビティのリソースの種類
- アクティビティ

![監査ログ](./media/active-directory-reporting-activity-audit-logs/23.png "監査ログ")


**[期間]** フィルターでは、返されるデータの期間を定義できます。  
次のいずれかの値になります。

- 1 か月
- 7 日
- 24 時間
- カスタム

カスタムの期間を選択すると、開始時刻と終了時刻を構成できます。

**[開始者 (アクター)]** フィルターでは、開始者の名前またはそのユニバーサル プリンシパル名 (UPN) を定義できます。

**[カテゴリ]** フィルターでは、次のフィルターのいずれかを選択できます。

- すべて
- コア カテゴリ
- コア ディレクトリ
- セルフサービスによるパスワード管理
- セルフサービスのグループ管理
- アカウント プロビジョニング
- 自動パスワード ロールオーバー
- 招待されたユーザー
- MIM サービス

**[アクティビティのリソースの種類]** フィルターでは、次のフィルターのいずれかを選択できます。

- すべて 
- グループ
- Directory
- User
- アプリケーション
- [ポリシー]
- デバイス
- 他の

**[アクティビティのリソースの種類]** として **[グループ]** が選択されている場合、次の**ソース**も指定できる追加のフィルター カテゴリが示されます。

- Azure AD
- O365




**[アクティビティ]** フィルターは、カテゴリとアクティビティ リソースの種類の選択に基づいたものです。 参照する特定のアクティビティを選択することも、すべてを選択することもできます。 

| アクティビティ カテゴリ| アクティビティのリソースの種類| アクティビティ |
| :-- | :-: | :-- |
| Core Directory (コア ディレクトリ)| グループ| グループ設定の削除|
| Core Directory (コア ディレクトリ)| Directory| ドメインの更新|
| Core Directory (コア ディレクトリ)| Directory| 会社からのパートナーの削除|
| Core Directory (コア ディレクトリ)| User| ロールの更新|
| Core Directory (コア ディレクトリ)| User| テンプレートからのロールの追加|
| Core Directory (コア ディレクトリ)| グループ| グループへのアプリ ロールの割り当ての追加|
| Core Directory (コア ディレクトリ)| グループ| ユーザーに対するグループ ベースのライセンスの適用の開始|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルの追加|
| Core Directory (コア ディレクトリ)| [ポリシー]| ポリシーの更新|
| Core Directory (コア ディレクトリ)| [ポリシー]| サービス プリンシパルへのポリシーの追加|
| Core Directory (コア ディレクトリ)| デバイス| デバイスへの登録済み所有者の追加|
| Core Directory (コア ディレクトリ)| デバイス| デバイスへの登録済みユーザーの追加|
| Core Directory (コア ディレクトリ)| デバイス| デバイス構成の更新|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| パスワードのリセット (セルフサービス)|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| ユーザー アカウントのロック解除 (セルフサービス)|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| パスワードのリセット (管理者)|
| セルフサービスによるグループ管理| グループ| 保留になっているグループへの参加要求の削除|
| アカウント プロビジョニング| アプリケーション| プロセス エスクロー|
| 自動パスワード ロールオーバー| アプリケーション| 自動パスワード ロールオーバー|
| 招待されたユーザー| 他の| 処理されたバッチ招待|
| Core Directory (コア ディレクトリ)| Directory| 確認済みドメインの削除|
| Core Directory (コア ディレクトリ)| Directory| 未確認ドメインの追加|
| Core Directory (コア ディレクトリ)| Directory| 確認済みドメインの追加|
| Core Directory (コア ディレクトリ)| Directory| テナントへのディレクトリ機能の設定|
| Core Directory (コア ディレクトリ)| Directory| DirSyncEnabled フラグの設定|
| Core Directory (コア ディレクトリ)| Directory| 会社の設定の作成|
| Core Directory (コア ディレクトリ)| Directory| 会社の設定の更新|
| Core Directory (コア ディレクトリ)| Directory| 会社の設定の削除|
| Core Directory (コア ディレクトリ)| Directory| 会社で許可されるデータの場所の設定|
| Core Directory (コア ディレクトリ)| Directory| 会社の多国対応機能を有効にする設定|
| Core Directory (コア ディレクトリ)| User| ユーザーの更新|
| Core Directory (コア ディレクトリ)| User| ユーザーの削除|
| Core Directory (コア ディレクトリ)| グループ| グループからのメンバーの削除|
| Core Directory (コア ディレクトリ)| グループ| グループ ライセンスの設定|
| Core Directory (コア ディレクトリ)| グループ| グループ設定の作成|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルの更新|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションの削除|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションの更新|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルの削除|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルの資格情報の追加|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルからのアプリ ロールの割り当ての削除|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションからの所有者の削除|
| Core Directory (コア ディレクトリ)| デバイス| デバイスからの登録済み所有者の削除|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| セルフサービスのパスワード リセット フロー アクティビティの進行状況|
| アカウント プロビジョニング| アプリケーション| 管理|
| アカウント プロビジョニング| アプリケーション| ディレクトリ操作|
| MIM サービス| グループ| メンバーの削除|
| Core Directory (コア ディレクトリ)| [ポリシー]| ポリシーの削除|
| 招待されたユーザー| User| バイラル テナントの作成|
| Core Directory (コア ディレクトリ)| Directory| 外部シークレットの更新|
| Core Directory (コア ディレクトリ)| Directory| 権限管理プロパティの設定|
| Core Directory (コア ディレクトリ)| Directory| 会社の更新|
| Core Directory (コア ディレクトリ)| User| ユーザーの追加|
| Core Directory (コア ディレクトリ)| User| フェデレーション ユーザーの管理対象ユーザーへの変換|
| Core Directory (コア ディレクトリ)| User| ユーザーのアプリケーション パスワードの作成|
| Core Directory (コア ディレクトリ)| グループ| グループへのメンバーの追加|
| Core Directory (コア ディレクトリ)| グループ| グループの追加|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションへの同意|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションの追加|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルへの所有者の追加|
| Core Directory (コア ディレクトリ)| アプリケーション| Oauth2Permissiongrant の削除|
| Core Directory (コア ディレクトリ)| [ポリシー]| ポリシー資格情報の削除|
| Core Directory (コア ディレクトリ)| デバイス| デバイス構成の削除|
| セルフサービスによるグループ管理| グループ| 動的グループのプロパティの設定|
| セルフサービスによるグループ管理| グループ| ライフサイクル管理ポリシーの更新|
| アカウント プロビジョニング| アプリケーション| 同期規則の操作|
| 招待されたユーザー| 他の| アップロードされたバッチ招待|
| MIM サービス| グループ| メンバーの追加|
| Core Directory (コア ディレクトリ)| User| ライセンス プロパティの設定|
| Core Directory (コア ディレクトリ)| User| ユーザーの復元|
| Core Directory (コア ディレクトリ)| User| ロールからのメンバーの削除|
| Core Directory (コア ディレクトリ)| User| ユーザーからのアプリ ロールの割り当ての削除|
| Core Directory (コア ディレクトリ)| User| ロールからのスコープを持つメンバーの削除|
| Core Directory (コア ディレクトリ)| グループ| グループの更新|
| Core Directory (コア ディレクトリ)| グループ| グループへの所有者の追加|
| Core Directory (コア ディレクトリ)| グループ| ユーザーに対するグループ ベースのライセンスの適用の終了|
| Core Directory (コア ディレクトリ)| グループ| グループからのアプリ ロールの割り当ての削除|
| Core Directory (コア ディレクトリ)| グループ| ユーザーが管理するグループの設定|
| Core Directory (コア ディレクトリ)| アプリケーション| Oauth2Permissiongrant の追加|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルへのアプリ ロールの割り当ての追加|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルの資格情報の削除|
| Core Directory (コア ディレクトリ)| [ポリシー]| サービス プリンシパルからのポリシーの削除|
| Core Directory (コア ディレクトリ)| デバイス| デバイスの更新|
| Core Directory (コア ディレクトリ)| デバイス| デバイスの追加|
| Core Directory (コア ディレクトリ)| デバイス| デバイス構成の追加|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| パスワードの変更 (セルフサービス)|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| セルフサービスのパスワード リセットを登録したユーザー|
| セルフサービスによるグループ管理| グループ| 保留になっているグループへの参加要求の承認|
| Core Directory (コア ディレクトリ)| Directory| 未確認ドメインの削除|
| Core Directory (コア ディレクトリ)| Directory| ドメインの確認|
| Core Directory (コア ディレクトリ)| Directory| ドメインの認証の設定|
| Core Directory (コア ディレクトリ)| Directory| パスワード ポリシーの設定|
| Core Directory (コア ディレクトリ)| Directory| 会社へのパートナーの追加|
| Core Directory (コア ディレクトリ)| Directory| パートナーへの会社の昇格|
| Core Directory (コア ディレクトリ)| Directory| パートナーシップの設定|
| Core Directory (コア ディレクトリ)| Directory| 誤削除のしきい値の設定|
| Core Directory (コア ディレクトリ)| Directory| パートナーの降格|
| 招待されたユーザー| User| 外部ユーザーの招待|
| アカウント プロビジョニング| アプリケーション| インポート|
| Core Directory (コア ディレクトリ)| アプリケーション| サービス プリンシパルからの所有者の削除|
| Core Directory (コア ディレクトリ)| デバイス| デバイスからの登録済みユーザーの削除|
| Core Directory (コア ディレクトリ)| Directory| 会社情報の設定|
| Core Directory (コア ディレクトリ)| Directory| ドメインのフェデレーションの設定|
| Core Directory (コア ディレクトリ)| Directory| 会社の作成|
| Core Directory (コア ディレクトリ)| Directory| 権限管理プロパティの消去|
| Core Directory (コア ディレクトリ)| Directory| DirSync 機能の設定|
| Core Directory (コア ディレクトリ)| Directory| 電子メール検証済みドメインの確認|
| Core Directory (コア ディレクトリ)| User| ユーザー ライセンスの変更|
| Core Directory (コア ディレクトリ)| User| ユーザー パスワードの変更|
| Core Directory (コア ディレクトリ)| User| ユーザー パスワードのリセット|
| Core Directory (コア ディレクトリ)| User| ユーザーへのアプリ ロールの割り当て許可の追加|
| Core Directory (コア ディレクトリ)| User| ロールへのメンバーの追加|
| Core Directory (コア ディレクトリ)| User| ユーザーのアプリケーション パスワードの削除|
| Core Directory (コア ディレクトリ)| User| ユーザーの資格情報の更新|
| Core Directory (コア ディレクトリ)| User| ユーザー管理の設定|
| Core Directory (コア ディレクトリ)| User| ロールへのスコープを持つメンバーの追加|
| Core Directory (コア ディレクトリ)| グループ| グループの削除|
| Core Directory (コア ディレクトリ)| グループ| グループからの所有者の削除|
| Core Directory (コア ディレクトリ)| グループ| グループ設定の更新|
| Core Directory (コア ディレクトリ)| アプリケーション| アプリケーションへの所有者の追加|
| Core Directory (コア ディレクトリ)| アプリケーション| 同意の取り消し|
| Core Directory (コア ディレクトリ)| [ポリシー]| ポリシーの追加|
| Core Directory (コア ディレクトリ)| デバイス| デバイスの削除|
| Self-service Password Management \(セルフサービスによるパスワード管理)| User| セルフサービスのパスワード リセットのブロック|
| セルフサービスによるグループ管理| グループ| グループへの参加要求|
| セルフサービスによるグループ管理| グループ| ライフサイクル管理ポリシーの作成|
| セルフサービスによるグループ管理| グループ| 保留になっているグループへの参加要求の拒否|
| セルフサービスによるグループ管理| グループ| 保留になっているグループへの参加要求のキャンセル|
| セルフサービスによるグループ管理| グループ| グループの更新|
| アカウント プロビジョニング| アプリケーション| エクスポート|
| アカウント プロビジョニング| アプリケーション| 他の|
| 招待されたユーザー| User| 外部ユーザーの招待の利用|
| 招待されたユーザー| User| バイラル ユーザーの作成|
| 招待されたユーザー| User| アプリケーションへの外部ユーザーの割り当て|




## <a name="audit-logs-shortcuts"></a>監査ログのショートカット

**[Azure Active Directory]** の他にも、Azure Portal には監査データに対するエントリ ポイントが 2 つ用意されています。

- [概要]
- エンタープライズ アプリケーション

監査レポート アクティビティの完全な一覧については、 [監査レポートのイベントの一覧](active-directory-reporting-audit-events.md#list-of-audit-report-events)を参照してください。


### <a name="users-and-groups-audit-logs"></a>ユーザーとグループの監査ログ

ユーザーとグループ ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

- どの種類の更新プログラムがユーザーによって適用されているか。

- 何人のユーザーが変更されたか。

- 何個のパスワードが変更されたか。

- 管理者がディレクトリで何を行ったか。

- 追加されたのはどのグループか。

- メンバーシップが変更されたグループはあるか。

- グループの所有者は変更されたか。

- グループまたはユーザーにどのライセンスが割り当てられているか。

ユーザーとグループに関連する監査データだけを確認する場合は、**[ユーザーとグループ]** の **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、**アクティビティのリソースの種類**として、**[ユーザーとグループ]** があらかじめ選択されています。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/93.png "監査ログ")

### <a name="enterprise-applications-audit-logs"></a>エンタープライズ アプリケーションの監査ログ

アプリケーション ベースの監査レポートを使用すると、次のような疑問に対する答えを得ることができます。

* 追加または更新されたのはどのアプリケーションか。
* 削除されたのはどのアプリケーションか。
* アプリケーションのサービス プリンシパルは変更されたか。
* アプリケーションの名前は変更されたか。
* アプリケーションに同意したのはだれか。

お使いのアプリケーションに関連する監査データだけを確認する場合は、**[エンタープライズ アプリケーション]** ブレードの **[アクティビティ]** セクションの **[監査ログ]** に、フィルター処理されたビューがあります。 このエントリ ポイントには、**アクティビティのリソースの種類**として、**[エンタープライズ アプリケーション]** があらかじめ選択されています。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/134.png "監査ログ")

このビューは、**グループ**または**ユーザー**のみになるまで、さらにフィルター処理できます。

![監査ログ](./media/active-directory-reporting-activity-audit-logs/25.png "監査ログ")


## <a name="next-steps"></a>次のステップ
「 [Azure Active Directory レポート ガイド](active-directory-reporting-guide.md)」を参照してください。


