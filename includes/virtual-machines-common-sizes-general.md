---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 10/08/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: fee35d8035238737f6be72d112fba382e00f0875
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501280"
---
VM-storlekar för generell användning ger balanserat förhållande mellan processor och minne. Utmärkt för tester och utveckling, små till mellanstora databaser och webbservrar med låg till medelhög trafik. Den här artikeln innehåller information om hur många virtuella processorer, diskar och nätverkskort samt genomflödet för storlekar i den här grupperingen. 

- Den [DC-serien](#dc-series) är en ny serie av virtuella datorer i Azure som kan hjälpa att skydda sekretessen och integriteten hos dina data och code medan den bearbetas i det offentliga molnet. De här datorernas backas av den senaste generationens Intel XEON E-2176G-processor på 3,7 GHz med SGX-teknik. Med Intel Turbo Boost Technology kan de här datorerna nå 4,7 GHz. DC-serien gör att kunder kan skapa säkra, enklavbaserade program som skyddar kod och data medan de används.

- Virtuella datorer i Av2-serien kan distribueras på en rad olika maskinvarutyper och processorer. Virtuella datorer i A-serien har CPU-prestanda och minneskonfigurationer som är bäst lämpade för arbetsbelastningar på ingångsnivån som utveckling och testning. Storleken begränsas, baserat på maskinvaran, för att erbjuda enhetliga processorprestanda på instansen som körs, oavsett vilken maskinvara instansen har distribuerats på. Du kan kontrollera vilken fysisk maskinvara som storleken har distribuerats på genom att köra en fråga mot den virtuella maskinvaran från den virtuella datorn.

  Exemplen omfattar utveckling och testservrar, webbservrar för låg trafik, små till medelstora databaser, proof-of-concept och kodlagringsplatser.

- Dv2-serien, en uppföljare till den ursprungliga D-serien, har en kraftfullare processor och optimal processor och minne-konfiguration, vilket gör dem lämpliga för de flesta produktionsarbetsbelastningar. Processorn i Dv2-serien är cirka 35 % snabbare än den i D-serien. Den är baserad på den senaste generationens Intel Xeon® E5-2673 v3 (Haswell) 2,4 GHz- eller E5-2673 v4 (Broadwell) 2,3 GHz-processorer och kan gå upp till 3,1 GHz med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

- Dv3-serien har 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor eller senaste 2,3 GHz Intel XEON® E5-2673 v4-processorn processorn (Broadwell) i en hyper-threaded konfiguration som ger ett mer prisvärt för arbetsbelastningar för de flesta allmänt syfte.  Minne har expanderats (från ~3.5 GiB/Vcpu(:er) för 4 GiB/vCPU) medan disk- och gränser har justerats på basis av per kärna för att anpassas till flytten till hypertrådning.  Dv3 inte längre har extra minne VM-storlekar av D/Dv2-familjer, de som har flyttats till nya Ev3-familjen.

  Exemplen i D-serien omfattar program, relationsdatabaser, minnesintern cachelagring och analys. 
  
## <a name="b-series"></a>B-serien

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds inte

Virtuella datorer med burst B-serien är idealiska för arbetsbelastningar som inte behöver fullständig prestanda för Processorn kontinuerligt, som webbservrar, små databaser och utveckling och testmiljöer. Dessa arbetsbelastningar har normalt anpassningsbara prestandakrav. B-serien ger dessa kunder möjligheten att köpa en VM-storlek med ett pris medvetna baslinje-prestanda som gör att den Virtuella datorinstansen att bygga upp krediter när Virtuellt datorn använder mindre än dess grundläggande prestanda. När den virtuella datorn har ackumulerats kredit, utöka den virtuella datorn över den Virtuella datorns baslinje med upp till 100% av CPU när ditt program kräver mer processorkraft.

Exemplen omfattar utveckling och testning, webbservrar med låg trafik, små databaser, mikrotjänster, servrar för proof-of-concept, kompileringsservrar.


| Size             | Virtuell processor  | Minne: GiB | Temporär lagring (SSD) GiB | CPU-basprestanda för VM | CPU-maxprestanda för VM | Initial kredit | Kredit till bank/timme | Max kredit till bank | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5 %                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10 %                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20 %                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202 %                  | 1 200 %                   | 360                   | 121                 | 2 909           | 16                                     | 6480 / 75                                 | 4320 / 50                                 | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270 %                  | 1 600 %                   | 480                   | 162                 | 3 888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337 %                  | 2 000 %                   | 600                   | 203                 | 4 860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls stöds endast på Linux

## <a name="dsv3-series-sup1sup"></a>Dsv3-serien <sup>1</sup>

ACU: 160-190

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

Dsv3-seriens storlekar bygger på 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor eller senaste 2,3 GHz Intel XEON® E5-2673 v4-processorn (Broadwell)-processor som kan 3,5 GHz med Intel Turbo Boost Technology 2.0 och använda premium storage. Storlekarna i Dsv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.


| Size             | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|------------------|--------|-------------|----------------|----------------|-----------------------------------------------------------------------|-------------------------------------------|------------------------------------------------|
| Standard_D2s_v3  | 2      | 8           | 16             | 4              | 4000 / 32 (50)                                                       | 3200 / 48                                | 2/1 000                                   |
| Standard_D4s_v3  | 4      | 16          | 32             | 8              | 8000 / 64 (100)                                                      | 6400 / 96                                | 2/2 000                                   |
| Standard_D8s_v3  | 8      | 32          | 64             | 16             | 16000 / 128 (200)                                                    | 12800 / 192                              | 4 / 4000                                      |
| Standard_D16s_v3 | 16     | 64          | 128            | 32             | 32000 / 256 (400)                                                    | 25600 / 384                              | 8 / 8000                                      |
| Standard_D32s_v3 | 32     | 128          | 256            | 32             | 64000 / 512 (800)                                                    | 51200 / 768                              | 8/16 000                                               |
| Standard_D48s_v3 | 48     | 192          | 384            | 32             | 96000 / 768 (1200)                                                    | 76800 / 1152                               | 8 / 24000                                               |
| Standard_D64s_v3 | 64     | 256          | 512            | 32             | 128000 / 1024 (1600)                                                    | 80000 / 1200                              | 8 / 30000                                               |

<sup>1</sup> Dsv3-serien Virtuella datorns funktion Intel® Hyper-Threading Technology

## <a name="dv3-series-sup1sup"></a>Dv3-serien <sup>1</sup>

ACU: 160-190

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

Storlekarna Dv3-serien är baserade på 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell)-processor eller 2,3 GHz Intel XEON® E5-2673 v4 (Broadwell)-processor som kan uppnå upp till 3,5 GHz med Intel Turbo Boost Technology 2.0. Storlekarna i Dv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du Dsv3-storlekarna. Pris- och debiteringsmätarna för Dsv3-storlekar är samma som för Dv3-serien. 


| Storlek            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal nätverkskort/nätverksbandbredd |
|-----------------|-----------|-------------|----------------|----------------|----------------------------------------------------------|------------------------------|
| Standard_D2_v3  | 2         | 8           | 50             | 4              | 3 000/46/23                                               | 2/1 000                    |
| Standard_D4_v3  | 4         | 16          | 100            | 8              | 6 000/93/46                                               | 2/2 000                    |
| Standard_D8_v3  | 8         | 32          | 200            | 16             | 12 000/187/93                                             | 4 / 4000                    |
| Standard_D16_v3 | 16        | 64          | 400            | 32             | 24 000/375/187                                            | 8 / 8000                    |
| Standard_D32_v3 | 32        | 128         | 800            | 32             | 48 000/750/375                                            | 8/16 000                   |
| Standard_D48_v3 | 48        | 192          | 1200            | 32             | 96 000/1 000/500                                            | 8 / 24000                             |
| Standard_D64_v3 | 64        | 256         | 1600           | 32             | 96 000/1 000/500                                           | 8 / 30000                   |

<sup>1</sup> Dv3-serien Virtuella datorns funktion Intel® Hyper-Threading Technology

## <a name="dsv2-series"></a>DSv2-serien

ACU: 210-250

Premium-lagring:  Stöds

Premium Storage cachelagring:  Stöds

| Size | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS1_v2 |1 |3.5 |7 |4 |4000 / 32 (43) |3200 / 48 |2/750 |
| Standard_DS2_v2 |2 |7 |14 |8 |8000 / 64 (86) |6400 / 96 |2/1 500 |
| Standard_DS3_v2 |4 |14 |28 |16 |16000 / 128 (172) |12800 / 192 |4/3 000 |
| Standard_DS4_v2 |8 |28 |56 |32 |32000 / 256 (344) |25600 / 384 |8/6 000 |
| Standard_DS5_v2 |16 |56 |112 |64 |64000 / 512 (688) |51200 / 768 |8 / 12000 |

## <a name="dv2-series"></a>Dv2-serien

ACU: 210-250

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte

| Size           | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar | Dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|----------------|------|-------------|------------------------|------------------------------------------------------------|----------------|------------------|----------------------------------------------|
| Standard_D1_v2 | 1    | 3.5         | 50                     | 3 000 / 46 / 23                                             | 4              | 4 × 500            | 2/750                                      |
| Standard_D2_v2 | 2    | 7           | 100                    | 6 000 / 93 / 46                                             | 8              | 8 × 500            | 2/1 500                                     |
| Standard_D3_v2 | 4    | 14          | 200                    | 12 000 / 187 / 93                                           | 16             | 16 × 500           | 4/3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24 000 / 375 / 187                                          | 32             | 32 × 500           | 8/6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48 000 / 750 / 375                                          | 64             | 64 x 500           | 8 / 12000                                    |

## <a name="av2-series"></a>Av2-serien

ACU: 100

Premium-lagring:  Stöds inte

Premium Storage cachelagring:  Stöds inte


| Size            | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt genomflöde för temporär lagring: IOPS / läsning / Skriv Mbit/s | Maximalt antal datadiskar / dataflöde: IOPS | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) | 
|-----------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_A1_v2  | 1         | 2           | 10             | 1 000 / 20 / 10                                           | 2 / 2 x 500               | 2/250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8_v2  | 8         | 16          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |
| Standard_A2m_v2 | 2         | 16          | 20             | 2 000 / 40 / 20                                           | 4 / 4 x 500               | 2/500                 |
| Standard_A4m_v2 | 4         | 32          | 40             | 4 000 / 80 / 40                                           | 8 / 8 x 500               | 4/1 000                     |
| Standard_A8m_v2 | 8         | 64          | 80             | 8 000 / 160 / 80                                          | 16 / 16 x 500             | 8/2 000                     |

## <a name="dc-series"></a>DC-serien

Premium-lagring: Stöds

Premium Storage cachelagring: Stöds



| Size          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Max cachelagrat och temporärt lagrat dataflödet: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort / förväntade nätverksbandbredd (Mbit/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000 / 32 (43)                                                          | 3200 /48                                  | 2/1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000 / 64 (86)                                                          | 6400 /96                                  | 2 / 3000                                     |
