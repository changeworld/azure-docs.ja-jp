<properties 
	pageTitle="Azure API Management の FAQ | Microsoft Azure" 
	description="Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。" 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/12/2016" 
	ms.author="sdanie"/>

# Azure API Management の FAQ

Azure API Management についてよく寄せられる質問の回答、パターン、ベスト プラクティスについて説明します。

## よく寄せられる質問

-	[API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-a-question-to-the-api-management-team)
-	[機能がプレビュー段階とはどういう意味ですか。](#what-does-it-mean-if-a-feature-is-in-preview)
-	[API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護する場合、どのような方法がサポートされていますか。](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
-	[API Management インスタンスを新しいインスタンスにコピーするにはどうすればよいですか。](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
-	[API Management インスタンスはプログラムで管理できますか。](#can-i-manage-my-api-management-instance-programmatically)
-	[ユーザーを Administrators グループに追加するにはどうすればよいですか。](#how-can-i-add-a-user-to-the-administrators-group)
-	[追加するポリシーがポリシー エディターで有効になっていないのはなぜですか。](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)

### API Management チームに質問するにはどうすればよいですか。

-	質問を [API Management MSDN フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt)に投稿できます。
-	質問を電子メール (`apimgmt@microsoft.com`) でお送りいただくこともできます。

### 機能がプレビュー段階とはどういう意味ですか。

プレビュー段階の機能は機能的には完成していますが、Microsoft ではこの機能に関するフィードバックを積極的に求めているため、プレビュー段階となっています。お客様からのフィードバックに応じて重大な変更を加える可能性があるため、機能によっては実稼動環境での使用はお勧めしません。プレビュー段階の機能に関するご意見やご感想があれば、「[API Management チームに質問するにはどうすればよいですか。](#how-can-i-ask-a-question-to-the-api-management-team)」に記載されているいずれかの方法でお知らせください。

### API Management ゲートウェイとバックエンド サービス間の接続をセキュリティで保護する場合、どのような方法がサポートされていますか。

複数の方法がサポートされています。

1. HTTP 基本認証を使用します。詳細については、「[API 設定の構成](api-management-howto-create-apis.md#configure-api-settings)」をご覧ください。
2. 「[Azure API Management でクライアント証明書認証を使用してバックエンド サービスを保護する方法](api-management-howto-mutual-certificates.md)」の説明に従って、SSL 相互認証を使用します。
3. バックエンド サービスで IP ホワイトリストを使用します。Standard または Premium レベルの API Management インスタンスを使用している場合、ゲートウェイの IP アドレスが変わることはないので、この IP アドレスを許可するようにホワイトリストを構成できます。API Management インスタンスの IP アドレスは、Azure クラシック ポータルの**ダッシュボード**で取得できます。
4. API Management インスタンスを Azure Virtual Network (クラシック) に接続できます。詳細については、「[Azure API Management で VPN 接続を設定する方法](api-management-howto-setup-vpn.md)」をご覧ください。

### API Management インスタンスを新しいインスタンスにコピーするにはどうすればよいですか。

API Management サービス インスタンスは、いくつかの方法で新しいインスタンスにコピーできます。

-	API Management のバックアップと復元機能を使用します。詳細については、「[Azure API Management でサービスのバックアップと復元を使用して障害復旧を実装する方法](api-management-howto-disaster-recovery-backup-restore.md)」をご覧ください。
-	[API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) を使用して独自のバックアップと復元機能を作成し、サービス インスタンスの目的のエンティティを保存して復元します。
-	Git を使用してサービス構成をダウンロードし、新しいインスタンスにアップロードしてバックアップします。詳細については、「[Git を使用して API Management サービス構成を保存および構成する方法](api-management-configuration-repository-git.md)」をご覧ください。

### API Management インスタンスはプログラムで管理できますか。

はい。[API Management REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx) と、PowerShell の[サービス デプロイメント](https://msdn.microsoft.com/library/mt619282.aspx)および[サービス管理](https://msdn.microsoft.com/library/mt613507.aspx)コマンドレットを使用して管理できます。

### ユーザーを Administrators グループに追加するにはどうすればよいですか。

現時点では、管理者は、API Management インスタンスを含む Azure サブスクリプションで、管理者または共同管理者として Azure クラシック ポータルからログインできるユーザーに制限されています。パブリッシャー ポータルで作成されたユーザーは、管理者として指定することも、Administrators グループに追加することもできません。


### 追加するポリシーがポリシー エディターで有効になっていないのはなぜですか。

追加するポリシーが有効になっていない場合、そのポリシーの正しいスコープが選択されていることを確認します。各ポリシー ステートメントは、特定のスコープおよびポリシー セクションで使用するように設計されています。ポリシーのポリシー セクションとスコープを確認するには、[ポリシー リファレンス](https://msdn.microsoft.com/library/azure/dn894080.aspx)に関するページでそのポリシーの**使用例**を参照してください。

<!---HONumber=AcomDC_0413_2016-->