---
title: "Installing SnowSQL"
url: "https://docs.snowflake.com/en/user-guide/snowsql-install-config"
---

# Installing SnowSQL

This topic describes how to download and install SnowSQL on all supported platforms.

To download the SnowSQL installer, go to the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page.

Note

The SnowSQL 1.3.0 release disabled automatic upgrades, so you must manually download and reinstall for each new version.

## Installing SnowSQL on Linux using the installer

This section describes how to download, verify, and run the installer package to install SnowSQL on Linux.

To upgrade SnowSQL manually (such as if your software installation policy prohibits upgrading automatically), use the
RPM package to install SnowSQL. The RPM package does not set up SnowSQL to upgrade automatically. For instructions, see
[Installing SnowSQL on Linux using the RPM package](#label-snowsql-linux-rpm) (in this topic).

### Setting the download directory and configuration file location

By default, the SnowSQL installer downloads the binaries to the following directory:

`~/.snowsql`

Consequently, the [configuration file](snowsql-config.html#label-configuring-snowsql) is located under the download directory:

`~/.snowsql/config`

To change both the download directory and location of the configuration file, set the `WORKSPACE` environment variable to
any user-writable directory. This approach is particularly useful if you have an isolated SnowSQL environment for each process.

In addition, you can separate the download directory from the configuration file by setting the `SNOWSQL_DOWNLOAD_DIR` environment variable so that
multiple SnowSQL processes can share the binaries. For example:

> CopyExpand
>
> ```
> $ SNOWSQL_DOWNLOAD_DIR=/var/shared snowsql -h
> ```
>
> Show lessSee more
>
> Scroll to top

Note that `SNOWSQL_DOWNLOAD_DIR` is supported starting with the SnowSQL 1.1.70 bootstrap version. To check the version you are using, execute the
following command from the terminal window prompt:

> CopyExpand
>
> ```
> $ snowsql --bootstrap-version
> ```
>
> Show lessSee more
>
> Scroll to top

### Downloading the SnowSQL installer

Go to the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page, find the version of the SnowSQL that you want to install, and download the files with the
following filename extensions:

* `.bash` (the installer script)
* `.bash.sig` (the signature that you can use to [verify the downloaded package](#label-snowsql-linux-verify-package-sig))

### Using curl to download the SnowSQL installer

If you want to download the installer from a script or a terminal window (such as using [curl](https://curl.se/), rather than your web browser),
you can download the installers directly from the [Snowflake Client Repository](snowflake-client-repository.html#label-client-download-repository). For increased flexibility, Snowflake
provides both Amazon Web Services (AWS) and Azure endpoints for the repository. Accounts hosted on any supported cloud platform
can download the installer from either endpoint.

Run `curl` (or an equivalent command-line tool) to download the installer. The `curl` syntax is as follows:

AWS endpoint:
:   CopyExpand

    ```
    $ curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/linux_x86_64/snowsql-<version>-linux_x86_64.bash
    ```

    Show lessSee more

    Scroll to top

Microsoft Azure endpoint:
:   CopyExpand

    ```
    $ curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/linux_x86_64/snowsql-<version>-linux_x86_64.bash
    ```

    Show lessSee more

    Scroll to top

Where:

* `<version>` is the combined SnowSQL major, minor, and patch versions. For example, for version 1.4.3, the major version is 1, the
  minor version is 4, and the patch version is 3. So, the version is 1.4.3.
* `<bootstrap_version>` is the combined SnowSQL major and minor versions. For example, for version 1.4.3, the major version is
  1 and the minor version is 3, so the bootstrap version is 1.4.

For example, to download the SnowSQL installer where `<bootstrap_version>` is 1.4 and `<version>` is 1.4.3:

AWS endpoint:
:   ```
    $ curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/1.4/linux_x86_64/snowsql-1.4.3-linux_x86_64.bash
    ```

Microsoft Azure endpoint:
:   ```
    $ curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/1.4/linux_x86_64/snowsql-1.4.3-linux_x86_64.bash
    ```

For more information about SnowSQL versions, see [Understanding SnowSQL Versioning](#understanding-snowsql-versioning) (in this topic).

### Verifying the package signature

To verify the signature for the downloaded package:

1. Download and import the latest Snowflake GPG public key from the public keyserver by entering the following command, using the GPG key associated with the SnowSQL version:

   > * For SnowSQL 1.3.3 and higher:
   >
   >   ```
   >   $ gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 2A3149C82551A34A
   >   ```
   > * For SnowSQL 1.2.24 through 1.3.2:
   >
   >   ```
   >   $ gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 630D9F3CAB551AF3
   >   ```
   >
   > Note
   >
   > If this command fails with the following error:
   >
   > > CopyExpand
   > >
   > > ```
   > > gpg: keyserver receive failed: Server indicated a failure
   > > ```
   > >
   > > Show lessSee more
   > >
   > > Scroll to top
   >
   > then specify that you want to use port 80 for the keyserver:
   >
   > > CopyExpand
   > >
   > > ```
   > > gpg --keyserver hkp://keyserver.ubuntu.com:80  ...
   > > ```
   > >
   > > Show lessSee more
   > >
   > > Scroll to top
2. Download the GPG signature and verify the signature:

   ```
   # If you prefer to use curl to download the signature file, run this command:
   curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/1.4/linux_x86_64/snowsql-1.4.3-linux_x86_64.bash.sig

   # Verify the package signature.
   gpg --verify snowsql-1.4.3-linux_x86_64.bash.sig snowsql-1.4.3-linux_x86_64.bash
   ```

   or, if you are downloading the signature file from the [Azure endpoint](#label-snowsql-linux-installer-curl):

   ```
   # If you prefer to use curl to download the signature file, run this command:
   curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/1.4/linux_x86_64/snowsql-1.4.3-linux_x86_64.bash.sig

   # Verify the package signature.
   gpg --verify snowsql-1.4.3-linux_x86_64.bash.sig snowsql-1.4.3-linux_x86_64.bash
   ```

   Note

   Verifying the signature produces a warning similar to the following:

   > CopyExpand
   >
   > ```
   > gpg: Signature made Mon 24 Sep 2018 03:03:45 AM UTC using RSA key ID <gpg_key_id>
   > gpg: Good signature from "Snowflake Computing <snowflake_gpg@snowflake.net>" unknown
   > gpg: WARNING: This key is not certified with a trusted signature!
   > gpg: There is no indication that the signature belongs to the owner.
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top

   To avoid the warning, you can grant the Snowflake GPG public key implicit trust.
3. Your local environment can contain multiple GPG keys; however, for security reasons, Snowflake periodically rotates the public GPG key.
   As a best practice, we recommend deleting the existing public key after confirming that the latest key works with the latest signed
   package. For example:

   CopyExpand

   ```
   gpg --delete-key "Snowflake Computing"
   ```

   Show lessSee more

   Scroll to top

### Installing SnowSQL using the installer

1. Open a terminal window.
2. Run the Bash script installer from the download location:

   > CopyExpand
   >
   > ```
   > bash snowsql-linux_x86_64.bash
   > ```
   >
   > Show lessSee more
   >
   > Scroll to top
3. Follow the instructions provided by the installer.

Note

The installation can be automated by setting the following environment variables:

* `SNOWSQL_DEST`: Target directory of the `snowsql` executable.
* `SNOWSQL_LOGIN_SHELL`: The login shell initialization file, which includes the `PATH` environment update.

CopyExpand

```
SNOWSQL_DEST=~/bin SNOWSQL_LOGIN_SHELL=~/.profile bash snowsql-linux_x86_64.bash
```

Show lessSee more

Scroll to top

When you install a new major or minor version, SnowSQL does not upgrade itself immediately. Rather, you must log into your [Snowflake account](gen-conn-config) using SnowSQL and remain connected for a sufficient period of time for the auto-upgrade feature to upgrade the client to the latest release. To verify the SnowSQL version that currently starts when you run the client, use the `-v` option without a value:

> CopyExpand
>
> ```
> snowsql -v
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Expand
>
> ```
> Version: 1.3.1
> ```
>
> Show lessSee more
>
> Scroll to top

To force SnowSQL to install and use a specific version, use the `-v` option and specify the version you want to install. For example, execute the following command for version 1.3.0:

> CopyExpand
>
> ```
> snowsql -v 1.3.0
> ```
>
> Show lessSee more
>
> Scroll to top

## Installing SnowSQL on Linux using the RPM package

To upgrade software manually, you can use the RPM package (rather than the
[installer](#label-snowsql-linux-installer)) to install SnowSQL. The RPM package does not support automatic upgrades.

### Downloading the SnowSQL RPM package

Go to the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page, find the version of the SnowSQL that you want to install, and download the file with the
`.rpm` filename extension.

### Installing the SnowSQL RPM package

The downloaded RPM file can be installed the way that any other RPM package is installed:

CopyExpand

```
rpm -i <package_name>
```

Show lessSee more

Scroll to top

## Installing SnowSQL on macOS using the installer

This section describes how to download and run the installer package to install SnowSQL on macOS.

### Setting the download directory and configuration file location

By default, the SnowSQL installer downloads the binaries to the following directory:

`~/.snowsql`

Consequently, the [configuration file](snowsql-config.html#label-configuring-snowsql) is located under the download directory:

`~/.snowsql/config`

You can change both the download directory and location of the configuration file by setting the `WORKSPACE` environment variable to any user-writable
directory. This is particularly useful if you have an isolated SnowSQL environment for each process.

In addition, you can separate the download directory from the configuration file by setting the `SNOWSQL_DOWNLOAD_DIR` environment variable so that
multiple SnowSQL processes can share the binaries. For example:

> CopyExpand
>
> ```
> SNOWSQL_DOWNLOAD_DIR=/var/shared snowsql -h
> ```
>
> Show lessSee more
>
> Scroll to top

Note that `SNOWSQL_DOWNLOAD_DIR` is supported starting with the SnowSQL 1.1.70 bootstrap version. To check the version you are using, execute the
following command from the terminal window prompt:

> CopyExpand
>
> ```
> snowsql --bootstrap-version
> ```
>
> Show lessSee more
>
> Scroll to top

### Downloading the SnowSQL installer

To download the SnowSQL installer, go to the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page. This version of the SnowSQL installer enables auto-upgrade
for patches.

### Using curl to download the SnowSQL installer

If you want to download the installer from a script or a terminal window (such as using [curl](https://curl.se/), rather than your web browser),
you can download the installers directly from the [Snowflake Client Repository](snowflake-client-repository.html#label-client-download-repository). For increased flexibility, Snowflake
provides both Amazon Web Services (AWS) and Azure endpoints for the repository. Accounts hosted on any supported cloud platform
can download the installer from either endpoint.

Run `curl` (or an equivalent command-line tool) to download the installer. The `curl` syntax is as follows:

AWS endpoint:
:   CopyExpand

    ```
    curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/darwin_x86_64/snowsql-<version>-darwin_x86_64.pkg
    ```

    Show lessSee more

    Scroll to top

Microsoft Azure endpoint:
:   CopyExpand

    ```
    curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/darwin_x86_64/snowsql-<version>-darwin_x86_64.pkg
    ```

    Show lessSee more

    Scroll to top

where:

* `<version>` is the combined SnowSQL major, minor, and patch versions. For example, for version 1.4.3, the major version is 1, the
  minor version is 4, and the patch version is 3. So, the version is 1.4.3.
* `<bootstrap_version>` is the combined SnowSQL major and minor versions. For example, for version 1.4.3, the major version is
  1 and the minor version is 4, so the bootstrap version is 1.4.

For example, to download the SnowSQL installer where `<bootstrap_version>` is 1.4 and `<version>` is 1.4.3:

AWS endpoint:
:   ```
    curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/1.4/darwin_x86_64/snowsql-1.4.3-darwin_x86_64.pkg
    ```

Microsoft Azure endpoint:
:   ```
    curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/1.4/darwin_x86_64/snowsql-1.4.3-darwin_x86_64.pkg
    ```

For more information about SnowSQL versions, see [Understanding SnowSQL Versioning](#understanding-snowsql-versioning) (in this topic).

The macOS operating system can verify the installer signature automatically, so GPG signature verification is not needed.

### Installing SnowSQL using the installer

1. Open `snowsql-darwin_x86_64.pkg` in the download location to run the installer PKG file.
2. Follow the instructions provided by the installer.

Note

The installation can be automated by running the installer from the command line. The target directory can be set to either
`CurrentUserHomeDirectory` (`~/Applications` directory) or `LocalSystem` (`/Applications` directory):

CopyExpand

```
installer -pkg snowsql-darwin_x86_64.pkg -target CurrentUserHomeDirectory
```

Show lessSee more

Scroll to top

When you install a new major or minor version, SnowSQL does not upgrade itself immediately. Rather, you must log into your Snowflake account using SnowSQL and remain connected for a sufficient period of time for the auto-upgrade feature to upgrade the client to the latest release. To verify the SnowSQL version that currently starts when you run the client, use the `-v` option without a value:

> CopyExpand
>
> ```
> snowsql -v
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Expand
>
> ```
> Version: 1.3.0
> ```
>
> Show lessSee more
>
> Scroll to top

To force SnowSQL to install and use a specific version, use the `-v` option and specify the version you want to install. For example, execute the following command for version 1.3.1:

> CopyExpand
>
> ```
> snowsql -v 1.3.1
> ```
>
> Show lessSee more
>
> Scroll to top

#### Configuring the Z shell alias (macOS only)

If Z shell (also known as zsh) is your default terminal shell, set an alias to the SnowSQL executable so that you can run SnowSQL on the command line in Terminal. The SnowSQL installer installs the executable in `/Applications/SnowSQL.app/Contents/MacOS/snowsql` and appends this path to the PATH or alias entry in `~/.profile`. Because zsh does not normally read this file, add an alias to this path in `~/.zshrc`, which zsh does read.

To add an alias to the SnowSQL executable:

1. Open (or create, if missing) the `~/.zshrc` file.
2. Add the following line:

   CopyExpand

   ```
   alias snowsql=/Applications/SnowSQL.app/Contents/MacOS/snowsql
   ```

   Show lessSee more

   Scroll to top
3. Save the file.

## Installing SnowSQL on macOS using homebrew cask

[Homebrew Cask](https://caskroom.github.io/) is a popular extension of [Homebrew](https://brew.sh/) used for package distribution, installation, and
maintenance. There is no separate SnowSQL installer to download. If Homebrew Cask is installed on your macOS platform, you can install Snowflake directly.

Run the `brew install` command, specifying `snowflake-snowsql` as the cask to install:

CopyExpand

```
brew install --cask snowflake-snowsql
```

Show lessSee more

Scroll to top

### Configuring the Z shell alias (macOS only)

If Z shell (also known as zsh) is your default terminal shell, set an alias to the SnowSQL executable so that you can run SnowSQL on the command line in Terminal. The SnowSQL installer installs the executable in `/Applications/SnowSQL.app/Contents/MacOS/snowsql` and appends this path to the PATH or alias entry in `~/.profile`. Because zsh does not normally read this file, add an alias to this path in `~/.zshrc`, which zsh does read.

To add an alias to the SnowSQL executable:

1. Open (or create, if missing) the `~/.zshrc` file.
2. Add the following line:

   CopyExpand

   ```
   alias snowsql=/Applications/SnowSQL.app/Contents/MacOS/snowsql
   ```

   Show lessSee more

   Scroll to top
3. Save the file.

## Installing SnowSQL on Microsoft Windows using the installer

This section describes how to download and run the installer package to install SnowSQL on Microsoft Windows.

### Setting the download directory and configuration file location

By default, the SnowSQL installer downloads the binaries to the following directory:

`%USERPROFILE%\.snowsql`

Consequently, the [configuration file](snowsql-config.html#label-configuring-snowsql) is located under the download directory:

`%USERPROFILE%\.snowsql\config`

You can change both the download directory and location of the configuration file by setting the `WORKSPACE` environment variable to any user-writable
directory. This is particularly useful if you have an isolated SnowSQL environment for each process.

In addition, you can separate the download directory from the configuration file by setting the `SNOWSQL_DOWNLOAD_DIR` environment variable so that
multiple SnowSQL processes can share the binaries. For example:

> CopyExpand
>
> ```
> SNOWSQL_DOWNLOAD_DIR=/var/shared snowsql -h
> ```
>
> Show lessSee more
>
> Scroll to top

Note that `SNOWSQL_DOWNLOAD_DIR` is supported starting with the SnowSQL 1.1.70 bootstrap version. To check the version you are using, execute the
following command from the terminal window prompt:

> CopyExpand
>
> ```
> snowsql --bootstrap-version
> ```
>
> Show lessSee more
>
> Scroll to top

### Downloading the SnowSQL installer

To download the SnowSQL installer, go to the [SnowSQL Download](https://developers.snowflake.com/snowsql/) page. This version of the SnowSQL installer enables auto-upgrade
for patches.

### Using curl to download the SnowSQL installer

If you want to download the installer from a script or a terminal window (such as using [curl](https://curl.se/), rather than your web browser),
you can download the installers directly from the [Snowflake Client Repository](snowflake-client-repository.html#label-client-download-repository). For increased flexibility, Snowflake
provides both Amazon Web Services (AWS) and Azure endpoints for the repository. Accounts hosted on any supported cloud platform
can download the installer from either endpoint.

Run `curl` (or an equivalent command-line tool) to download the installer. The `curl` syntax is as follows:

AWS endpoint:
:   CopyExpand

    ```
    curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/windows_x86_64/snowsql-<version>-windows_x86_64.msi
    ```

    Show lessSee more

    Scroll to top

Microsoft Azure endpoint:
:   CopyExpand

    ```
    curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/<bootstrap_version>/windows_x86_64/snowsql-<version>-windows_x86_64.msi
    ```

    Show lessSee more

    Scroll to top

Where:

* `<version>` is the combined SnowSQL major, minor, and patch versions. For example, for version 1.3.1, the major version is 1, the
  minor version is 3, and the patch version is 1. So, the version is 1.3.1.
* `<bootstrap_version>` is the combined SnowSQL major and minor versions. For example, for version 1.3.1, the major version is
  1 and the minor version is 3, so the bootstrap version is 1.3.

For example, to download the SnowSQL installer where `<bootstrap_version>` is 1.3 and `<version>` is 1.4.5:

AWS endpoint:
:   ```
    curl -O https://sfc-repo.snowflakecomputing.com/snowsql/bootstrap/1.4/windows_x86_64/snowsql-1.4.3-windows_x86_64.msi
    ```

Microsoft Azure endpoint:
:   ```
    curl -O https://sfc-repo.azure.snowflakecomputing.com/snowsql/bootstrap/1.4/windows_x86_64/snowsql-1.4.3-windows_x86_64.msi
    ```

For more information about SnowSQL versions, see [Understanding SnowSQL Versioning](#understanding-snowsql-versioning) (in this topic).

The Windows operating system can verify the installer signature automatically, so GPG signature verification is not needed.

### Installing SnowSQL using the installer

1. Open `snowsql-windows_x86_64.msi` in the download location to run the installer MSI file.
2. Follow the instructions provided by the installer.

Note

The installation can be automated by running the MSI installer `msiexec` from the command line. The target directory cannot be changed from
`%ProgramFiles%Snowflake SnowSQL`. For example:

CopyExpand

```
C:\Users\<username> msiexec /i snowsql-windows_x86_64.msi /q
```

Show lessSee more

Scroll to top

When you install a new major or minor version, SnowSQL does not upgrade itself immediately. Rather, you must log into your Snowflake account using SnowSQL and remain connected for a sufficient period of time for the auto-upgrade feature to upgrade the client to the latest release. To verify the SnowSQL version that currently starts when you run the client, use the `-v` option without a value:

> CopyExpand
>
> ```
> snowsql -v
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Expand
>
> ```
> Version: 1.3.1
> ```
>
> Show lessSee more
>
> Scroll to top

To force SnowSQL to install and use a specific version, use the `-v` option and specify the version you want to install. For example, execute the following command for version 1.3.0:

> CopyExpand
>
> ```
> snowsql -v 1.3.0
> ```
>
> Show lessSee more
>
> Scroll to top

## Understanding SnowSQL versioning

SnowSQL version numbers consist of three digits: `<major version>.<minor version>.<patch version>`.

For example, version 1.3.1 indicates the major version is 1, the minor version is 3, the patch version is 1.

To determine the SnowSQL version that currently starts when you run the client, use the `-v` option without a value:

> CopyExpand
>
> ```
> snowsql -v
> ```
>
> Show lessSee more
>
> Scroll to top
>
> Expand
>
> ```
> Version: 1.3.1
> ```
>
> Show lessSee more
>
> Scroll to top

In general, the following guidelines apply to the different version types:

Major version:
:   A change in the major version indicates dramatic improvements in the underlying Snowflake service. A new major version breaks backward
    compatibility. You will need to download and install the latest SnowSQL version from the web interface.

Minor version:
:   A change in the minor version indicates improvements to support forward compatibility in either SnowSQL or the underlying Snowflake
    service. A new minor version does not break backward compatibility, but Snowflake strongly recommends that you download and install the latest SnowSQL version
    from the web interface.

Patch version:
:   A change in the patch version indicates small enhancements or bug fixes were applied.

    The auto-upgrade feature automatically installs
    all patch versions. For more information about the auto-upgrade feature, see [What is Auto-upgrade?](#what-is-auto-upgrade) (in this topic).

    Note

    If Snowflake releases a new minor or patch version, the functionality in your current version should continue to work, but any newly-released bug fixes and features will
    not be available via the auto-upgrade feature. Therefore, we strongly recommended that you download and install the latest SnowSQL version
    when a new version is available.

### What is auto-upgrade?

Important

Starting with version 1.3.0, SnowSQL disables automatic upgrades by default to avoid potential issues that can affect production environments when an automatic upgrade occurs. To upgrade, you should download and install new versions manually, preferably in a non-production environment. Snowflake strongly recommends you leave this setting disabled, but if want to install new versions automatically when they are released, you can disable the SnowSQL `--noup` [option](#label-snowsql-enable-auto-upgrade).

If you choose to enable automatic upgrades for SnowSQL, SnowSQL automatically downloads the new binary in a background process and executes the current version. The next time you
run SnowSQL, the new version starts.

To illustrate the process:

1. For a fresh installation, you download the SnowSQL installer (such as version 1.3.0) using the Snowflake web interface and install the client.
2. Each time you run SnowSQL, the client checks whether a newer version is available in the SnowSQL upgrade repository.
3. If a newer version (such as version 1.3.1) is available, SnowSQL downloads it as a background process while the current installed version.
4. The next time you run SnowSQL, the client executes version 1.3.1 while checking if a newer version is available.

### Enabling auto-upgrade

The `-o noup=<value>` option lets you override the SnowSQL default behavior of requiring manual installations for new versions, where:

* `True` enables the no-upgrade behavior (Default value for version 1.3.0 and higher). SnowSQL does not automatically check for upgrades, nor does it automatically upgrade itself.
* `False` disables the no-upgrade behavior (Default value for version 1.2.32 and lower). SnowSQL automatically checks for upgrades and automatically upgrades itself if any new upgrade is available within the same `major.minor` version

You can specify this option while logging into
Snowflake to enable auto-upgrade during that specific session.

For example:

> CopyExpand
>
> ```
> snowsql -o noup=False
> ```
>
> Show lessSee more
>
> Scroll to top

Alternatively, add the `noup = False` option to the [configuration file](snowsql-config.html#label-configuring-snowsql) to enable automatic upgrades for SnowSQL.

### Running a previous SnowSQL version

Note

If you are running SnowSQL version 1.3.0 or newer, you cannot use this process to run a 1.2.x version. If you want to run a 1.2.x version, you must download and install the earlier version manually.

If you encounter an issue with the latest SnowSQL version, such as version 1.3.1, you can temporarily run another 1.3.x version.

To determine the SnowSQL version that currently starts when you run the client, use the `-v` option without a value:

> CopyExpand
>
> ```
> $ snowsql -v
>
>   Version: 1.3.1
> ```
>
> Show lessSee more
>
> Scroll to top

To display a list of available SnowSQL versions, use the `--versions` option:

> CopyExpand
>
> ```
> $ snowsql --versions
>
>  1.3.1
>  1.3.0
> ```
>
> Show lessSee more
>
> Scroll to top

To install an earlier SnowSQL version from the list, use the `-v` option and specify the version you want to install. For example, to install version 1.3.0 if you are running a newer version, such as 1.3.1:

> CopyExpand
>
> ```
> $ snowsql -v 1.3.0
>
>   Installing version: 1.3.0 [####################################]  100%
> ```
>
> Show lessSee more
>
> Scroll to top

Use the same option to specify the version you want to run when you start SnowSQL:

> CopyExpand
>
> ```
> $ snowsql -v 1.3.0
> ```
>
> Show lessSee more
>
> Scroll to top

## Changing the Snowflake client repository endpoint used by the SnowSQL auto-upgrade feature

By default, the SnowSQL auto-upgrade feature uses the AWS endpoint of the Snowflake Client Repository. To change the endpoint in the SnowSQL configuration file, complete the steps in this section.

### New users

To specify the Microsoft Azure endpoint of the Snowflake Client Repository as a new SnowSQL user, execute the following command:

CopyExpand

```
snowsql -o repository_base_url=https://sfc-repo.azure.snowflakecomputing.com/snowsql
```

Show lessSee more

Scroll to top

Verify the configuration file (i.e. `~/.snowsql/config` or `%USERPROFILE%\.snowsql\config`) includes the following line.

CopyExpand

```
repository_base_url=https://sfc-repo.azure.snowflakecomputing.com/snowsql
```

Show lessSee more

Scroll to top

### Existing users

To specify the Microsoft Azure endpoint of the Snowflake Client Repository as an existing SnowSQL user, add the following line to the configuration file (i.e. `~/.snowsql/config` or `%USERPROFILE%\.snowsql\config`):

CopyExpand

```
repository_base_url=https://sfc-repo.azure.snowflakecomputing.com/snowsql
```

Show lessSee more

Scroll to top
