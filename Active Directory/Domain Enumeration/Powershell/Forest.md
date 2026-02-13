### ADModule
- Forest Enumeration
```powershell
# Get Detials about forest
Get-ADForest
Get-ADForest -Identity eurocorp.local # the forest must have trust relationship

# Get list of domains in the forest
(Get-ADForest).Domains
```
- List all Global Catalogs ([Global Catalog](../../AD%20Basics/Basics.md#Global%20Calalog))
```powershell
# Get all global calalog for the current forest 
Get-ADForest | Select-Object -ExpandProperty GlobalCatalogs
```
- Map trust of a forest
```powershell
Get-ADTrust -Filter 'msDS-TrustForestTrustInfo -ne "$null"' 
```
### Powerview
- Forest Enumeration
```powershell
# Get Detials about forest
Get-Forest
Get-Forest -Forest eurocorp.local

# Get list of domains in the forest
Get-NetForestDomain
Get-NetForestDomain -Forest eurocorp.local # the forest must have a trust relationship
```
- List all Global Catalogs ([Global Catalog](../../AD%20Basics/Basics.md#Global%20Calalog))
```powershell
Get-NetForestCalalog
Get-NetForestCalalog -Forest eurocorp.local # needs to have trust 
```
- Map trust of a forest
```powershell
Get-NetForestTrust
Get-NetForestTrust -Forest eurocorp.local
```