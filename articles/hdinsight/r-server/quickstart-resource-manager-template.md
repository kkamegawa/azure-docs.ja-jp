---
title: クイック スタート:テンプレートを使用した ML Services クラスターの作成 - Azure HDInsight
description: このクイックスタートでは、Resource Manager テンプレートを使用し、Azure HDInsight に ML Services クラスターを作成します。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 85e46c0056b3bcf00544786f639fd7003ceeb7bd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "79475145"
---
# <a name="quickstart-create-ml-services-cluster-in-azure-hdinsight-using-resource-manager-template"></a>クイック スタート:Resource Manager テンプレートを使用して Azure HDInsight 内に ML Services クラスターを作成する

このクイックスタートでは、Azure Resource Manager テンプレートを使用して、Azure HDInsight に [ML Services](./r-server-overview.md) クラスターを作成します。 Azure で HDInsight クラスターを作成するときのデプロイ オプションとして Microsoft Machine Learning Server を選択できるようになりました。 このオプションを提供するクラスターの種類は、ML Services と呼ばれます。 この機能により、データ サイエンティスト、統計学者、R プログラマは、HDInsight でのスケーラブルで分散型の分析手法にオンデマンドでアクセスできるようになります。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-ml-services-cluster"></a>ML サービス クラスターの作成

### <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートで使用されるテンプレートは [Azure クイック スタート テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-rserver)からのものです。

:::code language="json" source="~/quickstart-templates/101-hdinsight-rserver/azuredeploy.json" range="1-171":::


テンプレートでは、次の 2 つの Azure リソースが定義されています。

* [Microsoft.Storage/storageAccounts](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts): Azure のストレージ アカウントを作成します。
* [Microsoft.HDInsight/cluster](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters): HDInsight クラスターを作成します。

### <a name="deploy-the-template"></a>テンプレートのデプロイ

1. 下の **[Azure へのデプロイ]** ボタンを選択して Azure にサインインし、Resource Manager テンプレートを開きます。

    [![Azure へのデプロイ](./media/quickstart-resource-manager-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-rserver%2Fazuredeploy.json)

1. 次の値を入力または選択します。

    |プロパティ |説明 |
    |---|---|
    |サブスクリプション|ドロップダウン リストから、このクラスターに使用する Azure サブスクリプションを選択します。|
    |Resource group|ドロップダウン リストから既存のリソース グループを選択するか、 **[新規作成]** を選択します。|
    |場所|この値には、リソース グループに使用される場所が自動入力されます。|
    |クラスター名|グローバルに一意の名前を入力します。 このテンプレートの場合、使用できるのは小文字と数字だけです。|
    |[Cluster Login User Name]\(クラスター ログイン ユーザー名\)|ユーザー名を指定します。既定値は **admin** です。|
    |[クラスター ログイン パスワード]|パスワードを指定します。 パスワードは 10 文字以上で、数字、大文字、小文字、英数字以外の文字 (' " ` を除く) が少なくとも 1 つずつ含まれる必要があります。 |
    |SSH ユーザー名|ユーザー名を指定します。既定値は sshuser です。|
    |SSH パスワード|パスワードを指定します。|

    ![Resource Manager テンプレート HBase](./media/quickstart-resource-manager-template/resource-manager-template-rserver.png)

1. 「**使用条件**」をご確認ください。 次に **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。 デプロイの進行状況が通知されます。 クラスターの作成には約 20 分かかります。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

クラスターが作成されると、 **[リソースに移動]** リンクを含む**デプロイ成功**通知を受け取ります。 [リソース グループ] ページには、新しい HDInsight クラスターと、クラスターに関連付けられている既定のストレージが一覧表示されます。 各クラスターには、[Azure Storage アカウント](../hdinsight-hadoop-use-blob-storage.md)または [Azure Data Lake Storage アカウント](../hdinsight-hadoop-use-data-lake-store.md)との依存関係があります。 このアカウントを、既定のストレージ アカウントと呼びます。 HDInsight クラスターとその既定のストレージ アカウントは、同じ Azure リージョンに配置されている必要があります。 クラスターを削除しても、ストレージ アカウントは削除されません。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートを完了したら、必要に応じてクラスターを削除できます。 HDInsight を使用すると、データは Azure Storage に格納されるため、クラスターは、使用されていない場合に安全に削除できます。 また、HDInsight クラスターは、使用していない場合でも課金されます。 クラスターの料金は Storage の料金の何倍にもなるため、クラスターを使用しない場合は削除するのが経済的にも合理的です。

Azure portal からクラスターに移動し、 **[削除]** を選択します。

![Resource Manager テンプレート HBase](./media/quickstart-resource-manager-template/azure-portal-delete-rserver.png)

リソース グループ名を選び、リソース グループ ページを開いて、 **[リソース グループの削除]** を選ぶこともできます。 リソース グループを削除すると、HDInsight クラスターと既定のストレージ アカウントの両方が削除されます。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Resource Manager テンプレートを使用して HDInsight で ML Services クラスターを作成する方法を学習しました。 次の記事では、RStudio Server で、分散 R 計算に Spark を使用する方法を示す R スクリプトを実行する方法について説明します。

> [!div class="nextstepaction"]
> [RStudio Server を使用して Azure HDInsight で ML サービス クラスターに対して R スクリプトを実行する](./machine-learning-services-quickstart-job-rstudio.md)
