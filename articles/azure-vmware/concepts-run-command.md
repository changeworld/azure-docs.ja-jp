---
title: 概念 - Azure VMware Solution の実行コマンド
description: Azure VMware Solution での実行コマンドの使用について学習します。
ms.topic: conceptual
ms.date: 08/31/2021
ms.openlocfilehash: 28cbf76dd035ce9b04909a61e3001063aa151162
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/31/2021
ms.locfileid: "123310679"
---
# <a name="run-commands-in-azure-vmware-solution"></a>Azure VMware Solution の実行コマンド

Azure VMware Solution では、CloudAdmin ロールによる vCenter へのアクセス権が得られます。 Azure VMware Solution の CloudAdmin ロールに[付与されている特権](concepts-identity.md#view-the-vcenter-privileges)は、Azure VMware Solution プライベート クラウドの vCenter で確認できます。 実行コマンドは、vCenter を対象として昇格された特権を必要とする特定の操作を実行する PowerShell コマンドレットのコレクションです。 

Azure VMware Solution では、次の操作をサポートしています。

- [JetStream DR ソリューションをインストールおよびアンインストールする](deploy-disaster-recovery-using-jetstream.md)

- [外部 ID ソースを構成する](configure-identity-source-vcenter.md)

- [ストレージ ポリシーを表示および編集する](configure-storage-policy.md) 


>[!NOTE]
>実行コマンドは、送信された順序で一度に 1 つずつ実行されます。

## <a name="view-the-status-of-an-execution"></a>実行の状態を表示する

実行されたすべての実行コマンドの状態 (出力、エラー、警告、情報など) を表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[実行コマンド]**  >  **[Run execution status]\(実行の実行状態\)** を選択します。

   実行名、パッケージ名、パッケージ バージョン、コマンド名、開始時刻、終了時刻、および状態で並べ替えることができます。  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="[Run execution status]\(実行の実行状態\) タブを示すスクリーンショット。" lightbox="media/run-command/run-execution-status.png":::

1. 表示する実行を選択します。

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="実行の実行例を示すスクリーンショット。":::

   実行の詳細 (出力、エラー、警告、情報など) を表示できます。

   - **[詳細]** - 実行の詳細 (名前、状態、パッケージ、実行されたコマンドの名前など) の概要。 

   - **[出力]** - コマンドレットの実行が正常に終了したときに出力されるメッセージ。 すべてのコマンドレットに出力があるわけではありません。

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="実行の実行の出力を示すスクリーンショット。":::

   - **[エラー]** - コマンドレットの実行が停止する原因となった例外。    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="実行の実行中に検出されたエラーを示すスクリーンショット。":::

   - **[警告]** - コマンドレットの実行中に発生した例外のうち、コマンドレットの終了に至らなかったもの。 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="実行の実行中に検出された警告を示すスクリーンショット。":::

   - **[情報]** - コマンドレットの実行中に出力される進行状況メッセージ。 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="実行されたコマンドレットについてリアルタイムの進行状況の全体を示したスクリーンショット。":::



## <a name="cancel-or-delete-a-job"></a>ジョブを取り消す、または削除する



### <a name="method-1"></a>方法 1

>[!NOTE]
>方法 1 は、元に戻すことのできない操作です。

1. **[実行コマンド]**  >  **[Run execution status]\(実行の実行状態\)** を選択し、取り消すジョブを選択します。

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-1.png" alt-text="実行コマンドを取り消して削除する方法を示すスクリーンショット。":::

2. すべてのユーザーのジョブを取り消して削除するには、 **[はい]** を選択します。



### <a name="method-2"></a>方法 2

1. **[実行コマンド]**  >  **[パッケージ]**  >  **[Run execution status]\(実行の実行状態\)** を選択します。

2. 取り消して削除するジョブの **[詳細]** (...) を選択します。

   :::image type="content" source="media/run-command/run-execution-cancel-delete-job-method-2.png" alt-text="省略記号を使用して実行コマンドを取り消して削除する方法を示すスクリーンショット。":::

3. すべてのユーザーのジョブを取り消して削除するには、 **[はい]** を選択します。



## <a name="next-steps"></a>次のステップ

実行コマンドの概念について学習した後は、実行コマンド機能を使用して次のことを行うことができます。

- [ストレージ ポリシーを構成する](configure-storage-policy.md) - vSAN データストアにデプロイされた各 VM には、少なくとも 1 つの VM ストレージ ポリシーが割り当てられます。 VM ストレージ ポリシーは、VM の初期デプロイ時に割り当てることも、複製や移行などの他の VM 操作を実行するときに割り当てることもできます。

- [vCenter の外部 ID ソースを構成する](configure-identity-source-vcenter.md) - vCenter には cloudadmin という組み込みのローカル ユーザーがあり、CloudAdmin ロールに割り当てられています。 このローカルの cloudadmin ユーザーを使用して、Active Directory (AD) にユーザーが設定されます。 実行コマンド機能を使用すると、Active Directory over LDAP または LDAPS for vCenter を外部 ID ソースとして構成できます。

- [JetStream を使用してディザスター リカバリーをデプロイする](deploy-disaster-recovery-using-jetstream.md) - vSAN の復旧クラスターにデータを直接格納します。 データは、vSphere 内で実行される I/O フィルターを介してキャプチャされます。 基になるデータ ストアには、VMFS、VSAN、vVol、または任意の HCI プラットフォームを使用できます。 
