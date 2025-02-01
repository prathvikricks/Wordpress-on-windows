# Hosting WordPress on Windows using Nginx with PHP-FPM

This guide outlines the steps to host a WordPress application on a Windows machine using Nginx as the web server, PHP-FPM for running PHP, and automatic startup for PHP-FPM and Nginx after system reboot.

## Prerequisites

- A Windows machine.
- A WordPress application that you want to host.
- Nginx (Web Server).
- PHP 7.x or 8.x (with PHP-FPM).
- NSSM (Non-Sucking Service Manager) to manage PHP-FPM as a service.

## Step 1: Install Nginx on Windows

1. Download Nginx for Windows from the [official Nginx website](https://nginx.org/en/download.html).
2. Extract the Nginx files to a folder (e.g., `C:\nginx`).
3. Open Command Prompt and navigate to the Nginx directory:
    ```bash
    cd C:\nginx
    ```
4. Start Nginx by running:
    ```bash
    start nginx
    ```
5. Verify that Nginx is running by visiting [http://localhost](http://localhost) in your browser.

## Step 2: Install PHP on Windows

1. Download PHP (Thread Safe version) from the [official PHP website](https://windows.php.net/download/).
2. Extract the files to a folder (e.g., `C:\php`).
3. Rename `php.ini-development` to `php.ini` and enable the necessary extensions:
    - Enable cgi by uncommenting: `extension=php_cgi.dll`
    - Enable openssl for HTTPS support: `extension=openssl`
4. Test PHP by running:
    ```bash
    php -v
    ```
    in Command Prompt to check that it is correctly installed.

## Step 3: Install PHP-FPM using NSSM

To keep PHP-FPM running as a service:

1. Download NSSM (Non-Sucking Service Manager) from [nssm.cc](https://nssm.cc/).
2. Extract NSSM to a folder (e.g., `C:\nssm`).
3. In Command Prompt, navigate to the NSSM folder:
    ```bash
    cd C:\nssm\win64
    ```
4. Run the following command to install PHP-FPM as a service:
    ```bash
    .\nssm.exe install PHP-FPM "C:\php\php-cgi.exe" -b 127.0.0.1:9000
    ```
    This command registers PHP-FPM to run as a service on `127.0.0.1:9000`.
5. To start the PHP-FPM service, use:
    ```bash
    .\nssm.exe start PHP-FPM
    ```
6. Verify the PHP-FPM service is running:
    - Open **Services Manager** (`services.msc`), find **PHP-FPM**, and ensure it is set to **Automatic**.

## Step 4: Configure Nginx to Use PHP-FPM

1. Open the Nginx configuration file `nginx.conf` or create a new file (e.g., `wordpress.conf`) in the `C:\nginx\conf\` folder.
2. Add a new server block to handle WordPress requests:
    ```nginx
    server {
        listen 80;
        server_name localhost;

        root C:/path/to/your/wordpress/directory;
        index index.php index.html index.htm;

        location / {
            try_files $uri $uri/ =404;
        }

        location ~ \.php$ {
            fastcgi_pass 127.0.0.1:9000;
            fastcgi_index index.php;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            include fastcgi_params;
        }
    }
    ```
    Replace `C:/path/to/your/wordpress/directory` with the path to your WordPress application.
3. Restart Nginx to apply the changes:
    ```bash
    nssm restart Nginx
    ```

## Step 5: Enable PHP-FPM and Nginx to Start Automatically

1. Open **Services Manager** (`services.msc`).
2. Locate **PHP-FPM** and **Nginx** in the list of services.
3. Right-click on each service and set the **Startup type** to **Automatic**.

## Step 6: Test the Setup

1. Visit [http://localhost](http://localhost) in your browser to check if the WordPress site loads.
2. Verify that PHP-FPM is running on port 9000 using the following command:
    ```bash
    netstat -ano | findstr :9000
    ```
3. If everything is working fine, your WordPress site should be accessible, and PHP should be processed via PHP-FPM.

## Troubleshooting

### Issue 1: 502 Bad Gateway Error
- **Problem**: Nginx cannot connect to PHP-FPM, resulting in a 502 Bad Gateway error.
- 
### Step 1: Install Required Software
We need to install PHP, MySQL, Nginx, and WordPress on your Windows machine.

### 1.1 Install PHP
Download the latest Non-Thread Safe version of PHP from PHP for Windows.

Extract the ZIP file to C:\php.

### Rename php.ini-development to php.ini.

Open php.ini in Notepad and uncomment the following lines by removing ;:

```bash
extension=curl
extension=gd
extension=mbstring
extension=mysqli
extension=openssl
extension=pdo_mysql
```
### Add PHP to your system environment variables:

Open Control Panel > System > Advanced System Settings > Environment Variables.
Under "System Variables", find Path, click Edit, and add C:\php.
Test PHP installation:

Open Command Prompt (cmd) and type:
```bash
php -v
```
It should show the installed PHP version.


1.2 Install MySQL
### Download MySQL Community Edition from MySQL Downloads.
Install MySQL with default settings.
Set up a root password and note it down.
Open MySQL command-line client and create a WordPress database:
sql
```bash
CREATE DATABASE wordpress;
CREATE USER 'wp_user'@'localhost' IDENTIFIED BY 'yourpassword';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wp_user'@'localhost';
FLUSH PRIVILEGES;
```



- **Solution**:
  - Ensure PHP-FPM is running by checking the **Services Manager**.
  - Verify that `fastcgi_pass` in Nginx is pointing to `127.0.0.1:9000` (the correct PHP-FPM port).
  - Restart both Nginx and PHP-FPM.

### Issue 2: PHP-FPM Not Starting
- **Problem**: PHP-FPM doesn't start on boot or stops unexpectedly.
- **Solution**:
  - Use NSSM to ensure PHP-FPM is running as a Windows service and starts automatically:
    ```bash
    .\nssm.exe install PHP-FPM "C:\php\php-cgi.exe" -b 127.0.0.1:9000
    ```
  - Check the **Services Manager** to ensure the PHP-FPM service is running.

### Issue 3: NSSM Not Recognized
- **Problem**: NSSM commands aren't recognized in PowerShell or Command Prompt.
- **Solution**:
  - Make sure NSSM is located in the correct directory.
  - Run the commands from the folder where `nssm.exe` is located (e.g., `C:\nssm\win64`).

### Issue 4: Nginx Not Starting Automatically

Open Command Prompt as Administrator.
Navigate to the folder where nssm.exe is located and run the following command:
This will open a promt UI.
```bash
nssm install Nginx
```
Now add the path where nginx.exe is located and it save.

## After this run command to start nginx.
```bash
net start Nginx
```

## Conclusion

By following the above steps, you have successfully set up WordPress on a Windows machine using Nginx as the web server, with PHP-FPM handling PHP requests. Additionally, Nginx and PHP-FPM have been configured to run automatically after system reboots, ensuring a smooth hosting environment.

This setup allows you to host WordPress on Windows with minimal manual intervention, offering an efficient and automated process.
