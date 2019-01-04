---
title: Configuration Manager を Log Analytics に接続する | Microsoft Docs
description: この記事では、Configuration Manager を Log Analytics に接続してデータの分析を開始する手順を示します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f2298bd7-18d7-4371-b24a-7f9f15f06d66
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: magoedte
ms.openlocfilehash: b13e92369168a43f529ed0b83c10bc65893da83d
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53193316"
---
# <a name="connect-configuration-manager-to-log-analytics"></a>Configuration Manager を Log Analytics に接続する
お使いの System Center Configuration Manager 環境を Azure Log Analytics に接続して、デバイス コレクション データを同期し、Log Analytics と Azure Automation でこれらのコレクションを参照することができます。  

## <a name="prerequisites"></a>前提条件

Log Analytics では、System Center Configuration Manager の現在のブランチ (バージョン 1606 以降) をサポートしています。  

## <a name="configuration-overview"></a>構成の概要
次の手順は、Configuration Manager と Log Analytics の統合を構成する手順をまとめたものです。  

1. Azure Portal で Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、Azure Active Directory への登録によるクライアント ID およびクライアント秘密鍵を持っていることを確認します。 この手順の詳細については、「[リソースにアクセスできる Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」を参照してください。
2. Azure Portal で、[Configuration Manager (登録した Web アプリ) に Log Analytics へのアクセス許可を付与](#grant-configuration-manager-with-permissions-to-log-analytics)します。
3. Configuration Manager で、[OMS 接続の追加ウィザードを使用して接続を追加](#add-an-oms-connection-to-configuration-manager)します。
4. パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Configuration Manager で、[接続プロパティを更新](#update-oms-connection-properties)します。
5. Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターに、[Microsoft Monitoring Agent をダウンロードしてインストール](#download-and-install-the-agent)します。 エージェントは、Configuration Manager のデータを Log Analytics ワークスペースに送信します。
6. Log Analytics で、コンピューター グループとして [Configuration Manager からコレクションをインポート](#import-collections)します。
7. Log Analytics で、Configuration Manager からのデータを[コンピューター グループ](../../azure-monitor/platform/computer-groups.md)として表示します。

Configuration Manager を Log Analytics に接続する方法について詳しくは、[Configuration Manager から Microsoft Log Analytics にデータを同期する方法](https://technet.microsoft.com/library/mt757374.aspx)に関するページをご覧ください。

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuration Manager に Log Analytics へのアクセス許可を付与する
次の手順では、前に Configuration Manager 用に作成した AD アプリケーションとサービス プリンシパルに、Log Analytics ワークスペースの "*共同作成者*" ロールを付与します。  まだワークスペースがない場合は、続行する前に「[Azure Log Analytics でワークスペースを作成する](../../azure-monitor/learn/quick-create-workspace.md)」をご覧ください。  これにより、Configuration Manager は認証を行って Log Analytics ワークスペースに接続できます。  

> [!NOTE]
> Log Analytics へのアクセス許可を Configuration Manager に指定する必要があります。 そうしないと、Configuration Manager で構成ウィザードを使用するときにエラー メッセージが表示されます。
>

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。<br><br> ![Azure Portal](media/collect-sccm/azure-portal-01.png)<br><br>  
2. Log Analytics ワークスペースの一覧で、変更するワークスペースを選びます。
3. 左側のウィンドウで **[アクセス制御 (IAM)]** を選びます。
4. [アクセス制御 (IAM)] ページで、**[ロールの割り当ての追加]** をクリックすると、**[ロールの割り当ての追加]** ウィンドウが表示されます。
5. **[ロールの割り当ての追加]** ウィンドウの **[ロール]** ドロップダウン リストで、**[共同作成者]** ロールを選びます。  
6. **[アクセスの割り当て先]** ドロップダウン リストで、前に AD で作成した Configuration Manager アプリケーションを選び、**[OK]** をクリックします。  

## <a name="download-and-install-the-agent"></a>エージェントのダウンロードとインストール
記事「[Windows コンピューターを Azure の Log Analytics サービスに接続する](../../azure-monitor/platform/agent-windows.md)」を読み、 Configuration Manager サービス接続ポイントのサイト システムの役割をホストしているコンピューターに Microsoft Monitoring Agent をインストールする方法を理解します。  

## <a name="add-a-log-analytics-connection-to-configuration-manager"></a>Configuration Manager に Log Analytics 接続を追加する
Log Analytics 接続を追加するには、Configuration Manager 環境に[サービス接続ポイント](https://technet.microsoft.com/library/mt627781.aspx)があり、オンライン モード用に構成されている必要があります。

1. Configuration Manager の **[管理]** ワークスペースで、**[OMS コネクタ]** を選択します。 これで、**Log Analytics 接続の追加ウィザード**が開かれます。 **[次へ]** を選択します。

   >[!NOTE]
   >OMS は、Log Analytics と呼ばれるようになりました。
   
2. **[全般]** 画面で、以下の操作が完了していることと、各項目の詳細を把握していることを確認し、**[次へ]** を選択します。

   1. Azure Portal で、Configuration Manager を Web アプリケーションまたは Web API アプリとして登録し、[登録からクライアント ID](../../active-directory/develop/quickstart-v1-add-azure-ad-app.md) を取得した。
   2. Azure Portal で、Azure Active Directory に登録したアプリのアプリ秘密鍵を作成した。  
   3. Azure portal で、登録した Web アプリに Log Analytics へのアクセス許可を付与した。  
      ![Log Analytics への接続ウィザードの [全般] ページ](./media/collect-sccm/sccm-console-general01.png)
3. **[Azure Active Directory]** 画面で、Log Analytics への接続設定を構成します。**[テナント]**、**[クライアント ID]**、および **[クライアントの秘密鍵]** を指定してから、**[次へ]** を選びます。  
   ![Log Analytics への接続ウィザードの [Azure Active Directory] ページ](./media/collect-sccm/sccm-wizard-tenant-filled03.png)
4. 他の手順がすべて正常に完了している場合は、**[OMS Connection Configuration (OMS 接続の構成)]** 画面の情報がこのページに自動的に表示されます。 接続設定の情報が表示されるのは、**[Azure サブスクリプション]**、**[Azure リソース グループ]**、および **[Operations Management Suite ワークスペース]** です。  
   ![Log Analytics への接続ウィザードの [Log Analytics Connection]\(Log Analytics 接続\) ページ](./media/collect-sccm/sccm-wizard-configure04.png)
5. ウィザードは、入力された情報を使って、Log Analytics サービスに接続します。 サービスと同期するデバイス コレクションを選び、**[追加]** をクリックします。  
   ![コレクションの選択](./media/collect-sccm/sccm-wizard-add-collections05.png)
6. **[概要]** 画面で接続の設定を確認し、**[次へ]** を選択します。 **[進行状況]** 画面に接続の状態が表示され、その後 **[完了]** になります。

> [!NOTE]
> 階層の最上層サイトを Log Analytics に接続する必要があります。 スタンドアロン プライマリ サイトを Log Analytics に接続した後で、環境に中央管理サイトを追加する場合は、接続を削除し、新しい階層内で接続を再作成する必要があります。
>
>

Configuration Manager を Log Analytics にリンクした後、コレクションを追加または削除したり、接続のプロパティを表示したりすることができます。

## <a name="update-log-analytics-connection-properties"></a>Log Analytics の接続プロパティを更新する
パスワードまたはクライアント秘密鍵が期限切れになるか、それらを紛失した場合は、Log Analytics の接続プロパティを手動で更新する必要があります。

1. Configuration Manager で **[Cloud Services]** に移動し、**[OMS コネクタ]** を選択して、**[OMS Connection Properties]\(OMS 接続プロパティ\)** ページを開きます。
2. このページで **[Azure Active Directory]** タブをクリックし、**[テナント]**、**[クライアント ID]**、**[Client secret key expiration (クライアントの秘密鍵の期限切れ)]** を表示します。 **クライアントの秘密鍵**が期限切れかどうかを**確認**します。

## <a name="import-collections"></a>コレクションをインポートする
Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールした後、次の手順は Configuration Manager からのコレクションをコンピューター グループとして Log Analytics にインポートすることです。

初期構成を完了して階層からデバイス コレクションをインポートした後は、メンバーシップを最新に保つために、コレクションのメンバーシップ情報が 3 時間ごとに取得されます。 この処理はいつでも無効にすることができます。

1. Azure Portal の左上隅にある **[すべてのサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
2. Log Analytics ワークスペースの一覧で、Configuration Manager が登録されているワークスペースを選びます。  
3. **[詳細設定]** を選択します。<br><br> ![Log Analytics の詳細設定](media/collect-sccm/log-analytics-advanced-settings-01.png)<br><br>  
4. **[コンピューター グループ]** を選び、**[SCCM]** を選びます。  
5. **[Configuration Manager コレクション メンバーシップをインポートする]** を選択し、**[保存]** をクリックします。  
   ![コンピューター グループ - [SCCM] タブ](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Configuration Manager のデータを表示する
Log Analytics 接続を Configuration Manager に追加し、Configuration Manager サービス接続ポイントのサイト システムの役割を実行しているコンピューターにエージェントをインストールすると、エージェントからのデータが Log Analytics に送信されます。 Log Analytics で、Configuration Manager コレクションが[コンピューター グループ](../../azure-monitor/platform/computer-groups.md)として表示されます。 グループは、**[Configuration Manager]** ページの **[設定] > [コンピューター グループ]** で見ることができます。

コレクションがインポートされると、コレクションのメンバーシップを持つコンピューターが何台検出されたかを確認できます。 インポートされたコレクションの数を確認することもできます。

![コンピューター グループ - [SCCM] タブ](./media/collect-sccm/sccm-computer-groups02.png)

いずれかをクリックすると、[検索] が開き、インポートされたすべてのグループか、各グループに属しているすべてのコンピューターが表示されます。 [ログの検索](../../azure-monitor/log-query/log-query-overview.md)を使用して、Configuration Manager のデータの詳細な分析を開始することができます。

## <a name="next-steps"></a>次の手順
* [ログの検索](../../azure-monitor/log-query/log-query-overview.md)を使用して、Configuration Manager のデータに関する詳細な情報を表示します。
