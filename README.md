# FTP Deploy & Remote Unzip 

A reusable GitHub Action that **zips** a specified path (file or directory), uploads it to an FTP server along with a generated PHP unzip script, and then triggers that script to extract the ZIP using a POST request to a public address.

## 🛠️ Inputs

| Input Name       | Required | Default | Description                                                                 |
|------------------|:--------:|:-------:|-----------------------------------------------------------------------------|
| `zip_source`     |   false  | `.`     | Path (file or directory) to zip.                                            |
| `ftp_host`       |   true   | —       | FTP server hostname (e.g., `ftp.example.com`).                               |
| `ftp_user`       |   true   | —       | FTP username.                                                               |
| `ftp_password`   |   true   | —       | FTP password.                                                               |
| `ftp_remote_path`|   true   | —       | Remote directory on FTP server (e.g., `/public_html/uploads`).                |
| `php_script_url` |   true   | —       | Public URL to call for unzipping (e.g., `https://example.com/uploads/unzip.php`). |

## 🚀 Usage

1. **Save** the composite action under `.github/actions/ftp-deploy/action.yml` (included in this repo).
2. **Set** the following repository secrets under **Settings → Secrets**:
   - `FTP_HOST`
   - `FTP_USER`
   - `FTP_PASSWORD`
3. **Create** a workflow file (e.g., `.github/workflows/deploy.yml`):

```yaml
name: Deploy via FTP + Auto-Unzip

on:
  workflow_dispatch:

jobs:
  ftp_deploy:
    uses: ./.github/actions/ftp-deploy
    with:
      zip_source:     'build/output'
      ftp_host:       ${{ secrets.FTP_HOST }}
      ftp_user:       ${{ secrets.FTP_USER }}
      ftp_password:   ${{ secrets.FTP_PASSWORD }}
      ftp_remote_path:'/public_html/uploads'
      php_script_url: 'https://yourdomain.com/uploads/unzip.php'
```

4. **Run** the workflow from the Actions tab or via the GitHub CLI.

## 🔍 How It Works

1. **Checkout**: checks out your repository.
2. **Generate Script**: creates an `unzip.php` script on the runner.
3. **Install Tools**: installs `lftp` and `zip` (Linux only).
4. **Upload Script**: transfers `unzip.php` to `ftp_remote_path`.
5. **Zip Files**: compresses the specified `zip_source` into `deploy-<run_id>.zip`.
6. **Upload ZIP**: uploads the ZIP archive to the FTP server.
7. **Trigger Unzip**: sends a HTTP POST to `php_script_url` with the ZIP filename to extract it in place.

## ⚙️ Prerequisites

- The FTP user must have write permissions to the specified `ftp_remote_path`, and that path must be web-accessible if you intend to call the PHP script via HTTP.
- Ensure your webserver is configured to execute PHP scripts in the upload directory.

## 📄 License

This action is released under the [MIT License](LICENSE).

