# Setting Up The Base System

In the Homelab series, I've demonstrated how to set up Proxmox and make Virtual Machines (VMs) and Linux Containers (LXCs) for various operating systems. Now, let's keep things simple and light by using a Debian LXC.

Here's a breakdown of what we'll do:

1. **Set up a Debian LXC**: We'll create a lightweight Debian Linux Container to work with.
2. **Install Docker**: We'll install Docker inside the Debian LXC to manage our containers efficiently.
3. **Connect via SSH in VS Code**: We'll establish a secure connection to the container directly from VS Code for easy coding.
4. **Create a Terraform Container**: We'll set up a container specifically for Terraform to streamline our development environment.

Now, some folks argue that installing Docker in LXCs is unnecessary and risky due to security concerns. However, since this is a homelab and development environment, we can overlook these risks to enjoy the benefits of a lightweight and easily reproducible setup.

### Creating the Base System

Let's dive into setting up a Debian LXC with Docker inside. Don't worry if you're not using Debian; as long as you have Docker installed, you're good to go. If Docker is already up and running on your system, feel free to jump ahead to the [Configuring SSH in the Base System](setting-up-the-base-system.md#configuring-ssh-in-the-base-system) section.

Reference: [Proxmox: Run Docker on Linux Containers (LXC)](https://benheater.com/proxmox-run-docker-on-linux-containers-lxc/)

1.  In the left panel, select any of your _Storage (Default: local) -> CT Templates -> Templates -> debian-12-standard -> Download._\


    <figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>
2.  To create the LXC container, click on **Create CT** located at the top right corner. A box will pop up with the following options.

    <figure><img src="../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

    Make sure that the settings are as follows:

    * _**CT ID**_: Use the default, unless you prefer something else.
    * _**Hostname**_: Use dev-debian so that its easier to follow along with the blog.
    * _**Unprivileged Container**_: Ticked
    * _**Nesting**_: Ticked
    * **Password**: Choose whatever you prefer. Don't forget it.
3.  Click on _**Next**_ and select the template we just downloaded (_debian-12-standard\_12.2-1\_amd64.tar.zst_)\


    <figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
4.  Click on _**Next**_ and select the Disk Size you want, the default 8 GiB should be sufficient, but since I am making this as my main development container, I have allocated 64 GiB as I have the space available for it.\


    <figure><img src="../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
5.  &#x20;Click on _**Next**_ and select the number of Cores you want, the default of 1 core should be sufficient, but to make it faster I allocated 6 cores. If you have the resources, I would suggest allocate more cores at the start (helps with faster environment setup) and once everything is set up, you can bring it down.\


    <figure><img src="../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
6.  Click on _**Next**_ and select the amount of Memory you want, I have removed the Swap Memory, and I am using 2048 MiB. Again, the default 512 MiB is also sufficient.\


    <figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>
7.  Click on _**Next**_ and keep the default settings for the network (unless you know what you are doing).\


    <figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

    Make sure that this one setting is as follows:

    1. _**IPv4**_: DHCP.
8. Click on _**Next**_ and don't change anything in the DNS section (unless you know what you are doing).
9. Click on _**Next**_ and then click on _**Finish**_.
10. After the container is created, click on the _container -> Options -> Features -> Enable **keyctl** and **FUSE** -> Click on **OK.**_
11. Open up the Proxmox Shell and run the following commands:

    {% code fullWidth="false" %}
    ```bash
    apt clean && apt update
    apt install -y fuse-overlayfs
    ```
    {% endcode %}
12. Select your container in _Proxmox -> Start -> Console_.
13. Enter the following credentials:\
    _Username_: root\
    _Password_: \<password-you-set>
14. Run the following commands:

    <pre class="language-bash"><code class="lang-bash"><strong>apt update &#x26;&#x26; apt upgrade -y
    </strong></code></pre>

### Installing Docker in LXC Container

Now that our operating system is set up, it's time to install Docker. Let's get started!

1.  Install the prerequisite packages for Docker in LXC (not required for VMs).

    <pre class="language-bash"><code class="lang-bash">apt clean &#x26;&#x26; apt update
    <strong>apt install -y fuse-overlayfs
    </strong>ln -s /usr/bin/fuse-overlayfs /usr/local/bin/fuse-overlayfs
    </code></pre>
2.  Run the following commands to install Docker:

    ```bash
    apt install curl -y
    curl -fsSL https://get.docker.com -o get-docker.sh
    sh get-docker.sh
    ```
3.  Check if Docker is installed correctly by running:

    ```bash
    docker run hello-world
    ```

    If Docker is installed correctly, you should get an output like below:

    <figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

### Configuring SSH in the Base System

Now that Docker is up and running on our system, let's set up SSH. This allows us to connect to our system via VS Code.

1.  Install SSH server.

    ```bash
    apt install openssh-server -y
    apt install nano -y
    ```
2.  Open up a terminal or command prompt from the system which has VS Code installed in it (Main System) and run the following commands to check if the server is reachable:

    ```bash
    # Make sure Proxmox LXC and Main System are on the same network.
    ping dev-debian
    ```

    \


    <figure><img src="../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>
3. (Optional) If you get the error "Could not find host", then you can find the IP address of the Linux container and then try pinging it.&#x20;
   1.  Find the IP address of LXC using:

       ```bash
       # Run inside LXC
       ip -br -c a
       ```

       \
       And the IP address will be the one corresponding to the network interface named eth0@\*\*\*.

       <figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>
   2.  Ping LXC using IP address:\


       <figure><img src="../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>
4.  Now that we can connect the 2 systems, we need to add a new user in LXC to be able to SSH into it because using the _root_ user is not best practice.\
    In the following commands replace \<username> with the username you want. For ease, use the same username that you have in your Main System.

    ```bash
    adduser <username>
    ```

    \
    Enter the details, and you should get an output similar to this:&#x20;

    <figure><img src="../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>
5.  Run the following to give the new user access to sudo permissions and Docker.

    ```bash
    apt install sudo -y
    usermod -aG sudo <username>
    usermod -aG docker <username>
    exit
    ```


6.  Login to the new user using the new credentials and check if sudo and Docker work:

    ```bash
    sudo apt update
    docker run hello-world
    ```



    <figure><img src="../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>
7.  Create SSH keys for the LXC using:

    ```bash
    ssh-keygen
    ```

    \
    Use all default options for ease, with the following output:

    <figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
8.  Create SSH keys for the Main System. Run the following commands:

    ```bash
    # Run in Main System command prompt or terminal.
    ssh-keygen
    ```
9.  Copy the SSH keys to the LXC for easier access.

    ```bash
    cd .ssh
    scp id_ed25519.pub <username>@dev-debian:~/.ssh/authorized_keys
    ```
10. Try SSHing into the LXC.

    ```bash
    ssh <usernama>@dev-debian

    # If the username of Main System is same as the new user made in LXC.
    ssh dev-debian
    ```



    <figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>
11. Open up VS Code and install the following extensions:
    1. Dev Containers
    2. Docker
    3. HashiCorp Terraform
    4. Remote Development
    5. Remote Explorer
    6. Remote - SSH: Editing Configuration Files
12. To add configuration for SSH connection to LXC, create a file named "config" in the \~/.ssh/ directory.

    ```
    Host dev-debian
      HostName dev-debian
      User <username>
    ```

    Or create the config using VS Code, follow the [Docs](https://code.visualstudio.com/docs/remote/ssh).
13. Connect to LXC via VS Code.

    <figure><img src="../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>
14. Here's what your VS Code connection to LXC over SSH should look like.

    <figure><img src="../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>
15. Create a directory for your project called "terraform-webserver" using the command:

    ```bash
    mkdir terraform-webserver
    ```
16. Open this directory in VS Code using the "Open Folder..." button and select your directory in the drop down and click on "OK".

    <figure><img src="../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
