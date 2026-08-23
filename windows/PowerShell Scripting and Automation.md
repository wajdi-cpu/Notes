# create a manifest file

```js
PS C:\htb> New-ModuleManifest -Path C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon\quick-recon.psd1 -PassThru
```

# Create Script File

```js
PS C:\htb> ni quick-recon.psm1 -ItemType File Directory: C:\Users\MTanaka\Documents\WindowsPowerShell\Modules\quick-recon
```

# Exclude From Export

```js
Export-ModuleMember
```

# Export Specific Functions and Variables

```js
Export-ModuleMember -Function Get-Recon -Variable Hostname
```

