# Firka IPA Release Monitor

This repository contains a GitHub Actions workflow that automatically monitors for new IPA releases and updates a JSON file that can be used as an app source for various iOS sideloading tools.

## How it Works

The workflow (`monitor-releases.yml`) does the following:

1. Checks every minute for new releases in the QwIT-Development/app-legacy repository
2. When a new release containing an IPA file is detected:
   - Extracts release information (version, date, description, etc.)
   - Updates `apps.json` with the new release information
   - Adds the new release at the beginning of the apps array
   - Commits and pushes the changes

## Setup Instructions

### 1. Personal Access Token (PAT)

The workflow needs a PAT to push changes to this repository. To set this up:

1. Create a Personal Access Token:
   - Go to GitHub.com → Your Profile → Settings → Developer Settings
   - Select "Personal Access Tokens" → "Tokens (classic)"
   - Click "Generate new token (classic)"
   - Give it a descriptive name (e.g., "FIRKA_WORKFLOW_TOKEN")
   - Select the `repo` scope
   - Click "Generate token"
   - **Copy the token immediately** (you won't see it again!)

2. Add the token to repository secrets:
   - Go to this repository's Settings
   - Navigate to "Secrets and variables" → "Actions"
   - Click "New repository secret"
   - Name: `PAT_TOKEN`
   - Value: Paste your copied token
   - Click "Add secret"

### 2. Configuration

The workflow is configured to:
- Monitor: `QwIT-Development/app-legacy` for new releases
- Update: `apps.json` in this repository
- Schedule: Checks every minute

To modify these settings, edit `.github/workflows/monitor-releases.yml`:
- Change the repository URL in the curl command to monitor a different repository
- Adjust the cron schedule (`* * * * *`) to change check frequency
- Modify the JSON template in the "Update apps.json" step to change the app information format

## JSON Format

The `apps.json` file maintains the following structure:
```json
{
    "name": "Firka",
    "identifier": "app.firka.legacy",
    "sourceURL": "https://git.spitkov.hu/spitkov/firkaipa",
    "apps": [
        {
            "name": "Firka",
            "bundleIdentifier": "app.firka.legacy",
            "developerName": "QwIT-Development",
            "version": "v5.1.5",
            "versionDate": "2025-02-28T16:03:26+00:00",
            "versionDescription": "Release notes...",
            "downloadURL": "https://github.com/..../firkaapp.ipa",
            "localizedDescription": "Firka App",
            "iconURL": "https://github.com/.../ic_android.png",
            "tintColor": "018084",
            "isLanZouCloud": 0,
            "size": 34.37,
            "type": 1
        }
    ]
}
```

## Manual Trigger

While the workflow runs automatically every minute, you can still trigger it manually:
1. Go to the "Actions" tab
2. Select "Monitor IPA Repository Releases"
3. Click "Run workflow"

## Troubleshooting

If the workflow fails:
1. Check if the PAT_TOKEN is properly set in repository secrets
2. Verify the token hasn't expired
3. Ensure the token has the correct permissions (repo scope)
4. Check the workflow logs in the Actions tab for specific error messages
5. Verify that the release contains a file with the .ipa extension
6. Check GitHub's rate limits - the workflow makes API calls every minute 