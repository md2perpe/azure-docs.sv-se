---
title: Konfigurera datavetenskapsmiljöer i Azure - Team Data Science Process
description: Ställ in data science-miljöer i Azure för användning i Team Data Science Process.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 2c66da6717018237aebaf5fdaf803a8ac98f82be
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60344690"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurera datavetenskapsmiljöer för användning i Team Data Science Process
Team Data Science Process använder olika data science-miljöer för lagring, bearbetning och analys av data. De inkluderar Azure Blob Storage, flera typer av virtuella Azure-datorer, kluster för HDInsight (Hadoop) och Azure Machine Learning-arbetsytor. Beslut om vilken miljö som ska använda beror på typen och mängden data till modelleras och målområdet för dessa data i molnet. 

* Anvisningar för saker att tänka på när du gör det här beslutet finns i [planera din Azure Machine Learning Datavetenskapsmiljö](plan-your-environment.md). 
* En katalog med några av de scenarier som kan uppstå när du gör avancerad analys, se [scenarier för Team Data Science Process](plan-sample-scenarios.md)

I följande artiklar beskrivs hur du ställer in datavetenskapsmiljöer för olika data som används av Team Data Science Process.

* [Azure storage-konto](../../storage/common/storage-quickstart-create-account.md)
* [Kluster för HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Azure Machine Learning Studio-arbetsyta](../studio/create-workspace.md)

Den **Microsoft Data Science Virtual Machine (DSVM)** är också tillgängligt som en avbildning i Azure-dator (VM). Den här virtuella datorn har redan installerats och konfigurerats med flera populära verktyg som vanligen används för dataanalys och maskininlärning. DSVM finns på både Windows och Linux. Mer information finns i [introduktion till den molnbaserade Data Science Virtual Machine för Linux och Windows](../data-science-virtual-machine/overview.md).

Lär dig hur du skapar:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
- [Virtuell dator för djupinlärning](../data-science-virtual-machine/provision-deep-learning-dsvm.md)
