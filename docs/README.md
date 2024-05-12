<img src="assets/company_logos/logo_lochem.jpg" width="20%" height="20%" align="middle" style="margin:0px 115px"/>
<img src="assets/company_logos/aurai_logo_black.png" width="20%" height="20%" align="middle" style="margin:0px 115px"/>

<hr>

# 1. Introductie: Project Woonmonitor
*Auteur: Marc Blomvliet - Aurai*</br>
Welkom!
Dit project is ontstaan vanwege een rapportageverplichting vanuit het Programma Woningbouw voor periode 2022-2030. Het is noodzakelijk om de afdeling *'Ruimte'* van Gemeente Lochem te voorzien van inzichten in de omvang, samenstelling en benutting van de woningvoorraad in de Gemeente Lochem.
Van Aurai hebben Fokke Dijkstra, Marc Blomvliet en Stefan Buhrmann aan dit project gewerkt.
Alie Vreemann en Guido Quick zijn de opdrachtgevers/stakeholders vanuit Gemeente Lochem.

# 2. Vervolg stappen
De woonmonitor is nu gebaseerd op **statische data**. Met de inzet van **Azure Data Factory** wordt die data in het Datalakehouse van Lochem **Databricks** de ingestie uitgevoerd.
Voor de toekomst moeten deze Dashboards gebasseerd zijn op **dynamische data**, dus moeten de pipelines in ADF worden ingericht op bijvoorbeeld API's/Databases van de BAG, WOZ, BRP, etc.. 


# 3. Overdracht document - Data Engineer

## Azure Datafactory (ADF)

<img src="assets/images/Architecture.png" width="70%" height="70%" style="margin:0px 115px"/>


## CBS Data verversen
Ook data vanuit het CBS is statisch, zie het volgende document om de link op te halen van de destbreffende datasets om ze opnieuw te downloaden in de blobstorage. (*cbs_bronnen_URLs.txt*) 
```
├── lochemlakehouseblob
    ├── stage-basisregistraties
        ├── CBS_2024
            ├── cbs_bronnen_URLs.txt
```

Of hier de Inhoud *cbs_bronnen_URLs.txt*:
```
Bevolking__geslacht__leeftijd__regio_per_leeftijd.csv = 
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/03759ned/table?dl=92B17

Bevolking__geslacht__leeftijd__regio.csv = 
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/03759ned/table?dl=92B40

Bevolkingsontwikkeling__regio_per_maand.csv = 
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/37230ned/table?dl=92B1A
Met maanden:
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/37230ned/table?dl=93BE1

Verhuisde_personen_per_maand.csv = 
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/84547NED/table?dl=930C9

Energieverbruik_particuliere_woningen =
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/81528NED/table?dl=92E1F

Zonnestroom__vermogen__bedrijf_won_regio.csv =
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/85005NED/table?dl=9340C

Tussen_gemeenten_verhuisde_personen.csv =
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/81734NED/table?dl=935D5

Tussen_gemeenten_verhuisde_personen_vestiging_lochem.csv = 
https://opendata.cbs.nl/statline/#/CBS/nl/dataset/81734NED/table?dl=935D5
```

LET OP: Wees zorgvuldig met de naamgeving, en behoud dus ten alle tijde dezelfde namen na het opnieuw downloaden van de (*.csv*) CBS datasets.

## Databricks
Databricks is in basis gebruikt als Datalakehouse. Echter is er ook voor gekozen om rechstreeks de visualisaties(Dashboards) voor de woonmonitor in Databricks te implementeren. In 2023 werdt dit gedaan met de eerste versie van Databricks dashboards (Legacy). In 2024 is er overgestapt naar de nieuwste versie 'Lakeview' van Databricks.
Het is natuurlijk ook mogelijk om dit in de toekomst te migreren naar PowerBI of Tableau (*indien dit een wens mocht zijn*), dit werkt prima in combinate met Databricks. 

Vanuit Azure Datafactory worden diverse notebooks in Databricks gebruikt in de pipelines, om vervolgens de databronnen te ingesten in het Datalakehouse van Gemeente Lochem.</br>
Deze notebooks zijn te vinden in de Productie Databricks workspace **lochemdatalakehouse**. Onder *Workspace* -> *Shared* -> *dwh-processing* -> *Ingestie*.

Alle databronnen die worden opgenomen:
- BRP
- BAG
- WOZ
- BRK 
- CBS

De schemas die in het Datalakehouse dan ontstaan:
- ods_basisregistraties
- ods_cbs

### Databricks - Start de Warehouse
Na 60 minuten van inactiviteit gaat de compute/warehouse op stop, om onnodige kosten te ontlopen.
Om de dashboards weer te refreshen en of te gebruiken, dien je de Warehouse binnen Databricks opnieuw op te starten.
Of om als Data Engineer Querys te kunnen schrijven binnen Databricks of nieuwe DBT models te executen.
Let op: Dit kan soms ~2 minuten duren.

<img src="assets/images/start_Warehouse.png" width="90%" height="90%" style="margin:0px 115px"/>

### Databricks Dashboards - Overview
In Databricks zijn alle Dashboards te vinden van de *Woonmonitor Lochem*, de woonmonitor is onderverdeeld in **5** verschillende dashboards. In de afbeelding hieronder is de gebruikers interface te zien van Databricks, links in het paneel is het mogelijk om via de knop **Dashboards** alle dashboards te bekijken (Indien je geautoriseerd bent).

- Huishoudens
- Verhuisstroom
- Duurzaamheid
- Woningen
- Inwoners

<img src="assets/images/dashboards_databricks.png" width="90%" height="90%" style="margin:0px 115px"/>

Zie hieronder een preview van hoe een dashboard eruit ziet in Databricks (*Dashboard: Verhuisstromen*):
<img src="assets/images/dashboard_verhuisstromen.png" width="90%" height="90%" style="margin:0px 115px"/>


## DBT

### Models en DataMarts
Voor nu bestaan er **7** schemas: **dwh_burgerzaken** en **dwh_ruimte**. En in totaal **22** models:
- **dwh_burgerzaken**
    - bevolkingsgroei
    - bevolkingsopbouw
    - brp
    - geslachtsopbouw
- **dwh_duurzaamheid**
    - aardgas
    - elektriciteit
    - lochem_nederland
    - opgesteld_vermogen_lochem
    - opgesteld_vermogen
- **dwh_ruimte**
    - bag_num_recent
    - verblijfsobjecten
    - woningen_lochem
    - woningtypes_huishoudtypes
    - woz
- **dwh_verhuisstromen**
    - gevestigd
    - verhuizingen
    - vertrokken

> Aantal Datamarts

- **MARTS**
    - huishouden
    - huishoudtypes_per_woonplaats
    - huishoudtypes
    - woningen_lochem
    - woningtypes_en_huishoudtypes


### Databricks connector
Alle DBT models staan in de GIT Repository *DWH-DBT-PRODUCTIE* en vervolgens onder *models*.
De repository *DWH-DBT* met de bijbehorende Databricks workspace *Lochem_Lakehouse*, zijn gebruikt destijds voor development.
Het is ook mogelijk om ipv DBT, direct in Databricks SQL querys (ipv. views) klaar te zetten voor Dashboards uiteraard.

<img src="assets/images/folder_structure_DBT_repo.png" width="90%" height="90%" style="margin:0px 115px"/>

Om veranderingen aan te brengen in de views of om bijvoorbeeld nieuwe views of schemas te creëren, dien je de lokaal aanpassingen door te voeren in de *main* branch van de GIT repository **DWH-DBT-PRODUCTIE**. Daarnaast is het noodzakelijk om DBT-core te hebben en de connectie met Databricks te hebben (Databricks connector).

**Opzetten van DBT Core:** </br>
Maak een virtual environment:
> python3 -m venv dbt_venv

Activeer je environment:
> source dbt_venv/bin/activate

Install dbt core met databricks-connector:
> pip3 install dbt-databricks

Verifieër:
> dbt --version

> dbt debug

Zorg ervoor dat je in je lokale User root folder een .dbt folder hebt aangemaakt met daarin de file: 
profiles.yml

Met als volgende er in:
- DWH:
  - target: dev
  - outputs:
    - dev:
      - type: databricks
      - catalog: hive_metastore
      - schema: dwh
      - host: ****
      - http_path: /sql/1.0/warehouses/1e0a32dc6d62a9dc
      - token: **** 
      - threads: 1  

**Vind de host string**
<img src="assets/images/host_databricks_profiles.png" width="90%" height="90%" style="margin:0px 115px"/>

Zodra je op '*Starter Warehouse*' hebt geklikt, kies dan voor *Connection details*.
De string voor host staat onder *Server hostname* en begint met **adb**.
Vul deze nu in jouw profiles.yml


**Maak een nieuwe token aan**
</br>
</br>
<img src="assets/images/token_databricks.png" width="30%" height="30%" style="margin:0px 115px"/> 

Zodra je op jouw '*emailadres/account*' en vervolgens op '*User Settings*' hebt geklikt, kies dan voor '*Generate new token*'.
En kopieer jouw token in de profiles.yml

Indien er vragen zijn over het configureren van DBT icm. Databricks, neem dan contact op met Aurai.
Het kan namelijk complex zijn, echter is dit maar eenmalig nodig om dit te configureren op je device.



