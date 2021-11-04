---
title: 条件付きアクセス ポリシーのセッション コントロール - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーのセッション コントロールとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: e33f2c7393a9c7b91dcd6fd9188bd9a89f190215
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131050722"
---
# <a name="conditional-access-session"></a>条件付きアクセス:Session

管理者は、条件付きアクセス ポリシー内でセッション コントロールを利用すると、特定のクラウド アプリケーション内でのエクスペリエンスを制限できるようになります。

![多要素認証を必要とする許可コントロールを使用した条件付きアクセス ポリシー](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>アプリケーションによって適用される制限

組織では、このコントロールを使用して、選択されたクラウド アプリにデバイス情報を渡すように Azure AD に要求できます。 このデバイス情報により、クラウドアプリケーションは、接続が準拠デバイスから開始されたのか、ドメインに参加しているデバイスから開始されたのかを知ることができ、セッションエクスペリエンスを変更することができます。 このコントロールでは、選択されたクラウド アプリとして SharePoint Online と Exchange Online のみがサポートされます。 選択すると、クラウドアプリはデバイス情報を使用して、デバイスの状態に応じて、制限付き (デバイスが管理されていない場合) または完全なエクスペリエンス (デバイスが管理および準拠している場合) をユーザーに提供します。

アプリによって適用される制限の使用と構成の詳細については、以下の記事を参照してください。

- [SharePoint Online での制限付きアクセスの有効化](/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online での制限付きアクセスの有効化](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>アプリケーションの条件付きアクセス制御

アプリの条件付きアクセス制御は、リバース プロキシ アーキテクチャを使用して Azure AD 条件付きアクセスと一意に統合されます。 Azure AD 条件付きアクセスによって、組織のアプリに対するアクセス制御を一定の条件に基づいて適用できます。 この条件では、条件付きアクセス ポリシーの適用先が、誰であるか (ユーザーまたはユーザーのグループ)、何であるか (どのクラウド アプリか)、どこであるか (どの場所およびネットワークか) が定義されます。 条件を決定したら、アクセス制御とセッション コントロールの適用によるアプリの条件付きアクセス制御を使用してデータを保護できる [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) にユーザーをルーティングできます。

アプリの条件付きアクセス制御を使用すると、アクセス ポリシーとセッション ポリシーに基づいて、ユーザー アプリのアクセスとセッションをリアルタイムで監視および制御できるようになります。 アクセス ポリシーとセッション ポリシーは、フィルターをさらに調整し、ユーザーに対して実行されるアクションを設定するため、 Cloud App Security ポータル内で使用されます。 アクセス ポリシーとセッション ポリシーにより、以下のことが可能です。

- データの流出を防止する:アンマネージド デバイスなどでの、機密性の高いドキュメントのダウンロード、切り取り、コピー、および印刷をブロックできます。
- ダウンロード時に保護する:機密性の高いドキュメントのダウンロードをブロックする代わりに、ドキュメントがラベル付けされ、Azure Information Protection で保護されるよう要求することができます。 このアクションにより、危険な可能性があるセッションで確実にドキュメントが保護され、ユーザー アクセスが制限されます。
- ラベル付けされていないファイルがアップロードされないようにする:機密性の高いファイルのアップロード、配布、他のユーザーによる使用が行われる前に、ファイルのラベルと保護が適切であると確認することが重要です。 ラベル付けされておらず機密性の高いコンテンツが含まれるファイルは、ユーザーがコンテンツを分類するまでアップロードがブロックされるようにできます。
- コンプライアンスのためにユーザー セッションを監視する (プレビュー): 危険なユーザーはアプリにサインインするときに監視され、そのアクションはセッション内からログに記録されます。 ユーザーの動作を調査して分析し、将来どこで、どのような条件においてセッション ポリシーを適用する必要があるかを理解することができます。
- アクセスをブロックする (プレビュー): いくつかのリスク要因に応じて、特定のアプリやユーザーのアクセスをきめ細かくブロックできます。 例えば、デバイス管理のフォームとしてクライアント証明書を使用している場合は、それらをブロックできます。
- カスタム アクティビティをブロックする:アプリによっては、リスクを伴う固有のシナリオがあります。Microsoft Teams や Slack のようなアプリで、機密性の高いコンテンツを含むメッセージを送信するなどです。 これらの種類のシナリオでは、機密性の高いコンテンツがないかメッセージをスキャンし、リアルタイムでメッセージをブロックできます。

詳細については、「[おすすめアプリのためにアプリの条件付きアクセス制御をデプロイする](/cloud-app-security/proxy-deployment-aad)」を参照してください。

## <a name="sign-in-frequency"></a>サインインの頻度

サインインの頻度は、ユーザーがリソースにアクセスしようとしたときにサインインし直すように求められるまでの期間を定義します。

サインイン頻度設定は、標準に従って OAUTH2 または OIDC プロトコルを実装したアプリで動作します。 次の Web アプリケーションを含む、Windows、Mac、および Mobile用のMicrosoftのネイティブ アプリの殆どがこの設定をフォローしています。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365 管理者ポータル
- Exchange Online
- SharePoint と OneDrive
- Teams Web クライアント
- Dynamics CRM Online
- Azure portal

詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)」という記事を参照してください。

## <a name="persistent-browser-session"></a>永続的ブラウザー セッション

永続的なブラウザー セッションでは、ユーザーはブラウザー ウィンドウを閉じてから再度開いた後でもサインインした状態を維持できます。

詳細については、「[条件付きアクセスを使用して認証セッション管理を構成する](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)」という記事を参照してください。

## <a name="customize-continuous-access-evaluation"></a>継続的アクセス評価をカスタマイズする

[継続的アクセス評価](concept-continuous-access-evaluation.md) は、組織の条件付きアクセスポリシーの一部として自動的に有効となります。 継続的なアクセス評価を無効にしたり、厳格に実施したりしたい組織のために、この設定が「条件付きアクセス」のセッションコントロール内のオプションとして追加されました。 継続的アクセス評価ポリシーは、すべてのユーザーまたは特定のユーザーとグループに予約範囲を設定できます。 管理者は、新しいポリシーを作成する際、または既存の条件付きアクセスポリシーを編集する際に、次の選択を行うことができます。

- **[無効]** は、**すべてのクラウドアプリ** が選択されていて、条件が選択されていない **場合に実行** され ます
- **厳密な** 適用とは、重要なイベントとポリシーがリアルタイムで適用されるという意味です。 すべての CAE 対応サービスは、クライアントまたはユーザーが要求または実行する可能性があるものに関して、常に CAE トークンを取得します。 厳密な強制モードが有効になっている場合、CAE が再生しないシナリオが 2 つがあります。
   - 非 CAE 対応クライアントは、CAE 対応サービスの通常のトークンを取得してはならない。
   - リソース プロバイダーによって表示される IP が、許可された範囲内に含められない場合は拒否します。

:::image type="content" source="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png" alt-text=" Azureポータルの新しい条件付きアクセスポリシーにCAEの設定。" lightbox="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png":::

## <a name="disable-resilience-defaults-preview"></a>復元の既定値を無効にする (プレビュー)

障害発生時、Azure ADは、条件付きアクセスポリシーを適用しながら、既存のセッションへのアクセスを拡張します。 ポリシーが評価されない場合、アクセスは回復設定によって解決されます。 

既存の回復が無効の場合、現在のセッションが終了するとアクセスが拒否されます。 詳細については、「[条件付きアクセスのデプロイを計画する](resilience-defaults.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)
