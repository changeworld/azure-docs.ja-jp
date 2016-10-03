<properties
	pageTitle="Azure Active Directory Identity Protection | Microsoft Azure"
	description="Azure AD Identity Protection を使用して、侵害された ID またはデバイスを攻撃者が悪用する能力を制限する方法、および以前に疑われた、または侵害を確認された ID またはデバイスを保護する方法について説明します。"
	services="active-directory"
	keywords="Azure Active Directory Identity Protection, Cloud App Discovery, アプリケーションの管理, セキュリティ, リスク, リスク レベル, 脆弱性, セキュリティ ポリシー"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection 

Azure Active Directory Identity Protection は、リスク イベントや組織の ID に影響する潜在的な脆弱性に関する統合ビューを提供する、Azure AD Premium P2 エディションの機能です。Microsoft は 10 年以上にわたってクラウド ベースの ID を保護してきました。Azure AD Identity Protection は、同じ保護システムを企業のお客様が利用できるようにするものです。Identity Protection は、既存の Azure AD 異常検出機能 (Azure AD の異常アクティビティ レポートで利用可能) を利用し、リアルタイムで異常を検出できる新しいリスク イベントの種類が導入されています。



##Getting Started (概要)

ほとんどのセキュリティ侵害は、攻撃者がユーザーの ID を盗むことにより環境にアクセスできるようになると発生します。攻撃者は、ますます巧妙にサード パーティの侵害を利用し、高度なフィッシング攻撃を使用するようになっています。低い特権のユーザー アカウントであってもアクセス権を得た攻撃者は、比較的簡単に横方向への移動によって重要な企業リソースにアクセスしてしまいます。したがって、すべての ID を保護し、ID が侵害された場合は侵害された ID が悪用されるのを事前に防止することが不可欠です。

侵害された ID を検出するのは簡単な作業はありません。Identity Protection がそれを助けてくれます。Identity Protection はアダプティブ機械学習アルゴリズムとヒューリスティックを使用して、ID が侵害されたことを示している可能性のある異常とリスク イベントを検出します。
 
このデータを使用して、Identity Protection はレポートとアラートを生成し、ユーザーがこれらのリスク イベントを調査して、適切な修復または軽減のアクションを実行できるようにします。
 
ただし、Azure Active Directory Identity Protection は単なる監視とレポート作成のツールではありません。リスク イベントを基にして、Identity Protection は各ユーザーのユーザー リスク レベルを計算し、ユーザーがリスク ベースのポリシーを構成して組織の ID を自動的に保護するできるようにします。これらのリスクに基づくポリシーと、Azure Active Directory および EMS によって提供される他の条件付きアクセス コントロールにより、パスワードのリセットや多要素認証の適用などのアダプティブ修復アクションを自動的にブロックまたは提供できます。

####Identity Protection の機能 

**リスク イベントとリスクの高いアカウントの検出:**

- 機械学習とヒューリスティック規則を使用して、6 種類のリスク イベントを検出します

- ユーザーのリスク レベルを計算します

- 脆弱性を目立たせることにより全体的なセキュリティ対策を向上させるためのカスタム推奨事項を提供します



**リスク イベントの調査:**

- リスク イベントの通知を送信します

- 関連情報とコンテキスト情報を使用してリスク イベントを調査します

- 調査を追跡するための基本的なワークフローを提供します

- パスワード リセットなどの修復アクションへの簡単なアクセスを提供します



**リスクに基づく条件付きアクセス ポリシー:**

- サインインのブロックまたは多要素認証チャレンジの要求によりリスクの高いサインインを軽減するポリシー

- リスクの高いユーザー アカウントをブロックまたはセキュリティ保護するためのポリシー

- 多要素認証用に登録するようユーザーに要求するポリシー


## 検出とリスク

### リスク イベント

リスク イベントは Identity Protection によって疑いありのフラグが設定されたイベントであり、ID が侵害されている可能性を示します。すべてのリスク イベントについては、「[Azure Active Directory Identity Protection で検出されるリスク イベントの種類](active-directory-identityprotection-risk-events-types.md)」を参照してください。

これらのリスク イベントの一部は、Azure Portal の Azure AD 異常アクティビティ レポートで利用できたものです。次の表では、さまざまなリスク イベントの種類と、対応する **Azure AD 異常なアクティビティ** レポートの一覧を示します。Microsoft はこの分野への投資を続けることにより、既存のリスク イベントの検出精度を継続的に向上させ、必要に応じて新しいリスク イベントの種類を追加する予定です。



| Identity Protection のリスク イベントの種類 | 対応する Azure AD 異常アクティビティ レポート |
| :-- | :-- |
| 漏洩した資格情報 | 資格情報が漏洩したユーザー |
| 特殊な場所へのあり得ない移動 |	不規則なサインイン アクティビティ |
| 感染しているデバイスからのサインイン | 感染している可能性があるデバイスからのサインイン |
| 匿名の IP アドレスからのサインイン | 不明なソースからのサインイン |
| 不審なアクティビティのある IP アドレスからのサインイン |	不審なアクティビティのある IP アドレスからのサインイン |
| 未知の場所からのサインイン | - | | ロックアウト イベント | - |

以下の Azure AD 異常アクティビティ レポートは Azure AD Identity Protection のリスク イベントには含まれず、したがって Identity Protection では利用できません。これらのレポートは Azure Portal でまだ使用できますが、Identity Protection のリスク イベントによって置き換えられるため、将来的には廃止されます。

- 複数のエラー後のサインイン
- 複数の地域からのサインイン

### リスク レベル

リスク イベントのリスク レベルは、リスク イベントの重大度 (高、中、低) を示します。リスク レベルは、Identity Protection のユーザーが組織に対するリスクの軽減に必要なアクションの優先順位を決定するときの参考になります。リスク イベントの重大度は、一般に含まれるノイズの量との組み合わせで、ID 侵害の予測因子としての信号の強度を表します。

- **高**: 確実性が高く、重大度が高いリスク イベント。これらのイベントはユーザーの ID が侵害されたことの強力なインジケーターであり、影響を受けたすべてのユーザー アカウントをすぐに修復する必要があります。

- **中**: 重大度が高く、確実性が低いリスク イベント、またはその逆。これらのイベントはリスクが高い可能性があり、影響を受けたすべてのユーザー アカウントを修復する必要があります。

- **低**: 確実性が低く、重大度が低いリスク イベント。このイベントだけでは早急な対応は必要ありませんが、他のリスク イベントと組み合わせた場合、ID が侵害された強い指標となることがあります。


![リスク レベル](./media/active-directory-identityprotection/01.png "リスク レベル")

 

リスク イベントは、**リアルタイム**で識別されるか、リスク イベントが既に発生した後の後処理 (オフライン) で識別されます。現在、Identity Protection のほとんどのリスク イベントはオフラインで計算され、2 ～ 4 時間以内に Identity Protection に表示されます。リアルタイムのリスク イベントは、評価はリアルタイムで行われますが、Identity Protection コンソールに表示されるまでには 5 ～ 10 分かかります。

現在、一部のレガシー クライアントではリアルタイムのリスク イベントの検出と防止はサポートされていません。その結果、これらのクライアントからのサインインをリアルタイムで検出または防止することはできません。


## 調査
Identity Protection を使用するときは、通常、Identity Protection ダッシュボードから開始します。

![修復](./media/active-directory-identityprotection/1000.png "修復")

ダッシュボードからは次のものにアクセスできます。
 
- **リスクのフラグ付きユーザー**、**リスク イベント**、**脆弱性**などのレポート
- **セキュリティ ポリシー**、**通知**、**多要素認証の登録**の構成などの設定
 

通常、これが調査の起点になります。調査は、修復または軽減の手順が必要であるかどうかを決定し、ID が侵害を受けているかどうか、およびどのように侵害されたかを理解し、侵害された ID がどのように使用されたかを理解するために、アクティビティ、ログ、およびリスク イベントに関連するその他の関連情報を確認するプロセスです。

調査アクティビティを、Azure Active Directory Protection が電子メールごとに送信する[通知](active-directory-identityprotection-notifications.md)と関連付けることができます。

次のセクションでは、調査に関連する詳細と手順について説明します。



## ユーザーのリスク レベルとは

ユーザーのリスク レベルは、ユーザーの ID が侵害された可能性を示す値 (高、中、低) です。ユーザーの ID に関連付けられているユーザー リスク イベントに基づいて計算されます。

リスク イベントの状態は、**アクティブ**と**クローズ**のいずれかです。ユーザー リスクの計算に使用されるのは、**アクティブ**なリスク イベントのみです。

ユーザー リスク レベルは、次の入力を使用して計算されます。

- ユーザーに影響を与えるアクティブなリスク イベント
- それらのイベントのリスク レベル
- すべての修復アクションが実施されたかどうか

![ユーザーのリスク](./media/active-directory-identityprotection/1001.png "ユーザーのリスク")



ユーザー リスク レベルを使用して、リスクの高いユーザーのサインインをブロックする、またはパスワードの安全な変更を強制する、条件付きアクセス ポリシーを作成できます。


## リスク イベントの手動クローズ

ほとんどの場合、セキュリティ保護されたパスワードのリセットなどの修復アクションを実行すると、リスク イベントは自動的にクローズします。ただし、それが不可能な場合もあります。たとえば次のような場合です。

- アクティブなリスク イベントの対象ユーザーが削除されている
- 報告されたリスク イベントが正当なユーザーによって実行されたことが調査でわかっている

**アクティブ**なリスク イベントはユーザー リスク計算に使用されるため、リスク イベントを手動でクローズしてリスク レベルを下げることが必要になる場合があります。調査の過程で、リスク イベントの状態を変更するいずれかの操作を実行できます。

![アクション](./media/active-directory-identityprotection/34.png "アクション")

- **解決** - リスク イベントを調査した後、Identity Protection の外部で適切な修復アクションを行い、リスク イベントがクローズしたことが確実な場合、イベントを解決済みとしてマークします。解決されたイベントはリスク イベントの状態をクローズに設定し、そのリスク イベントはユーザー リスクに対して考慮されなくなります。

- **誤検知としてマーク** - 場合によっては、リスク イベントを調査した結果、誤って高リスクのフラグが設定されたことがわかる場合があります。リスク イベントを誤検知としてマークすることにより、このようなことの発生を減らすことができます。これは、今後機械学習アルゴリズムが同様のイベントの分類を向上させるのに役立ちます。誤検知イベントの状態は**クローズ**になり、ユーザー リスクに対して考慮されなくなります。

- **無視** - どのような修復アクションも行わずに、リスク イベントをアクティブ リストから削除する場合は、リスク イベントを無視に指定できます。イベントはクローズ状態になります。無視されたイベントは、ユーザー リスクに対して考慮されません。このオプションは、特殊な状況下でのみ使用する必要があります。

- **再アクティブ化** - **解決**、**誤検知**、または**無視**を選択することによって手動でクローズされたリスク イベントを再アクティブ化し、イベントの状態を**アクティブ**に戻すことができます。再アクティブ化されたリスク イベントは、ユーザー リスク レベルの計算に対して考慮されます。修復 (セキュリティ保護されたパスワードのリセットなど) によってクローズされたリスク イベントを再アクティブ化することはできません。




**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードの **[Investigate (調査)]** で **[Risk events (リスク イベント)]** をクリックします。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1002.png "パスワードの手動リセット")

2. **[Risk events (リスク イベント)]** の一覧で、リスクをクリックします。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1003.png "パスワードの手動リセット")

2. リスクのブレードで、ユーザーを右クリックします。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1004.png "パスワードの手動リセット")



### ユーザーのすべてのリスク イベントを手動で閉じる

Azure Active Directory Identity Protection には、ユーザーのリスク イベントを個別に手動で閉じる代わりに、1 回のクリックでユーザーのすべてのイベントを閉じる方法が用意されています。


![アクション](./media/active-directory-identityprotection/2222.png "アクション")

**[Dismiss all events (すべてのイベントを閉じる)]** をクリックすると、すべてのイベントが閉じられ、影響を受けるユーザーは危険な状態ではなくなります。



## ユーザー リスク イベントの修復

修復とは、以前に侵害の疑いがある、または侵害を検知した ID またはデバイスをセキュリティで保護するアクションです。修復アクションでは、ID またはデバイスを安全な状態に復元し、ID またはデバイスに関連付けられた以前のリスク イベントを解決します。

次の方法でユーザー リスク イベントを修復できます。

- セキュリティ保護されたパスワードのリセットを実行し、ユーザー リスク イベントを手動で修復します。

- ユーザーのリスク セキュリティ ポリシーを構成し、ユーザー リスク イベントを自動的に緩和または修復します。

- 感染したデバイスを再イメージングします。


### セキュリティ保護されたパスワードの手動リセット

セキュリティ保護されたパスワードのリセットは、多くのリスク イベントに対する効果的な修復手段であり、実行すると、そのリスク イベントは自動的にクローズして、ユーザー リスク レベルが再計算されます。Identity Protection ダッシュボードを使用して、リスクの高いユーザーのパスワード リセットを開始できます。

関連するダイアログでは、2 つの方法でパスワードをリセットできます。

**パスワードのリセット** - **[ユーザーにパスワードのリセットを要求する]** を選択すると、ユーザーが多要素認証に登録している場合、ユーザーは自分で回復できます。ユーザーは、次にサインインするとき、多要素認証のチャレンジを正しく解決することを要求され、その後、パスワードの変更を強制されます。ユーザー アカウントがまだ多要素認証に登録されていない場合、このオプションは使用できません。

**一時パスワード** - **[一時パスワードを生成する]** を選択すると、すぐに既存のパスワードが無効化され、ユーザーに対して新しい一時パスワードが作成されます。ユーザーの連絡用メール アドレスまたはユーザーのマネージャーに、新しい一時パスワードを送信します。パスワードは一時的なので、ユーザーはサインイン時にパスワードの変更を求められます。


![ポリシー](./media/active-directory-identityprotection/1005.png "ポリシー")


**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードで、**[Users flagged for risk (リスクのフラグ付きユーザー)]** をクリックします。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1006.png "パスワードの手動リセット")


2. ユーザーの一覧から、少なくとも 1 つのリスク イベントを持つユーザーを選択します。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1007.png "パスワードの手動リセット")


2. ユーザーのブレードで、**[パスワードのリセット]** をクリックします。

	![パスワードの手動リセット](./media/active-directory-identityprotection/1008.png "パスワードの手動リセット")





## ユーザーのリスク セキュリティ ポリシー

ユーザーのリスク セキュリティ ポリシーは、特定のユーザーに対するリスク レベルを評価し、定義されている条件とルールに基づいて修復および軽減のアクションを適用する条件付きアクセス ポリシーです。


![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1009.png "ユーザーのリスク ポリシー")


Azure AD Identity Protection では、リスクのフラグ付きユーザーの軽減策と修復を管理するために、次のことが可能です。

- ポリシーを適用するユーザーとグループを設定する。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1010.png "ユーザーのリスク ポリシー")


- ポリシーをトリガーするユーザー リスク レベルのしきい値 (低、中、高) を設定する。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1011.png "ユーザーのリスク ポリシー")


- ポリシーがトリガーされたときに適用するコントロールを設定する。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1012.png "ユーザーのリスク ポリシー")


- ポリシーの状態を切り替える。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/403.png "MFA 登録")


- 変更を行う前に、変更の影響を確認、評価する。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1013.png "ユーザーのリスク ポリシー")


**高**しきい値を選択すると、ポリシーがトリガーされる回数が減り、ユーザーへの影響が最小限になります。ただし、**低**および**中**レベルのリスクのフラグ付きユーザーはポリシーから除外されるため、以前に疑いのあった、または侵害されたことが知られていた、ID またはデバイスをセキュリティで保護することはできません。

ポリシーを設定するときは次のようにします。

- 多数の誤検知を生成する可能性があるユーザー (開発者、セキュリティ アナリスト) を除外します。

- ポリシーを有効にするのが実際的でないロケールのユーザー (ヘルプデスクにアクセスできないユーザーなど) を除外します。

- ポリシーの初期展開中、またはエンド ユーザーに表示されるチャレンジを最小限に抑える必要がある場合は、**高**しきい値を使用します。

- 組織のセキュリティを強化する必要がある場合は、**低**しきい値を使用します。**低**しきい値を選択すると、追加のユーザー サインイン チャレンジが導入されますが、セキュリティは強化されます。

ほとんどの組織に対する既定の設定として、**中**しきい値の規則を構成し、使いやすさとセキュリティのバランスを取ることをお勧めします。

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

- [侵害されたアカウントの復旧フロー](active-directory-identityprotection-flows.md#compromised-account-recovery)。

- [ブロック済みの侵害されたアカウントのフロー](active-directory-identityprotection-flows.md#compromised-account-blocked)。


**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードの **[構成]** セクションで **[User risk policy (ユーザー リスク ポリシー)]** をクリックします。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1009.png "ユーザーのリスク ポリシー")






## ユーザー リスク イベントの緩和
管理者は、ユーザー リスク セキュリティ ポリシーを設定し、リスク レベルに応じてサインイン時にユーザーをブロックできます。

サインインをブロックすると、以下のことが行われます。
 
- 影響を受けるユーザーに対して新しいユーザー リスク イベントが生成されなくなります。

- 管理者は、ユーザーの ID に影響を与えるリスク イベントを手動で修復して、セキュリティで保護された状態に復元できます。



## サインイン リスク レベルとは何ですか。

サインイン リスク レベルは、ユーザーの ID を使用して他のユーザーが認証しようとしている、特定のサインインの可能性 (高、中、低) を示します。サインイン リスク レベルはサインイン時に評価され、その特定のサインインに対してリアルタイムで検出されたリスク イベントとインジケーターが考慮されます。

## サインイン リスク イベントの軽減 
軽減とは、ID またはデバイスを安全な状態に復元せずに、侵害された ID またはデバイスを悪用されないように、攻撃者の能力を制限するアクションです。軽減策では、ID またはデバイスと関連付けられた以前のサインイン リスク イベントを解決することはありません。

Azure AD Identity Protection の条件付きアクセスを使用して、サイン イン リスク イベントを自動的に軽減できます。これらのポリシーを使用するときは、リスクの高いサインインをブロックする、またはユーザーに多要素認証の実行を要求する、ユーザーまたはサインインのリスク レベルを検討します。これらのアクションにより、攻撃者が盗んだ ID 利用して損害を与えるのを防ぎ、ID をセキュリティで保護する時間を稼ぐことができます。


## サインインのリスク セキュリティ ポリシー

サインインのリスク ポリシーは、事前定義された条件と規則に基づいて、特定のサインインのリスクを評価し、対応策を適用する条件付きアクセス ポリシーです。

![サインインのリスク ポリシー](./media/active-directory-identityprotection/1014.png "サインインのリスク ポリシー")


Azure AD Identity Protection では、リスクの高いサインインの軽減策を管理するために、次のことが可能です。

- ポリシーを適用するユーザーとグループを設定する。

	![サインインのリスク ポリシー](./media/active-directory-identityprotection/1015.png "サインインのリスク ポリシー")


- ポリシーをトリガーするサインイン リスク レベルのしきい値 (低、中、高) を設定する。

	![サインインのリスク ポリシー](./media/active-directory-identityprotection/1016.png "サインインのリスク ポリシー")


- ポリシーがトリガーされたときに適用するコントロールを設定する。

	![サインインのリスク ポリシー](./media/active-directory-identityprotection/1017.png "サインインのリスク ポリシー")


- ポリシーの状態を切り替える。

	![MFA 登録](./media/active-directory-identityprotection/403.png "MFA 登録")

- 変更を行う前に、変更の影響を確認、評価する。

	![サインインのリスク ポリシー](./media/active-directory-identityprotection/1018.png "サインインのリスク ポリシー")

 
**高**しきい値を選択すると、ポリシーがトリガーされる回数が減り、ユーザーへの影響が最小限になります。
 
ただし、**低**および**中**レベルのリスクのフラグ付きサインインはポリシーから除外されるため、攻撃者が侵害された ID を悪用するのをブロックすることはできません。

ポリシーを設定するときは次のようにします。

- 多要素認証を使用しない/できないユーザーを除外します。

- ポリシーを有効にするのが実際的でないロケールのユーザー (ヘルプデスクにアクセスできないユーザーなど) を除外します。

- 多数の誤検知を生成する可能性があるユーザー (開発者、セキュリティ アナリスト) を除外します。

- ポリシーの初期展開中、またはエンド ユーザーに表示されるチャレンジを最小限に抑える必要がある場合は、**高**しきい値を使用します。

- 組織のセキュリティを強化する必要がある場合は、**低**しきい値を使用します。**低**しきい値を選択すると、追加のユーザー サインイン チャレンジが導入されますが、セキュリティは強化されます。

ほとんどの組織に対する既定の設定として、**中**しきい値の規則を構成し、使いやすさとセキュリティのバランスを取ることをお勧めします。

 
サインイン リスク ポリシーは次のように使用されます。

- 最新の認証を使用するすべてのブラウザー トラフィックとサインインに適用されます。
- ADFS などのフェデレーション IDP で WS-Trust エンドポイントを無効にすることによって従来のセキュリティ プロトコルを使用するアプリケーションには適用されません。

Identity Protection コンソールの **[リスク イベント]** ページには、以下に該当するすべてのイベントが一覧表示されます。

- そのポリシーが適用されたイベント
- アクティビティを確認して、アクションが適切だったかどうかを判断できるイベント

関連するユーザー エクスペリエンスの概要については、以下を参照してください。

- [リスクの高いサインインの復旧](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

- [ブロックされたリスクの高いサインイン](active-directory-identityprotection-flows.md#risky-sign-in-blocked)

- [リスクの高いサインインの間の多要素認証の登録](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)





**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードの **[構成]** セクションで **[Sign-in risk policy (サインイン リスク ポリシー)]** をクリックします。

	![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1014.png "ユーザーのリスク ポリシー")





## 多要素認証登録ポリシー

Azure Multi-Factor Authentication は、本人性の確認において、ユーザー名とパスワードに加えて、その他の要素を取り入れる手法です。ユーザーのサインインとトランザクションに 2 層構造のセキュリティを確保することができます。次のような理由から、ユーザーのサインインに対して Azure Multi-Factor Authentication を要求することをお勧めします。

- 簡単な検証オプションで強力な認証が提供されます。

- アカウント侵害からの組織の保護と復旧の準備において重要な役割を果たします。

![ユーザーのリスク ポリシー](./media/active-directory-identityprotection/1019.png "ユーザーのリスク ポリシー")



詳細については、「[Azure Multi-Factor Authentication とは](../multi-factor-authentication/multi-factor-authentication.md)」を参照してください。


Azure AD Identity Protection を使用すると、多要素認証の登録の展開を管理するために、以下のことを有効にするポリシーを構成できます。



- ポリシーを適用するユーザーとグループを設定する。

	![MFA policy](./media/active-directory-identityprotection/1020.png "MFA policy")



- ポリシーがトリガーされたときに適用するコントロールを設定する。

	![MFA policy](./media/active-directory-identityprotection/1021.png "MFA policy")


- ポリシーの状態を切り替える。

	![MFA policy](./media/active-directory-identityprotection/403.png "MFA policy")

- 現在の登録状態を表示する。

	![MFA policy](./media/active-directory-identityprotection/1022.png "MFA policy")


関連するユーザー エクスペリエンスの概要については、以下を参照してください。

- [多要素認証の登録のフロー](active-directory-identityprotection-flows.md#multi-factor-authentication-registration)。

- [リスクの高いサインインの間の多要素認証の登録](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)。





**関連する構成ダイアログ ボックスを開くには**:

1. **[Azure AD Identity Protection]** ブレードの **[構成]** セクションで **[Multi-factor authentication registration (多要素認証の登録)]** をクリックします。

	![MFA policy](./media/active-directory-identityprotection/1019.png "MFA policy")





## 次のステップ

 - [Channel 9: Azure AD and Identity Show: Identity Protection Preview (Channel 9: Azure AD および Identity ショー: Identity Protection プレビュー)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Azure Active Directory Identity Protection で検出されるリスク イベントの種類](active-directory-identityprotection-risk-events-types.md)
 - [Azure Active Directory Identity Protection で検出される脆弱性](active-directory-identityprotection-vulnerabilities.md)
 - [Azure Active Directory Identity Protection の通知](active-directory-identityprotection-notifications.md)
 - [Azure Active Directory Identity Protection のフロー](active-directory-identityprotection-flows.md)
 - [Azure Active Directory Identity Protection プレイブック](active-directory-identityprotection-playbook.md)
 - [Azure Active Directory Identity Protection 用語集](active-directory-identityprotection-glossary.md)

 - [Azure Active Directory Identity Protection の有効化](active-directory-identityprotection-enable.md)
 - [Azure Active Directory Identity Protection - ユーザーのブロックを解除する方法](active-directory-identityprotection-unblock-howto.md)

 - [Azure Active Directory Identity Protection と Microsoft Graph の基本](active-directory-identityprotection-graph-getting-started.md)

<!---HONumber=AcomDC_0921_2016-->