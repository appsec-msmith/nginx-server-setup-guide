# Building an NGINX Server from Scratch (DigitalOcean Droplet)

A walkthrough for provisioning a DigitalOcean droplet and installing NGINX on both Ubuntu (via `apt-get`) and CentOS (via `yum`).

## Prerequisites

- A DigitalOcean account
- An SSH client (e.g. [Transmit](https://panic.com/transmit/), [Cyberduck](https://cyberduck.io/), [FileZilla](https://filezilla-project.org/))

---

## 1. Create the Droplet

1. Create a new Droplet with the following settings:
   - **OS:** Ubuntu
   - **CPU:** Single core
   - **IPv6:** Enabled
2. Once the droplet is created, note its IP address.

## 2. Connect via SSH

Connect to the droplet using its IP address:

```bash
ssh root@<your_droplet_ip>
```

When prompted:

```
Are you sure you want to continue connecting (yes/no)?
```

Type `yes`.

---

## 3. Install NGINX on Ubuntu

Ubuntu uses the `apt-get` package manager.

> **Note:** Unlike some Linux package managers, `apt-get` doesn't install extra/optional add-ons by default — only what you explicitly request.

### Update the package index

```bash
apt-get update
```

### Install NGINX

```bash
apt-get install nginx
```

If prompted with dependency packages:

```
Do you want to continue? [Y/n]
```

Type `Y`.

### Verify NGINX is running

```bash
ps aux | grep nginx
```

You should see a **master process** and a **worker process** listed.

### Test in a browser

Get the server's IP address:

```bash
ifconfig
```

Copy the `inet` address and paste it into a web browser — you should see the default NGINX welcome page.

### Locate the configuration files

```bash
ls -l /etc/nginx/
```

This directory contains NGINX's configuration files.

### Log out

```bash
exit
```

---

## 4. Install NGINX on CentOS (Alternative)

To use CentOS instead of Ubuntu, rebuild the droplet:

1. In the DigitalOcean dashboard, select **Rebuild Droplet**.
2. Choose the latest version of **CentOS 7.4 x64** and confirm.
3. Clear your local terminal:

   ```bash
   clear
   ```

### Reconnect via SSH

Since the droplet was rebuilt, the same IP now has a new host key, so SSH will throw a host key verification error. Resolve it one of two ways:

- Manually remove the old host entry, **or**
- Run:

  ```bash
  ssh-keygen -R <your_droplet_ip>
  ```

  (`-R` removes the old key data associated with that IP.)

Then reconnect:

```bash
ssh root@<your_droplet_ip>
```

### Install NGINX via YUM

CentOS uses the `yum` package manager:

```bash
yum install nginx
```

By default, **YUM won't find the NGINX package** — it doesn't ship with CentOS out of the box.

### Enable the EPEL repository

NGINX is available through the EPEL (Extra Packages for Enterprise Linux) repository:

```bash
yum install epel-release
```

Confirm any prompts with `y` until the install completes.

### Install NGINX again

```bash
yum install nginx
```

Let the installation run to completion.

### Confirm the install

```bash
ls -l /etc/nginx/
```

You should see the configuration files listed.

### Start the NGINX service

Unlike the Ubuntu install, YUM installs NGINX as a service **without starting it automatically**. Check for a running process:

```bash
ps aux | grep ng
```

At this point, nothing will show up, and the browser won't load the page yet. Start the service manually:

```bash
service nginx start
```

You should now see a **master** and **worker** process running, and reloading the browser will show the NGINX welcome page (a different version/style than the Ubuntu default).

---

## Notes

- This method (via `apt-get`/`yum`) is the quickest way to get NGINX running.
- **The recommended approach for production use is to build NGINX from source**, which allows for custom modules and configuration options not available through prebuilt packages.
