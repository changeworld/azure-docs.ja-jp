---
title: Azure Security Center for IoT のセキュリティ エージェントのアーキテクチャについて | Microsoft Docs
description: Azure Security Center for IoT サービスで使用されるエージェントのセキュリティ エージェントのアーキテクチャについて説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: 998aeab197931a75579fc39b28e3a248b85fc57b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "68596912"
---
# <a name="security-agent-reference-architecture"></a>セキュリティ エージェントの参照アーキテクチャ

Azure Security Center for IoT は、IoT Hub 経由でセキュリティ データをログ記録、処理、集計、および送信するセキュリティ エージェントのための参照アーキテクチャを提供します。

セキュリティ エージェントは、制約のある IoT 環境で機能するように設計されており、消費するリソースと比較した場合、提供する価値という観点で高度にカスタマイズ可能です。

セキュリティ エージェントは、次の機能をサポートしています。

- 基になるオペレーティング システム (Linux、Windows) から未処理のセキュリティ イベントを収集する。 使用可能なセキュリティ データ コレクターの詳細については、[Azure Security Center for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- 未加工のセキュリティ イベントを収集して、IoT Hub を介して送信されるメッセージにする。

- 既存のデバイス ID、または専用のモジュール ID で認証する。 詳細については、[セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

- **azureiotsecurity** モジュール ツインを使用してリモートで構成する。 詳細については、[Azure Security Center for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

Azure Security Center for IoT セキュリティ エージェントはオープン ソース プロジェクトとして開発されており、GitHub から入手できます。 

- [Azure Security Center for IoT の C ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-C) 
- [Azure Security Center for IoT の C# ベースのエージェント](https://github.com/Azure/Azure-IoT-Security-Agent-CS)

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

Azure Security Center for IoT では、32 ビットと 64 ビットの Windows のエージェントには異なるインストーラー、32 ビットと 64 ビットの Linux のエージェントには同じインストーラーが用意されています。 次の表に従って、ご使用のデバイスごとに適切なエージェント インストーラーがあることを確認してください。

| Architecture | Linux | Windows |    詳細|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 ビット  | C  | C#  ||
| 64 ビット  | C# または C           | C#      | デバイス リソースがさらに制限されているか少ないデバイスについては、C エージェントを使用することをお勧めします。|
|

## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center for IoT セキュリティ エージェントのアーキテクチャと利用できるインストーラーについて学習しました。

Azure Security Center for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)を理解する
- [セキュリティ エージェント](how-to-deploy-agent.md)を選択してデプロイする
- Azure Security Center for IoT [サービスの前提条件](service-prerequisites.md)を確認する
- [IoT Hub で Azure Security Center for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法を学習する
- [Azure Security Center for IoT に関してよく寄せられる質問](resources-frequently-asked-questions.md)から、サービスについて確認する
