1. Generate key at [Keys page](https://login.tailscale.com/admin/settings/keys) (read more at [Auth keys](https://tailscale.com/kb/1085/auth-keys)). Key name as machine name is a good practice.
2. Login to target server.
3. Ensure tailscale service is running.
4. Add tailscale to path: `export PATH=$PATH:$(getcfg SHARE_DEF defVolMP -f /etc/config/def_share.info)/.qpkg/Tailscale/`
5. Run `sudo tailscale up --auth-key=tskey-auth-ktsPX...`
