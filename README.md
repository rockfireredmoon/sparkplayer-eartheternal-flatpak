# Sparkplayer - Earth Eternal (Valkals Shadow) - Flatpak

This repository provides information about the Earth Eternal client *Sparkplayer*, for both players and developers. 

## For Players

If you just want to play [Earth Eternal - Valkal's Shadow](http://www.theanubianwar.com/valkals-shadow), you can install this [Flatpak](https://flatpak.org) from our repository. Flatpaks run on many different Linux distributions, and many of the most popular distributions have built in support for them in their package managers. We chose Flatpak as our distribution method for this reason,
and for the fact we can also provide automatic updates. 

### Install Flatpak

You may already have it! Distributions like Linux Mint, Fedora Core and more now include it by default. See [here](https://flatpak.org/setup/) for easy instructions on getting Flatpak.

### Install Sparkplayer
 
Now install Sparkplayer itself. Both of the techniques below will install the currently live version for players,
*Earth Eternal - Valkal's Shadow*.  

#### From Your Desktop

If your software manager supports Flatpak, simple click the Install button below and allow your software manager to do
the rest.

[[Install]](http://files.theanubianwar.com/flatpak/sparkplayer-earth-eternal-valkals-shadow.flatpakref)

#### From The Command Line

```
flatpak install http://files.theanubianwar.com/flatpak/sparkplayer-earth-eternal-valkals-shadow.flatpakref
```


### Running Earth Eternal

At the time of writing this, the current live version of Earth Eternal is *Valkal's Shadow*. To play the game, provide
the URL of the game bootstrap file. The client will start up, download the game bootstrap files and take you to the 
login screen (you will need a game account, sign up at http://www.theanubianwar.com).

#### From Your Desktop

You will have a new entry in your desktop menu in the *Games* section, *Earth Eternal - Valkal's Shadow*.

#### From The Command Line   

```
flatpak run org.icemoon.EarthEternal http://live.theanubianwar.com/Release/Current/EarthEternal.car
```

## Updating the Flatpak

Once this initial porting phase is complete, Sparkplayer itself should rarely require updating. The game itself dynamically 
downloads content and has it's own built in update mechanism. However, sometimes the engine will be updated as well.
Either use your distros GUI tools to update, or run the following command. 

```
flatpak update org.icemoon.EarthEternal
```

## For Developers

If you are are here to build a Flatpak for distributing the SparkPlayer client to users with
the Linux operating system, then follow the instructions below.

### Preparation

You will need :-

 * A Linux Distribution
 * Flatpak
 * Flatpak Builder
 * The Sparkplayer sources
 * GPG and signing key
 * A local copy of the appropriate repository to publish too
 
The Sparkplayer sources should be cloned into the same directory as this one and named 
`sparkplayer`. Make sure Flatpak and dependencies are installed :-

```
sudo apt install flatpak flatpak-builder elfutils
```

Add Flathub repository :-

```
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Install the Flatpak runtime and SDK we will be using :-

```
flatpak install flathub org.freedesktop.Platform//18.08 org.freedesktop.Sdk//18.08
```

### Preparing The Repository

Sparkplayer is hosted in our own repository rather than Flathub for various reasons. So if you are going to
publish a build to this repository you must first copy the live repository to your computer so you can
publish to it and then re-sync back with the public repository.

This will of course require SSH access to the repository web server.

```
rsync -avz -e "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" --progress \
   admin@flatpak.theanubianwar.com:/srv/flatpak.theanubianwar.com/public/htdocs earth-eternal

```

### Building The Package

Now build the package (signing it and publishing to the repository in one step) :-

```
flatpak-builder --repo=earth-eternal --gpg-sign=your.email.address@somedomain.com \
	app org.icemoon.EarthEternal.json
```

If you need to build again after making adjustments, add `--force-clean` to the flatpak-builder arguments.
To test the build package, use :-

```
flatpak-builder --run app org.icemoon.EarthEternal.json player http://live.theanubianwar.com/Release/Current/EarthEternal.car
```

### Publishing

Now the local copy of the repository should be synchronized with live remote repository.

```
rsync -avz -e "ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null" \
		--delete-before --progress earth-eternal/*  \
		admin@flatpak.theanubianwar.com:/srv/flatpak.theanubianwar.com/public/htdocs
```
