---
title: 'Emplacements et fournisseurs de connectivité : Azure ExpressRoute | Microsoft Docs'
description: Cet article fournit une vue d’ensemble détaillée des emplacements où les services sont proposés et de la façon de se connecter à des régions Azure. Trié par emplacement.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a68a5f49e82a00c4c64137169237d8d1e7d99ed7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580340"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partenaires ExpressRoute et emplacements de peering

> [!div class="op_single_selector"]
> * [Emplacements par fournisseur](expressroute-locations.md)
> * [Fournisseurs par emplacement](expressroute-locations-providers.md)


Les tableaux de cet article fournissent des informations sur la couverture géographique et les emplacements ExpressRoute, les fournisseurs de connectivité ExpressRoute et les intégrateurs système ExpressRoute.

> [!Note]
> Les régions Azure et les emplacements ExpressRoute sont deux concepts distincts et différents, il est essentiel de comprendre ce qui les distingue pour explorer la connectivité réseau hybride Azure. 
>
>

## <a name="azure-regions"></a>Régions Azure
Les régions Azure sont des centres de données globaux où se trouvent des ressources de stockage, de mise en réseau et de calcul Azure. Lors de la création d’une ressource Azure, un client doit sélectionner un emplacement de ressource. L’emplacement de la ressource détermine le centre de ressources Azure (ou zone de disponibilité) dans lequel la ressource est créée.

## <a name="expressroute-locations"></a>Emplacements ExpressRoute
Les emplacements ExpressRoute (parfois appelés emplacements d’homologation ou emplacements de rencontre) sont des installations de colocalisation où se trouvent des appareils Microsoft Enterprise Edge (MSEE). Les emplacements ExpressRoute sont le point d’entrée du réseau de Microsoft, et sont distribués à l’échelle mondiale, ce qui offre aux clients la possibilité de se connecter au réseau Microsoft dans le monde entier. Ces emplacements sont là où les partenaires ExpressRoute et les clients ExpressRoute Direct émettent des connexions croisées au réseau de Microsoft. En général, l’emplacement ExpressRoute n’a pas besoin de correspondre à la région Azure. Par exemple, un client peut créer un circuit ExpressRoute avec l’emplacement de ressource *USA Est*, dans l’emplacement de peering de *Seattle*.

Vous aurez accès aux services Azure dans toutes les régions au sein d’une région géopolitique si vous êtes connecté à au moins un emplacement ExpressRoute dans la région géopolitique. 

## <a name="locations"></a>Régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique
Le tableau ci-dessous fournit une carte des régions Azure vers des emplacements ExpressRoute au sein d’une région géopolitique.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Secteur public australien** | Australie Centre, Australie Centre 2 |Canberra, Canberra2 |
| **Europe** | France Centre, France Sud, Europe Nord, Europe Ouest, Royaume-Uni Ouest, Royaume-Uni Sud |Amsterdam, Amsterdam2, Copenhague, Dublin, Francfort, Genève, Londres, Londres2, Marseille, Milan, Munich, Newport(Pays de Galles), Paris, Stavanger, Stockholm, Zurich, Munich |
| **Amérique du Nord** | USA Est, USA Ouest, USA Est 2, USA Ouest 2, USA Centre, USA Centre Sud, USA Centre Nord, USA Centre-Ouest, Canada Centre, Canada Est |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montréal, Québec, Toronto |
| **Asie** | Asie Est, Asie Sud-Est |Hong Kong (R.A.S.), Jakarta, Kuala Lumpur, Singapour, Singapour2, Taipei |
| **Inde** | Inde Ouest, Inde Centre, Inde Sud |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Japon** | Japon Ouest, Japon Est |Osaka, Tokyo |
| **Océanie** | Australie Sud-Est, Australie Est |Auckland, Melbourne, Perth, Sydney | 
| **Corée du Sud** | Centre de la Corée, Corée du Sud |Busan, Séoul|
| **Émirats Arabes Unis** | Émirats arabes unis Centre, Émirats arabes unis Nord | Dubaï, Dubaï2 |
| **Afrique du Sud** | Afrique du Sud Ouest, Afrique du Sud Nord |Le Cap, Johannesburg |
| **Amérique du Sud** | Brésil Sud |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Régions Azure et limites géopolitiques pour les clouds nationaux
Le tableau ci-dessous fournit des informations sur les régions et les limites géopolitiques et des clouds nationaux.

| **Région géopolitique** | **Régions Azure** | **Emplacements ExpressRoute** |
| --- | --- | --- |
| **Cloud du gouvernement des États-Unis** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginie, US DoD Centre, US DoD Est  |Chicago, Dallas, New York, Seattle, Phoenix, San Antonio, Silicon Valley, Washington DC |
| **Chine Est** |Chine Est, Chine Est2 |Shanghai, Shanghai2 |
| **Chine Nord** |Chine Nord, Chine Nord2 |Beijing, Beijing2 |
| **Allemagne** |Allemagne centrale, Allemagne de l’est |Berlin, Francfort |

La connectivité entre les régions géopolitiques n’est pas prise en charge dans la référence ExpressRoute Standard. Vous devez activer le module complémentaire ExpressRoute Premium pour prendre en charge la connectivité globale. La connectivité à des environnements de cloud nationaux n’est pas prise en charge. En cas de besoin, vous pouvez collaborer avec votre fournisseur de connectivité.

## <a name="partners"></a>Fournisseurs de connectivité ExpressRoute

Le tableau suivant présente les emplacements de connectivité et les fournisseurs de services pour chaque emplacement. Si vous souhaitez afficher les fournisseurs de service et les lieux pour lesquels ils peuvent fournir le service, consultez la page [Lieux par fournisseur de services](expressroute-locations.md).

* Les **régions Azure locales** sont celles auxquelles [ExpressRoute Local](expressroute-faqs.md) de chaque emplacement de peering peut accéder. **n/a** indique que ExpressRoute Local n’est pas disponible à cet emplacement de peering.

* **Zone** fait référence aux [tarifs](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Production Azure commerciale mondiale
| **Lieu** | **Adresse** | **Zone** | **Régions Azure locales** | **ER Direct** | **Fournisseurs de services** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europe Ouest | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europe Ouest | 10 G, 100 G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/a | n/a | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/a | 10 G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Corée du Sud | n/a | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Centre de l’Australie | n/a | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Centre de l’Australie 2| n/a | CDC |
| **Le Cap** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Afrique du Sud Ouest | 10 G | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | Inde Sud | n/a | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | Inde Sud | n/a | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Centre-Nord des États-Unis | 10 G, 100 G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhague** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/a | 10 G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/a | 10 G, 100 G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Centre-USA Ouest | n/a | CoreSite, Megaport, Zayo |
| **Dubaï** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Émirats arabes unis Nord | n/a | Etisalat UAE |
| **Dubaï2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Émirats arabes unis Nord | n/a | du datamena, Megaport, Orixcom |
| **Dublin** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europe Nord | 10 G, 100 G | Colt, eir, Equinix, Interxion, Megaport |
| **Francfort** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Allemagne Centre-Ouest | n/a | DE-CIX, Interxion, Orange |
| **Genève** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Suisse Ouest | 10 G, 100 G | |
| **Hong Kong (R.A.S.)** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asie Est | n/a | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Jakarta** | Telkom Indonesia | 4 | n/a | 10 G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Afrique du Sud Nord | n/a | British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/a | n/a | TIME dotCom |
| **Las Vegas** | [Basculer LV](https://www.switch.com/las-vegas) | 1 | n/a | n/a | CenturyLink Cloud Connect, Megaport |
| **Londres** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Sud du Royaume-Uni | 10 G, 100 G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **Londres2** | [Telehouse North Two](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Sud du Royaume-Uni | 10 G, 100 G | IX REACH, Equinix, Telehouse – KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/a | n/a | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Transtelco, Zayo |
| **Marseille** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | France Sud | n/a | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Sud-Australie Est | 10 G, 100 G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/a | 10 G | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Milan** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/a | 10 G | |
| **Montréal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/a | n/a | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | Inde Ouest | n/a | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | Inde Ouest | n/a | Airtel, Sify, Vodafone Idea |
| **Munich** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | n/a | 10 G, 100 G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/a | 10 G, 100 G | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Packet, Zayo |
| **Newport (Nouvelle-Galles du Sud)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | Ouest du Royaume-Uni | n/a | British Telecom, Colt, Level 3 Communications, Next Generation Data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | OuJapon Est | n/a | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Paris** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | France Centre | n/a | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/a | 10 G | Megaport, NextDC |
| **Québec** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Est du Canada | n/a | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | États-Unis - partie centrale méridionale | 10 G, 100 G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brésil Sud | n/a | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | USA Ouest 2 | 10 G, 100 G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Séoul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Centre de la Corée | 10 G, 100 G | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | USA Ouest | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | USA Ouest | 10 G, 100 G | Colt, Coresite | 
| **Singapour** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Asie Sud-Est | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Asie Sud-Est | 10 G, 100 G | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | n/a | 10 G, 100 G | |
| **Stockholm** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/a | 10 G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australie Est | 10 G, 100 G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Taipei** | Chief Telecom | 2 | n/a | 10 G | Chief Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Japon Est | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Centre du Canada | 10 G, 100 G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | USA Est, USA Est 2 | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, InterCloud, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | USA Est, USA Est 2 | 10 G, 100 G | Coresite, Viasat, Zayo | 
| **Zurich** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | n/a | n/a | Intercloud, Interxion, Swisscom |

 **+** = bientôt disponible

### <a name="national-cloud-environments"></a>Environnements de cloud national

Les clouds nationaux Azure sont isolés les uns des autres et des productions Azure commerciales mondiales. ExpressRoute pour un cloud Azure ne peut pas se connecter aux régions Azure dans les autres clouds.

### <a name="us-government-cloud"></a>Cloud du gouvernement des États-Unis
| **Lieu** | **Adresse** | **Régions Azure locales**| **ER Direct** | **Fournisseurs de services** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/a | 10 G, 100 G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/a | 10 G, 100 G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/a | 10 G, 100 G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | Gouvernement des États-Unis – Arizona | n/a | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | Gouvernement des États-Unis – Texas | n/a | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/a | 10 G, 100 G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/a | n/a | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD Est, US Gov Virginie | 10 G, 100 G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Chine
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Beijing** |China Telecom |
| **Beijing2** | China Telecom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

Pour plus d’informations, consultez [ExpressRoute en Chine](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Allemagne
| **Lieu** | **Fournisseurs de services** |
| --- | --- |
| **Berlin** |e-shelter, Megaport+, T-Systems |
| **Francfort** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connectivité via des fournisseurs Exchange
Si votre fournisseur de connectivité ne se trouve pas dans la liste des sections précédentes, vous pouvez quand même créer une connexion.

* Vérifiez auprès de votre fournisseur de connectivité s’il est connecté à l’un des échanges dans le tableau ci-dessous. Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les fournisseurs d’échange. Plusieurs fournisseurs de connectivité sont déjà connectés à des échanges Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Demandez à votre fournisseur de connectivité d’étendre votre réseau à l’emplacement de peering de votre choix.
  * Vérifiez que votre fournisseur de connectivité étend votre connectivité avec une haute disponibilité pour éviter tout point de défaillance unique.
* Commandez un circuit ExpressRoute avec échange en tant que fournisseur de connectivité pour se connecter à Microsoft.
  * Pour définir la connectivité, procédez de la manière décrite dans [Création d’un circuit ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Connectivité via des opérateurs satellite
Si vous êtes à distance et que vous n’avez pas de connectivité par fibre ou si vous voulez explorer d’autres options de connectivité, vous pouvez consulter les opérateurs satellite suivants. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Connectivité via d’autres fournisseurs de services
| **Lieu** | **Microsoft Exchange** | **Fournisseurs de connectivité** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown Castle
| **Le Cap** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Francfort** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hambourg** | Equinix | Cinia |
| **Hong Kong (R.A.S.)** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londres** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montréal** | Cologix, Equinix | Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Paris** | Equinix | Proximus |
| **Québec** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapour** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Intégrateurs système ExpressRoute
L’activation de la connectivité privée pour l’adapter à vos besoins peut s’avérer difficile selon l’échelle de votre réseau. Vous pouvez faire appel à l’un des intégrateurs système figurant dans le tableau ci-dessous pour vous aider à intégrer ExpressRoute.

| **Continent** | **Intégrateurs système** |
| --- | --- |
| **Asie** |Avanade Inc., OneAs1a |
| **Australie** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europe** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **Amérique du Nord** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **Amérique du Sud** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur ExpressRoute, consultez la [FAQ sur ExpressRoute](expressroute-faqs.md).
* Assurez-vous que toutes les conditions préalables sont remplies. Consultez la page [Configuration requise pour ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Carte géographique"
