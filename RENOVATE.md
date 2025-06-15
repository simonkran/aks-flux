# Renovate Setup Guide

This repository is configured with [Renovate](https://docs.renovatebot.com/) for automated dependency management. Renovate will automatically create pull requests to update Helm charts, container images, and other dependencies.

## 🚀 Features Configured

### **Automated Updates**
- **Helm Charts**: cert-manager, external-secrets, ingress-nginx, kube-prometheus-stack, podinfo
- **Container Images**: Automatic detection and updates of image tags
- **Flux Components**: Flux controllers and system components
- **Security Updates**: Priority handling for vulnerability fixes

### **Environment-Specific Behavior**
- **Development**: More aggressive updates with shorter stability periods
- **Production**: Conservative updates requiring manual approval
- **Staging/UAT**: Balanced approach between dev and prod

### **Smart Scheduling**
- **Weekly Schedule**: Runs every Monday at 2:30 AM UTC
- **Rate Limiting**: Maximum 2 PRs per hour, 5 concurrent PRs
- **Stability Days**: Wait periods before creating PRs for stability

## 🔧 Setup Instructions

### **GitHub Setup**

1. **Create Personal Access Token**:
   ```bash
   # Go to GitHub Settings > Developer settings > Personal access tokens
   # Create token with these permissions:
   # - repo (full control)
   # - workflow
   # - read:org (if using GitHub organizations)
   ```

2. **Add Repository Secret**:
   ```bash
   # In your repository: Settings > Secrets and variables > Actions
   # Add new repository secret:
   Name: RENOVATE_TOKEN
   Value: your_github_personal_access_token
   ```

3. **Enable GitHub Actions**:
   - Ensure GitHub Actions are enabled in your repository settings
   - The workflow will run automatically on schedule

### **Azure DevOps Setup**

For Azure DevOps, the `azure-pipelines.yml` file is already configured:

1. **Create Personal Access Token**:
   ```bash
   # In Azure DevOps: User Settings > Personal Access Tokens
   # Create token with these permissions:
   # - Code (Read & Write)
   # - Pull Request (Read & Write)
   # - Work Items (Read & Write)
   ```

2. **Add Pipeline Variable**:
   ```bash
   # In Azure DevOps: Pipelines > Library > Variable Groups
   # Or in Pipeline > Variables
   # Add secret variable:
   Name: RENOVATE_TOKEN
   Value: your_azure_devops_personal_access_token
   Keep this value secret: ✓
   ```

3. **Create Pipeline**:
   ```bash
   # In Azure DevOps: Pipelines > New Pipeline
   # Select your repository
   # Choose "Existing Azure Pipelines YAML file"
   # Select azure-pipelines.yml
   ```

4. **Enable Scheduled Runs**:
   - The pipeline is configured to run weekly on Mondays at 2:30 AM UTC
   - Ensure scheduled runs are enabled in pipeline settings

### **Self-Hosted Renovate**

For enterprise environments, you can run Renovate on your own infrastructure:

```bash
# Install Renovate CLI
npm install -g renovate

# Run Renovate for Azure DevOps
export RENOVATE_TOKEN="your_token"
export RENOVATE_PLATFORM="azure"
export RENOVATE_ENDPOINT="https://dev.azure.com/your-org"
export RENOVATE_REPOSITORIES="your-project/your-repo"
renovate
```

## 📋 Configuration Overview

### **Update Strategies**

| Component | Minor/Patch | Major | Approval Required |
|-----------|-------------|-------|-------------------|
| **Infrastructure Helm** | Auto-merge | Manual | Yes (Major) |
| **Application Helm** | Auto-merge | Manual | Yes (Major) |
| **Container Images** | Auto-merge | Manual | Yes (Major) |
| **Flux Components** | Manual | Manual | Yes |
| **Security Updates** | Auto-merge | Auto-merge | No |

### **Environment Policies**

| Environment | Update Speed | Approval | Stability Days |
|-------------|--------------|----------|----------------|
| **Development** | Aggressive | Auto | 1 day |
| **Staging/UAT** | Moderate | Auto (patch) | 3 days |
| **Production** | Conservative | Manual | 7 days |

### **File Patterns Monitored**

- `**/*.yaml` and `**/*.yml` - Kubernetes manifests
- `**/values*.yaml` - Helm values files
- `**/Chart.yaml` - Helm charts
- `**/kustomization.yaml` - Kustomize files

## 🔍 How It Works

### **Detection**
Renovate scans your repository for:
- Helm chart versions in `HelmRelease` manifests
- Container image tags in Kubernetes YAML
- Dependencies in various file formats

### **Update Process**
1. **Scan**: Renovate checks for updates weekly
2. **Stability**: Waits for configured stability period
3. **PR Creation**: Creates pull request with updates
4. **Auto-merge**: Merges automatically if configured and CI passes
5. **Manual Review**: Requires approval for major updates

### **Dependency Dashboard**
Renovate creates an issue in your repository called "🤖 Dependency Dashboard" that shows:
- Pending updates
- Rate-limited PRs
- Configuration errors
- Update statistics

## 🛠️ Customization

### **Environment-Specific Overrides**

Add environment-specific rules in `renovate.json`:

```json
{
  "packageRules": [
    {
      "matchFileNames": ["**/prod/**"],
      "dependencyDashboardApproval": true,
      "automerge": false
    }
  ]
}
```

### **Add New Package Types**

```json
{
  "packageRules": [
    {
      "matchPackageNames": ["your-app"],
      "automerge": true,
      "stabilityDays": 2
    }
  ]
}
```

### **Exclude Specific Dependencies**

```json
{
  "ignoreDeps": [
    "legacy-chart",
    "manual-managed-image"
  ]
}
```

## 🚨 Security

### **Vulnerability Handling**
- **Security updates** are prioritized and auto-merged
- **OSV vulnerability database** integration enabled
- **CVE scanning** for container images

### **Access Control**
- Renovate bot only has access to create PRs
- Manual approval required for production changes
- All changes go through CI/CD pipeline validation

## 📊 Monitoring

### **Dashboard Features**
- View all pending updates in one place
- See which updates are waiting for approval
- Track update success/failure rates
- Monitor security vulnerability status

### **Notifications**
Configure GitHub/GitLab notifications for:
- New Renovate PRs
- Failed updates
- Security alerts
- Weekly digest

## 🔄 Manual Operations

### **Force Update Check**

**GitHub Actions:**
```bash
# Go to Actions > Renovate > Run workflow
```

**Azure DevOps:**
```bash
# Go to Pipelines > Select Renovate pipeline > Run pipeline
```

### **Emergency Updates**
For critical security updates:
1. Go to the Dependency Dashboard issue
2. Click "Create all rate-limited PRs at once 🚀"
3. Review and merge critical updates immediately

### **Override Schedule**

**GitHub Actions:**
```bash
# Set "Override schedule" to true when running manually
```

**Azure DevOps:**
```bash
# Run pipeline manually to override schedule
# Pipeline can be triggered on-demand via Azure DevOps UI
```

## 🐛 Troubleshooting

### **Common Issues**

1. **No PRs Created**:
   - Check RENOVATE_TOKEN permissions
   - Verify renovate.json syntax
   - Check repository access

2. **Updates Not Detected**:
   - Ensure file patterns match your structure
   - Check ignorePaths configuration
   - Verify datasource configuration

3. **Auto-merge Not Working**:
   - Check branch protection rules
   - Verify CI/CD pipeline status
   - Ensure proper permissions

### **Debug Mode**
Run with debug logging:
```bash
LOG_LEVEL=debug renovate
```

### **Dry Run**
Test configuration without making changes:
```bash
RENOVATE_DRY_RUN=true renovate
```

## 📚 Resources

- [Renovate Documentation](https://docs.renovatebot.com/)
- [Configuration Options](https://docs.renovatebot.com/configuration-options/)
- [Flux Integration](https://docs.renovatebot.com/modules/manager/flux/)
- [Helm Support](https://docs.renovatebot.com/modules/manager/helm-values/)

---

## ✅ Quick Start Checklist

- [ ] Add `RENOVATE_TOKEN` to repository secrets
- [ ] Enable GitHub Actions (or setup GitLab CI)
- [ ] Customize `renovate.json` for your needs
- [ ] Test with manual workflow trigger
- [ ] Monitor Dependency Dashboard issue
- [ ] Configure notifications
- [ ] Document approval process for your team

**Your GitOps repository is now ready for automated dependency management! 🎉**
