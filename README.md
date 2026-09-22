# GitHub Automation Tool

![Python](https://img.shields.io/badge/Python-3.13-blue)
![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)

## Description

GitHub Automation Tool is a single-file, interactive command-line application for batch-managing one or more GitHub accounts. It automates repository creation, bulk deletion, visibility toggling, archiving, cloning, gist administration, branch and topic management, license injection, follow/star operations, badge automation, and portfolio listing. Every selection-driven action fetches the full candidate list from GitHub and shows it on screen before you choose, and every workflow runs identically on Windows, macOS, and Linux.

## Features

- Multi-Account Support: store unlimited GitHub accounts in `settings.ini` and switch between them at startup.
- Universal Target Selector: every selection action (Single, Select, All, Load from .txt) fetches and displays all candidates before you choose.
- Batch Upload Subfolders: converts each subfolder of a directory into its own repository with git init, commit, push, and secret-detection auto-fix.
- Bulk Repository Management: delete, change visibility, archive/unarchive, clone/backup, and list repositories in bulk.
- Repository Details: branch deletion, license injection (MIT, Apache-2.0, GPLv3), topic add/remove/overwrite, and description sync from local files.
- Gist Administration: create gists from local files and remove gists (paginated fetch shows every gist).
- Follow and Star Management: bulk follow/unfollow and star/unstar with .txt bulk loading.
- Badge Automation: automates YOLO, Quickdraw, Pull Shark, and Pair Extraordinaire achievements with a Run Once / Auto Repeat flow (choose the repository first, then repeat N times); Pair commits use the real `voidbit404` account as co-author.
- Network Safety: every GitHub API call carries a 30-second timeout, so a stalled connection can never hang the tool.
- Credential Safety: git authentication tokens are used only transiently; after every push or clone the local `origin` remote is restored to a clean, token-free URL, and token values are redacted from printed git error messages so they can never leak into the terminal or git config.
- Timeout Protection: every git subprocess call has a 300-second timeout with explicit error reporting; git is never executed through a shell (`shell=False`), removing command-injection risk on all platforms, including Windows.
- Pagination Safeguards: all list fetchers (repos, gists, followers, following, starred) cap pagination defensively so a malformed API response cannot trigger an infinite loop.
- Visibility Markers: every repository shown in any repo-related option, sub-option, or section is marked `[PRIVATE]` or `[PUBLIC]`, so you always know each repo's status before acting on it.
- Runtime Banner: the startup banner displays the tool title plus the detected OS version and Python version (auto-detected at runtime, never hardcoded).
- Listing and Analytics: export repositories, forks, and starred repos; sort and analyze repository statistics.
- Activity Logging: every major operation is recorded to `Result/activity.log`.
- Custom Gitignore Integration: auto-generates `.gitignore` files from patterns configured in `settings.ini`.
- Automated Privacy Control: `[git_exceptions]` keeps sensitive files and folders out of git tracking.
- Token Verification: validates tokens, checks `repo`/`public_repo` scopes, and monitors rate limits.
- Cross-Platform: pure Python with ANSI color fallbacks; explicitly handles Windows, macOS, and Linux paths and git execution.

## How to Use

### Installation

Requirements:

- Python 3.8 or newer (developed and verified on Python 3.13).
- Git installed and available on `PATH` (required for batch upload, clone/backup, and update flows).
- A GitHub Personal Access Token (PAT) with `repo` (and optionally `delete_repo`, `gist`, `user`) scopes.

Install the Python dependencies:

```bash
pip install requests colorama
```

`colorama` is optional; the tool falls back to raw ANSI escape codes if it is not installed. `configparser` is part of the Python standard library.

### Run

```bash
python run.py
```

On Linux and macOS use `python3 run.py` in shell environments where `python` is not mapped.

| Operating System | Command | Terminal |
| :--- | :--- | :--- |
| Windows | `python run.py` | Command Prompt, PowerShell, Windows Terminal |
| macOS | `python3 run.py` | Terminal.app, iTerm2 |
| Ubuntu / Debian / Linux | `python3 run.py` | Bash, Zsh |

### Authentication Flow

1. On startup the banner shows the tool title, the detected OS version, and the running Python version, then lists the saved accounts from `settings.ini` (e.g. `[1] user1`, `[2] user2`).
2. Enter the account number to log in, or `0` to add a new account.
3. For a new account, enter the GitHub username and Personal Access Token. The tool verifies the pair against the GitHub API and optionally saves it to `settings.ini`.
4. The main menu appears with the signed-in user and live account statistics (repos, stars, followers, badges).

### Universal Target Selection

Most management options reuse the same selection prompt. Every mode fetches the complete candidate list first and displays it, so you always see everything available. Repository items are always marked with their visibility status, e.g. `[3] DevAgent-Setup-CLI [PRIVATE]` or `[2] nashzhee404 [PUBLIC]`, in every repo-related option, sub-option, and section.

- `[1] Single Action`: the full list is shown, then you type one value or the list number of the item to target.
- `[2] Select Action`: the full list is shown, then you enter comma-separated numbers (e.g. `1,3,5`) to target multiple items.
- `[3] All Action`: the full list is shown and every listed item is selected; the selected items are re-printed as a numbered list.
- `[4] Load from .txt` (where available): reads one target per line from a UTF-8 text file and displays the loaded entries.

## Menu Options Reference

The main menu groups 24 options into six categories. `[0]` exits the application.

### Category 1: Badge and Profile

#### [1] Auto Badge Automation

Shows the earned/status of each achievement badge, then runs automation flows.

Badges displayed: YOLO, Quickdraw, Pull Shark, Pair Extraordinaire (automated); Starstruck, Galaxy Brain (helper/manual); Public Sponsor, Heart on Your Sleeve (manual); Arctic Code Vault, Mars 2020 (legacy).

Run-mode sub-options (asked first, in the badge screen):

- `[1]` Run Once: execute the selected badge action 1 time.
- `[2]` Auto Repeat: enter a total repeat count (e.g. `5`), and the auto-repeater runs the badge action that many times.
- `[0]` Back.

After choosing a run mode, the flow always asks you to **select the repository/repositories first** (Single / Select / All, full list displayed), then asks which badge(s) to run (numbers like `1,4` or `A` for ALL automated badges). The repeater then executes every selected badge on every selected repository, prints a per-run summary, and re-checks the profile afterward.

Workflow chain: badge status is scraped from the public profile, the repository is chosen first, then the badge actions run (every selected repo x every cycle x every badge), a summary is printed, and the profile is re-checked afterward.

Automated badge actions:

- YOLO: creates a patch file, opens a branch, opens a PR, requests a reviewer, then merges the PR.
- Quickdraw: opens an issue and immediately closes it.
- Pull Shark: repeats the YOLO flow N times to merge multiple PRs.
- Pair Extraordinaire: commits with a real `Co-authored-by` trailer on a branch and merges the PR. The co-author is the saved GitHub account `voidbit404` (id `259850816`), not a bot account, so the merged commit genuinely lists a second account as co-author.

Helper tools: Starstruck Helper (creates a star-exchange friendly repo), Galaxy Brain Helper (lists popular discussion hubs).

#### [2] Profile Repository Manager

Manages the special `username/username` profile repository.

- If the profile repo does not exist: `[1]` Create Profile Repository, `[0]` Back.
- If it exists: `[1]` Edit Profile README.md, `[2]` Delete Profile Repository, `[0]` Back.

README edit sub-options:

- `[1]` Edit Manually: shows the current README, accepts multi-line input, finish by typing `END` on a new line.
- `[2]` Upload from Local File: lists `.md` files in the current directory, pick one by number or name.

Deletion requires typing the account username to confirm.

### Category 2: Collaborations and Interactions

#### [3] Archive / Unarchive

Sub-options:

- `[1]` Archive Repository: shows only non-archived repositories; makes them read-only.
- `[2]` Unarchive Repository: shows only archived repositories; restores edit access.

Then target selection via Single/Select/All.

#### [4] Bulk Follow / Unfollow

Sub-options:

- `[1]` Follow User(s): manual or `.txt` targets.
- `[2]` Unfollow User(s): fetches your current following list as candidates.

Then target selection via Single/Select/All/.txt.

#### [5] Bulk Star / Unstar

Sub-options:

- `[1]` Star Repositories: manual or `.txt` targets in `owner/repo` or full URL format.
- `[2]` Unstar Repositories: fetches your current starred repositories as candidates.

Then target selection via Single/Select/All/.txt.

#### [6] Collaborator Manager

Sub-options:

- `[1]` Add Collaborator.
- `[2]` Remove Collaborator.

Workflow chain: select repositories (Single/Select/All), then enter the collaborator's GitHub username; the action is applied to every selected repo.

#### [7] Follows Manager

Sub-options:

- `[1]` View Following List: prints every user you follow and exports to `Result/Following.txt`.
- `[2]` View Followers List: prints every follower and exports to `Result/Followers.txt`.
- `[3]` Follow users from .txt: one username per line.
- `[4]` Unfollow users from .txt: one username per line.
- `[0]` Back.

#### [8] Manage Forks

Sub-options:

- `[1]` Fork Repository: targets are `owner/repo` or full `https://github.com/owner/repo` values (manual or `.txt`); each is forked to your account via the API.
- `[2]` Delete Fork (Repository): identical flow to option `[13]` Delete Repository.

### Category 3: Repository Management

#### [9] Batch Upload Subfolders

Workflow chain (per subfolder):

1. Enter the absolute path to a directory that contains project subfolders.
2. Choose global visibility: `[1]` Public, `[2]` Private, `[3]` Ask for each repository.
3. Choose upload mode: `[1]` Normal Upload (preserves existing local history), `[2]` Fresh Upload (removes `.git` and force-pushes).
4. Choose debug output: `y` shows git hints/warnings, `n` suppresses them.
5. Each subfolder name is sanitized into a repository name; a `.gitignore` is generated from `settings.ini`; repository description is taken from the first line of a local `README.md` if present.
6. If the repository already exists you may: `[1]` Rename and Retry, `[2]` Update existing repository (force push latest version), `[3]` Skip this folder.
7. Git identity is set per-repo, files are committed, and the branch is pushed to `main`.
8. Push results are summarized (successful/failed counts). GitHub secret-detection blocks trigger an automatic fix: blocked files are added to `.gitignore`, history is rewritten, and the push is retried with `--force`.
9. After a successful push, the local `origin` remote is restored to the clean HTTPS URL (no token embedded), so the token is never persisted in `.git/config`.

#### [10] Bulk Delete Repositories

Same workflow as `[13]` Delete Repository (below), provided as a dedicated bulk entry.

#### [11] Change Repository Visibility

Sub-options:

- `[1]` Make Private.
- `[2]` Make Public.

Then target selection via Single/Select/All; every selected repo is updated via the PATCH API.

#### [12] Clone / Backup Repositories

Target selection via Single/Select/All (full repo objects, so clone URLs are known). All selected repositories are cloned with authenticated URLs into a timestamped folder `Github_Backup_YYYYMMDD_HHMMSS/` created in the current working directory. After each clone the local `origin` remote is restored to the clean HTTPS URL, so the token never persists in `.git/config`.

#### [13] Delete Repository

Workflow chain: target selection via Single/Select/All, a PERMANENT DELETION WARNING is printed with the full list, then a simple `(y/n)` confirmation (no text to type); confirmed repositories are deleted via the API and a summary is printed. This is the same flow used by `[10]` and by `[8]` Delete Fork.

#### [14] List Repositories

Target selection via Single/Select/All; for each repository prints name, visibility, description, stars, forks, and URL. In Single mode, typing a repository name (instead of a list number) resolves it to full details automatically (matched against the fetched list, with an API fallback for repos outside the list). The list is also saved to `Result/Repository.txt`.

#### [15] Update All Repositories

Updates local git repositories (folders in the current directory that contain `.git`).

Sub-options:

- `[1]` Normal Pull: merges remote changes.
- `[2]` Force Pull: `git fetch` then `git reset --hard origin/<default branch>` (the default branch, `main` or `master`, is detected per repository, so neither is hardcoded); warns and asks `(y/n)` before discarding local changes.

Then target selection via Single/Select/All, and a summary is printed.

### Category 4: Repository Details Manager

#### [16] Branch Manager

Sub-options:

- `[D]` Delete a branch: enter the exact branch name, then confirm with `(y/n)`. The `main` and `master` branches are blocked by design. The branch is deleted from every selected repo (Single/Select/All) via the git refs API.

#### [17] License Manager

Sub-options:

- `[1]` MIT License.
- `[2]` Apache License 2.0.
- `[3]` GPLv3.

Workflow chain: select repositories (Single/Select/All), then the license text (with current year and your username embedded) is written to `LICENSE` in each repo via the Contents API.

#### [18] Sync Descriptions

Target selection via Single/Select/All of local git repositories. If a repo folder contains `description.txt`, its content is read and PATCHed as the repository's GitHub description.

#### [19] Topic Manager

Sub-options:

- `[1]` Add Tag(s): merges the entered tags with current topics.
- `[2]` Remove Tag(s): removes the entered tags from current topics.
- `[3]` Overwrite All Tags: replaces every current topic with the entered tags.

Workflow chain: select repositories (Single/Select/All), enter comma-separated tags, and each repo's current topics are fetched, transformed, and pushed back via the Topics API.

### Category 5: Others

#### [20] Check Token Info

Prints the authenticated username, remaining core rate limit, total limit, and the reset time.

#### [21] Create Gist

Workflow chain: the current directory's files are listed and selected via Single/Select/All; enter one description for all gists and choose public/private (`y/n`); each selected file is uploaded as its own gist and its URL is printed.

#### [22] Remove Gist

Workflow chain: every gist is fetched with pagination and displayed as `first-filename - description`; select via Single/Select/All; a warning with the full list is shown; confirm with `(y/n)` and the gists are deleted via the API.

#### [23] Repository Analytics

Workflow chain: select repositories (Single/Select/All; full objects or names/URLs are resolved), then sort by:

- `[1]` Stars.
- `[2]` Forks.
- `[3]` Size (Large to Small, shown in MB).

The top 50 repositories are printed with the selected metric.

### Category 6: Listing

#### [24] Listing Manager

Sub-options (data source):

- `[1]` List All Repositories (tagged `[PUBLIC]` or `[PRIVATE]`).
- `[2]` List All Forks (tagged `[PUBLIC]` or `[PRIVATE]`).
- `[3]` List All Starred.

Output format:

- `[1]` Name only.
- `[2]` Full URL.

The complete dataset is printed and saved to `Result/Repositories.txt`, `Result/Forks.txt`, or `Result/Starred.txt` depending on the source.

## Output / Results

All generated artifacts are written relative to the application's working directory:

```
Result/
|-- activity.log        # timestamped log of every operation (created on first use)
|-- Repository.txt      # URLs/names from [14] List Repositories
|-- Following.txt       # followed users from [7] Follows Manager
|-- Followers.txt       # followers from [7] Follows Manager
|-- Repositories.txt    # listing output from [24] Listing Manager
|-- Forks.txt           # fork listing from [24] Listing Manager
`-- Starred.txt         # starred listing from [24] Listing Manager

Github_Backup_YYYYMMDD_HHMMSS/   # clone/backup folder from [12]
```

## Configuration (settings.ini)

`settings.ini` is created automatically on first run and stores three sections. All files are read and written as UTF-8 for cross-platform safety.

### [account_NAME]

One section per saved account, where `NAME` is the GitHub username.

```ini
[account_youruser]
token = ghp_your_personal_access_token
```

### [gitignore]

Newline-separated glob patterns that the tool writes into the `.gitignore` of every repository it uploads.

```ini
[gitignore]
patterns = __pycache__/
    *.pyc
    *.log
    .env
    .DS_Store
    Result/
    Output/
```

### [git_exceptions]

Additional files and folders that must never enter git tracking (privacy protection). Each entry goes on its own line.

```ini
[git_exceptions]
files = secrets.txt
folders = .gemini
```

A one-time automatic migration imports legacy `config.json` accounts into `settings.ini` and renames the old file to `config.json.bak`.

## Project Structure

```
Github Automation Tool/          # project root
|-- run.py                       # main application (single-file CLI, 3079 lines)
|-- settings.ini                 # accounts, gitignore patterns, git exceptions
|-- README.md                    # this documentation
`-- Result/                      # created at runtime: activity.log + exported lists
```

## License

A `LICENSE` file is not bundled with this project. The application can generate MIT, Apache-2.0, and GPLv3 license files for your repositories through option `[17]` License Manager; the year 2026 and the signed-in username are embedded in the generated text.