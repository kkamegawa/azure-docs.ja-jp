---
title: 低速クエリ ログにアクセスする - Azure CLI - Azure Database for MariaDB
description: この記事では、Azure CLI コマンド ライン ユーティリティを使用して Azure Database for MariaDB の低速ログにアクセスする方法について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: f33a02ff0e287c135a7d63277cf3d8d3c0cd13d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527658"
---
# <a name="configure-and-access-slow-query-logs-by-using-azure-cli"></a>Azure CLI を使用して低速クエリ ログを構成してアクセスする
Azure CLI (Azure のコマンドライン ユーティリティ) を使用して Azure Database for MariaDB の低速クエリ ログをダウンロードできます。

## <a name="prerequisites"></a>前提条件
このハウツー ガイドの手順を実行するには、以下が必要です。
- [Azure Database for MariaDB サーバー](quickstart-create-mariadb-server-database-using-azure-cli.md)
- ブラウザーでの [Azure CLI](/cli/azure/install-azure-cli) または Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mariadb"></a>Azure Database for MariaDB のログ記録の構成
以下の手順に従って、MariaDB 低速クエリ ログにアクセスするサーバーを構成できます。
1. **slow\_query\_log** パラメーターをオンに設定してログ記録を有効にします。
2. **long\_query\_time** や **log\_slow\_admin\_statements** などのパラメーターを調整します。

これらのパラメーターの値を Azure CLI で設定する方法については、[サーバーのパラメーターを構成する方法](howto-configure-server-parameters-cli.md)に関する記事をご覧ください。

たとえば、次の CLI コマンドは低速クエリ ログをオンにし、長時間クエリを 10 秒に設定してから、低速管理ステートメントのログ記録をオフにします。 最後に、確認のために構成のオプションが一覧表示されます。
```azurecli-interactive
az mariadb server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mariadb server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mariadb server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mariadb server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mariadb-server"></a>Azure Database for MariaDB サーバーのログを一覧表示する
サーバーの利用可能な低速クエリ ログ ファイルを一覧表示するには、[az mariadb server-logs list](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-list) コマンドを実行します。

リソース グループ **myresourcegroup** にあるサーバー **mydemoserver.mariadb.database.azure.com** のログ ファイルを一覧表示できます。 その後、ログ ファイルの一覧を **log\_files\_list.txt** という名前のテキスト ファイルに送信します。
```azurecli-interactive
az mariadb server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>サーバーからログをダウンロードする
[az mariadb server-logs download](/cli/azure/mariadb/server-logs#az-mariadb-server-logs-download) コマンドで、サーバーの個々のログ ファイルをダウンロードできます。

次の例を使用して、リソース グループ **myresourcegroup** のサーバー **mydemoserver.mariadb.database.azure.com** の特定のログ ファイルを、ローカル環境にダウンロードします。
```azurecli-interactive
az mariadb server-logs download --name mysql-slow-mydemoserver-2018110800.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>次のステップ
- [Azure Database for MariaDB の低速クエリ ログ](concepts-server-logs.md)について学習します。
