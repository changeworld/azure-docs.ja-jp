---
title: Azure Blockchain Tokens とは
description: Azure Blockchain Tokens は、トークンの発行と管理を行うためのサービスとしてのプラットフォーム (PaaS) です。
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: e7c106ffee8ea06c7c0ce738aa8be0aba96a38ed
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75387175"
---
# <a name="what-is-azure-blockchain-tokens"></a>Azure Blockchain Tokens とは何ですか?

[!INCLUDE [Preview note](./includes/preview.md)]

Azure Blockchain Tokens は、Azure でのブロックチェーン台帳全体で標準化されたトークンの発行と管理を行うためのサービスとしてのプラットフォーム (PaaS) です。

Azure Blockchain Tokens を使用すると、事前に構築されたトークン テンプレートを使用して、ブロックチェーン ソリューション用の標準化されたトークンを作成できます。 サービスを使用して、独自のトークン テンプレートを作成することもできます。 作成したら、Azure Blockchain Tokens を使用して、ブロックチェーンでトークンに接続して発行します。 発行されると、複数のブロックチェーン ネットワーク全体でトークンを管理できます。

## <a name="templates"></a>テンプレート

Azure Blockchain Tokens を使用して、事前に構築されたトークン テンプレートを選択するか、独自のトークン テンプレートを作成します。 Azure Blockchain Tokens では、サポートされている動作に基づいて独自のトークン テンプレートを作成できるようにするトークン テンプレートの構成可能性をサポートしています。 トークン テンプレートは、最も一般的に使用されるトークンにマップされるため、ほとんどのブロックチェーン ソリューションで使用できます。 テンプレートの使用を開始し、カスタマイズして、ソリューション用のトークンをデプロイすることができます。

Azure Blockchain Tokens テンプレートの詳細については、「[Azure Blockchain Tokens テンプレート](templates.md)」を参照してください。

## <a name="management"></a>管理

Azure Blockchain Tokens では、既存のブロックチェーン ネットワークに接続するための Azure portal 管理と API を提供します。 現時点では、[Azure Blockchain Service](../service/overview.md) または別の Ethereum ファミリ ブロックチェーンに接続できます。

1 つまたは複数のブロックチェーン ネットワークに接続した後、Azure Blockchain Tokens API を使用して、ブロックチェーン ソリューションで使用するトークンを発行および管理できます。 API を使用して、トークン管理をビジネス アプリケーションとロジックに統合できます。 たとえば、ブロックチェーンでトークンを直接管理するのではなく、REST API を使用してトークンを管理できます。

## <a name="blockchains-and-accounts"></a>ブロックチェーンとアカウント

Azure Blockchain Tokens では、接続されたブロックチェーン ネットワークに新しいグループと新しいブロックチェーン アカウントを作成するための Azure portal 管理と API を提供します。 接続されたネットワーク上に直接新しいアカウントを作成することができ、Azure Blockchain Tokens でユーザーに代わってお客様のアカウントの秘密キーを管理します。 グループを使用すると、複数のネットワークからさまざまなブロックチェーン アカウントをグループ化し、グループを使用してアクセスの制御を管理できます。

Azure Blockchain Tokens のアカウント管理の詳細については、「[Azure Blockchain Tokens のアカウント管理](account-management.md)」を参照してください。

## <a name="token-taxonomy-framework"></a>Token Taxonomy Framework

Azure Blockchain Tokens は、Token Taxonomy Framework (TTF) という標準ベースの基盤に基づいて構築されています。 TTF は、[Token Taxonomy Initiative](https://entethalliance.org/participate/token-taxonomy-initiative/) (TTI) トークンの作業グループから作成された成果物のセットです。 TTI 作業グループは、トークンとその動作のビジネス分類を定義します。これは、Ethereum、Quorum、Corda、Hyperledger Fabric を含むすべての主要な台帳にわたって適用できます。 作業グループの目標は、ビジネスの観点からトークンの使用を標準化するフレームワークを作成し、簡素化を推進して、トークン ベースの開発をだれもが行えるようにすることです。 業界でこれらのトークンとその動作をビジネス レベルで定義できるようにすることにより、トークンの詳細な実装は、トークンを操作するビジネス ロジックから抽象化されます。

## <a name="support-and-feedback"></a>サポートとフィードバック

Azure Blockchain の最新情報については、[Azure Blockchain のブログ](https://azure.microsoft.com/blog/topics/blockchain/)をご覧ください。Azure Blockchain エンジニアリング チームからの情報やブロックチェーン サービスの内容に関する最新の情報を把握することができます。

Azure Blockchain Tokens のプレビューにアクセスするには、[Azure Blockchain Tokens チームにお問い合わせください](https://aka.ms/PreviewForm)。

製品に関するフィードバックや新機能のご要望をお寄せいただくには、[ブロックチェーンに関する Azure フィードバック フォーラム](https://aka.ms/blockchainuservoice)でアイデアに投票してください。

## <a name="next-steps"></a>次のステップ

利用できる [Azure Blockchain Tokens テンプレート](templates.md)について詳しく確認します。
