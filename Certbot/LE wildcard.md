
---

- [support:VPS:SSL:lets encrypt wildcard](https://support.beget.ru/questions/view?id=1766)
- [support:VPS:HestiaCP:делегирование lets encrypt wildcard](https://support.beget.ru/questions/view?id=1784)
- [Установка ssl на готовое решение, которое развернуто в docker:](https://hp.beget.ru/ticket/1970732/post-11852924)
- [Перевыпуск серта BBB:]([https://hp.beget.ru/ticket/2395666/post-14586762](https://hp.beget.ru/ticket/2395666/post-14586762))
- [Не подхватывался уже установленный серт на панель HestiaCP, которая работает на порту 8083:]([https://hp.beget.ru/ticket/2398162/post-14632147](https://hp.beget.ru/ticket/2398162/post-14632147))

---

- https://certbot.eff.org/instructions?ws=nginx&os=snap&tab=wildcard

Основной момент, certbot должен через плагин поддерживать DNS сервера куда делегирован домен.

### Snap Support

The Certbot snap supports the x86_64, ARMv7, and ARMv8 architectures. While we strongly recommend that most users install Certbot through the snap, you can find alternate installation instructions [here](https://certbot.eff.org/docs/install.html).

### 1. Check if your DNS provider is supported

See if your DNS provider is supported by Certbot by checking this list in our [documentation](https://certbot.eff.org/docs/using.html#dns-plugins).

[check if your DNS provider supports Certbot](https://certbot.eff.org/docs/using.html#dns-plugins)

 Not supported?

If your DNS provider is not supported, pause here: run Certbot with the manual plugin by using [these steps from our documentation](https://certbot.eff.org/docs/using.html#manual).

Supported?

If your DNS provider is supported, continue with the remaining instructions below.

### 2. SSH into the server

SSH into the server running your HTTP website as a user with sudo privileges.

### 3. Install snapd

You'll need to install snapd and make sure you follow any instructions to enable classic snap support.

Follow these instructions on [snapcraft's site to install snapd](https://snapcraft.io/docs/installing-snapd/).

[install snapd](https://snapcraft.io/docs/installing-snapd/)

### 4. Remove certbot-auto and any Certbot OS packages

If you have any Certbot packages installed using an OS package manager like `apt`, `dnf`, or `yum`, you should remove them before installing the Certbot snap to ensure that when you run the command `certbot` the snap is used rather than the installation from your OS package manager. The exact command to do this depends on your OS, but common examples are `sudo apt-get remove certbot`, `sudo dnf remove certbot`, or `sudo yum remove certbot`.

### 5. Install Certbot

Run this command on the command line on the machine to install Certbot.
```
sudo snap install --classic certbot
```
### 6. Prepare the Certbot command

Execute the following instruction on the command line on the machine to ensure that the `certbot` command can be run.

```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### 7. Confirm plugin containment level

Run this command on the command line on the machine to acknowledge that the installed plugin will have the same `classic` containment as the Certbot snap.

```
sudo snap set certbot trust-plugin-with-root=ok
```

If you encounter issues with running Certbot, you may need to follow this step, then the "Install correct DNS plugin" step, again.

### 8. Install correct DNS plugin

Run the following command, replacing `<PLUGIN>` with the name of your DNS provider.
```
sudo snap install certbot-dns-<PLUGIN>
```
For example, if your DNS provider is Cloudflare, you'd run the following command:
```
sudo snap install certbot-dns-cloudflare
```
### 9. Set up credentials

You'll need to set up DNS credentials.

Follow the steps in the "Credentials" section for your DNS provider to access or create the appropriate credential configuration file. Find credentials instructions for your DNS provider by clicking the [DNS plugin's name on the Documentation list](https://certbot.eff.org/docs/using.html#dns-plugins).

[find your DNS plugin's Credential steps](https://certbot.eff.org/docs/using.html#dns-plugins)

### 10. Choose how you'd like to run Certbot

**Either get and install your certificates...**

Run one of the commands in the "Examples" section of the [instructions for your DNS provider](https://certbot.eff.org/docs/using.html#dns-plugins'), along with the flag `-i nginx`.

**Or, just get a certificate**

Run one of the commands in the "Examples" section of the [instructions for your DNS provider](https://certbot.eff.org/docs/using.html#dns-plugins').

### 11. Test automatic renewal

The Certbot packages on your system come with a cron job or systemd timer that will renew your certificates automatically before they expire. You will not need to run Certbot again, unless you change your configuration. You can test automatic renewal for your certificates by running this command:
```
sudo certbot renew --dry-run
```

The command to renew certbot is installed in one of the following locations:

- `/etc/crontab/`
- `/etc/cron.*/*`
- `systemctl list-timers`
### 12. Confirm that Certbot worked

To confirm that your site is set up properly, visit `https://yourwebsite.com/` in your browser and look for the lock icon in the URL bar.
