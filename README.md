# Asciisync
Asciisync performs a local asciibinder build and syncs the distribution of your choosing to a file server.

<!-- toc -->
- [Linux installation](#linux-installation)
  - [Installing asciisync using a setup script (Linux)](#installing-asciisync-using-a-setup-script-linux)
  - [Installing asciisync manually (Linux)](#installing-asciisync-manually-linux)
- [Mac installation](#mac-installation)
  - [Installing asciisync using a setup script (Mac)](#installing-asciisync-using-a-setup-script-mac)
  - [Installing asciisync manually (Mac)](#installing-asciisync-manually-mac)
- [Using Asciisync](#using-asciisync)


## Linux installation

### Installing asciisync using a setup script (Linux)

#### *Prerequisites*

* You are using a Linux distribution
* You are on the VPN
* You have `file.<geo>` [access](https://docs.google.com/document/d/1fLMpK4bqthtFlCwA36yeo2cI-pExFpX76VMnSi_In6Q/edit#heading=h.vrlifsbvvjd8)
* You have a `public_html` directory in your `file.<geo>` server with `755` permissions:
    * ```shell
        $ ssh <kerberos>@file.<geo>.redhat.com
        $ mkdir public_html
        $ chmod 755 public_html
        ```
* Optional: You have `kinit` [configured](https://source.redhat.com/groups/public/ccs-onboarding-program/ccs_onboarding_wiki/setting_up_a_kerberos_ticket_and_red_hat_idm)
    **NOTE**: You will need to enter your kerberos password to authenticate if you do not have `kinit` configured and have obtaind a current kerberos ticket.

#### *Procedure*

1. Download the setup script [here](http://file.rdu.redhat.com/antaylor/asciisync-setup/).
2. Make the file executable:
    **NOTE**: Your file may download in a different location.    
    ```shell
    $ chmod 755 ~/Downloads/asciisync-setup.sh
    ```
    
3. Run the script:
    ```shell
    $ ./Downloads/asciisync-setup.sh
    ```
    
4. Provide your kerberos ID and `file.<geo>` when prompted, then press `Enter`:
    ```
    Enter your Kerberos ID:
    : 
    Enter your geo (rdu,emea,etc):
    :
    ```

5. Use [asciisync](#Using-asciisync).

### Installing asciisync manually (Linux)

#### *Prerequisites*

* You are using a Linux distribution
* You are on the VPN
* You have `file.<geo>` [access](https://docs.google.com/document/d/1fLMpK4bqthtFlCwA36yeo2cI-pExFpX76VMnSi_In6Q/edit#heading=h.vrlifsbvvjd8)
* You have a `public_html` directory in your `file.<geo>` server with `755` permissions:
    * ```shell
        $ ssh <kerberos>@file.<geo>.redhat.com
        $ mkdir public_html
        $ chmod 755 public_html
        ```
* Optional: You have `kinit` [configured](https://source.redhat.com/groups/public/ccs-onboarding-program/ccs_onboarding_wiki/setting_up_a_kerberos_ticket_and_red_hat_idm)
    **NOTE**: You will need to enter your kerberos password to authenticate if you do not have `kinit` configured and have obtaind a current kerberos ticket.

#### *Procedure*

1. Create a file within your `~/bin` called "asciisync":
    **NOTE**: I use `vim` as an editor. You may have `nano`, etc.
	```shell
    $ vim ~/bin/asciisync
    ```
    
2. Paste the following text, then save and quit:
    **IMPORTANT**: Change the `GEO=`, and `KERB=` to your geographical region and kerberos ID. 
    ```bash=
    #!/bin/bash
    BRANCH=$(git branch --show-current)
    KERB=<kerberos>
    GEO=<geo>
    DISTRO_LIST=$(ls -1 _preview)
    podman run --rm -it -v `pwd`:/docs:Z quay.io/openshift-cs/asciibinder asciibinder clean
    podman run --rm -it -v `pwd`:/docs:Z quay.io/openshift-cs/asciibinder asciibinder build -l fatal
    echo "
    Which distribution would you like to preview?
    $DISTRO_LIST"
    read -p ": " DISTRO
    echo "Performing the rsync."
    rsync -az --rsync-path="mkdir -p ~/public_html/$BRANCH/* && rsync" --info=progress2 --human-readable _preview/$DISTRO/$BRANCH/* $KERB@file.$GEO.redhat.com:~/public_html/$BRANCH
    echo "
    Complete!

    URL: http://file.$GEO.redhat.com/$KERB/$BRANCH/welcome"
    ```
    
3. Make the file executable:
    ```shell
    $ chmod 755 ~/bin/asciisync
    ```
    
4. Use [asciisync](#Using-asciisync).

## Mac installation

### Installing asciisync using a setup script (Mac)

#### *Prerequisites*

* You are using MacOS

#### *Procedure*

1. Install [homebrew](https://brew.sh/).
2. Brew install podman
3. Download asciisync-mac
4. chmod +x asciisync-mac
5. ./asciisync-mac
6. script needs mv /usr/local/bin/asciisync
7. podman machine init
8. podman machine start
9. cd openshift docs
10. asciisync

### Installing asciisync manually (Mac)

#### *Prerequisites*

* You are using MacOS

#### *Procedure*

1. Install [homebrew](https://brew.sh/).
2. Brew install podman
3. vim /usr/local/bin/asciisync and paste file contents here
4. chmod 755 asciisync

## Using asciisync

#### *Prerequisites*

* You are on the VPN
* You have `file.<geo>` [access](https://docs.google.com/document/d/1fLMpK4bqthtFlCwA36yeo2cI-pExFpX76VMnSi_In6Q/edit#heading=h.vrlifsbvvjd8)
* You have a `public_html` directory in your `file.<geo>` server with `755` permissions:
    * ```shell
        $ ssh <kerberos>@file.<geo>.redhat.com
        $ mkdir public_html
        $ chmod 755 public_html
        ```
* Optional: You have `kinit` [configured](https://source.redhat.com/groups/public/ccs-onboarding-program/ccs_onboarding_wiki/setting_up_a_kerberos_ticket_and_red_hat_idm)
    **NOTE**: You will need to enter your kerberos password to authenticate if you do not have `kinit` configured and have obtaind a current kerberos ticket.

Asciisync is configured to build all possible distributions and will upload the distribution of your choosing.

1. Change directory to the git branch that you want to preview:
    **NOTE**: Your `openshift-docs` repository may installed in another location.

    ```shell 
    $ cd ~/openshift-docs/
    ```
    
2. Run asciisync:
    
    ```shell
    $ asciisync
    ```

3. Choose the disribution you wish to preview. Type your response and press `Enter`.
    ```
    Which distribution would you like to preview?
    microshift
    openshift-dedicated
    openshift-enterprise
    openshift-rosa
    openshift-rosa-portal
    : 
    ```

4. The script completes, providing a URL. `Ctrl + left click` on the URL to open in your browser.
    ```
    Complete!
    
    URL: http://file.<geo>.redhat.com/<kerb>/<branch>/welcome"
    ```
    
