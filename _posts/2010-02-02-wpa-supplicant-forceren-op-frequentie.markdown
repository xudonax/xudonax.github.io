---
layout: post
title: "WPA Supplicant forceren op frequentie"
date: 2010-02-02 22:02:57 +0200
categories: tech laptop wifi linux wpa-supplicant
author: Fluffy
---

Ik heb in mijn Eee PC een andere netwerkkaart geplaatst, namelijk de Atheros AR5008. Het grootste voordeel hiervan tegenover de meegeleverde RaLink RT2800 is de ondersteuning van de 5GHz standaard. Nu er op de Hogeschool Utrecht ook een 5GHz netwerk is uitgerold, is dit een nog groter voordeel. Dit omdat het grootste gedeelte van de laptop's en de mobiele telefoons enkel de 2.4GHz frequentie kan gebruiken. Hierdoor is de 5GHz band dus zo goed als uitgestorven. Zeker nu dit ook op 802.11n snelheden gaat (maximaal 600Megabit/s) is de maximale snelheid hierop vele male hoger dan op de 2.4GHz band.

<!--more-->

Het nadeel is echter dat zowel de 2.4GHz als de 5GHz dezelfde netwerknaam (SSID) gebruiken, waardoor je niet makkelijk kunt forceren om het 5GHz netwerk te gebruiken. Dit is ook lastig omdat de 2.4GHz signalen wat sterker zijn dan de 5GHz signalen, waardoor alle machines automatisch de 2.4GHz netwerken zullen gebruiken. In principe zou dit nog geen groot probleem zijn, als er tenminste één BSSID zou zijn voor het 5GHz netwerk. Dit is ook al niet het geval. Voor zover ik weet is het niet mogelijk om Windows met de standaard configuratie te forceren naar een 5GHz netwerk als er ook een 2.4GHz netwerk is met dezelfde naam en signaalsterkte. Van Mac OS X weet ik niet of dit mogelijk is omdat ik alleen een PowerMac G5 heb met een 2.4GHz WiFi kaart.

Onder Linux is dit echter wel mogelijk. Tenminste, als je de laatste WPA Supplicant draait. Dit is ofwel versie 0.7.1 of de laatste GIT release. Beide zijn nog niet stabiel, maar de GIT release heeft een belangrijke fix als je in een omgeving zit met veel accesspoints met hetzelfde netwerk, namelijk:

> Try to avoid some unnecessary roaming
>
> When multiple APs are present in scan results with similar signal strength, wpa_supplicant may end up bounching between them frequently whenever new scan results are available (e.g., due to periodic scans requested by NetworkManager). This can result in unnecessary roaming and in case of the current cfg80211 version, to frequent network disconnections.
>
> Do not request a roam if the current BSS is still present in the scan results and the selected BSS is in the same ESS and has only a slighly stronger signal strength.

Bron: [WPA Supplicant GIT commit **48563d86b21070c3db276d68af34f76ac84acbf1**](http://hostap.epitest.fi/gitweb/gitweb.cgi?p=hostap.git;a=commit;h=48563d86b21070c3db276d68af34f76ac84acbf1)

Simpel gezegd betekent dit dat er niet van netwerk veranderd wordt als er slechts een klein verschil in kwaliteit is tussen het huidige netwerk en een ander netwerk. Omdat je op de Hogeschool Utrecht vaak 15 of meer access points ziet per frequentie, werd er dus veel en vaak van netwerk gewijzigd. Dit zorgt ervoor dat je tijdelijk geen verbinding meer hebt, en verpest het internetgebruik.

De reden om de development serie van WPA Supplicant te gebruiken in plaats van de stabiele versie is dat de mogelijkheid om een frequentie te forceren pas sinds versie 0.7.0 erin zit. Nu moet ik wel zeggen dat makkelijk anders is, maar de configuratie is in ieder geval duidelijk. Je moet namelijk iedere frequentie waarop je wilt verbinden opgeven. Aangezien dit 19 kanalen zijn voor de 5GHz band, is dit een wat langere configuratieoptie. Een voorbeeld netwerkconfiguratie voor WPA Supplicant welke de 5GHz band forceert:

    network={
        ssid="ADSL-WiFi"
        key_mgmt=NONE
        scan_freq=5180 5200 5220 5240 5260 5280 5300 5320 5500 5520 5540 5560 5580 5600 5620 5640 5660 5680 5700
    }

Zoals te zien is, is iedere mogelijke frequentie in de 5GHz band geforceerd voor dit netwerk. Voor de rest is dit een standaard netwerkconfiguratie. De SSID is ingesteld op **ADSL-WiFi** en er is geen beveiligingssleutel. Geen veilige setup, maar het is dan slechts een voorbeeld.
