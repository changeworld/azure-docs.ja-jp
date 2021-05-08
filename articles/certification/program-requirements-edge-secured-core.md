---
title: Edge Secured-Core 認定の要件
description: Edge Secured-Core 認定プログラムの要件
author: cbroad
ms.author: cbroad
ms.topic: conceptual
ms.date: 03/15/2021
ms.custom: Edge Secured-core Certification Requirements
ms.service: certification
ms.openlocfilehash: 5bb02f939bb63fd1c6365fd4570996f09119e958
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166906"
---
# <a name="azure-certified-device---edge-secured-core-preview"></a>Azure Certified Device - Edge Secured-Core (プレビュー) #

## <a name="edge-secured-core-certification-requirements"></a>Edge Secured-Core 認定の要件 ##

このドキュメントでは、認定を完了するために、満たす必要があるデバイス固有の機能と要件について概説し、Edge Secured-Core ラベルを使用して Azure IoT デバイス カタログにデバイスを一覧表示します。

### <a name="program-purpose"></a>プログラムの目的 ###
Edge Secured-Core は、Linux や Windows 10 IoT など、完全なオペレーティング システムを実行する IoT デバイス向けの Azure Certified Device プログラムの増分認定です。このプログラムにより、デバイス パートナーは、追加の一連のセキュリティ基準を満たすことで、デバイスを差別化できます。 この基準を満たすデバイスは、次のことが可能になります。
1. ハードウェアベースのデバイス ID 
2. システムの整合性を適用できる 
3. 最新の状態を維持し、リモートで管理できる
4. 保存データを保護する
5. 転送中のデータを保護する
6. 組み込みのセキュリティ エージェントとセキュリティ強化
### <a name="requirements"></a>必要条件 ###

---
|名前|SecuredCore.Built-in.Security|
|:---|:---|
|Status|必須|
|説明|テストの目的は、Azure Defender for IoT にデータを送信して、セキュリティ情報とイベントをデバイスでレポートできることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証 |デバイスでは、セキュリティ ログとアラートを生成する必要があります。 デバイスでは、Azure Security Center にメッセージを記録してアラートを送信します。<ol><li>GitHub からセキュリティ エージェントをダウンロードしてデプロイします。</li><li>Azure Defender for IoT からのアラート メッセージを検証します。</li></ol>|
|リソース|[Azure Docs IoT Defender for IoT](../defender-for-iot/how-to-configure-agent-based-solution.md)|

---
|名前|SecuredCore.Encryption.Storage|
|:---|:---|
|Status|必須|
|説明|テストの目的は、機密データを不揮発性ストレージで暗号化できることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|ストレージの暗号化が有効になっていることを確認するために、ツールセットでデバイスを検証します。既定のアルゴリズムは、キーの長さが 128 ビット以上の XTS-AES-AES です。|
|リソース||

---
|名前|SecuredCore.Hardware.SecureEnclave|
|:---|:---|
|Status|省略可能|
|説明|テストの目的は、セキュリティで保護されたエンクレーブが存在することと、セキュリティで保護されたエージェントからエンクレーブにアクセスできることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|セキュリティで保護されたエンクレーブと Azure Security Agent が通信できることを確認するために、ツールセットでデバイスを検証します。|
|リソース|https://github.com/openenclave/openenclave/blob/master/samples/BuildSamplesLinux.md|

---
|名前|SecuredCore.Hardware.Identity|
|:---|:---|
|Status|必須|
|説明|テストの目的は、デバイスの識別がハードウェアでルート化されていることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|デバイスに TPM が存在し、TPM 保証キーを使用して IoT Hub でプロビジョニングできることを確認するために、ツールセットでデバイスを検証します。|
|リソース|[DPS による自動プロビジョニングを設定する](../iot-dps/quick-setup-auto-provision.md)|

---
|名前|SecuredCore.Update|
|:---|:---|
|Status|必須|
|説明|テストの目的は、デバイスがファームウェアとソフトウェアを受信して更新できることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|パートナーは、Microsoft Update、Azure Device Update、またはその他の承認されたサービスを通じて、デバイスに更新プログラムを送信できたことを確認します。|
|リソース|[Device Update for IoT Hub](../iot-hub-device-update/index.yml)|

---
|名前|SecuredCore.Manageability.Configuration|
|:---|:---|
|Status|必須|
|説明|テストの目的は、デバイスがリモート セキュリティ管理をサポートしていることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|リモート管理、特にセキュリティ構成の機能がデバイスでサポートされていることを確認するために、ツールセットでデバイスを検証します。 状態は、IoT Hub/Azure Defender for IoT にレポートされます。|
|リソース||

---
|名前|SecuredCore.Manageability.Reset|
|:---|:---|
|Status|必須|
|説明|テストの目的は、2 つのユース ケース: a) リセット (ユーザー データの削除、ユーザー構成の削除) を実行できること、b) 更新プログラムが問題の原因となっている場合に、前回の正常起動時の状態にデバイスを復元できることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|この機能がデバイスでサポートされていることを確認するために、ツールセットと送信されたドキュメントを組み合わせてデバイスを検証します。 デバイスの製造元は、これらの機能を実装して、リモート リセットまたはローカル リセットのみをサポートするかどうかを判断できます。|
|リソース||

---
|名前|SecuredCore.Updates.Duration|
|:---|:---|
|Status|必須|
|説明|このポリシーの目的は、デバイスがセキュリティで保護さされていることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|マニュアル|
|検証|送信日から 60 か月間デバイスを最新の状態に保つには、デバイスで認定された送信からのコミットメントが必要になります。 購入者とデバイス自体が何らかの方法で使用できる仕様では、ソフトウェアを更新する期間を示す必要があります。|
|リソース||

---
|名前|SecuredCore.Policy.Vuln.Disclosure|
|:---|:---|
|Status|必須|
|説明|このポリシーの目的は、製品の脆弱性に関するレポートを収集して配布するメカニズムがあることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|マニュアル|
|検証|認定デバイスの脆弱性レポートの送受信のプロセスに関するドキュメントを確認します。|
|リソース||

---
|名前|SecuredCore.Policy.Vuln.Fixes|
|:---|:---|
|Status|必須|
|説明|このポリシーの目的は、高/重大な脆弱性 (CVSS 3.0 を使用) が、修正プログラムが利用可能になってから 180 日以内に解決されることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|マニュアル|
|検証|認定デバイスの脆弱性レポートの送受信のプロセスに関するドキュメントを確認します。|
|リソース||


---
|名前|SecuredCore.Encryption.TLS|
|:---|:---|
|Status|必須|
|説明|テストの目的は、必要な TLS バージョンと暗号スイートのサポートを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
検証|TLS バージョン 1.2 以上と、それ以降に必要となる TLS 暗号スイートがデバイスでサポートされていることを確認するために、ツールセットでデバイスを検証します。<ul><li>TLS_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_RSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256</li><li>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256</li><li>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256</li></ul>|
|リソース| [IoT Hub の TLS サポート](../iot-hub/iot-hub-tls-support.md) <br /> [Windows 10 の TLS 暗号スイート](https://docs.microsoft.com/windows/win32/secauthn/tls-cipher-suites-in-windows-10-v1903) |

---
|名前|SecuredCore.Protection.SignedUpdates|
|:---|:---|
|Status|必須|
|説明|テストの目的は、更新プログラムに署名が必要であることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|オペレーティング システム、ドライバー、アプリケーション ソフトウェア、ライブラリ、パッケージ、ファームウェアの更新プログラムが適切に署名および検証されない限り、これらの更新プログラムが適用されないことを確認するために、ツールセットでデバイスを検証します。
|リソース||

---
|名前|SecuredCore.Firmware.SecureBoot|
|:---|:---|
|Status|必須|
|説明|テストの目的は、デバイスのブートの整合性を検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|デバイスがブートされるたびに、ファームウェアとカーネルの署名が検証されることを確認するために、ツールセットでデバイスを検証します。 <ul><li>UEFI: セキュア ブートが有効</li><li>Uboot: 検証されたブートが有効</li></ul>|
|リソース||

---
|名前|SecuredCore.Protection.CodeIntegrity|
|:---|:---|
|Status|必須|
|説明|このテストの目的は、コードの整合性がこのデバイスで使用可能であることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|コードの整合性が有効になっていることを確認するために、ツールセットでデバイスを検証します。 </br> Windows: HVCI </br> Linux: dm-verity および IMA|
|リソース||

---
|名前|SecuredCore.Protection.NetworkServices|
|:---|:---|
|Status|必須|
|説明|テストの目的は、ネットワークからの入力を受け付けるアプリケーションが、昇格された特権で実行されていないことを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|ネットワーク接続を受け入れるサービスが SYSTEM またはルートの特権で実行されていないことを確認するために、ツールセットでデバイスを検証します。|
|リソース||

---
|名前|SecuredCore.Protection.Baselines|
|:---|:---|
|Status|必須|
|説明|テストの目的は、システムがベースライン セキュリティ構成に準拠していることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|Defender IOT システム構成のベンチマークが実行されていることを確認するために、ツールセットでデバイスを検証します。|
|リソース| https://techcommunity.microsoft.com/t5/microsoft-security-baselines/bg-p/Microsoft-Security-Baselines <br> https://www.cisecurity.org/cis-benchmarks/ |

---
|名前|SecuredCore.Firmware.Protection|
|:---|:---|
|Status|必須|
|説明|テストの目的は、ファームウェアのセキュリティの脅威に対する十分な軽減策がデバイスにあることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|次のいずれかの方法を使用して、ファームウェアのセキュリティの脅威から保護されていることを確認するために、ツールセットでデバイスを検証します。 <ul><li>DRTM + UEFI 管理モード軽減策</li><li>DRTM + UEFI 管理モード強化</li><li>SRTM + ランタイム ファームウェアを強化する承認済みの FW</li></ul> |
|リソース| https://trustedcomputinggroup.org/ |

---
|名前|SecuredCore.Firmware.Attestation|
|:---|:---|
|Status|必須|
|説明|テストの目的は、Microsoft Azure Attestation サービスに対してリモートで構成証明できることを確認することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|プラットフォームのブート ログとブート アクティビティの測定値を収集して、Microsoft Azure Attestation サービスに対してリモートで構成証明できることを確認するために、ツールセットでデバイスを検証します。|
|リソース| [Microsoft Azure Attestation](../attestation/index.yml) |

---
|名前|SecuredCore.Hardware.MemoryProtection|
|:---|:---|
|Status|必須|
|説明|テストの目的は、外部からアクセスできるポートで DMA が有効になっていないことを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|DMA 対応の外部ポートがデバイスに存在する場合、それらポートに対して IOMMU または SMMU が有効で構成されていることをツールセットで検証します。|
|リソース||

---
|名前|SecuredCore.Protection.Debug|
|:---|:---|
|Status|必須|
|説明|テストの目的は、デバイスのデバッグ機能が無効になっていることを検証することです。|
|ターゲットの可用性|2021|
|適用対象|任意のデバイス|
|OS|非依存|
|検証の種類|手動/ツール|
|検証|認可の有効化がデバッグ機能で必要であることを確認するために、ツールセットでデバイスを検証します。|
|リソース||
