---
title: Celo Local Envorinment using Windows
description: How to set up a local development environment for Celo using Windows.
---

# Using Windows

How to set up a local development environment for Celo using Windows.

:::warning
As of block height 31,056,500 (March 26, 2025, 3:00 AM UTC), Celo is no longer a standalone Layer 1 blockchain—it is now an Ethereum Layer 2!
Some documentation may be outdated as updates are in progress. If you encounter issues, please [file a bug report](https://github.com/celo-org/docs/issues/new/choose).

For the most up-to-date information, refer to our [Celo L2 documentation](https://docs.celo.org/cel2).
:::

---

:::tip

Many popular tools and resources for blockchain development are written for developers working on UNIX machines. It is common for developers working on Windows to encounter errors that are not covered in the documentation and have no luck with Google. Fortunately, Microsoft makes it easy to run a UNIX machine directly from a Windows desktop with the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/faq).

:::

### **Getting set up with Windows**

Open PowerShell as an administrator and run

```text
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

Restart your computer when prompted.

Next, install a Linux distribution from the Microsoft Store. When developing this guide, we chose [Ubuntu](https://www.microsoft.com/en-us/p/ubuntu-1804-lts/9n9tngvndl3q?rtc=1&activetab=pivot:overviewtab).

Set up your Linux distro by setting a username and password then update and upgrade the packages by running the following command in the terminal:

```shell
sudo apt update && sudo apt upgrade
```

You can view the source documentation for setting up the Linux distro [here](https://learn.microsoft.com/en-us/windows/wsl/) and the Microsoft documentation for setting up the Windows Subsystem for Linux [here](https://docs.microsoft.com/en-us/windows/wsl/install-win10?WT.mc_id=smashingmag-article-buhollan).

### Set up the Linux Environment

Now that you have Linux installed, let’s install [nvm](https://github.com/nvm-sh/nvm) and [yarn](https://yarnpkg.com/). Nvm \(node version manager\) makes it easy to install and manage different versions of Node.js. The following instructions are from the [celo-monorepo setup documentation for Linux](https://github.com/celo-org/celo-monorepo/blob/master/SETUP.md#linux).

Run the following commands in the Linux terminal.

```bash
# Installing Nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash
source ~/.bashrc

# Setting up the desired Nvm version
nvm install x
nvm alias default x
```

Running `$ node -v` in the terminal should print a node version if it is installed correctly.

Yarn is a package manager similar to npm. The [celo-monorepo](https://github.com/celo-org/celo-monorepo/) uses yarn to build and manage packages. Install yarn with the following command.

```bash
# Installing Yarn - https://yarnpkg.com/en/docs/install#debian-stable
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt-get update && sudo apt-get install yarn
```

Test that yarn is installed by running `$ yarn --version`.

### Developing with WSL

You can now start working on your projects in your Linux environment. Install [the WSL VS Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) for a seamless integration between VS Code and WSL.

Be aware that networking will be different depending on which version of WSL you are using. The details of managing network interfaces goes beyond the scope of this guide, but you can [learn more here](https://adamtheautomator.com/windows-subsystem-for-linux/#developing-on-wsl-with-visual-studio-code-vs-code-).  
You are good to go! If you have any questions, [join our Discord server](https://chat.celo.org) and just ask.

### Additional Resources

- [**Windows Subsystem for Linux Installation Guide for Windows 10**](https://docs.microsoft.com/en-us/windows/wsl/install-win10?WT.mc_id=smashingmag-article-buhollan)
- [**WSL: Ultimate Guide**](https://adamtheautomator.com/windows-subsystem-for-linux/#developing-on-wsl-with-visual-studio-code-vs-code-)
