<table><tbody><tr><td><i title="Important"></i></td><td>With Fedora 38 there is an issue where Steam installed from Flathub will not start. This issue is being tracked on the <a href="https://discussion.fedoraproject.org/t/steam-from-flathub-might-not-start-on-fedora-38/80839">Fedora Discussion</a> page</td></tr></tbody></table>

<table><tbody><tr><td><i title="Note"></i></td><td>This will require enabling an external repository.</td></tr></tbody></table>

<table><tbody><tr><td><i title="Note"></i></td><td>If you enabled "Third Party Software" (<code>rpmfusion Nonfree</code>) at installation you can skip to installing Steam.</td></tr></tbody></table>

## Using the terminal

### enabling The external repository (`rpmfusion Nonfree`)

-   Launch The terminal prompt of your choice
    
-   Run the following command with a user that has root acess or can use the `sudo` command
    

```bash
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm -y
```

```bash
sudo dnf config-manager --enable fedora-cisco-openh264 -y
# 这个好像没啥用
```

### installing Steam

-   Run the following command with a user that has root acess or can use the `sudo` command
    

```bash
sudo dnf install steam -y
```

## Using `Software` GUI Package Manager

Open Software under the Activities in the top left corner.

![Software](https://docs.fedoraproject.org/en-US/gaming/_images/software.png)

### enabling The external repository (`rpmfusion Nonfree`)

-   Click the Menu Button (☰) on upper right corner and choose Software Repositories. (Circled red for visual aide)
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/hamburger.png)

-   Scroll down to the bottom of the new window till you see “Fedora Third Party Repositories”.
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/repos.png)

-   Enable "RPM Fusion for Fedora XX - Nonfree - Steam".
    
-   Close the window.
    
-   Click on the upper left corner (search icon).
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/search.png)

### installing Steam

-   Search for Steam and install it.
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/steam.png)

-   Launch Steam.
    

<table><tbody><tr><td><i title="Note"></i></td><td>Following installation requires you to be online.</td></tr></tbody></table>

## Enable Proton engine

<table><tbody><tr><td><i title="Note"></i></td><td>Following installation requires you to be online.</td></tr></tbody></table>

-   Open Steam and click Steam on the menu bar (upper left corner) and click Settings.
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/settings.png)

-   Click Steam Play.
    
-   Check Enable Steam Play for supported titles.
    
-   Check Enable Steam Play for all other titles and select the Proton version you installed.
    
-   Press OK
    

![800](https://docs.fedoraproject.org/en-US/gaming/_images/steam_play.png)

-   Restart Steam
    

Once Steam restarts all games in your Steam Library should be available to install and play.

### Next steps

Install available title from the Library menu and play it.

<table><tbody><tr><td><i title="Note"></i></td><td>It is recommended that games are checked on <a href="https://www.protondb.com/">Proton games</a>. This will give an idea of how well a game might run under Steam on Fedora.</td></tr></tbody></table>