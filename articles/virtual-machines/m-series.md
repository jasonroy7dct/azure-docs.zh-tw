---
title: M 系列-Azure 虛擬機器
description: M 系列 Vm 的規格。
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/05/2019
ms.author: lahugh
ms.openlocfilehash: 49b12341e5ca119ee20c7e509d9bbef64d4d5b37
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493666"
---
# <a name="m-series"></a>M 系列

M 系列提供高 vCPU 計數（最多128個 vcpu）和海量儲存體（最多 3.8 TiB）。 這也適用于極大的資料庫或其他受益于高 vCPU 計數和海量儲存體的應用程式。 M 系列大小是根據 Intel®的® CPU E7-8890 v3 @ 2.50 g h z

M 系列 VM 的功能 Intel®超執行緒技術

ACU：160-180

進階儲存體：支援

進階儲存體快取：支援

寫入加速器：[支援](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)

| 大小 | vCPU | 記憶體：GiB | 暫存儲存體 (SSD) GiB | 最大資料磁碟 | 最大快取和暫存儲存體輸送量： IOPS/MBps （GiB 中的快取大小） | 最大未快取的磁片輸送量： IOPS/MBps | 最大 Nic/預期的網路頻寬（Mbps） |
|---|---|---|---|---|---|---|---|
| Standard_M8ms <sup>2</sup>       | 8   | 218.75 | 256   | 8  | 10000/100 （793）     | 5000/125   | 4/2000  |
| Standard_M16ms <sup>2</sup>      | 16  | 437.5  | 512   | 16 | 20000/200 （1587）    | 10000/250  | 8/4000  |
| Standard_M32ts                   | 32  | 192    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M32ls                   | 32  | 256    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M32ms <sup>2</sup>      | 32  | 875    | 1024  | 32 | 40000/400 （3174）    | 20000/500  | 8/8000  |
| Standard_M64s                    | 64  | 1024   | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M64ls                   | 64  | 512    | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M64ms <sup>3</sup>      | 64  | 1792   | 2048  | 64 | 80000/800 （6348）    | 40000/1000 | 8/16000 |
| Standard_M128s <sup>1</sup>      | 128 | 2048   | 4096  | 64 | 160000/1600 （12696） | 80000/2000 | 8/30000 |
| Standard_M128ms <sup>1、2、3</sup> | 128 | 3892   | 4096  | 64 | 160000/1600 （12696） | 80000/2000 | 8/30000 |
| Standard_M64                     | 64  | 1024   | 7168  | 64 | 80000/800 （1228）    | 40000/1000 | 8/16000 |
| Standard_M64m                    | 64  | 1792   | 7168  | 64 | 80000/800 （1228）    | 40000/1000 | 8/16000 |
| Standard_M128 <sup>1</sup>       | 128 | 2048   | 14336 | 64 | 250000/1600 （2456）  | 80000/2000 | 8/32000 |
| Standard_M128m <sup>1</sup>      | 128 | 3892   | 14336 | 64 | 250000/1600 （2456）  | 80000/2000 | 8/32000 |

<sup>1</sup> 64 以上的 vCPU 需要下列其中一個支援的客體作業系統： Windows Server 2016、UBUNTU 16.04 LTS、SLES 12 SP2 和 Red Hat Enterprise Linux、CentOS 7.3 或 Oracle Linux 7.3 with .lis 4.2.1。

<sup>2</sup> 可用限制核心大小。

<sup>3</sup> 執行個體會隔離至單一客戶專用的硬體。

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [一般用途](sizes-general.md)
- [記憶體最佳化](sizes-memory.md)
- [儲存體最佳化](sizes-storage.md)
- [GPU 最佳化](sizes-gpu.md)
- [高效能計算](sizes-hpc.md)
- [前幾代](sizes-previous-gen.md)

## <a name="next-steps"></a>後續步驟

深入了解 [Azure 計算單位 (ACU)](acu.md) 如何協助您比較各個 Azure SKU 的計算效能。