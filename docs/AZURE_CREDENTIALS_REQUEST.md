# Azure Credentials Request

## What We Need From You

We'll create all the Azure infrastructure ourselves via Bicep (IaC). We just need access.

### 1. An Azure Subscription We Can Use

Your MSDN/Visual Studio Enterprise sub works great ($150/mo free credits — more than enough for dev).

Share these two values:
- **Subscription ID** — `az account show --query id -o tsv`
- **Tenant ID** — `az account show --query tenantId -o tsv`

### 2. Invite Brandon as a Contributor

Add Brandon's email as **Contributor** on the subscription (or a resource group you create — we can work with either):

```bash
az role assignment create \
  --assignee <brandon-email> \
  --role "Contributor" \
  --scope /subscriptions/<sub-id>
```

That's it for permissions. Contributor lets us create resource groups, deploy services, and manage everything via code.

### 3. Confirm Azure OpenAI Access

Azure OpenAI requires an approved subscription. Most MSDN Enterprise subs have it. Quick check:

```bash
az cognitiveservices account list-kinds | grep OpenAI
```

If it's not enabled, request access at: https://aka.ms/oai/access

### What We'll Create (You Don't Have To)

Once we have Contributor access, our Bicep templates handle everything:
- Resource Group (`rg-sermon-rating-dev`)
- Azure OpenAI + GPT-4o deployment
- Azure AI Speech Service
- Cosmos DB (serverless)
- Azure Functions
- Blob Storage
- Key Vault
- Static Web Apps

### Estimated Cost

~$20-50/mo during dev. Well within MSDN credits.

### How to Share Credentials Safely

DM Brandon directly — not in a public channel:
- Subscription ID
- Tenant ID
- Confirm Contributor role assigned
- Confirm Azure OpenAI is available on the sub
