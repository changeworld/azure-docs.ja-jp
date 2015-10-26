<properties 
	pageTitle="Microsoft Passport の概要と、この新しい証明書ベースの認証の詳細。| Microsoft Azure" 
	description="最初の実行エクスペリエンス中に Azure AD への参加を設定する方法について説明するトピックです。" 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/21/2015" 
	ms.author="femila"/>

# Microsoft Passport 経由でのパスワードを使用しない ID の認証

パスワードだけで認証する現在の方法は、ユーザーの安全を保つうえで十分ではありません。ユーザーは、パスワードを再利用したり、忘れたりすることがあります。パスワードに対しては、攻撃やフィッシング、解読、推測が可能です。また、パスワードは、覚えるのが難しく、"Pass the Hash" のような攻撃の対象にもなります。"Pass the Hash" は、通常であれば関連付けられたプレーンテキスト パスワードを要求する代わりに、攻撃者がユーザーのパスワードの基になる NTLM ハッシュや LanMan ハッシュを使用してリモートのサーバー/サービスを認証できるようにするハッキング手法です。詳細については、[Pass the Hash](https://technet.microsoft.com/JA-JP/dn785092.aspx) に関するページを参照してください。

## Microsoft Passport とは
Microsoft Passport は、組織とコンシューマー向けの、パスワードを超える新しい秘密/公開キーまたは証明書ベースの認証方式です。この認証方式は、パスワードに代わるこれらのキー ペア資格情報を利用し、セキュリティ侵害、盗難、フィッシングに対する耐性を備えています。Microsoft Passport では、ユーザーは、Microsoft アカウント、Active Directory アカウント、Microsoft Azure Active Directory (AD) アカウント、または Fast ID Online (FIDO) 認証をサポートする Microsoft 以外のサービスに対して認証できます。Microsoft Passport の登録時の 2 段階の初期検証の後、Microsoft Passport がユーザーのデバイスに設定されます。次に、ユーザーがジェスチャ (Windows Hello または PIN) を設定します。ユーザーは、身元を確認するジェスチャを提供します。その後、Windows が Microsoft Passport を使ってユーザーを認証し、保護されたリソースとサービスにアクセスできるようにします。

秘密キーは、PIN、生体認証、リモート デバイス (ユーザーがデバイスへのサインインに使用したスマート カードなど) などの "ユーザー ジェスチャ" を通じてのみ使用可能になります。この情報は、証明書または非対称のキー ペアにリンクされます。デバイスにトラステッド プラットフォーム モジュール (TPM) チップが搭載されている場合、この秘密キーはハードウェアで証明されます。秘密キーはデバイスの外部に移動されることはありません。

公開キーは、Azure Active Directory と Windows Server Active Directory (オンプレミスの場合) に登録されます。ID プロバイダー (IDP) は、ユーザーの公開キーを秘密キーにマップしてユーザーを検証し、One Time Password (OTP)、Phonefactor、その他の通知メカニズムを使ってサインイン情報を提供します。
## Microsoft Passport を導入する理由
Microsoft Passport を有効にすると、次の操作によって会社のリソースのセキュリティを高めることができます。

* ハードウェア推奨オプションで Microsoft Passport を設定します。この場合、キーは、使用可能な場合は TPM 1.2 または TPM 2.0 によって、または TPM が使用可能でない場合はソフトウェアによって生成されます。 

* PIN の複雑さと長さ、および組織で Hello の使用が有効であるかどうかを定義します。

* 証明書ベースの信頼を使って、スマート カードのようなシナリオをサポートするように Microsoft Passport を構成します。

## Microsoft Passport のしくみ
1. キーは、ハードウェアで生成されます。多くのコンピューターには、暗号化キーをデバイスに統合してハードウェアをセキュリティで保護する、組み込みのトラステッド プラットフォーム モジュール (TPM) チップが搭載されています。キー、または生成されたキーから生成される証明書の生成には、TPM 1.2 または TPM 2.0 が使用されます。

2. これらのハードウェア バインド キーは、TPM によって証明されます。

3. 1 つのロック解除ジェスチャによってデバイスのロックが解除されます。このジェスチャにより、デバイスがドメインに参加している場合や Azure AD に参加している場合に複数のリソースにアクセスできるようになります。

## Microsoft Passport のライフサイクル
Microsoft Passport による認証のライフサイクル![](./media/active-directory-azureadjoin/active-directory-azureadjoin-microsoft-passport.png) 上の図は、秘密/公開キーのペアと、ID プロバイダーによる検証を示しています。これらの各手順について、以下で詳しく説明します。

1. ユーザーが組み込みの複数の証明方法 (ジェスチャ、物理スマート カード、Multi-Factor Authentication) を通じて自分の身元を証明し、Azure Active Directory、Active Directory などの ID プロバイダー (IDP) にこの情報を送信します。

2.  デバイスが、キーを作成し、キーを証明した後、このキーの公開部分を取り出してステーションのステートメントをアタッチします。署名後 IDP に送信して、このキーを登録します。

3. キーの公開部分が IDP に登録されるとすぐに、IDP はデバイスにチャレンジを返信して、キーの秘密部分に署名するよう要求します。次に、IDP は、ユーザーが保護されたリソースにアクセスできるようにする認証トークンを検証し、発行します。

4. キーの公開部分が IDP に登録されるとすぐに、IDP はチャレンジの対象のデバイスにチャレンジを返信して、キーの秘密部分に署名するよう要求します。

5. 次に、IDP は、ユーザーとデバイスが保護されたリソースにアクセスできるようにする認証トークンを検証し、発行します。IDP は、クロスプラットフォーム アプリを作成するか、または (JS/Webcrypto API を使用して) ブラウザーのサポートを使用して、ユーザーの Microsoft Passport の資格情報を作成および使用します。

## デプロイの要件
企業レベル
---------------------------
* Azure サブスクリプション

ユーザー レベル
-------------------------------------------------------------
* コンピューターで Windows 10 Professional または Enterprise SKU が実行されている必要があります。

## 追加情報

* [Azure Active Directory 参加を使用したクラウド機能の Windows 10 デバイスへの拡張](active-directory-azureadjoin-overview.md)
* [Azure AD 参加の設定](active-directory-azureadjoin-setup.md)
* [Microsoft Passport を使った本人確認の管理](https://technet.microsoft.com/library/mt219735(v=vs.85).aspx)

<!---HONumber=Oct15_HO3-->