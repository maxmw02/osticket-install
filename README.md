<div align="center">

![osTicket Lab](https://capsule-render.vercel.app/api?type=waving&color=0:0f172a,45:2563eb,100:38bdf8&height=250&section=header&text=osTicket%20Lab&fontSize=56&fontColor=ffffff&fontAlignY=42&desc=Build%20a%20local%20help%20desk%20on%20Azure.&descAlignY=63&animation=fadeIn)

<br />

`Windows VM` &nbsp;·&nbsp; `IIS + PHP` &nbsp;·&nbsp; `MySQL` &nbsp;·&nbsp; `osTicket`

<br /><br />

[![Platform](https://img.shields.io/badge/platform-Windows%2010%20%7C%2011-111827?style=for-the-badge&labelColor=2563eb)](#-before-you-begin)
[![Environment](https://img.shields.io/badge/environment-Microsoft%20Azure-111827?style=for-the-badge&labelColor=0284c7)](#-1-create-the-virtual-machine)

<br />

[Overview](#overview) &nbsp;·&nbsp; [Installation](#installation) &nbsp;·&nbsp; [Launch](#launch) &nbsp;·&nbsp; [Cleanup](#cleanup)

</div>

<br />

> This lab walks through deploying **osTicket**, an open-source ticketing system, on a Windows virtual machine in Microsoft Azure. By the end, you will have a working local help desk with a web interface and MySQL database.

<br />

<a id="overview"></a>

## ✦ The build

```text
Azure VM  ──►  IIS + PHP  ──►  MySQL database  ──►  osTicket help desk
 Windows        web server       ticket storage       localhost/osTicket
```

| Phase | Goal |
|:--:|:--|
| `01` | Create and connect to a Windows VM in Azure. |
| `02` | Install IIS, PHP, and the required supporting software. |
| `03` | Configure MySQL and create the `osTicket` database. |
| `04` | Install, configure, and secure osTicket. |

## ◌ Before you begin

- An active Microsoft Azure subscription
- A computer with Remote Desktop access
  - **Windows:** Remote Desktop Connection
  - **macOS:** [Windows App](https://apps.apple.com/us/app/windows-app/id1295203466)
- The [osTicket installation files](https://drive.google.com/uc?export=download&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD)

> **Cost note:** Azure bills virtual machines while they are running. When you are finished with the lab, stop and **deallocate** the VM to avoid ongoing compute charges.

## 1. Create the virtual machine

1. In Azure, create a new **Virtual Machine** in a new resource group.
2. Choose a **Windows 10** or **Windows 11** image.
3. Select a size with at least **2 vCPUs**.
4. Create a username and password—these are your Remote Desktop credentials.
5. Confirm your Windows license eligibility, then select **Review + create** → **Create**.

<details>
<summary><strong>View the VM setup screens</strong></summary>

<br />

![Azure virtual machine configuration](assets/osticket-lab/01-azure-vm-configuration.png)
![Azure review and create screen](assets/osticket-lab/02-azure-review-create.png)
</details>

### Connect to the VM

1. Open Remote Desktop on your computer.
2. In Azure, open the VM’s overview page and copy its **Public IP address**.
3. Paste the IP address into Remote Desktop and sign in with the credentials you created.

<details>
<summary><strong>View the Remote Desktop sign-in screen</strong></summary>

<br />

![Remote Desktop credentials prompt](assets/osticket-lab/03-remote-desktop-sign-in.png)
</details>

<a id="installation"></a>

## 2. Installation

### Get the installation files

1. Inside the VM, download the [osTicket installation files](https://drive.google.com/uc?export=download&id=1b3RBkXTLNGXbibeMuAynkfzdBC1NnqaD).
2. Move the downloaded archive to the desktop, then choose **Extract All**.
3. Delete the `.zip` archive after extracting it; keep the `osTicket-Installation-Files` folder.

<details>
<summary><strong>View the download and extraction screens</strong></summary>

<br />

![Downloaded file location](assets/osticket-lab/04-download-location.png)
![Moving the installation files to the desktop](assets/osticket-lab/05-move-installation-files.png)
![Extracting the installation archive](assets/osticket-lab/06-extract-installation-files.png)
![Deleting the archive after extraction](assets/osticket-lab/07-delete-zip-archive.png)
</details>

### Enable IIS and CGI

1. Open **Control Panel** → **Programs** → **Turn Windows features on or off**.
2. Enable the following feature path:

   ```text
   Internet Information Services
   └── World Wide Web Services
       └── Application Development Features
           └── CGI
   ```

3. Select **OK** and wait for Windows to install the services.

<details>
<summary><strong>View the IIS feature screens</strong></summary>

<br />

![Windows features dialog](assets/osticket-lab/08-windows-features.png)
![IIS and CGI selected](assets/osticket-lab/09-enable-iis-cgi.png)
</details>

### Install PHP and supporting programs

From `osTicket-Installation-Files`, install:

1. `PHPManagerForIIS`
2. `rewrite_amd64`
3. `VC_redistx86`
4. `mysql-5.5.62-win32` — choose **Typical** setup and check **Launch** before finishing.

Create `C:\PHP`, then extract `php-7.3.8-nts-Win32-VC15-x86` into that folder.

<details>
<summary><strong>View the dependency installation screens</strong></summary>

<br />

![Installing URL Rewrite](assets/osticket-lab/10-install-url-rewrite.png)
![Extracting PHP into C drive](assets/osticket-lab/11-extract-php.png)
![MySQL installer](assets/osticket-lab/12-mysql-setup.png)
</details>

## 3. Database setup

### Configure MySQL

1. In the MySQL configuration wizard, choose **Standard Configuration**.
2. Leave the default service name unchanged.
3. Set and securely store the MySQL root password.
4. Select **Execute** → **Finish**.

> For a disposable lab environment, a simple password may be convenient. Never use weak or reused credentials in a real deployment.

### Create the `osTicket` database

1. Install **HeidiSQL** from the installation-files folder.
2. Create a new session using the MySQL credentials you just configured, then select **Open**.
3. Right-click `Unnamed` → **Create new** → **Database**.
4. Name the database `osTicket`.

<details>
<summary><strong>View the database setup screens</strong></summary>

<br />

![HeidiSQL connection screen](assets/osticket-lab/13-heidisql-connect.png)
![Creating the osTicket database](assets/osticket-lab/14-create-osticket-database.png)
</details>

## 4. Configure IIS and osTicket

### Register PHP with IIS

1. Search for **IIS**, then select **Run as administrator**.
2. Open **PHP Manager** → **Register new PHP version**.
3. Set the executable path to:

   ```text
   C:\PHP\php-cgi.exe
   ```

4. In the IIS actions pane, select **Stop**, wait for the server to stop, then select **Start**.

<details>
<summary><strong>View the PHP registration screens</strong></summary>

<br />

![PHP Manager register new PHP version](assets/osticket-lab/15-php-manager-register.png)
![PHP CGI executable path](assets/osticket-lab/16-php-cgi-path.png)
![Restarting IIS](assets/osticket-lab/17-restart-iis.png)
</details>

### Add osTicket to the web root

1. Extract `osTicket-v1.15.8` from the installation-files folder.
2. Copy its `upload` folder to:

   ```text
   C:\inetpub\wwwroot
   ```

3. Rename `upload` to `osTicket`.
4. Reload IIS: **Stop** → **Start**.

<details>
<summary><strong>View the osTicket IIS site screen</strong></summary>

<br />

![osTicket site in IIS](assets/osticket-lab/18-osTicket-iis-site.png)
</details>

### Enable required PHP extensions

In IIS, navigate to **Sites** → **Default Web Site** → **osTicket** → **PHP Manager** → **Enable or disable an extension**. Enable:

```text
php_imap.dll
php_intl.dll
php_opcache.dll
```

<details>
<summary><strong>View the PHP extension screens</strong></summary>

<br />

![Opening PHP extensions in IIS](assets/osticket-lab/19-enable-php-extensions.png)
![Required PHP extensions list](assets/osticket-lab/20-php-extension-list.png)
![Enabling PHP opcache](assets/osticket-lab/21-enable-opcache.png)
</details>

### Create the configuration file

1. Open `C:\inetpub\wwwroot\osTicket\include`.
2. Rename `ost-sampleconfig.php` to `ost-config.php`.
3. Right-click `ost-config.php` → **Properties** → **Security** → **Advanced**.
4. Select **Disable inheritance** → **Remove**.
5. Select **Add** → **Select a principal** → enter `Everyone` → **OK**.
6. Grant **Full control**, then apply the changes.

<details>
<summary><strong>View the configuration and permissions screens</strong></summary>

<br />

![Renaming the osTicket config file](assets/osticket-lab/22-rename-ost-config.png)
![Advanced security settings for the config file](assets/osticket-lab/23-config-security-advanced.png)
![Disabling inherited permissions](assets/osticket-lab/24-disable-inheritance.png)
![Granting temporary full control](assets/osticket-lab/25-grant-full-control.png)
</details>

> **Temporary lab permission:** Full control is only needed during installation. It is intentionally removed in the cleanup step—do not leave it enabled.

<a id="launch"></a>

## 5. Launch & verify

1. In the VM’s browser, visit [http://localhost/osTicket](http://localhost/osTicket) and select **Continue**.
2. Enter a help desk name, default email address, and administrator details.
3. Use a **different** email address for the default email and administrator email.
4. Enter the database settings:

   | Field | Value |
   |:--|:--|
   | Database | `osTicket` |
   | Username | Your MySQL username |
   | Password | Your MySQL password |

5. Select **Install Now**.

<details>
<summary><strong>View the osTicket installer screens</strong></summary>

<br />

![osTicket web installer configuration](assets/osticket-lab/26-osticket-web-installer.png)
![osTicket installation complete](assets/osticket-lab/27-install-complete.png)
</details>

<a id="cleanup"></a>

## ↗ Cleanup

After a successful install:

1. Delete the setup directory:

   ```text
   C:\inetpub\wwwroot\osTicket\setup
   ```

2. Change `ost-config.php` permissions back to **read-only**.
3. Deallocate the Azure VM whenever the lab is not in use.

## ✧ Useful links

| Destination | Address |
|:--|:--|
| Admin panel | [http://localhost/osTicket/scp/login.php](http://localhost/osTicket/scp/login.php) |
| End-user portal | [http://localhost/osTicket/](http://localhost/osTicket/) |

<br />

---

<div align="center">

</div>
