---
title: Koppla ifrån en datadisk från en Windows-dator – Azure | Microsoft Docs
description: Koppla ifrån en datadisk från en virtuell dator i Azure med hjälp av Resource Manager-distributionsmodellen.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: 21b9ec78c4b4ca3a0f96bdd84be1d11e9d7257d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681861"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Hur du koppla ifrån en datadisk från en Windows-dator

När du inte längre behöver en datadisk som är ansluten till en virtuell dator kan du enkelt koppla bort den. Detta tar bort disken från den virtuella datorn, men tas inte bort från storage.

> [!WARNING]
> Om du koppla bort en disk som den inte tas bort automatiskt. Om du har prenumererat på Premium-lagring, fortsätter att kosta lagringsavgifter för disken. Mer information finns i [priser och fakturering när du använder Premiumlagring](disks-types.md#billing).

Om du vill använda befintliga data på disken igen kan du ansluta den igen till samma virtuella dator, eller till en annan.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="detach-a-data-disk-using-powershell"></a>Koppla ifrån en datadisk med hjälp av PowerShell

Du kan *frekvent* ta bort en datadisk med hjälp av PowerShell, men kontrollera att inget aktivt använder disken innan du kopplar bort det från den virtuella datorn.

I det här exemplet tar vi bort disken med namnet **myDisk** från den virtuella datorn **myVM** i den **myResourceGroup** resursgrupp. Först du tar bort en disk med hjälp av den [Remove-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/remove-azvmdatadisk) cmdlet. Sedan du uppdatera tillståndet för den virtuella datorn med hjälp av den [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) cmdleten för att slutföra processen med att ta bort datadisken.

```azurepowershell-interactive
$VirtualMachine = Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

Disken kvar i lagring, men är inte längre kopplad till en virtuell dator.

## <a name="detach-a-data-disk-using-the-portal"></a>Koppla ifrån en datadisk med hjälp av portalen

1. I den vänstra menyn väljer du **virtuella datorer**.
2. Välj den virtuella dator som har datadisken som du vill koppla från och klickar på **stoppa** till frigör den virtuella datorn.
3. I den virtuella dator i rutan Välj **diskar**.
4. Överst på den **diskar** väljer **redigera**.
5. I den **diskar** i fönstret till höger i datadisken som du vill koppla från, klickar du på den ![koppla från knappbild](./media/detach-disk/detach.png) koppla från knappen.
5. När disken har tagits bort, klickar du på **spara** överst i fönstret.
6. I den virtuella dator i rutan klickar du på **översikt** och klicka sedan på den **starta** längst upp i fönstret för att starta om den virtuella datorn.

Disken kvar i lagring, men är inte längre kopplad till en virtuell dator.

## <a name="next-steps"></a>Nästa steg

Om du vill återanvända datadisken kan du enkelt [koppla den till en annan virtuell dator](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)