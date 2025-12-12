# LionsMax V3 - Local Development Setup

## Quick Start Guide

This guide will help you clone the repository, set up your local WordPress development environment, and sync with Hostinger.

## Prerequisites

- Git installed
- PHP 7.4+ or 8.0+
- Composer
- Local development environment (Local by Flywheel, XAMPP, or Docker)
- SSH access to Hostinger (optional but recommended)
- GitHub account with SSH/HTTPS access configured

## Step 1: Clone Repository Locally

```bash
# Clone the repository
git clone https://github.com/iladimm3/lionsmax-V3.git
cd lionsmax-V3

# Create a development branch
git checkout -b develop
```

## Step 2: Set Up WordPress Structure

```bash
# Create essential directories
mkdir -p wp-content/plugins/lionsmax-custom
mkdir -p wp-content/themes/lionsmax-theme
mkdir -p .github/workflows
mkdir -p docs/api
mkdir -p scripts
```

## Step 3: WordPress Configuration

### Create wp-config-local.php

```bash
cp wp-config-sample.php wp-config-local.php
```

Edit `wp-config-local.php` with your local database credentials:

```php
define('DB_NAME', 'lionsmax_local');
define('DB_USER', 'root');
define('DB_PASSWORD', 'your_password');
define('DB_HOST', 'localhost');
define('WP_DEBUG', true);
define('WP_DEBUG_LOG', true);
define('WP_DEBUG_DISPLAY', false);
```

## Step 4: Create Essential Documentation Files

- `docs/WORDPRESS_STRUCTURE.md` - WP custom post types and taxonomy
- `docs/N8N_WORKFLOWS.md` - N8N automation documentation
- `docs/API_ENDPOINTS.md` - Custom API documentation
- `scripts/deploy.sh` - Deployment script for Hostinger

## Step 5: Set Up GitHub Actions for Deployment

Create `.github/workflows/deploy.yml` for automated FTP deployment to Hostinger:

```yaml
name: Deploy to Hostinger
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Sync files to Hostinger via FTP
        uses: SamKirkland/FTP-Deploy-Action@v4.3.4
        with:
          server: ${{ secrets.FTP_SERVER }}
          username: ${{ secrets.FTP_USERNAME }}
          password: ${{ secrets.FTP_PASSWORD }}
          local-dir: ./
          server-dir: /
          exclude: |
            **/.git*
            **/.gitignore
            **/node_modules
            **/wp-config.php
            **/wp-content/uploads
```

## Step 6: Sync with Hostinger

### Option A: Using Git (Recommended)

```bash
# From Hostinger shell, pull latest changes
cd /path/to/lionsmax.com
git pull origin main
```

### Option B: Using FTP

```bash
# Exclude sensitive files and sync
rsync -avz --exclude='wp-config.php' --exclude='wp-content/uploads' \
  ./ ftp://user@lionsmax.com/public_html/
```

## Step 7: Daily Workflow

```bash
# Create feature branch
git checkout -b feature/your-feature-name

# Make changes and commit
git add .
git commit -m "feat: description of changes"

# Push to GitHub
git push origin feature/your-feature-name

# Create pull request and merge to main
# GitHub Actions will auto-deploy to Hostinger
```

## Project Structure

```
lionsmax-V3/
├── .github/
│   └── workflows/          # GitHub Actions CI/CD
├── docs/
│   ├── API_ENDPOINTS.md
│   ├── N8N_WORKFLOWS.md
│   └── WORDPRESS_STRUCTURE.md
├── scripts/
│   ├── deploy.sh
│   └── backup.sh
├── wp-content/
│   ├── plugins/
│   │   └── lionsmax-custom/    # Custom plugin
│   └── themes/
│       └── lionsmax-theme/     # Custom theme
├── .gitignore              # Git exclusions
├── README.md               # Project overview
├── SETUP.md               # This file
└── package.json           # Node dependencies (if using npm)
```

## Environment Variables

Create `.env` file (ensure it's in .gitignore):

```
WP_ENV=local
WP_DEBUG=true
N8N_URL=http://n8n.lionsmax.local
N8N_API_KEY=your_api_key_here
IHERB_API_KEY=your_iherb_key
```

## Troubleshooting

### Git permission denied
```bash
# Generate SSH key if needed
ssh-keygen -t ed25519 -C "your_email@example.com"
# Add to GitHub SSH keys
```

### WordPress database not found
```bash
# Create database in local environment
php -r "mysqli_connect('localhost', 'root', 'password'); 
mysqli_query(\$connection, 'CREATE DATABASE lionsmax_local;');"
```

### N8N Connection Issues
```bash
# Check N8N status on Hostinger
curl https://n8n.lionsmax.com/api/v1/health
```

## Next Steps

1. Clone this repo locally
2. Set up local WordPress
3. Create custom theme/plugin structure
4. Configure GitHub Actions for deployment
5. Begin development on feature branches

## Resources

- [GitHub Documentation](https://docs.github.com)
- [WordPress Plugin Development](https://developer.wordpress.org/plugins/)
- [N8N Workflows](https://docs.n8n.io/workflows/)
- [Hostinger API Documentation](https://support.hostinger.com/en/articles/)

## Support

For questions, refer to NotebookLM documentation or check Notion task board.
