---
title: Azure IoT Central 管理者ガイド
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central の管理者ロールの概要を示します。
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105110493"
---
# <a name="iot-central-administrator-guide"></a>IoT Central 管理者ガイド

*この記事は管理者向けです。*

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 このガイドは、IoT Central アプリケーションを管理する管理者を対象としています。

IoT Central では、管理者は次のことを行います。

- アプリケーションのユーザーとロールを管理します。
- デバイス認証などのセキュリティを管理します。
- アプリケーション設定を構成します。
- アプリケーションをアップグレードします。
- アプリケーションをエクスポートして共有します。
- アプリケーションの正常性を監視します。

## <a name="users-and-roles"></a>ユーザーと役割

IoT Central は、ロールベースのアクセス制御システムを使用して、アプリケーション内でユーザーのアクセス許可を管理します。 IoT Central には、管理者、ソリューション ビルダー、およびオペレーター用の 3 つの組み込みロールがあります。 管理者は、特定のアクセス許可セットを持つカスタム ロールを作成できます。 管理者は、ユーザーをアプリケーションに追加し、ロールに割り当てる役割を担います。

詳細については、「[IoT Central アプリケーションでユーザーとロールを管理する](howto-manage-users-roles.md)」を参照してください。

## <a name="application-security"></a>アプリケーションのセキュリティ

IoT Central アプリケーションに接続するデバイスは、通常、資格情報として x.509 証明書または Shared Access Signature (SAS) を使用します。 管理者は、デバイスの資格情報の派生元であるグループ証明書またはキーを管理します。

詳細については、「[x.509 グループの登録](concepts-get-connected.md#x509-group-enrollment)」、「[SAS グループの登録](concepts-get-connected.md#sas-group-enrollment)」、および「[x.509 デバイス証明書の登録方法](how-to-roll-x509-certificates.md)」を参照してください。

管理者は、クライアント アプリケーションが IoT Central アプリケーションでの認証に使用する API トークンを作成および管理することもできます。 クライアント アプリケーションは、REST API を使用して IoT Central を操作します。

## <a name="configure-an-application"></a>アプリケーションの構成

管理者は、IoT Central アプリケーションの動作と外観を構成できます。 詳細については、次を参照してください。

- [アプリケーションの名前と URL を変更する](howto-administer.md#change-application-name-and-url)
- [UI のカスタマイズ](howto-customize-ui.md)
- [アプリケーションを別の価格プランに移動する](howto-view-bill.md)
- [ファイルのアップロードを構成する](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>アプリケーションのエクスポート

管理者は以下を行うことができます。

- アプリケーションの 1 つの複製が必要なだけの場合は、アプリケーションのコピーを作成します。 たとえば、テスト用に複製したコピーが必要になる場合があります。
- 複数のコピーを作成する予定がある場合は、既存のアプリケーションからアプリケーション テンプレートを作成します。

詳細については、「[Azure IoT Central アプリケーションをエクスポートする](howto-use-app-templates.md)」を参照してください。

## <a name="migrate-to-a-new-version"></a>新しいバージョンに移行する

管理者は、アプリケーションを新しいバージョンに移行できます。 現在、新しく作成されたアプリケーションは V3 アプリケーションです。 管理者が V2 アプリケーションを V3 アプリケーションに移行することが必要になる場合があります。

詳細については、 「[V2 IoT Central アプリケーションの V3 への移行](howto-migrate.md)」を参照してください。

## <a name="monitor-application-health"></a>アプリケーションの正常性を監視する

管理者は IoT Central メトリックを使用して、接続されているデバイスの正常性と、実行中のデータのエクスポートの正常性を評価できます。

メトリックを表示するために、管理者は Azure portal、REST API、PowerShell、または Azure CLI クエリのグラフを使用できます。

詳細については、「[IoT Central アプリケーションの全体的な正常性状態を監視する](howto-monitor-application-health.md)」を参照してください。

## <a name="tools"></a>ツール

管理者として使用するツールの多くは、各 IoT Central アプリケーションの **[管理]** セクションで使用できます。 次のツールを使用して、いくつかの管理タスクを実行することもできます。

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>次のステップ

ここまでで、Azure IoT Central アプリケーションを管理する方法について学習しました。次は、Azure IoT Central で[ユーザーとロールを管理する](howto-manage-users-roles.md)方法について学習することをお勧めします。
