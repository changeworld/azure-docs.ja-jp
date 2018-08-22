---
title: Azure Stack の物理デバイスの監査
description: Azure Stack における物理デバイスのアクセスの監査を統合する方法について説明します。
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 08/01/2018
ms.author: patricka
ms.reviewer: fiseraci
keywords: ''
ms.openlocfilehash: 459cdf4e1a70ee02d818dd6abe101e4fc3475b68
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036684"
---
# <a name="azure-stack-datacenter-integration---physical-device-auditing"></a>Azure Stack とデータセンターの統合 - 物理デバイスの監査

ベースボード管理コントローラー (BMC) やネットワーク スイッチなど、Azure Stack 内のすべての物理デバイスは監査ログを出力します。そうしたデバイスから出力された監査ログは、既にある全体的な監査ソリューションに統合する必要があります。 デバイスには Azure Stack の OEM ハードウェア ベンダーごとの違いがあるため、監査の統合に関するドキュメントについては、ご利用のベンダーに問い合わせてください。 以降の各セクションに記載したのは、Azure Stack の物理デバイスの監査に関する一般的な情報です。  

## <a name="physical-device-access-auditing"></a>物理デバイスのアクセスの監査

Azure Stack 内のすべての物理デバイスは、TACACS または RADIUS の使用をサポートします。 これには、ベースボード管理コント ローラー (BMC) およびネットワーク スイッチへのアクセスが含まれます。

Azure Stack ソリューションには、RADIUS と TACACS のいずれも組み込まれていません。 ただしこのソリューションは、市場で入手可能な既存の RADIUS または TACACS ソリューションの使用をサポートすることが検証済みです。

RADIUS に対してのみ、MSCHAPv2 が検証されました。 これは、RADIUS を使用する最も安全な実装です。
Azure Stack ソリューションに含まれているデバイスで TACAS または RADIUS を有効にするには、OEM ハードウェア ベンダーに問い合わせてください。

## <a name="syslog-forwarding-for-network-devices"></a>ネットワーク デバイスの Syslog 転送

Azure Stack 内のすべての物理ネットワーク デバイスは、Syslog メッセージをサポートしています。 Azure Stack ソリューションには、Syslog サーバーは含まれていません。 ただしデバイスは、市場で入手可能な既存の Syslog ソリューションへのメッセージ送信をサポートすることが検証済みです。

Syslog の送信先アドレスは、デプロイ時に収集される省略可能なパラメーターですが、デプロイ後に追加することもできます。 お使いのネットワーク デバイスで Syslog 転送を構成するにあたっては、ご利用の OEM ハードウェア ベンダーに相談してください。

## <a name="next-steps"></a>次の手順

[サービス ポリシー](azure-stack-servicing-policy.md)
