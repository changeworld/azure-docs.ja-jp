---
title: Key Vault 証明書での SSL 終了
description: HTTPS が有効なリスナーにアタッチされているサーバー証明書の Key Vault と Azure Application Gateway を統合する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 37707d56caabf0ae8b0020eb8714245a27501ea6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696492"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>Key Vault 証明書での SSL 終了

[Azure Key Vault](../key-vault/key-vault-whatis.md) はプラットフォームで管理されるシークレット ストアです。シークレット、キー、SSL 証明書を保護するために使用できます。 Application Gateway では、HTTPS が有効なリスナーにアタッチされているサーバー証明書用の Key Vault との統合 (パブリック プレビュー段階) をサポートします。 このサポートは、Application Gateway の v2 SKU に制限されます。

> [!IMPORTANT]
> Application Gateway と Key Vault の統合は、現在パブリック プレビューの段階です。 このプレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。

このパブリック プレビューでの SSL 終了には次の 2 つのモデルがあります。

- リスナーにアタッチされている SSL 証明書を明示的に提供できます。 これは、SSL 終了のために SSL 証明書を Application Gateway に渡す従来のモデルです。
- HTTPS が有効なリスナーの作成時に、必要に応じて、既存の Key Vault 証明書またはシークレットへの参照を指定できます。

Key Vault の統合には、次のような多くのメリットがあります。

- セキュリティの強化 (SSL 証明書がアプリケーション開発チームによって直接処理されないため)。 Key Vault との統合により、別のセキュリティ チームが、プロビジョニング、ライフサイクルの制御、Key Vault に保存されている証明書にアクセスする Application Gateway を選択するための適切なアクセス権の付与を行うことができます。
- Key Vault への既存の証明書のインポートや、任意の信頼できる Key Vault パートナーによる Key Vault API を使用した新しい証明書の作成と管理に対するサポート。
- Key Vault に保存されている証明書の自動更新のサポート。

Application Gateway で現在サポートされているのは、ソフトウェアで検証された証明書のみです。 ハードウェア セキュリティ モジュール (HSM) で検証された証明書はサポートされません。 Key Vault 証明書を使用するように Application Gateway を構成すると、そのインスタンスは Key Vault から証明書を取得し、SSL 終了のためにその証明書をローカルにインストールします。 また、インスタンスは Key Vault を 24 時間間隔で定期的にポーリングし、証明書の更新されたバージョン (存在する場合) を取得します。 更新された証明書が検出されると、HTTPS リスナーに現在関連付けられている SSL 証明書が自動的にローテーションされます。

## <a name="how-it-works"></a>動作のしくみ

Key Vault との統合には、3 つの手順の構成プロセスが必要です。

1. **ユーザー割り当てマネージド ID を作成する**

   Application Gateway がユーザーに代わって Key Vault から証明書を取得するために使用する、ユーザー割り当てマネージド ID を作成するか、既存の ID を使用する必要があります。 詳細については、「[Azure リソースのマネージド ID とは](../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。 この手順により、ID を作成するために使用されるサブスクリプションによって信頼される Azure AD テナントに、新しい ID が作成されます。
1. **Key Vault を構成する**

   次に、Application Gateway を介して実行中のアプリケーションが使用する Key Vault に、証明書をインポートするか新規作成する必要があります。 この手順では、パスワードのない Base 64 でエンコードされた PFX ファイルとして保存されている Key Vault シークレットを使用することもできます。 Key Vault では証明書の種類のオブジェクトに自動更新機能を使用できるため、証明書の種類を使用することを推奨します。 証明書またはシークレットを作成したら、Key Vault でアクセス ポリシーを定義し、その ID がシークレットをフェッチするためのアクセスを*取得*できるようにする必要があります。

1. **Application Gateway を構成する**

   前の 2 つの手順が完了したら、ユーザー割り当てマネージド ID を使用するように Application Gateway をプロビジョニングするか、既存の Application Gateway を変更することができます。 また、Key Vault の証明書またはシークレットの ID の完全な URI をポイントするように、HTTP リスナーの SSL 証明書を構成できます。

![Key Vault 証明書](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>次の手順

[Azure PowerShell で Key Vault 証明書を使用して SSL 終了を構成する](configure-keyvault-ps.md)