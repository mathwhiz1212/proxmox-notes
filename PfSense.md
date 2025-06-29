# Dynamic DNS

Go to pfsense > Services > Dynamic DNS

Select Add

Service type `Namecheap`

Interface `WAN`

hostname: `lab` `joshmudge.com` (`lab` in the first box, `joshmudge.com` in the second, for `lab.joshmudge.com`).

If you want to do a sub-sub-domain, do: hostname: `pve1.lab` `joshmudge.com` (`pve1.lab` in the first box for `pve1.lab.joshmudge.com`)

Grab your dynamic DNS password from your Advanced DNS page for your domain name on Namecheap and input it in the password field.

Add a description and hit save.

Use the duplicate icon to duplicate the Dynamic DNS updater for other domains and change the domain name and description as needed.

You will need to make sure the record you want to update is `A + Dynamic DNS` and not just an `A` record.

**Backup your PfSense configuration.**
