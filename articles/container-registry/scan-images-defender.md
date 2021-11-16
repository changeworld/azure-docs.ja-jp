---
title: Microsoft Defender for Cloud でレジストリ イメージをスキャンする
description: Microsoft Defender for container registries を使用して Azure コンテナー レジストリ内のイメージをスキャンする方法について説明します
ms.topic: article
ms.date: 05/19/2021
ms.openlocfilehash: 0170895ede2be40cc57d9ecfd82806e1ee3a617c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323488"
---
# <a name="scan-registry-images-with-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud でレジストリ イメージをスキャンする

Azure コンテナー レジストリでイメージで脆弱性をスキャンするために、利用可能ないずれかの Azure Marketplace ソリューションを統合することができます。または、Microsoft Defender for Cloud を使用する場合は、必要に応じて、**Microsoft Defender for container registries** をサブスクリプション レベルで有効にすることができます。 

* [Microsoft Defender for container registries](../security-center/defender-for-container-registries-introduction.md) の詳細
* [Microsoft Defender for Cloud でのコンテナーのセキュリティ](../security-center/container-security.md)の詳細

## <a name="registry-operations-by-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud によるレジストリの操作

Microsoft Defender for Cloud では、レジストリにプッシュされたイメージ、レジストリにインポートされたイメージ、または過去 30 日以内にプルされたイメージがスキャンされます。 脆弱性が検出された場合、[推奨される改善策](../security-center/defender-for-container-registries-usage.md#view-and-remediate-findings)が Microsoft Defender for Cloud に表示されます。

 推奨された手順に従ってセキュリティの問題を修復したら、レジストリ内のイメージを置き換えます。 脆弱性が修復されたことを確認するため、Microsoft Defender for Cloud によってイメージが再スキャンされます。 

詳細については、[Microsoft Defender for container registries の使用](../security-center/defender-for-container-registries-usage.md)に関するページを参照してください。

> [!TIP]
> Microsoft Defender for Cloud は、脆弱性スキャンの対象イメージをプルするために、レジストリに対して認証を行います。 レジストリ用に[リソース ログ](monitor-service-reference.md#resource-logs)が収集されている場合は、Microsoft Defender for Cloud によって生成されたレジストリ ログイン イベントとイメージ プル イベントが表示されます。 これらのイベントは、英数字 ID (`b21cb118-5a59-4628-bab0-3c3f0e434cg6` など) に関連付けられています。

## <a name="scanning-a-network-restricted-registry"></a>ネットワーク制限付きレジストリのスキャン

Microsoft Defender for Cloud では、パブリックにアクセス可能なコンテナー レジストリ内のイメージ、またはネットワーク アクセス ルールで保護されているイメージをスキャンできます。 ネットワーク ルールが構成されている場合 (つまり、パブリック レジストリ アクセスを無効にしたり、IP アクセス ルールを構成したり、プライベート エンドポイントを作成したりする場合)、[**信頼された Microsoft サービス**](allow-access-trusted-services.md)がレジストリにアクセスできるようにネットワーク設定を有効にする必要があります。 新しいコンテナー レジストリでは、この設定は既定で有効になっています。

## <a name="next-steps"></a>次のステップ

* [信頼されたサービス](allow-access-trusted-services.md)によるレジストリ アクセスの詳細について説明します。
* 仮想ネットワーク内のプライベート エンドポイントを使用してレジストリへのアクセスを制限するには、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。
* レジストリのファイアウォール規則を設定するには、「[パブリック IP ネットワーク ルールを構成する](container-registry-access-selected-networks.md)」を参照してください。
