---
title: Azure DevTest Labs でのクレーム可能 VM の作成と管理 | Microsoft Docs
description: Azure DevTest Labs でラボに要求可能な仮想マシンを追加する方法を説明します
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: f671e66e-9630-4e30-a131-a6bad9ed9c11
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: spelluru
ms.openlocfilehash: 13d642597fdf5d0eae6c6fd4f0cab16181f033c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79233107"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Azure DevTest Labs でのクレーム可能 VM の作成と管理
要求可能な VM をラボに追加する方法は、"[ベース](devtest-lab-add-vm.md)" (*カスタム イメージ*、[数式](devtest-lab-create-template.md)、[Marketplace イメージ](devtest-lab-manage-formulas.md)のいずれか) から[標準の VM を追加](devtest-lab-configure-marketplace-images.md)する方法と似ています。 このチュートリアルでは、Azure Portal を使用して DevTest Labs でクレーム可能 VM をラボに追加する方法を説明し、ユーザーが VM を要求および解放する際に従うプロセスを示します。

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Azure DevTest Labs で要求可能 VM をラボに追加する手順
1. [Azure portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) にサインインする
1. **[すべてのサービス]** を選択し、 **[DEVOPS]** セクションの **[DevTest Labs]** を選択します。 **[DEVOPS]** セクションで **[DevTest Labs]** の隣の [*] (星) を選択した場合。 この操作により、次に簡単にアクセスできるように左側のナビゲーション メニューに **[DevTest Labs]** が追加されます。 その後は、左側のナビゲーション メニューの **[DevTest Labs]** を選択できます。

    ![[すべてのサービス] - [DevTest Labs] を選択する](./media/devtest-lab-create-lab/all-services-select.png)
1. ラボの一覧で、VM を作成するラボを選択します。
2. ラボの **[概要]** ページで、 **[+ 追加]** を選択します。

    ![VM ボタンを追加する](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. **[ベースの選択]** ページで、VM のマーケットプレイス イメージを選択します。
1. **[仮想マシン]** ページの **[基本設定]** タブで、次のようにします。
    1. **[仮想マシン名]** テキスト ボックスに、VM の名前を入力します。 テキスト ボックスには自動生成された一意の名前が自動的に入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しており、その後に一意の 3 桁の数字が続きます。 この機能により、マシンを作成するたびにマシンの名前を考えて入力する時間を節約できます。 必要に応じて、任意の名前でこの自動入力されるフィールドをオーバーライドすることができます。 VM の自動入力される名前をオーバーライドするには、 **[仮想マシン名]** テキスト ボックスに名前を入力します。
    2. **[ユーザー名]** に、仮想マシンの管理者権限を付与するユーザー名を入力します。 マシンの**ユーザー名**は、自動生成された一意の名前で事前入力されます。 この名前は、電子メール アドレス内のユーザー名に対応しています。 この機能により、新しいマシンを作成するたびにユーザー名を決める時間を節約できます。 ここでも、必要に応じて、任意のユーザー名でこの自動入力されるフィールドをオーバーライドすることができます。 ユーザー名の自動入力される値をオーバーライドするには、 **[ユーザー名]** テキスト ボックスに値を入力します。 このユーザーには、仮想マシンの**管理者**権限が付与されます。
    3. ラボで最初の VM を作成する場合は、ユーザーの**パスワード**を入力します。 このパスワードをラボに関連付けられている Azure Key Vault での既定のパスワードとして保存するには、 **[既定のパスワードとして保存する]** を選択します。 既定のパスワードは、**VmPassword** の名前でキー コンテナーに保存されます。 ラボで次の VM を作成しようとすると、**VmPassword** が**パスワード**に自動的に選択されます。 値をオーバーライドするには、 **[保存されているシークレットを使用する]** チェック ボックスをオフにして、パスワードを入力します。

        最初にシークレットをキー コンテナーに保存してから、ラボで VM を作成するときにそれを使用することもできます。 詳細については、「[Azure DevTest Labs でキー コンテナーにシークレットを格納する](devtest-lab-store-secrets-in-key-vault.md)」を参照してください。 キー コンテナーに格納されているパスワードを使用するには、 **[保存されているシークレットを使用する]** を選択し、シークレット (パスワード) に対応するキー値を指定します。
    4. **[その他のオプション]** セクションで、 **[サイズの変更]** を選択します。 定義済みの項目のいずれかを選択して、作成する VM のプロセッサ コア、RAM サイズ、ハード ドライブ サイズを指定します。
    5. **[成果物を追加または削除]** を選択します。 基本イメージに追加する成果物を選択して構成します。
    **注:** DevTest Labs やアーティファクトの構成に関する経験がない場合は、「[既存のアーティファクトの VM への追加](./devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm)」セクションを参照し、終了してからここに戻ってください。
2. 上部で **[詳細設定]** タブに切り替えて、次の操作を行います。
    1. VM が含まれる仮想ネットワークを変更するには、 **[Vnet の変更]** を選択します。
    2. サブネットを変更するには、 **[サブネットの変更]** を選択します。
    3. VM の IP アドレスが**パブリック、プライベート、共有**のいずれかを指定します。
    4. VM を自動的に削除するには、**有効期限の日付と時刻**を指定します。
    5. ラボ ユーザーが VM を要求できるようにするには、 **[このマシンを要求可能にする]** オプションで **[はい]** を選択します。
    6. ラボ ユーザーが使用できる **VM のインスタンス**の数を指定します。
3. **[作成]** を選択して、指定した VM をラボに追加します。

   ラボのページには VM の作成状況が表示されます。最初は **[作成中]** 、VM が起動した後は **[実行中]** と表示されます。

> [!NOTE]
>  [Azure Resource Manager テンプレート](devtest-lab-create-environment-from-arm.md)を使用してラボ VM をデプロイする場合、要求可能な VM を作成するには、プロパティ セクションで **allowClaim** プロパティを true に設定します。


## <a name="using-a-claimable-vm"></a>要求可能 VM の使用

ユーザーが "要求可能な仮想マシン" の一覧から任意の VM を要求するには、次のいずれかの手順を実行します。

* ラボの [概要] ウィンドウの下部にある [要求可能な仮想マシン] の一覧で、一覧にある VM のいずれかを右クリックし、 **[マシンの要求]** を選択します。

  ![特定の要求可能 VM を要求する。](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* [概要] ウィンドウの上部で、 **[要求]** を選択します。 要求可能 VM の一覧から仮想マシンがランダムに割り当てられます。

  ![任意の claimable VM を要求する。](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


ユーザーが VM を要求すると、DevTest Labs はそのマシンを起動して、ラボ ユーザーの [自分の仮想マシン] の一覧に移動します。 これは、ラボ ユーザーがこのマシンの所有者特権を持つようになったことを意味します。 この手順に必要な時間は、起動時間や、要求イベント中に実行されるその他のカスタム アクションによって異なる場合があります。 要求されたマシンは、要求可能なプールで利用できなくなります。  

## <a name="unclaim-a-vm"></a>VM の解放

ユーザーは、要求した VM の使用を終了し、それを他のユーザーが利用できるようにする場合、次のいずれかの手順を実行して、要求した VM を要求可能な仮想マシンの一覧に戻すことができます。

- [自分の仮想マシン] の一覧から、いずれかの VM を右クリックするかその省略記号 [...] を選択し、 **[解放]** を選択します。

  ![VM の一覧で VM を解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- [自分の仮想マシン] の一覧で、VM を選択してその管理ウィンドウを開き、上部のメニュー バーにある **[解放]** を選択します。

  ![VM の管理ウィンドウで VM を解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

ユーザーは VM を解放すると、その特定のラボ VM に対する所有者アクセス許可を失い、その VM はプールに戻された状態で他のラボ ユーザーが要求できるようになります。 

### <a name="transferring-the-data-disk"></a>データ ディスクの譲渡
クレーム可能 VM にデータ ディスクが接続されていて、ユーザーがその VM を解放した場合、そのデータ ディスクは VM で保持されたままになります。 その後、別のユーザーがその VM を要求するときに、その新しいユーザーは VM に加えてデータ ディスクも要求します。

これは "データ ディスクの譲渡" と呼ばれます。 このデータ ディスクは、新しいユーザーが管理するためにそのユーザーの **[個人用データ ディスク]** の一覧に表示されるようになります。

![データ ディスクを解放する。](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>次のステップ
* 作成されたら、管理ウィンドウの **[接続]** を選択して VM に接続できます。
* [DevTest Labs Azure Resource Manager のクイックスタート テンプレート ギャラリー](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)を検索します。
