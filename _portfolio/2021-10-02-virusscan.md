---
layout: post
author-id: sbasischeck
title: Virusscan
feature-img: "/assets/img/background/search-map.jpeg"
img: "/assets/img/background/search-map.jpeg"
date: 2021-10-02 14:00:00  +0200
tags: [virusscan, vsapworksphere]
---

# Virenscanner einrichten auf S4HANA

du kannst es bitte nochmal mit der CLAMSAP Konfiguration versuchen Deinen Import zu machen.

Wir haben auf die Doku in Github gewechselt und sind eine Version höher gekommen.
Damit lief es dann besser als mit der Suse Doku.

Immer noch ist kurz sehr viel Last auf den Workprozessen nach dem Anstarten. Aber das System reagiert danach weiter.

Getestet haben wir es mit:  
Report: RSVSCANTEST

![RSVSCANTEST](/assets/img/vsapworkspace/rsvscantest.png)

Results

![RESULTS](/assets/img/vsapworkspace/results.png)

ZusatzInfo:  
>Die zu scannenden Files landen in /tmp  

(Einzige Chance momentan zu sehen wenn es in Richtung CLAMSAP geschickt wird)
 

## Installation & Configuration
Architekturbild
https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/202009.000/en-US/4e2606bdc61920cee10000000a42189c.html

https://help.sap.com/viewer/3cd5ac93e7ec4690bd804f0d23fed9da/202009.000/en-US/4df582ed472d41c4e10000000a42189c.html


- Install CLAMSAP virus scanner on OS  
  CLAMSAP is included into SuSe Linux packages

- Trx: VSCANGROUP  
  Define Scanner Group  
  
  ![Scanner Group](/assets/img/vsapworkspace/scan_group.png)
  
- Trx: VSCAN  
  Define Virus Scan Provider  
  Provider Type: Adapter  
  Provider Name  
  Scanner Group: s.o.  
  Adapter Path: libclamsap.so  
  
  ![VSCAN Provider Definition](/assets/img/vsapworkspace/scan_provider_def.png)
  
  VSAN Provider Data  
  
  ![VSCAN Provider Definition Data](/assets/img/vsapworkspace/scan_provider_data.png)
  
  Parameters  
  
  ![VSCAN Provider Definition Supported Parameters](/assets/img/vsapworkspace/scan_provider_params.png)

- Test  
  Trx: VSCANTEST  
  Under Scann er options activate option Virus Scan   Provider and choose installed one  
  
  ![SSCAN Test Run](/assets/img/vsapworkspace/scan_test_run.png)

- Run Test  
  Testvirus found:  
  
  ![Test Results](/assets/img/vsapworkspace/scan_test_results.png)
