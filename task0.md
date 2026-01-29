# Esercizio Dario
## Problema principale scaricare diverse centinaia di file mp4 da bucket s3.
### Soluzione trovare un modo per far si che si scaricano questi file con un singolo comando tramite aws cli.

# Passaggi 
- Scaricare aws da sito ufficiale.
- Dare tutti next e andare avanti fino all'installazione.
- Creare una nuova cartella(aws-mp4-video)
# comandi:
- Controllare l'installazione su cmd o powershell con comando: # aws --version 
- Verificare che le chiavi funzionino comando: # aws sts get-caller-identity
- Verifica contenuto cartella: # aws s3 ls s3://nome-bucket/cartella1/cartella2/ --recursive
- aws configure
- Inserire nei campi l'access key e la secret key
- Inserire la regione in questo caso uso: eu-south-1(Milano)

# Soluzione
$bucket = "preventivi-digitali-sdlc"
$dest   = "$env:USERPROFILE\Desktop\s3-download-flat"
$log    = "$env:USERPROFILE\Desktop\s3_download_log.txt"

# Pulisci
Remove-Item $dest -Recurse -Force -ErrorAction SilentlyContinue
New-Item -ItemType Directory -Path $dest -Force | Out-Null
Remove-Item $log -Force -ErrorAction SilentlyContinue

# Lista paginata + filtro "video"
$keys = New-Object System.Collections.Generic.List[string]
$token = $null

do {
    $resp = if ($token) {
        aws s3api list-objects-v2 --bucket $bucket --continuation-token $token --output json | ConvertFrom-Json
    } else {
        aws s3api list-objects-v2 --bucket $bucket --output json | ConvertFrom-Json
    }

    if ($resp.Contents) {
        foreach ($obj in $resp.Contents) {
            $k = $obj.Key
            if ($k -and -not $k.EndsWith("/") -and $k -match "video") {
                $keys.Add($k)
            }
        }
    }

    $token = $resp.NextContinuationToken
} while ($resp.IsTruncated -eq $true)

"Video trovati (match 'video'): $($keys.Count)" | Tee-Object -FilePath $log -Append

# Download flat + rinomina duplicati
$seen = @{}
$done = 0
$total = $keys.Count

foreach ($key in $keys) {
    $done++

    $base  = Split-Path $key -Leaf

    # Se manca estensione, aggiungi .mp4 (se non la vuoi, elimina queste 2 righe)
    if (-not [IO.Path]::GetExtension($base)) { $base = "$base.mp4" }

    $final = $base
    if ($seen.ContainsKey($base)) {
        $seen[$base]++
        $name = [IO.Path]::GetFileNameWithoutExtension($base)
        $ext  = [IO.Path]::GetExtension($base)
        $final = "{0}_DUP{1:000}{2}" -f $name, $seen[$base], $ext
    } else {
        $seen[$base] = 0
    }

    $target = Join-Path $dest $final
    $msg = "[$done/$total] DOWNLOADING: s3://$bucket/$key -> $final"
    $msg | Tee-Object -FilePath $log -Append

    aws s3 cp "s3://$bucket/$key" "$target" --no-progress --only-show-errors
}

"`nFINE. File locali: $((Get-ChildItem $dest -File | Measure-Object).Count)" | Tee-Object -FilePath $log -Append




# riferimenti youtube e IA 
