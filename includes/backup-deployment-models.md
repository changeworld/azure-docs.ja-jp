Azure Backup サービスには、バックアップ コンテナーと Recovery Services コンテナーの 2 種類のコンテナーがあります。

| **デプロイ** | **ポータル** | **コンテナー** |
|-----------|------|-----|
| クラシック | [クラシック](https://manage.windowsazure.com) | バックアップ資格情報コンテナー |
| リソース マネージャー (推奨) | [Azure](https://portal.azure.com) | Recovery Services コンテナー |

> [AZURE.NOTE] Resource Manager モデルでデプロイされたソリューションをバックアップ コンテナーで保護することはできません。ただし、Recovery Services コンテナーを使用して、クラシック デプロイによるサーバーと VM を保護することはできます。

<!---HONumber=AcomDC_0831_2016-->