---
title: Azure Defender を使用してレジストリ イメージをスキャンする
description: コンテナー レジストリ用の Azure Defender を使用して Azure コンテナー レジストリ内のイメージをスキャンする方法について説明します
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: d00e23ffa7e3bd2fc1084ba6253274d14031d624
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553067"
---
# <a name="scan-registry-images-with-azure-defender"></a>Azure Defender を使用してレジストリ イメージをスキャンする

Azure コンテナー レジストリでイメージで脆弱性をスキャンするために、利用可能ないずれかの Azure Marketplace ソリューションを統合することができます。または、Azure Security Center を使用する場合は、必要に応じて、**コンテナー レジストリに対して Azure Defender** をサブスクリプション レベルで有効にすることができます。 

* [コンテナー レジストリ用 Azure Defender](../security-center/defender-for-container-registries-introduction.md) の詳細
* [Azure Security Center でのコンテナーのセキュリティ](../security-center/container-security.md)の詳細

## <a name="registry-operations-by-azure-defender"></a>Azure Defender によるレジストリ操作

Azure Defender では、レジストリにプッシュされたイメージ、レジストリにインポートされたイメージ、または過去 30 日以内にプルされたイメージがスキャンされます。 脆弱性が検出された場合、[推奨される改善策](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings)が Azure Security Center に表示されます。

 推奨された手順に従ってセキュリティの問題を修復したら、レジストリ内のイメージを置き換えます。 脆弱性が修復されたことを確認するため、Azure Defender によってイメージが再スキャンされます。 

詳細については、[コンテナー レジストリ用 Azure Defender の使用](../security-center/defender-for-container-registries-usage.md)に関するページを参照してください。

> [!TIP]
> Azure Defender では、脆弱性スキャンのためにイメージをプルするために、レジストリを使用して認証が行われます。 レジストリ用に[リソース ログ](monitor-service-reference.md#resource-logs)が収集されている場合は、Azure Defender によって生成されたレジストリ ログイン イベントとイメージ プル イベントが表示されます。 これらのイベントは、英数字 ID (`b21cb118-5a59-4628-bab0-3c3f0e434cg6` など) に関連付けられています。

## <a name="scanning-a-network-restricted-registry"></a>ネットワーク制限付きレジストリのスキャン

Azure Defender では、パブリックにアクセス可能なコンテナー レジストリ内のイメージ、またはネットワーク アクセス ルールで保護されているイメージをスキャンできます。 ネットワーク ルールが構成されている場合 (つまり、パブリック レジストリ アクセスを無効にしたり、IP アクセス ルールを構成したり、プライベート エンドポイントを作成したりする場合)、[**信頼された Microsoft サービス**](allow-access-trusted-services.md)がレジストリにアクセスできるようにネットワーク設定を有効にする必要があります。 新しいコンテナー レジストリでは、この設定は既定で有効になっています。

## <a name="next-steps"></a>次のステップ

* [信頼されたサービス](allow-access-trusted-services.md)によるレジストリ アクセスの詳細について説明します。
* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* レジストリのファイアウォール規則を設定するには、「[パブリック IP ネットワーク ルールを構成する](container-registry-access-selected-networks.md)」を参照してください。
