# GitLab SSH Key Setup - Quick Reference

## ✅ What Was Created

1. **Directory**: `~/.ssh/gitlab/`
   - Contains your ED25519 key pair
   
2. **Private Key**: `~/.ssh/gitlab/id_ed25519` (permissions: 600)
   - Keep this secret and never share it
   
3. **Public Key**: `~/.ssh/gitlab/id_ed25519.pub` (permissions: 644)
   - This is what you add to GitLab
   
4. **SSH Config**: `~/.ssh/config` (permissions: 600)
   - Automatically uses your GitLab key when connecting

## 📋 Next Steps

### 1. Add Your Public Key to GitLab

Copy this public key:
```
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDi7RmNgshBWAbYBX2SYrdCXuZCwasNNredLKvvlAF0z ganesh-gitlab
```

**Add to GitLab:**
1. Go to GitLab → Settings → SSH Keys (or https://gitlab.com/-/user_keys)
2. Paste the public key above
3. Give it a title like "Ganesh Work Machine"
4. Click "Add key"

### 2. Test the Connection

```bash
# Test connection to gitlab.com
ssh -T git@gitlab.com

# For self-hosted GitLab, update the config first:
# Edit ~/.ssh/config and change 'gitlab.your-domain.com' to your actual GitLab hostname
```

You should see a message like:
```
Welcome to GitLab, @yourusername!
```

## 🚀 Usage

The SSH config is already set up to automatically use your GitLab key. Just use git commands normally:

```bash
# Clone a repository
git clone git@gitlab.com:username/repository.git

# Add remote to existing repo
git remote add origin git@gitlab.com:username/repository.git

# Push/pull as usual
git push origin main
git pull origin main
```

## 🔧 Configuration Details

**SSH Config (~/.ssh/config):**
- Matches: `gitlab.com`, `*.gitlab.com`, and custom `gitlab` host
- Automatically uses: `~/.ssh/gitlab/id_ed25519`
- Uses only this key (IdentitiesOnly yes)
- Authenticates via public key only

## 📝 For Self-Hosted GitLab

If you're using a self-hosted GitLab instance, edit `~/.ssh/config`:

```bash
nano ~/.ssh/config
```

Change this line:
```
HostName gitlab.your-domain.com
```

To your actual GitLab hostname, for example:
```
HostName gitlab.mycompany.com
```

Then you can use the short alias:
```bash
git clone git@gitlab:username/repository.git
```

## 🔐 Security Notes

- The private key (`id_ed25519`) should NEVER be shared or copied elsewhere
- ED25519 keys are more secure and faster than RSA keys
- The config uses `IdentitiesOnly yes` to prevent SSH agent from trying other keys
- Proper file permissions are already set (600 for private files, 644 for public key)

## 🛠️ Troubleshooting

**Connection refused or timeout:**
```bash
# Check if SSH port is open
ssh -vT git@gitlab.com

# Try specifying port explicitly in config
# Add: Port 22
```

**Permission denied:**
- Verify the public key is added to your GitLab account
- Check key permissions: `ls -l ~/.ssh/gitlab/`
- Verify config syntax: `ssh -G gitlab.com | grep identity`

**Multiple GitLab accounts:**
If you need different keys for different GitLab accounts, add more Host entries:
```
Host gitlab-work
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/gitlab/id_ed25519

Host gitlab-personal
    HostName gitlab.com
    User git
    IdentityFile ~/.ssh/personal/id_ed25519
```

Then clone with: `git clone git@gitlab-work:username/repo.git`

## 📚 Key Fingerprint

Your key's SHA256 fingerprint (for verification in GitLab):
```
SHA256:TP+CbMJioHroT4K9StVtrHrT4Ldk9Zf9RIc5qlok/BU
```

## 🔄 Backup Reminder

Consider backing up your `~/.ssh/gitlab/` directory securely:
```bash
# Create encrypted backup
tar -czf gitlab-ssh-backup.tar.gz ~/.ssh/gitlab/
# Store in secure location (password manager, encrypted drive, etc.)
```