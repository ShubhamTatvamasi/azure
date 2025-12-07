# azure

https://azure.microsoft.com/en-in/pricing/calculator/

https://github.com/Azure/azure-quickstart-templates

https://aka.ms/azpipelines-parallelism-request

https://azure.microsoft.com/free/

Login to Azure:
```bash
az login
```

List account subscriptions:
```bash
az account list -o table
```

List all images from Canonical:
```bash
az vm image list --publisher Canonical -o table
```

List Azure VMs:
```bash
az vm list
```

Create a new VM:
```bash
az vm create
```

