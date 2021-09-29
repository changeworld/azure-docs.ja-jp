---
title: 概念 - Azure VMware Solution の実行コマンド (プレビュー)
description: Azure VMware Solution での実行コマンドの使用について学習します。
ms.topic: conceptual
ms.date: 09/17/2021
ms.openlocfilehash: a4121f8479b22eb1c0666a1e7d7a23ece4fb3d20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668851"
---
# <a name="run-command-in-azure-vmware-solution-preview"></a>Azure VMware Solution の実行コマンド (プレビュー)

Azure VMware Solution では、vCenter に *cloudadmin* という組み込みのローカル ユーザーがあり、CloudAdmin ロールに割り当てられています。 CloudAdmin ロールには、他の VMware クラウド ソリューションやオンプレミスのデプロイとは異なる vCenter [特権](concepts-identity.md#view-the-vcenter-privileges)があります。 実行コマンド (プレビュー) 機能を使用すると、PowerShell コマンドレットのコレクションによって昇格された特権を通常ならば必要とする操作を実行できます。 

Azure VMware Solution では、次の操作をサポートしています。

- [外部 ID ソースを構成する](configure-identity-source-vcenter.md)

- [ストレージ ポリシーを表示および設定する](configure-storage-policy.md) 

- [JetStream を使用してディザスター リカバリーをデプロイする](deploy-disaster-recovery-using-jetstream.md)


>[!NOTE]
>実行コマンドは、送信された順序で一度に 1 つずつ実行されます。

## <a name="view-the-status-of-an-execution"></a>実行の状態を表示する

実行されたすべての実行コマンドの状態 (コマンドレットの出力、エラー、警告、情報ログなど) を表示できます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. **[実行コマンド]**  >  **[Run execution status]\(実行の実行状態\)** を選択します。

   列を選択すると、さまざまな列の並べ替えを行うことができます。  

   :::image type="content" source="media/run-command/run-execution-status.png" alt-text="[Run execution status]\(実行の実行状態\) タブを示すスクリーンショット。" lightbox="media/run-command/run-execution-status.png":::

1. 表示する実行を選択します。 ペインが開いて、実行の詳細に加えて、コマンドレットによって生成されたさまざまな種類の出力に関するタブも表示されます。

   :::image type="content" source="media/run-command/run-execution-status-example.png" alt-text="実行の実行例を示すスクリーンショット。":::

   実行の詳細 (出力、エラー、警告、情報など) を表示できます。

   - **[詳細]** - 実行の詳細 (名前、状態、パッケージ、コマンドレット名、コマンドが失敗した場合のエラーなど) の概要。 

   - **[出力]** - コマンドレットによって出力されるメッセージ。 進行状況または操作の結果を含めることができます。 すべてのコマンドレットに出力があるわけではありません。

      :::image type="content" source="media/run-command/run-execution-status-example-output.png" alt-text="実行の実行の出力を示すスクリーンショット。":::

   - **[エラー]** - コマンドレットの実行中に生成されるエラー メッセージ。 これは、詳細ペインの終了エラーメッセージに追加されます。    

      :::image type="content" source="media/run-command/run-execution-status-example-error.png" alt-text="実行の実行中に検出されたエラーを示すスクリーンショット。":::

   - **[警告]** - 実行中に生成された警告メッセージ。 

      :::image type="content" source="media/run-command/run-execution-status-example-warning.png" alt-text="実行の実行中に検出された警告を示すスクリーンショット。":::

   - **[情報]** - コマンドレットの実行中の進行状況と診断によって生成されたメッセージ。 

      :::image type="content" source="media/run-command/run-execution-status-example-information.png" alt-text="実行されたコマンドレットについてリアルタイムの進行状況の全体を示したスクリーンショット。":::



## <a name="cancel-or-delete-a-job"></a>ジョブを取り消す、または削除する



### <a name="method-1"></a>方法 1

この方法では、実行の取り消しを試行し、完了時にそれを削除します。

>[!IMPORTANT]
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

- [ストレージ ポリシーを構成する](configure-storage-policy.md) - vSAN データストアにデプロイされた各 VM には、vSAN ストレージ ポリシーが割り当てられます。 vSAN ストレージ ポリシーは、VM の初期デプロイで割り当てることも、複製や移行などの他の VM 操作を実行するときに割り当てることもできます。

- [vCenter に対して外部 ID ソースを構成する (実行コマンド)](configure-identity-source-vcenter.md) - vCenter に対して LDAP または LDAPS を介して Active Directory を構成します。これにより、外部 ID ソースを Active Directory として使用できるようになります。 これで、外部 ID ソースから CloudAdmin ロールにグループを追加できます。

- [JetStream を使用してディザスター リカバリーをデプロイする](deploy-disaster-recovery-using-jetstream.md) - vSAN の復旧クラスターにデータを直接格納します。 データは、vSphere 内で実行される I/O フィルターを介してキャプチャされます。 基になるデータ ストアには、VMFS、VSAN、vVol、または任意の HCI プラットフォームを使用できます。 