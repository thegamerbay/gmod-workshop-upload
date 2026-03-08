# Garry's Mod Workshop Upload Action

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/thegamerbay/gmod-workshop-upload)](https://github.com/thegamerbay/gmod-workshop-upload/releases)

> **Note:** This is a fork of the original action by [vurvdev/gmod-upload](https://github.com/vurvdev/gmod-upload).

A GitHub Action that automatically packs a Garry's Mod addon into a `.gma` file and uploads it directly to the Steam Workshop.

## 🚀 Features
* **Automated Packing:** Automatically packs your addon folder into a `.gma` file using a pure Lua implementation (`gma.lua`).
* **SteamCMD Integration:** Downloads, bootstraps, and caches SteamCMD for fast deployments on Linux.
* **Workshop Publishing:** Generates the required `workshop.vdf` manifest and uploads your item.
* **Metadata Updating:** Supports updating workshop item title, description, changelog, and visibility directly from your workflow.

## ⚠️ Prerequisites
1. **Linux Runner:** This action relies on the Linux version of SteamCMD. Your workflow job **must** run on `ubuntu-latest` or another Linux runner.
2. **Steam Account Requirements:** The account used in the `STEAM_USERNAME` and `STEAM_PASSWORD` secrets must meet the following criteria to allow automated, non-interactive uploads via SteamCMD:
   * **Game Ownership:** The account *must* own the game Garry's Mod in its library.
   * **Steam Guard Disabled:** To allow SteamCMD to log in and upload without prompting for a 2FA mobile code or email code, Steam Guard **must be fully disabled** on this account.
   * *(Recommended)* Since disabling Steam Guard on your main account is a massive security risk, it is highly advised to create a separate "bot" Steam account, configure Steam Family Sharing to give it access to Garry's Mod (or buy a second copy), disable Steam Guard on it, and add its developer rights to your mod project.

## 🛠 Usage

Create a workflow file in your mod repository (e.g., `.github/workflows/deploy.yml`):

### Basic Upload
```yaml
name: Deploy to Workshop

on:
  workflow_dispatch:

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Upload Addon to Steam Workshop
        uses: thegamerbay/gmod-workshop-upload@v1
        with:
          id: '1234567890' # Replace with your Workshop item ID
          changelog: 'Deployment via Github to latest changes'
        env:
          STEAM_USERNAME: ${{ secrets.STEAM_USERNAME }}
          STEAM_PASSWORD: ${{ secrets.STEAM_PASSWORD }}
```

### Advanced Update (Title, Description, Visibility)
```yaml
name: Deploy Addon Update

on:
  push:
    tags:
      - 'v*' # Triggers on version tags

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Update Addon on Steam Workshop
        uses: thegamerbay/gmod-workshop-upload@v1
        with:
          id: '1234567890'
          title: 'My Super Mod 2.0'
          description_file: 'workshop_description.txt' # Path from the repository root
          changelog: 'Release ${{ github.ref_name }}'
          previewfile: 'images/new_preview.jpg'
          visibility: '0'
        env:
          STEAM_USERNAME: ${{ secrets.STEAM_USERNAME }}
          STEAM_PASSWORD: ${{ secrets.STEAM_PASSWORD }}
```

## 📥 Inputs

| Input | Required | Default | Description |
| --- | --- | --- | --- |
| `id` | Yes | | Workshop item ID. Specify the ID of the item you are uploading or updating. |
| `config` | No | `addon.json` | Path of `addon.json`, or a JSON file to treat as such. |
| `changelog` | No | `""` | The changelog text to be posted on the Steam Workshop page. |
| `title` | No | | Update the title of the workshop item. |
| `description` | No | | Update the description of the workshop item. |
| `description_file` | No | | Local path to a text file containing the description (overrides `description` input if provided). |
| `previewfile` | No | | Local path to a preview image (e.g., `preview.jpg`). |
| `visibility` | No | | Visibility of the workshop item (`0` = Public, `1` = Friends Only, `2` = Private, `3` = Unlisted). |
| `metadata` | No | | Additional hidden metadata for the workshop item. |

## 🔐 Environment Variables

This action requires Steam credentials to authenticate and upload items via SteamCMD:

| Variable | Description |
| --- | --- |
| `STEAM_USERNAME` | Your Steam account username. Store this in your repository's **Secrets**. |
| `STEAM_PASSWORD` | Your Steam account password. Store this in your repository's **Secrets**. |