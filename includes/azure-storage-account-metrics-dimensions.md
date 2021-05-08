---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 53850e7d9e27e0321e9337531b341ff9eebde548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97628713"
---
| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **GeoType** | プライマリ クラスターまたはセカンダリ クラスターからのトランザクション。 使用できる値には、**Primary** と **Secondary** が含まれています。 セカンダリ テナントからオブジェクトを読み取るときに、読み取りアクセス geo 冗長ストレージ (RA-GRS) に適用されます。 |
| **ResponseType** | トランザクション応答の種類。 次の値をご利用いただけます。 <br/><br/> <li>**ServerOtherError**:記述されていない、その他すべてのサーバー側エラー </li> <li>**ServerBusyError**:HTTP 503 ステータス コードを返した認証済み要求。 </li> <li>**ServerTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 タイムアウトは、サーバー エラーが原因で発生しました。 </li><li>**AuthenticationError**:サーバーによる要求の認証が失敗しました。</li><li>**AuthorizationError**:データの不正アクセスまたは承認エラーが原因で失敗した認証済み要求。 </li> <li>**NetworkError**:ネットワーク エラーが原因で失敗した認証済み要求。 クライアントがタイムアウト期限が切れる前に途中で接続を終了したときによく発生します。 </li><li>**ClientAccountBandwidthThrottlingError**:この要求は、[ストレージ アカウントのスケーラビリティの制限を超えた](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)ため、帯域幅が調整されます。</li><li>**ClientAccountRequestThrottlingError**:この要求は、[ストレージ アカウントのスケーラビリティの制限を超えた](../articles/storage/common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)ため、要求レートが調整されます。<li>**ClientThrottlingError**:その他のクライアント側の調整エラー。 ClientAccountBandwidthThrottlingError と ClientAccountRequestThrottlingError は除外されます。</li><li>**ClientShareEgressThrottlingError**:Premium ファイル共有にのみ適用されます。 その他のクライアント側の調整エラー。 共有の制限が超えたことによるエグレス帯域幅の調整によって要求が失敗しました。 ClientAccountBandwidthThrottlingError は除外されます。</li><li>**ClientShareIngressThrottlingError**:Premium ファイル共有にのみ適用されます。 その他のクライアント側の調整エラー。 共有の制限が超えたことによるイングレス帯域幅の調整によって要求が失敗しました。 ClientAccountBandwidthThrottlingError は除外されます。</li><li>**ClientShareIopsThrottlingError**:Premium ファイル共有にのみ適用されます。 その他のクライアント側の調整エラー。 IOPS 調整によって要求が失敗しました。 ClientAccountRequestThrottlingError は除外されます。</li><li>**ClientTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 クライアントのネットワーク タイムアウトまたは要求タイムアウトが、ストレージ サービスで予期される値よりも低く設定されている場合、これは予期されるタイムアウトです。 それ以外の場合は、ServerTimeoutError としてレポートされます。 </li> <li>**ClientOtherError**:記述されていない、その他すべてのクライアント側エラー。 </li> <li>**成功**:成功した要求</li> <li> **SuccessWithThrottling**: 最初に試みたときは SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li><li> **SuccessWithShareEgressThrottling**:Premium ファイル共有にのみ適用されます。 最初に試みたときはエグレス帯域幅の調整によって SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li><li> **SuccessWithShareIngressThrottling**:Premium ファイル共有にのみ適用されます。 最初に試みたときはイングレス帯域幅の調整によって SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li><li> **SuccessWithShareIopsThrottling**:Premium ファイル共有にのみ適用されます。 最初に試みたときは IOPS の調整によって SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li> |
| **ApiName** | 操作の名前。 [操作](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)の名前が識別される前にエラーが発生した場合、名前は "Unknown" と表示されます。 **ResponseType** ディメンションの値を使用して、エラーの詳細について確認できます。
| **認証** | トランザクションで使用される認証の種類。 次の値をご利用いただけます。 <br/> <li>**AccountKey**:トランザクションは、ストレージ アカウント キーを使って認証されます。</li> <li>**SAS**:トランザクションは、Shared Access Signature を使って認証されます。</li> <li>**OAuth**:トランザクションは、OAuth アクセス トークンを使って認証されます。</li> <li>**Anonymous**:トランザクションは匿名で要求されます。 プリフライト要求は含まれません。</li> <li>**AnonymousPreflight**:トランザクションは、プリフライト要求です。</li> |

