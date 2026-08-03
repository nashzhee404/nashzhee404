# Github Automation Tool
![Python](https://img.shields.io/badge/language-python-blue.svg) ![Platform](https://img.shields.io/badge/platform-windows%20%7C%20macos%20%7C%20linux-lightgrey.svg)

A professional, high-performance command-line interface (CLI) suite designed for comprehensive GitHub account and repository management. This tool automates complex tasks such as bulk repository creation, management of forks, gist administration, and account analytics through a standardized interactive workflow.

## Features
- **Multi-Account Support**: Securely manage and toggle between multiple GitHub profiles stored in `settings.ini`.
- **Universal Target Selector**: Deep-integrated selection logic offering Single, Select, Bulk, and File-based targeting for all tools. Select/Bulk always fetch and display **all** items from the GitHub API (repos, gists, starred, following, forks) or from your local machine (folders, files, git repos).
- **Advanced Repository Logic**: Mass-toggle visibility (Public/Private), sync descriptions, manage topics, and inject licenses in bulk.
- **Intelligent Gist Administration**: Cleanly manage Gists with filename-based displays and interactive removal.
- **Smart Archive Filtering**: Dynamically identifies and filters repositories based on their current archived state for accurate targeting.
- **Batch Processing**: Automated subfolder-to-repo conversion with multi-threaded performance and real-time status tracking.
- **Automated Privacy Control**: Integrated exclusion system for files and folders ensuring sensitive data is never accidentally processed.
- **Structured Activity Logging**: Comprehensive logs and result exports generated in the `Result/` directory for historical tracking.
- **Custom Gitignore Integration**: Automatically generates and merges `.gitignore` patterns based on your global settings.
- **Identity & Token Verification**: Real-time validation of GitHub Personal Access Tokens, including permission scope and rate limit monitoring.
- **Standardized UI/UX**: Professional CLI design with consistent confirmation prompts and "Press Enter" steps for maximum readability.
- **Native Cross-Platform Support**: Optimized for zero-dependency execution across Windows, macOS, and Linux terminal environments.

## Operating System Support

### Windows
- Recommended: Python 3.10 or higher.
- Requirements: Standard command prompt or PowerShell.
- Note: The tool uses ANSI escape sequences for color; modern Windows terminals handle this natively.

### macOS
- Recommended: Python 3.9 or higher.
- Requirements: Terminal.app or iTerm2.
- Installation: Standard `pip` installation for dependencies.

### Linux / Ubuntu / Debian / Unix
- Recommended: Python 3.8 or higher.
- Requirements: Bash or Zsh terminal.
- Note: Ensure `python3-pip` and `python3-venv` are installed if using a virtual environment.

## Installation and Requirements

1. Ensure Python is installed on your system.
2. Install the following required dependencies via your terminal:

```bash
pip install requests colorama configparser
```

## Configuration (settings.ini)

The tool uses a `settings.ini` file for persistent configuration. This file is automatically created on the first run if it does not exist.

### [account_NAME]
Store multiple GitHub accounts. Each section must start with `account_` followed by a unique identifier.
- `username`: Your GitHub username.
- `token`: Your Personal Access Token (PAT) with `repo`, `delete_repo`, `gist`, and `user` scopes.

### [gitignore]
Manage patterns for the auto-generated `.gitignore` file during batch uploads.
- `patterns`: A comma-separated list of file patterns to ignore (e.g., `*.exe, *.pyc, node_modules`).

### [git_exceptions]
Files or folders that should always be excluded from git operations, ensuring data privacy and script integrity.
- `files`: Comma-separated list of individual files to ignore.
- `folders`: Comma-separated list of directories to ignore (e.g., `.gemini`, `Result`).

## Tool Categories and Features

The application is divided into six logical categories for efficient navigation.

### Universal Action Modes

Every option that operates on a list of items uses the same **Universal Target Selector** with these modes:

| Mode | What it does |
|------|--------------|
| `[1] Single Action` | Type one value manually (repo name, username, owner/repo, or local path). |
| `[2] Select Action` | Fetches **ALL available items** and lets you pick specific ones by number (e.g. `1,3,5`). |
| `[3] Bulk Action` | Fetches **ALL available items** and processes every one of them. |
| `[4] Load from .txt` | Lists every `.txt` file on this machine (current folder + `Result/`), then loads entries line-by-line. |

The item list always comes from one of two sources, and the tool **displays every item in that source** before any action runs:

- **GitHub API** — options prefixed by GitHub data (repos, gists, starred, following, forks) fetch and paginate **all** matching items from the API.
- **This Machine** — options like Batch Upload, Update, Sync Descriptions, and Create Gist scan the local filesystem and list **all** available folders/files found on the machine.

Options that have no displayable item list (Check Token Info, Follows Manager views, Listing Manager export) intentionally do not offer Single/Select/Bulk, since there is nothing to list from either source.

### 1. Badge & Profile
- **Auto Badge Automation**: Automates recurring tasks related to profile badges and repository metadata.
- **Profile Repository Manager**: Specific tools for managing your `username/username` profile README repository.

### 2. Collaborations & Interactions
- **Archive / Unarchive**: Bulk toggle the archived status of repositories to make them read-only or restore edit access.
- **Bulk Follow / Unfollow**: Interactive management of user following. Supports manual input, selection lists, and `.txt` file bulk loading.
- **Bulk Star / Unstar**: Mass-manage repository stars. Useful for bookmarking or cleaning up interests.
- **Collaborator Manager**: Add or remove collaborators from your repositories in bulk.
- **Follows Manager**: Audit your followers and following lists with direct export and management options.
- **Manage Forks**: Identify, analyze, and delete forked repositories to keep your profile clean.

### 3. Repository Management
- **Batch Upload Subfolders**: Converts a local directory of folders into individual GitHub repositories automatically. Supports custom visibility (Public/Private) and debug logging.
- **Bulk Delete Repositories**: Safely remove multiple repositories after verification.
- **Change Repository Visibility**: Switch between Public and Private modes for any number of selected repositories.
- **Clone / Backup Repositories**: Automates the backup process by cloning multiple repositories to your local machine.
- **Delete Repository**: Single repository targeted deletion with verification.
- **List Repositories**: View a categorized list of your repositories with visibility status.
- **Update All Repositories**: Performs bulk metadata updates or synchronization across your repository portfolio.

### 4. Repository Details Manager
- **Branch Manager**: Mass creation or deletion of branches across selected repositories.
- **License Manager**: Standardize legal compliance by adding or updating LICENSE files (MIT, GPL, Apache, etc.) in bulk.
- **Sync Descriptions**: Update and synchronize repository descriptions from a standardized source.
- **Topic Manager**: Add or remove GitHub Topics to improve repository discoverability.

### 5. Others
- **Check Token Info**: View real-time API rate limits, token scopes, and account status.
- **Create Gist**: Quickly upload local files as Gists with custom descriptions.
- **Remove Gist**: Clean, interactive removal of Gists by name or ID.
- **Repository Analytics**: Analyze repositories based on size, stars, forks, and last updated timestamps.

### 6. Listing
- **Listing Manager**: Export detailed data collections (all repos, only forks, or all stars) into structured `.txt` files saved in the `Result/` directory.

## Execution Example

### Initializing the Tool
```text
--- Authentication ---
Enter Username: UserID
Enter Token: ghp_xxxxxxxxxxxxxxxxxxxx
OK
Save credentials to settings.ini? (y/n): y
```

### Batch Upload Output
```text
Creating repository: Project-A... OK
Uploading files...
- file1.txt: OK
- file2.py: OK
Pushing to GitHub... OK
Repository processing complete.

Summary:
Total Success: 1
Total Failed: 0
Duration: 12 seconds

Press Enter to continue..
```

## Documentation and Logs
- **Activity Log**: All operations are logged to `Result/activity.log`.
- **Exported Lists**: Structured data from the Listing Manager is saved to `Result/`.

---
The Github Automation Tool is built for reliability. Every major action includes verification steps and clear feedback messages to ensure your account data remains safe and organized.
