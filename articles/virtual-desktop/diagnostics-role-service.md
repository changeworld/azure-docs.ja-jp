---
title: Azure Virtual Desktop の診断の問題 - Azure
description: Azure Virtual Desktop の診断機能を使用して問題を診断する方法。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 06/19/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 0154bb61dcfbcf8024f09052d7a199ac63f71d8e
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112412543"
---
# <a name="identify-and-diagnose-azure-virtual-desktop-issues"></a>Azure Virtual Desktop の問題を特定し診断する

>[!IMPORTANT]
>この内容は、Azure Resource Manager Azure Virtual Desktop オブジェクトを含む Azure Virtual Desktop に適用されます。 Azure Resource Manager オブジェクトを含まない Azure Virtual Desktop (クラシック) を使用している場合は、[こちらの記事](./virtual-desktop-fall-2019/diagnostics-role-service-2019.md)を参照してください。

Azure Virtual Desktop では、管理者が単一のインターフェイスを使用して問題を特定できる診断機能が提供されます。 Azure Virtual Desktop の診断機能の詳細については、[「診断機能にログ分析を使用する」](diagnostics-log-analytics.md)を参照してください。

診断ロール サービス自体が Azure Virtual Desktop の一部であるため、Azure Virtual Desktop に到達しない接続は診断結果に表示されません。 Azure Virtual Desktop 接続の問題は、エンドユーザーにネットワーク接続の問題が発生しているときに発生する可能性があります。

## <a name="common-error-scenarios"></a>一般的なエラー シナリオ

WVDErrors テーブルでは、あらゆる種類のアクティビティを対象にエラーが追跡記録されます。 "ServiceError" という名前の列には、"True" または "False" という印の付いた追加フラグがあります。 このフラグにより、エラーがサービスに関連しているのかどうかがわかります。

* 値が "True" の場合、サービス チームはこの問題を既に調査している可能性があります。 これがユーザー エクスペリエンスに影響を与え、高い頻度で現れる場合、Azure Virtual Desktop のサポート チケットを送信することをお勧めします。
* 値が "False" の場合、構成に誤りがある可能性があり、自分で修正できます。 エラー メッセージを見れば、どこから始めたら良いかヒントが与えられます。

次の表では、管理者が経験する可能性のある一般的なエラーの一覧を示します。

>[!NOTE]
>この一覧には、最も一般的なエラーが含まれており、定期的に更新されます。 最新の情報を得るために、必ず 1 か月に 1 回以上この記事をご確認ください。

### <a name="connection-error-codes"></a>接続エラー コード

|数値コード|エラー コード|推奨されている解決方法|
|---|---|---|
|-2147467259|ConnectionFailedAdTrustedRelationshipFailure|セッション ホストは、Active Directory に正しく参加していません。|
|-2146233088|ConnectionFailedUserHasValidSessionButRdshIsUnhealthy|セッション ホストが使用できないため、接続が失敗しました。 セッション ホストの正常性を確認してください。|
|-2146233088|ConnectionFailedClientDisconnect|このエラーが頻繁に発生する場合は、ユーザーのコンピューターがネットワークに接続されていることを確認してください。|
|-2146233088|ConnectionFailedNoHealthyRdshAvailable|ホスト ユーザーが接続しようとしたセッションが正常ではありません。 仮想マシンをデバッグしてください。|
|-2146233088|ConnectionFailedUserNotAuthorized|ユーザーは、公開されたアプリまたはデスクトップにアクセスするためのアクセス許可を持っていません。 このエラーは、公開されたリソースを管理者が削除した後に発生する可能性があります。 リモート デスクトップ アプリケーション内のフィードを更新するようユーザーに依頼してください。|
|2|FileNotFound|ユーザーがアクセスしようとしたアプリケーションが正しくインストールされていないか、正しくないパスに設定されています。<br><br>ユーザーがアクティブなセッションを使用しているときに新しいアプリを公開すると、ユーザーはこのアプリにアクセスできなくなります。 ユーザーがアプリにアクセスできるようにするには、セッションをシャットダウンして再起動する必要があります。 |
|3|InvalidCredentials|ユーザーが入力したユーザー名またはパスワードが、既存のどのユーザー名またはパスワードとも一致しません。 資格情報に入力ミスがないことを確認し、もう一度やり直してください。|
|8|ConnectionBroken|クライアントとゲートウェイまたはサーバーとの間の接続が削除されました。 予期せず発生した場合を除き、アクションは不要です。|
|14|UnexpectedNetworkDisconnect|ネットワークへの接続が削除されました。 ユーザーにもう一度接続するよう依頼してください。|
|24|ReverseConnectFailed|ホスト仮想マシンには、RD ゲートウェイへの直接の見通し線がありません。 ゲートウェイ IP アドレスを解決できることを確認してください。|

## <a name="next-steps"></a>次のステップ

Azure Virtual Desktop 内のロールの詳細については、「[Azure Virtual Desktop 環境](environment-setup.md)」を参照してください。

Azure Virtual Desktop に使用可能な PowerShell コマンドレットの一覧を表示するには、[PowerShell リファレンス](/powershell/windows-virtual-desktop/overview)に関する記事をご覧ください。
