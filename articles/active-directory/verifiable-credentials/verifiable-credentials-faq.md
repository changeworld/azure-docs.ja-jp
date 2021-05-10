---
title: よく寄せられる質問 - Azure Verifiable Credentials (プレビュー)
description: Verifiable Credentials についてよく寄せられる質問の回答を示します
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280719"
---
# <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

このページには、Verifiable Credentials と分散化 ID に関してよく寄せられる質問が含まれています。 質問は、次の各セクションに整理されています。

- [用語と基本](#the-basics)
- [分散化 ID に関する概念的な質問](#conceptual-questions)
- [Verifiable Credentials のプレビューの使用に関する質問](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory Verifiable Credentials は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="the-basics"></a>基本

### <a name="what-is-a-did"></a>DID とは何ですか。 

分散化識別子 (DID) は、リソースへのアクセスのセキュリティ保護、資格情報の署名と検証、およびアプリケーション データ交換の支援に使用できる識別子です。 従来のユーザー名やメール アドレスとは異なり、DID はエンティティ自体 (個人、デバイス、または会社) によって所有および制御されます。 DID は、外部組織や信頼されている仲介者からは独立した存在です。 [W3C の分散化識別子の仕様](https://www.w3.org/TR/did-core/)では、これについての詳細が説明されています。

### <a name="why-do-we-need-a-did"></a>なぜ DID が必要なのですか。

デジタル信頼では、基本的に、参加者が ID を所有して制御する必要があります。ID の基礎は、識別子です。
集中型識別子ハニーポットに対して毎日大規模なシステム侵害および攻撃が行われる時代において、ID の分散化は、ユーザーと企業にとって重要なセキュリティ上のニーズになりつつあります。
ID を所有および制御する個人間では、検証可能なデータと証明を交換できます。 分散化資格情報環境を使用すると、現在手動で労力を要する多くのビジネス プロセスを自動化できます。

### <a name="what-is-a-verifiable-credential"></a>検証可能な資格情報とは何ですか。 

資格情報は、毎日の生活の一部です。運転免許証は自動車を運転できることを主張するために使用され、学位は教育レベルを主張するために使用でき、政府発行のパスポートは国家間の移動を可能にします。 Verifiable Credentials は、Web 上でこれらの種類の資格情報を、暗号的に安全で、プライバシーを尊重し、マシンによる検証が可能な方法で表現するためのメカニズムを提供します。 [W3C の検証可能な資格情報の仕様](https://www.w3.org/TR/vc-data-model//)では、これについて詳細に説明しています。


## <a name="conceptual-questions"></a>概念に関する質問

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>ユーザーが電話を紛失した場合はどうなりますか。 ID を復旧することはできますか。

ユーザーに復旧メカニズムを提供する方法は複数あり、それぞれに独自のトレードオフがあります。 現在はオプションを評価しており、ユーザーのプライバシーと自己裁量権を尊重しつつ、便利で安全な復旧のアプローチを設計しているところです。

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>ユーザーが発行者または検証者からの要求を信頼するにはどうすればよいですか。 DID が組織にとって本当の DID であることは、どうすればわかりますか。

DID をよく知られている既存のシステム (ドメイン名) に接続するために、[分散化 ID 基盤の既知の DID 構成の仕様](https://identity.foundation/.well-known/resources/did-configuration/)を実装しました。 Azure Active Directory Verifiable Credentials を使用して作成された各 DID には、DID ドキュメントにエンコードされるルート ドメイン名を含めるオプションがあります。 詳細については、「[ドメインを分散化 ID にリンクする](how-to-dnsbind.md)」というタイトルの記事を参照してください。  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Verifiable Credentials のプレビューで DID の方法として ION が使用されており、そのためにビットコインによって分散化公開キー インフラストラクチャが提供されるのはなぜですか。

ION は、ビットコイン上で実行される、分散型で許可なしのスケーラブルな分散化識別子レイヤー 2 ネットワークです。 特別な暗号資産トークン、信頼された検証コントロール、または集中型の合意メカニズムを含まずに、スケーラビリティを実現します。 基本レイヤー 1 サブストレートにビットコインを使用するのは、時系列イベント レコード システムに対して高いレベルの不変性を提供する分散ネットワークの強度が理由です。

## <a name="using-the-preview"></a>プレビューの使用

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Verifiable Credentials のプレビューで NodeJS を使用する必要があるのはなぜですか。 他のプログラミング言語に対応する計画はありますか。 

Microsoft が NodeJS を選択したのは、それがアプリケーション開発者に広く使用されているプラットフォームであるためです。 開発者が資格情報の発行と検証を行うことができる Rest API をリリースする予定です。 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>プレビューで使用されているコードに、オープンソースになっているものがありますか。

はい、ご利用いただけます。 次のリポジトリは、Microsoft サービスのオープンソース コンポーネントです。

1. [SideTree (GitHub)](https://github.com/decentralized-identity/sidetree)
2. [Node 用 VC SDK (GitHub)](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [分散化 ID ウォレットを構築するための Android SDK (GitHub)](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [分散化 ID ウォレットを構築するための iOS SDK (GitHub)](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>ライセンス要件は何ですか?

Verifiable Credentials のプレビューを使用するには、Azure AD P2 ライセンスが必要です。 このサービスの価格は使用量に基づいて課金される予定であるため、これは一時的な要件です。 


## <a name="next-steps"></a>次の手順

- [Azure Active Directory の検証可能な資格情報をカスタマイズする方法](credential-design.md)
