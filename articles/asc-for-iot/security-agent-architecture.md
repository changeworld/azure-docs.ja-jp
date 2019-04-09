---
title: ASC for IoT のセキュリティ エージェントのアーキテクチャについて (プレビュー) | Microsoft Docs
description: ASC for IoT サービスで使用されるエージェントのセキュリティ エージェントのアーキテクチャについて説明します。
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/25/2019
ms.author: mlottner
ms.openlocfilehash: 7c8f256cf91a479c45f21b933efdb6a5e0212796
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541527"
---
# <a name="security-agent-reference-architecture"></a>セキュリティ エージェントの参照アーキテクチャ

> [!IMPORTANT]
> ASC for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


ASC for IoT には、IoT ハブを介してセキュリティ データにログイン、処理、集計、および送信する、セキュリティ エージェントの参照アーキテクチャがあります。

セキュリティ エージェントは、制約のある IoT 環境で機能するように設計されており、消費するリソースと比較した場合、提供する価値という観点で高度にカスタマイズ可能です。

セキュリティ エージェントは、次の IoT ソリューションの機能をサポートしています。

- 基になる OS (Linux、Windows) から未加工のセキュリティ イベントを収集する。 使用可能なセキュリティ データ コレクターの詳細については、[ASC for IoT エージェントの構成](concept-agent-configuration.md)に関するページを参照してください。

- 未加工のセキュリティ イベントを収集して、IoT ハブを介して送信されるメッセージにする。

- 既存のデバイス ID、または専用のモジュール ID で認証する。 詳細については、[セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)に関するページを参照してください。

- **ascforiot** モジュール ツインを使用してリモートで構成する。 詳細については、[ASC for IoT エージェントの構成](concept-agent-configuration.md)に関するページを参照してください。

ASC for IoT セキュリティ エージェントはオープン ソース プロジェクトとして開発されており、GitHub から入手できます。 

- [IoT-ASC-Agent-C](https://github.com/Azure/IoT-ASC-Agent-C) 
- [IoT-ASC-Agent-CS](https://github.com/Azure/IoT-ASC-Agent-CS)

## <a name="agent-supported-platforms"></a>エージェントでサポートされているプラットフォーム

ASC for IoT では、32 ビットと 64 ビットの Windows のエージェントには異なるインストーラー、32 ビットと 64 ビットの Linux のエージェントには同じインストーラーが用意されています。 次の表に従って、ご使用のデバイスごとに適切なエージェント インストーラーがあることを確認してください。

| 32 または 64 ビット | Linux |  Windows |    詳細|
|----------|----------------------------------------------|-------------|-------------------------------------------|
| 32 ビット  | C  | C#  ||
| 64 ビット  | C# または C           | C#      | リソースが少ないデバイスの場合は C エージェントを使用|

## <a name="next-steps"></a>次の手順

この記事では、ASC for IoT セキュリティ エージェントのアーキテクチャと利用できるインストーラーについて学習しました。

ASC for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。


- ASC for IoT の[サービスの要件](service-prerequisites.md)を確認する
- [IoT Hub で ASC for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- クイック スタートを使用して[ソリューションを構成する](quickstart-configure-your-solution.md)
- [セキュリティ エージェントの認証方法](concept-security-agent-authentication-methods.md)を理解する
- [セキュリティ エージェント](select-deploy-agent.md)を選択してデプロイする
- [ASC for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスについて確認する