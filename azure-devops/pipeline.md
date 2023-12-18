# Build Pipeline

### Sparse Checkout

```yml
steps:
  - checkout: none
    clean: true
    persistCredentials: true

  - task: CmdLine@2
    displayName: 'Check out source'
    inputs:
      script: |
        git sparse-checkout init --cone
        git sparse-checkout set Source VersionControl
        git remote add origin https://$(System.AccessToken)@dev.azure.com/{origanization}/EL%%20Frontend/_git/{repo}
        git fetch https://$(System.AccessToken)@dev.azure.com/{origanization}/EL%%20Frontend/_git/{repo} --progress --verbose --depth=1 $(Build.SourceBranch):$(Build.SourceBranch)
        git pull https://$(System.AccessToken)@dev.azure.com/{origanization}/EL%%20Frontend/_git/{repo} --progress --verbose --allow-unrelated-histories $(Build.SourceBranch):$(Build.SourceBranch)
```