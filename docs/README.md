<img src="assets/company_logos/logo_lochem.jpg" width="20%" height="20%" align="middle" style="margin:0px 115px"/>
<img src="assets/company_logos/aurai_logo_black.png" width="20%" height="20%" align="middle" style="margin:0px 115px"/>

<hr>

# Introductie: Project Woonmonitor
*Auteur: Marc Blomvliet - Aurai*</br>
Dit project is ontstaan vanwege een rapportageverplichting vanuit het Programma Woningbouw voor periode 2022-2030. Het is noodzakelijk om de afdeling *'Ruimte'* van Gemeente Lochem te voorzien van inzichten in de omvang, samenstelling en benutting van de woningvoorraad in de Gemeente Lochem.

# Databricks
Databricks is in basis gebruikt als Datalakehouse. Echter is er ook voor gekozen om rechstreeks de visualisaties voor de woonmonitor in Databricks te implementeren. Het is natuurlijk ook mogelijk om dit in de toekomst te migreren naar PowerBI of Tableau (*indien dit een wens mocht zijn*), dit werkt prima in combinate met Databricks. 

Vanuit Azure Datafactory worden diverse notebooks in Databricks gebruikt in de pipelines, om vervolgens de databronnen te ingesten in het Datalakehouse van Gemeente Lochem.
Voorbeelden van deze databronnen:
- BRP
- BAG
- WOZ
- BRK 
- CBS

# DBT

## Databricks connector

# Databricks Dashboards

# Azure Datafactory (ADF)
