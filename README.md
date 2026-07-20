# ☁ Salesforce DX Dev Container

Get a current Node.js and Salesforce CLI development environment by opening this
repository in a Dev Container. No local CLI installation is required.

> **日本語の手順は[English](#english)の後にあります。**

## English

### What's included

- The latest `javascript-node` Dev Container image (and therefore the current
  Node.js version supplied by that image)
- The latest unified Salesforce CLI (`sf`), installed during the image build
- [Salesforce Extension Pack (Expanded) for VS Code](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode-expanded)
- Prettier for VS Code

Java, TypeScript, the legacy `sfdx-cli` package, and a separate creation script
are intentionally not installed. Add project-specific tools only when the
project needs them.

### 1. Start the container

1. Use this repository as a template or clone it.
2. Open the repository in VS Code.
3. Run **Dev Containers: Reopen in Container** from the Command Palette.
4. Verify the environment:

   ```bash
   node --version
   sf --version
   ```

### 2. Enable a Dev Hub

A Dev Hub is required to create and manage scratch orgs. In the Salesforce org:

1. Sign in as a system administrator.
2. Open **Setup**, enter `Dev Hub` in **Quick Find**, and select **Dev Hub**.
3. Click **Enable Dev Hub**. 

For a guided walkthrough, complete Trailhead's
[Quick Start: Salesforce DX](https://trailhead.salesforce.com/ja/content/learn/projects/quick-start-salesforce-dx).
See also the official
[Salesforce DX Developer Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_setup_enable_devhub.htm).

### 3. Connect the CLI to Salesforce

From the VS Code terminal inside the container, run:

```bash
sf org login web --set-default-dev-hub --alias DevHub
sf org list
```

Complete the browser login for the org in which you enabled Dev Hub. If it is a
sandbox, add `--instance-url https://test.salesforce.com` to the login command.
Use a My Domain URL with `--instance-url` when your organization requires it.
Never commit authentication files or access tokens.

### 4. Create or use a Salesforce DX project

Create a project when starting from an empty workspace:

```bash
sf project generate --name my-salesforce-app
cd my-salesforce-app
sf org create scratch --alias MyScratchOrg --set-default --duration-days 7
sf org open
```

---

## 日本語

### 含まれるもの

- 最新の `javascript-node` Dev Container イメージ（そのイメージが提供する現行の Node.js）
- イメージのビルド時に導入する最新の統合 Salesforce CLI（`sf`）
- [Salesforce Extension Pack (Expanded) for VS Code](https://marketplace.visualstudio.com/items?itemName=salesforce.salesforcedx-vscode-expanded)
- VS Code 用 Prettier

Java、TypeScript、旧 `sfdx-cli` パッケージ、別途の初期化スクリプトは導入しません。
必要なツールはプロジェクトごとに追加してください。

### 1. コンテナーを起動する

1. このリポジトリーをテンプレートとして使うか、clone します。
2. VS Code でこのリポジトリーを開きます。
3. コマンドパレットから **Dev Containers: Reopen in Container** を実行します。
4. 環境を確認します。

   ```bash
   node --version
   sf --version
   ```

### 2. Dev Hub を有効化する

スクラッチ組織の作成と管理には Dev Hub が必要です。Salesforce 組織で次を実行します。

1. システム管理者でサインインします。
2. **設定** を開き、**クイック検索** に `Dev Hub` と入力し、**Dev Hub** を選びます。
3. **Dev Hub を有効化** をクリックします。

画面付きの手順は Trailhead の
[クイックスタート: Salesforce DX](https://trailhead.salesforce.com/ja/content/learn/projects/quick-start-salesforce-dx)を参照してください。
公式の [Salesforce DX 開発者ガイド](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_setup_enable_devhub.htm)も参照できます。

### 3. CLI と Salesforce を連携する

コンテナー内の VS Code ターミナルで実行します。

```bash
sf org login web --set-default-dev-hub --alias DevHub
sf org list
```

Dev Hub を有効化した組織にブラウザーでログインします。

### 4. Salesforce DX プロジェクトを作成または利用する

空のワークスペースから始める場合はプロジェクトを作成します。

```bash
sf project generate --name my-salesforce-app
cd my-salesforce-app
sf org create scratch --alias MyScratchOrg --set-default --duration-days 7 --definition-file config/project-scratch-def.json
sf org open
```
