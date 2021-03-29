<p align="center">
  <a href="https://github.com/github-developer/upload-azure-blob/actions"><img alt="typescript-action status" src="https://github.com/github-developer/upload-azure-blob/workflows/build-test/badge.svg"></a>
</p>

# Upload Blob

This is an example GitHub action built in TypeScript that uploads file(s) to [Azure Blob Storage](https://azure.microsoft.com/en-us/services/storage/blobs/), a "massively scalable and secure object storage for cloud-native workloads, archives, data lakes, high-performance computing, and machine learning".  

Best viewed together with the accompanying [blog post](TODO).

For more about GitHub Actions, refer to [the documentation](https://docs.github.com/en/actions/creating-actions).

## Pre-reqs

- Use an existing Azure account or [sign up](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) for a free account
- Make sure you have access to a new or existing resource group, storage account, and container – for example, by following the first few steps of [this quickstart](https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli)
- Then, [configure credentials](https://github.com/Azure/login#configure-deployment-credentials) that can write Azure Storage containers and blobs, like a service principal with the ["Storage Blob Data Contributor"](https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) role.
```
az ad sp create-for-rbac 
  --name $SP_NAME
  --sdk-auth
  --role "Storage Blob Data Contributor"
  --scopes /subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP_NAME
```
- Finally, store these credentials as a secret named `AZURE_CREDENTIALS`

## Usage

Simple example:

```yml
# GitHub Actions repository workflow file, e.g .github/workflows/upload.yml

# ...
# previous steps to choose a runner type, prepare files, etc
# ...

- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}

# Upload `.jpg`s to Azure Blob Storage
- name: Upload all JPGs to Azure Blob Storage
  id: upload
  uses: github-developer/upload-azure-blob@v1
  with:
    account: octodex
    destination: octocats
    source: '**/*.jpg'

# Print out the urls to uploaded files
- name: Print URLs
  run: echo $URLS # { ["filename":"hulatocat.jpg","url":"https://octodex.blob.core.windows.net/octocats/hulatocat.jpg"] }
  env:
    URLS: ${{ steps.upload.outputs.urls }}

# ...
```

### Inputs

- `account` (required): Storage account name, e.g. `mystorageaccount`
- `destination` (required): Name of container to upload blob to, e.g. `$web` to upload a static website.
- `source` (required): Path to file(s) to upload to `destination`, e.g. `.` to upload all files in the current directory. Supports globbing, e.g. `images/**.jpg`. For more information, please refer to https://www.npmjs.com/package/glob.

### Outputs

- `urls`: data structure with names and urls to uploaded files

## License

[MIT](LICENSE)

## Contributing

Pull requests are welcome! See CONTRIBUTING.md for more.
