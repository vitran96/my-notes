It is not possible to sign a zip file.

The workaround this is to create catalog inside the file. However, that won't work with every AV

## Snippet on Windows

Tested with [[Window OS]]

```powershell
New-FileCatalog -Path <FILE> -CatalogFilePath <CATALOG> -CatalogVersion 2.0
```