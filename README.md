# Secure-Vhost

## Description

Create a self-signed **SSL-secured VirtualHost** with a simple command.

#### But why?

Modern websites and web-based apps are going full-encrypted, some API vendors (such as Google Cloud Platform, Mapbox, Facebook...) **require**  that you use a secure SSL/TLS certificate to use their services. Now you can easily do it in localhost!

#### Important notes

- **This script is intended to use on XAMPP for macOS.** Non-XAMPP version coming soon!
- It requires `sudo`.
- It requires `openssl`.
- I'm not a bash scripting expert, so this may have failures. GitHub issues are very welcomed.
___

### How it works

1. Script will check if you already have the provided domain in your `httpd-vhosts.conf` file named `yourdomain.local:443`. If it exists, the whole process will stop.

2. If it does not exist that vhost entry, check if that domain exists in your Mac's `hosts` file. If doesn't exists, add an entry to it and continue. Otherwise, will continue.

3. Check if exists a folder called "Secure-Vhost" in your `$HOME` directory, and create it if does not.

4. Check if exists a folder called "yourdomain.local" inside that "Secure-Vhost" folder, and ask you if you want to create it and make you the owner (`chown -R $USER:staff`).

5. Self-signed certificate is created.

6. Safe VirtualHost is created in XAMPP's configuration file.

7. Self-signed certificate is added to your System keychain.

8. Optionally, XAMPP Apache will restart. You need to restart to apply this changes in your server.

___

## About the certificate

**IMPORTANT. Read this carefully and a couple times if necessary:** this certificate does **not** work on production environments, and is not intended to!

If you need a certificate for production environments, just use [Let's Encrypt](https://letsencrypt.org/), it's free, easy and supported on every browser (even IE!).

#### Technology

This certificate is a self-signed [X.509](https://en.wikipedia.org/wiki/X.509) (v3) certificate created with OpenSSL. It uses **SHA512** as message digest and a **2048-bits RSA key**. It'll automatically expire in 365 days (this can be modified, read below "_Usage and options_"). All the Subject information fields are empty, except **_Common Name_ and _Alternative Name_** for obvious reasons. This can be modified as you need.

#### How this looks in the browser (Chrome):

1. VirtualHost created successfully:

![VirtualHost created successfully](https://i.imgur.com/TIkk8eT.png)

2. Chrome Devtools:

![Chrome Devtools](https://i.imgur.com/r0W5yAB.png)

3. Certificate information:

![Certificate information](https://i.imgur.com/E9150eQ.png)

___

## Install

Add the main file (`secure-vhost`) in your local bin folder and make it executable so you can use it globally.

```
mv /your/path/to/macos-apache-secure-vhost/secure-vhost /usr/local/bin/secure-vhost && chmod +x /usr/local/bin/secure-vhost
```

___

## Usage and options

You can see **all options available** running one of this commands:

```
$ secure-vhost
```

or

```
$ secure-vhost -h
```

### Basic usage

**You have to** pass `-d` as required flag for the VirtualHost domain. This will automatically create your secure VirtualHost (as explained on "_How it works_"), **assuming** you have a folder with the same name as your domain inside your XAMPP _htdocs_ folder.

```
$ secure-vhost -d yourdomain.local
```

### Set foldername

If you don't have a `htdocs/yourdomain.local` folder (and don't want to create one), or want to specify any other folder name, you can use:

```
$ secure-vhost -d yourdomain.local -f myproject
```

This will tell the script to set the VirtualHost `DocumentRoot` and `Directory` pointing to your `htdocs/myproject` folder.

### Set certificate expiration days

By default, the certificate is valid for 365 days, but you can change that using the flag `-D`.

```
$ secure-vhost -d yourdomain.local -D 730
```

This will make the certificate expire in 2 years counting since the script execution.

### Restart Apache after installation

You can pass `-r` so the script will automatically restart XAMPP Apache after adding the certificate to your System keychain.

```
$ secure-vhost -d yourdomain.local -r
```

Behind the scenes, _Secure-Vhost_ will run this:

```
sudo /Applications/XAMPP/xamppfiles/xampp restartapache
```

### Everything together

Of course you can have all this options together!

```
$ secure-vhost -d yourdomain.local -f myproject -D 730 -r
```

### Show variables

_Secure-Vhost_ uses variables -that you can change- to run everything. You can see them using `-v`.  

```
$ secure-vhost -v
```

By default it will return this:

```
USAGE: secure-vhost -d yourdomain.local [-f foldername] [-D 365] [-r] [-v]

  -d: Domain for your new VirtualHost
  -f: Folder name inside of default XAMPP htdocs folder
  -D: Days until certificate expires
  -r: Restart XAMPP Apache after VirtualHost creation
  -v: Display current variables



  More info: https://github.com/jimmyadaro/macos-apache-secure-vhost



```

Need to change this? Just edit the main script (`secure-vhost`), you'll find the variables there.

### Done! ✅

**After Apache has been restarted**, just open `https://yourdomain.local` on your favorite browser and _voilà_!

#### It's not working

Please check this:

- If you didn't restarted Apache (`-r`) yet, you need to do it in order to allow Apache recognize this new VirtualHost.

- Check other VirtualHosts (if you have others, or _localhost_) are running besides this new one. If not, then **Apache may not fully restarted**, just wait.

---

### License

Copyright 2018 Jimmy Adaro - MIT License
