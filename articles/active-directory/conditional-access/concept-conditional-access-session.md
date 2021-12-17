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
ms.openlocfilehash: 4d46bece61b686552b548461e39a94c26a264174
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132344829"
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

アプリの条件付きアクセス制御は、リバース プロキシ アーキテクチャを使用して Azure AD 条件付きアクセスと一意に統合されます。 Azure AD 条件付きアクセスによって、組織のアプリに対するアクセス制御を一定の条件に基づいて適用できます。 この条件では、条件付きアクセス ポリシーの適用先が、誰であるか (ユーザーまたはユーザーのグループ)、何であるか (どのクラウド アプリか)、どこであるか (どの場所およびネットワークか) が定義されます。 条件を決定したら、[Microsoft Defender for Cloud Apps](/cloud-app-security/what-is-cloud-app-security) にユーザーをルーティングできます。このツールでは、アプリの条件付きアクセス制御でアクセスとセッションを制御し、データを保護できます。

アプリの条件付きアクセス制御を使用すると、アクセス ポリシーとセッション ポリシーに基づいて、ユーザー アプリのアクセスとセッションをリアルタイムで監視および制御できるようになります。 アクセス ポリシーとセッション ポリシーは Defender for Cloud Apps ポータル内で使用され、フィルターをさらに調整し、ユーザーに対して実行されるアクションを設定します。 アクセス ポリシーとセッション ポリシーにより、以下のことが可能です。

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

- **無効** は、**すべてのクラウド アプリ** が選択されており、条件が選択されていない場合にのみ機能します。**無効** は、条件付きアクセス ポリシーで、 **[セッション]** の **[継続的アクセス評価をカスタマイズする]** の下で選択します。 すべてのユーザー、特定のユーザー、グループを無効にすることを選択できます。
- **厳格な強制** を使用すると、CAE のセキュリティ上の利点をさらに強化できます。 確実に重要なイベントとポリシーがリアルタイムで適用されるようにします。  厳格な強制モードがオンになっているとき、CAE が適用される追加シナリオが 2 つあります。
   - CAE に対応していないクライアントは、CAE に対応しているサービスにアクセスできません。
   - リソース プロバイダーに検出されたクライアントの IP アドレスが条件付きアクセスの許可範囲にない場合、アクセスは拒否されます。

> [!NOTE] 
> 厳格な強制は、すべてのクライアント アプリケーションが CAE をサポートし、Azure AD で検出されたすべての IP アドレスと、Exchange Online や Azure Resource Mananger などのリソース プロバイダーが条件付きアクセス下の場所ポリシーに含まれていることを確認した後でのみ有効にします。 そうしないと、テナント内のユーザーがブロックされる可能性があります。

:::image type="content" source="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png" alt-text=" Azureポータルの新しい条件付きアクセスポリシーにCAEの設定。" lightbox="media/concept-conditional-access-session/continuous-access-evaluation-session-controls.png":::

## <a name="disable-resilience-defaults-preview"></a>復元の既定値を無効にする (プレビュー)

障害発生時、Azure ADは、条件付きアクセスポリシーを適用しながら、既存のセッションへのアクセスを拡張します。 ポリシーが評価されない場合、アクセスは回復設定によって解決されます。 

既存の回復が無効の場合、現在のセッションが終了するとアクセスが拒否されます。 詳細については、「[条件付きアクセスのデプロイを計画する](resilience-defaults.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

- [レポート専用モード](concept-conditional-access-report-only.md)
