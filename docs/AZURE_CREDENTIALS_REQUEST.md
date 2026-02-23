# Azure Credentials & Permissions Request

## What We Need from You (Microsoft Employee)

Hey! To get the sermon rating MVP running on Azure, we need you to set up a few things using your Microsoft employee access. Here's exactly what's needed.

---

## 1. Azure Subscription + Resource Group

- **An Azure subscription** we can both use (your MSDN/Visual Studio Enterprise sub is perfect — comes with monthly credits)
- **A shared resource group** — something like `rg-sermon-rating-dev` in a region close to us (e.g., `southcentralus` for Texas)

```bash
az group create --name rg-sermon-rating-dev --location southcentralus
```

## 2. Invite Brandon as a Contributor

Add Brandon's Microsoft account (or personal email) as a **Contributor** on the resource group so he can create/manage resources but not touch IAM:

```bash
az role assignment create \
  --assignee <brandon-email> \
  --role "Contributor" \
  --scope /subscriptions/<sub-id>/resourceGroups/rg-sermon-rating-dev
```

## 3. Azure OpenAI Access

This is the most important one — Azure OpenAI requires an approved subscription.

- **Deploy an Azure OpenAI resource** in the resource group
- **Deploy a GPT-4o model** (needed for sermon analysis and scoring)
- **Deploy a Whisper model** (optional — we can use Azure AI Speech instead)
- Share the **endpoint URL** and **API key** (or we use RBAC — see below)

```bash
# Create the OpenAI resource
az cognitiveservices account create \
  --name sermon-openai-dev \
  --resource-group rg-sermon-rating-dev \
  --kind OpenAI \
  --sku S0 \
  --location southcentralus

# Deploy GPT-4o
az cognitiveservices account deployment create \
  --name sermon-openai-dev \
  --resource-group rg-sermon-rating-dev \
  --deployment-name gpt-4o \
  --model-name gpt-4o \
  --model-version "2024-08-06" \
  --model-format OpenAI \
  --sku-capacity 30 \
  --sku-name Standard
```

## 4. Azure AI Speech Service

For sermon transcription with timestamps:

```bash
az cognitiveservices account create \
  --name sermon-speech-dev \
  --resource-group rg-sermon-rating-dev \
  --kind SpeechServices \
  --sku S0 \
  --location southcentralus
```

## 5. What Brandon Needs Back

Once you've set things up, share these values (DM or secure channel — not in public chat):

| Item | How to Get It |
|------|--------------|
| **Subscription ID** | `az account show --query id -o tsv` |
| **Tenant ID** | `az account show --query tenantId -o tsv` |
| **Resource Group** | Name you created (e.g., `rg-sermon-rating-dev`) |
| **OpenAI Endpoint** | `az cognitiveservices account show -n sermon-openai-dev -g rg-sermon-rating-dev --query properties.endpoint -o tsv` |
| **OpenAI Key** | `az cognitiveservices account keys list -n sermon-openai-dev -g rg-sermon-rating-dev --query key1 -o tsv` |
| **Speech Key** | `az cognitiveservices account keys list -n sermon-speech-dev -g rg-sermon-rating-dev --query key1 -o tsv` |
| **Speech Region** | `southcentralus` (or whatever region you chose) |

## 6. Summary of RBAC Roles Needed

| Who | Scope | Role | Why |
|-----|-------|------|-----|
| **Friend (owner)** | Subscription or RG | **Owner** | Full control, can assign roles |
| **Brandon** | Resource Group | **Contributor** | Create/manage all resources |
| **Brandon** | OpenAI resource | **Cognitive Services OpenAI User** | Call the OpenAI API |
| **Brandon** | Speech resource | **Cognitive Services User** | Call the Speech API |
| **App (later)** | Various | Managed Identity + scoped roles | Production auth (no keys) |

## 7. Estimated Cost Impact

All MVP services run on consumption/serverless tiers:

| Service | Estimated Cost |
|---------|---------------|
| Azure Functions | ~$0 (consumption plan free grant) |
| Cosmos DB Serverless | ~$0-5/mo at dev scale |
| Blob Storage | ~$1-2/mo |
| AI Speech | ~$1/hr of audio |
| Azure OpenAI GPT-4o | ~$0.50-2/sermon |
| Static Web Apps | Free tier |
| **Total during dev** | **~$20-50/mo** |

MSDN Enterprise subscription comes with $150/mo credits — more than enough.

---

## Quick Start (After Credentials)

Once Brandon has the credentials, the setup on his end is:

```bash
# Login
az login

# Set subscription
az account set --subscription <sub-id>

# Verify access
az group show --name rg-sermon-rating-dev

# Store keys locally
az keyvault secret set ... # (or .env file for local dev)
```

Then we start building the Bicep templates and Durable Functions pipeline. 🚀
