---
title: リリース ノート
description: Azure Synapse Analytics のリリース ノート (ワークスペース)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: c1b5b9ac5d7c3f04dd3ae2e843425a5ead0d4c07
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419806"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Azure Synapse Analytics (プレビュー) のリリース ノート

この記事では、Azure Synapse Analytics (ワークスペース) の制限と問題について説明します。 関連情報については、[Azure Synapse Analytics (ワークスペース)](overview-what-is.md) に関するページを参照してください。

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (ワークスペース) 

### <a name="azure-synapse-cli"></a>Azure Synapse CLI

- 問題およびユーザーへの影響:SDK によって作成されたワークスペースから、Synapse Studio を起動できません。

- 対処法:次の手順を実行します。 
  1.    `az synapse workspace create`2 を実行してワークスペースを作成します。    `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")` を実行してマネージド ID を抽出します。
  3.    ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}` を実行して、ストレージ アカウントにロールとしてワークスペースを追加します。
  4.    ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 ` を実行して、ファイアウォール規則を追加します。

## <a name="next-steps"></a>次のステップ

* [ワークスペースを作成する](quickstart-create-workspace.md)
* [Synapse Studio を使用する](quickstart-synapse-studio.md)
* [SQL プールを作成する](quickstart-create-sql-pool.md)
* [SQL オンデマンドを使用する](quickstart-sql-on-demand.md)
* [Apache Spark プールを作成する](quickstart-create-apache-spark-pool.md)