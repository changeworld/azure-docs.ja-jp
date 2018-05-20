---
title: Azure Payment Processing Blueprint - 概略
description: Azure Payment Processing Blueprint - お客様の責任マトリックス (概要)
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>PCI DSS 要件 - 概略

Payment Card Industry Data Security Standard (PCI DSS) は、カード所有者のデータのセキュリティの推奨と強化を行い、一貫性のあるデータ セキュリティ基準の幅広い対応をグローバルに促進するために開発されました。 PCI DSS は、アカウント データの保護を目的とする技術要件と運用要件のベースラインです。 PCI DSS は、ペイメント カードの処理に関係するすべてのエンティティ (業者、処理者、取得者、発行者、およびサービス プロバイダー) に適用されます。 PCI DSS は、カード所有者のデータ (CHD) または機密性の高い認証データ (SAD) を保存、処理、または送信するその他すべてのエンティティにも適用されます。 12 PCI DSS 要件の概略を次に示します。

> [!NOTE]
> これらの要件は、[PCI データ セキュリティ基準 (DSS) バージョン 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss)の一部として [Payment Card Industry (PCI) Security Standards Council](https://www.pcisecuritystandards.org/pci_security/) によって定義されています。 各要件のテスト手順およびガイダンスについては、PCI DSS をご覧ください。

|   |   |
|---|---|
| **セキュリティで保護された<br/>ネットワークとシステムを構築して維持する** | 1.[カード所有者のデータを保護するファイアウォール構成をインストールして維持する](pci-dss-requirement-1-firewall.md)<br/><br/> 2.[システムのパスワードとその他のセキュリティ パラメーターで、ベンダーが指定した既定値を使用しない](pci-dss-requirement-2-password.md) |  
| **カード所有者のデータを保護する** | 手順 3.[保存されたカード所有者のデータを保護する](pci-dss-requirement-3-chd.md)<br/><br/> 4.[オープンなパブリック ネットワークを経由するカード所有者のデータ転送を暗号化する](pci-dss-requirement-4-encryption.md) |
| **脆弱性管理プログラムを<br/>維持する** | 5.[すべてのシステムをマルウェアから保護し、ウイルス対策ソフトウェアまたはアプリケーションを定期的に更新する](pci-dss-requirement-5-malware.md)<br/><br/> 6.[セキュリティで保護されたシステムとアプリケーションを開発して維持する](pci-dss-requirement-6-secure-system.md) |
| **強力なアクセス制御手段を<br/>実装する** | 7.[業務上の知る必要によってカード所有者データへのアクセスを制限する](pci-dss-requirement-7-access.md)<br/><br/> 8.[システム コンポーネントへのアクセスを識別して認証する](pci-dss-requirement-8-identity.md) <br/><br/> 9.[カード所有者データへの物理的なアクセスを制限する](pci-dss-requirement-9-physical-access.md) |
| **ネットワークを定期的に<br/>監視してテストする** | 10.[ネットワーク リソースとカード所有者のデータへのすべてのアクセスを追跡して監視する](pci-dss-requirement-10-monitoring.md) <br/><br/> 11.[セキュリティ システムとプロセスを定期的にテストする](pci-dss-requirement-11-testing.md) |
| **情報セキュリティ ポリシーを<br/>維持する** | 12.[すべての職員が情報セキュリティに注目するポリシーを維持する](pci-dss-requirement-12-policy.md) |

