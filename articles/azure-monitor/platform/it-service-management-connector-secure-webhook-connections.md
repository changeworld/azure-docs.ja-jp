---
title: IT Service Management Connector - Azure Monitor のセキュア エクスポート
description: この記事では、ITSM 製品/サービスを Azure Monitor のセキュア エクスポートに接続して、ITSM 作業項目を一元的に監視および管理する方法について説明します。
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 09/08/2020
ms.openlocfilehash: 64d45861f37e2015b747a4db0feb2d32e68fe893
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427319"
---
# <a name="connect-azure-to-itsm-tools-by-using-secure-export"></a>セキュア エクスポートを使用して Azure を ITSM ツールに接続する

この記事では、セキュア エクスポートを使用して、IT Service Management (ITSM) 製品またはサービスの間の接続を構成する方法について説明します。

セキュア エクスポートは、[IT Service Management Connector (ITSMC)](./itsmc-overview.md) の更新されたバージョンです。 どちらのバージョンでも、Azure Monitor がアラートを送信するときに、ITSM ツールで作業項目を作成できます。 この機能には、メトリック、ログ、およびアクティビティ ログ アラートが含まれます。

ITSMC では、ユーザー名とパスワードの資格情報が使用されます。 セキュア エクスポートでは Azure Active Directory (Azure AD) が使用されるため、認証がより強力です。 Azure AD は、Microsoft のクラウドベースの ID およびアクセス管理サービスです。 ユーザーがサインインしたり、内部または外部のリソースにアクセスしたりするために役立ちます。 ITSM と共に Azure AD を使用すると、外部システムに送信された Azure アラートを識別できます (Azure AD アプリケーション ID を使用)。

> [!NOTE]
> セキュア エクスポートを使用して Azure を ITSM ツールに接続する機能は、プレビュー段階です。

## <a name="secure-export-architecture"></a>セキュア エクスポートのアーキテクチャ

セキュア エクスポートのアーキテクチャには、次の新機能が導入されています。

* **新しいアクション グループ** :アラートは、ITSMC で使用される ITSM アクション グループではなく、セキュア Webhook アクション グループを介して ITSM ツールに送信されます。
* **Azure AD の認証** :認証は、ユーザー名とパスワードの資格情報ではなく Azure AD を通じて行われます。

## <a name="secure-export-data-flow"></a>セキュア エクスポートのデータ フロー

セキュア エクスポートのデータ フローのステップは次のとおりです。

1. Azure Monitor が、セキュア エクスポートを使用するように構成されたアラートを送信します。
2. アラートのペイロードが、セキュア Webhook アクションによって ITSM ツールに送信されます。
3. アラートが ITSM ツールに送られることが承認されているかどうかについて、ITSM アプリケーションが Azure AD で確認します。
4. アラートが承認されている場合、アプリケーションは次のように動作します。
   
   1. ITSM ツールで作業項目 (インシデントなど) を作成します。
   2. 構成項目 (CI) の ID を顧客管理データベース (CMDB) にバインドします。

![ITSM ツールが Azure AD、Azure アラート、およびアクション グループと通信する方法を示す図。](media/it-service-management-connector-secure-webhook-connections/secure-export-diagram.png)

## <a name="benefits-of-secure-export"></a>セキュア エクスポートの利点

統合の主な利点は次のとおりです。

* **認証の強化** :Azure AD は、ITSMC で一般的に発生するタイムアウトを発生させることなく、より安全な認証を提供します。
* **アラートが ITSM ツールで解決される** :メトリック アラートは、"発生" と "解決済み" の状態を実装しています。 条件が満たされると、アラートの状態は "発生" になります。 条件が満たされなくなると、アラートの状態は "解決済み" になります。 ITSMC では、アラートを自動的に解決することはできません。 セキュア エクスポートでは、解決された状態が ITSM ツールに送られるため、自動的に更新されます。
* **[共通アラート スキーマ](./alerts-common-schema.md)** :ITSMC では、アラートのペイロードのスキーマは、アラートの種類によって異なります。 セキュア エクスポートでは、すべてのアラートの種類に共通のスキーマがあります。 この共通スキーマには、すべてのアラートの種類の CI が含まれています。 すべてのアラートの種類で、その CI を CMDB にバインドすることができます。

ITSM Connector ツールの使用を開始するには、次の手順を実行します。

1. アプリを Azure AD に登録する。
2. セキュア Webhook アクション グループを作成します。
3. パートナー環境を構成します。 

セキュア エクスポートは、次の ITSM ツールとの接続をサポートしています。
* [BMC Helix](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#connect-bmc-helix-to-azure-monitor)

## <a name="register-with-azure-active-directory"></a>Azure Active Directory に登録する

アプリケーションを Azure AD に登録するには、次の手順に従います。

1. 「[Microsoft ID プラットフォームにアプリケーションを登録する](../../active-directory/develop/quickstart-register-app.md)」の手順に従います。
2. Azure AD で、 **[アプリケーションの公開]** を選択します。
3. **[アプリケーション ID の URI]** に **[設定]** を選択します。

   [![[アプリケーション ID の URI] を設定するためのオプションのスクリーンショット。](media/it-service-management-connector-secure-webhook-connections/azure-ad.png)](media/it-service-management-connector-secure-webhook-connections/azure-ad-expand.png#lightbox)
4. **[保存]** を選択します。

## <a name="create-a-secure-webhook-action-group"></a>セキュア Webhook アクション グループを作成する

アプリケーションが Azure AD に登録された後、アクション グループでセキュア Webhook アクションを使用することによって、Azure アラートに基づいて ITSM ツールで作業項目を作成できます。

アクション グループでは、Azure アラートのアクションをトリガーする再利用可能なモジュール化された方法が提供されます。 Azure Portal で、アクション グループと、メトリック アラート、アクティビティ ログ アラート、および Azure Log Analytics アラートを使用できます。
アクション グループの詳細については、「[Azure Portal でのアクション グループの作成および管理](./action-groups.md)」を参照してください。

アクションに Webhook を追加するには、セキュア Webhook の手順に従います。

1. [Azure portal](https://portal.azure.com/) で、 **[モニター]** を検索して選択します。 **[モニター]** ウィンドウでは、すべての監視設定とデータが 1 つのビューにまとめられています。
2. **[アラート]**  >  **[アクションの管理]** を選択します。
3. [[アクション グループの追加]](./action-groups.md#create-an-action-group-by-using-the-azure-portal) を選択し、フィールドに入力します。
4. **[アクション グループ名]** ボックスおよび **[短い名前]** ボックスに名前を入力します。 短い名前は、通知がこのグループを使用して送信されるときに長い名前の代わりに使用されます。
5. **[セキュア Webhook]** を選択します。
6. 次の詳細を選択します。
   1. 登録した Azure Active Directory インスタンスのオブジェクト ID を選択します。
   2. URI に、[ITSM ツール環境](https://docs.microsoft.com/azure/azure-monitor/platform/it-service-management-connector-secure-webhook-connections#configure-the-partner-environment)からコピーした Webhook URL を貼り付けます。
   3. **[共通アラート スキーマを有効にする]** に **[はい]** を設定します。 

   次の図は、セキュア Webhook アクションの構成の例を示しています。

   ![セキュア Webhook アクションを示すスクリーンショット。](media/it-service-management-connector-secure-webhook-connections/secure-webhook.png)

## <a name="configure-the-itsm-tool-environment"></a>ITSM ツール環境を構成する

構成には次の 2 つのステップが含まれます。
1. セキュア エクスポート定義の URI を取得します。
2. ITSM ツールのフローに従った定義。

### <a name="connect-bmc-helix-to-azure-monitor"></a>BMC Helix を Azure Monitor に接続する

以降のセクションでは、BMC Helix 製品と Azure のセキュア エクスポートを接続する方法について詳細に説明します。

### <a name="prerequisites"></a>前提条件

次の前提条件が満たされていることを確認します。

* Azure AD が登録されている。
* サポートされているバージョンの BMC Helix Multi-Cloud Service Management (バージョン 19.08 以降) がある。

### <a name="configure-the-bmc-helix-connection"></a>BMC Helix の接続を構成する

1. セキュア エクスポートの URI を取得するために、BMC Helix 環境で次の手順を使用します。

   1. Integration Studio にログインします。
   2. **[Create Incident from Azure Alerts]** フローを探します。
   3. Webhook URL をコピーします。
   
   ![Integration Studio の Webhook URL のスクリーンショット。](media/it-service-management-connector-secure-webhook-connections/bmc-url.png)
   
2. バージョンに応じた手順に従ってください。
   * [Azure Monitor バージョン 20.02 との事前構築済み統合の有効化](https://docs.bmc.com/docs/multicloud/enabling-prebuilt-integration-with-azure-monitor-879728195.html)。
   * [Azure Monitor バージョン 19.11 との事前構築済み統合の有効化](https://docs.bmc.com/docs/multicloudprevious/enabling-prebuilt-integration-with-azure-monitor-904157623.html)。

3. BMC Helix での接続の構成の一部として、統合 BMC インスタンスにアクセスし、次の手順に従います。

   1. **[カタログ]** を選択します。
   2. **[Azure アラート]** を選択します。
   3. **[コネクタ]** を選択します。
   4. **[構成]** を選択します。
   5. **[新しい接続を追加]** 構成を選択します。
   6. 構成セクションの情報を入力します。
      - **Name** :名前を指定します。
      - **承認の種類** : **NONE**
      - **説明** :名前を指定します。
      - **サイト** : **Cloud**
      - **インスタンスの数** : **2** (既定値)。
      - **チェック** :既定では、使用が有効になるように選択されています。
      - Azure テナント ID と Azure アプリケーション ID は、前に定義したアプリケーションから取得されます。

![BMC の構成を示すスクリーンショット。](media/it-service-management-connector-secure-webhook-connections/bmc-configuration.png)

## <a name="next-steps"></a>次のステップ

* [Azure アラートから ITSM 作業項目を作成する](./itsmc-overview.md)
