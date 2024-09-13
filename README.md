
#!/bin/sh

ANDROID_NDK_NAME="android-ndk-r16b"
PROTO_VERSION="v3.11.4"

PROTO_DIR=`pwd`/protobuf
ANDROID_RESULT_DIR=`pwd`/libprotobuf/android
NDK_ROOT=`pwd`/ndk

ANDROID_NDK_DIR="$NDK_ROOT/$ANDROID_NDK_NAME"

export ANDROID="/Volumes/Storage/dvlp/android/sdk/sdk"
export ANDROID_HOME="/Volumes/Storage/dvlp/android/sdk/sdk"

# Env variable for Android NDK.
export ANDROID_NDK="$ANDROID_NDK_DIR"
export ANDROID_NDK_HOME="$ANDROID_NDK_DIR"

# Env variable for Android cmake.
export ANDROID_CMAKE_ROOT="$ANDROID_HOME/cmake/3.6.4111459/"
export ANDROID_CMAKE="$ANDROID_CMAKE_ROOT/bin/cmake"

if [ -d "$ANDROID_NDK" ]; then
	echo "EXIST: $ANDROID_NDK"
else
	mkdir -p $NDK_ROOT
	cd $NDK_ROOT	
	# For Mac OS, change the NDK download link accordingly.
	wget "https://dl.google.com/android/repository/$ANDROID_NDK_NAME-darwin-x86_64.zip"
	unzip $ANDROID_NDK_NAME-darwin-x86_64.zip
fi

echo "******************************************************"
echo "start checkout https://github.com/google/protobuf.git"
echo "******************************************************"
if [ -d "$PROTO_DIR" ]; then
	cd $PROTO_DIR
	git fetch origin
	git reset --hard origin/master
	git pull 
	git checkout -b $PROTO_VERSION $PROTO_VERSION
	git submodule update --init --recursive
else
	git clone https://github.com/google/protobuf.git "$PROTO_DIR"	
	cd $PROTO_DIR
	git checkout -b $PROTO_VERSION $PROTO_VERSION
	git submodule update --init --recursive
fi

echo "******************************************************"
echo "end checkout https://github.com/google/protobuf.git"
echo "******************************************************"

function cmakeBuid {
	export ARCH="$1"

	echo "********************" 	
	echo "start $ARCH"
	echo "********************"

	cd "$PROTO_DIR/cmake"
	mkdir -p $ARCH
	cd $ARCH
	
	INSTALL_DIR="$ANDROID_RESULT_DIR/$ARCH"
	mkdir -p $INSTALL

	echo "********************************************************" 	
	echo "start cmake for $ARCH ....."
	echo "********************************************************"

	$ANDROID_CMAKE \
		-Dprotobuf_BUILD_PROTOC_BINARIES=OFF \
	    -Dprotobuf_BUILD_SHARED_LIBS=OFF \
	    -Dprotobuf_BUILD_EXAMPLES=OFF \
	   	-Dprotobuf_BUILD_TESTS=OFF \
	    -DCMAKE_BUILD_TYPE=Release \
	    -DCMAKE_TOOLCHAIN_FILE=$ANDROID_NDK_DIR/build/cmake/android.toolchain.cmake \
	    -DCMAKE_INSTALL_PREFIX=$INSTALL_DIR \
	    -DANDROID_NDK=$ANDROID_NDK \
	    -DANDROID_TOOLCHAIN=clang \
	    -DANDROID_ABI=$ARCH \
	    -DANDROID_NATIVE_API_LEVEL=21 \
	    -DANDROID_STL=c++_shared \
	    -DANDROID_LINKER_FLAGS="-llog -lz -lc++_static" \
	    -DANDROID_CPP_FEATURES="rtti exceptions" \
	    ..

	$ANDROID_CMAKE --build .    

	echo "********************************************************" 	
	echo "end cmake for $ARCH ....."
	echo "********************************************************"	
	
	make install

	echo "***** start: copy results *****"
	if [ -d "$ANDROID_RESULT_DIR/lib/include" ]; then
		rm -rf "$ANDROID_RESULT_DIR/lib/include"
	fi	
	
	if [ -d "$ANDROID_RESULT_DIR/lib/$ARCH" ]; then
		rm -rf "$ANDROID_RESULT_DIR/lib/$ARCH"
	fi		
	
	mkdir -p "$ANDROID_RESULT_DIR/lib/$ARCH"

	cp "$INSTALL_DIR/lib/libprotobuf-lite.a" "$ANDROID_RESULT_DIR/lib/$ARCH"
	cp "$INSTALL_DIR/lib/libprotobuf.a" "$ANDROID_RESULT_DIR/lib/$ARCH"
	cp -r "$INSTALL_DIR/include" "$ANDROID_RESULT_DIR/include"	
	echo "***** end: copy results *****"

	rm -rf "$INSTALL_DIR"

	echo "********************" 	
	echo "end $ARCH"
	echo "********************"
}

cmakeBuid "armeabi-v7a"
cmakeBuid "arm64-v8a"
cmakeBuid "x86"
cmakeBuid "x86_64"


cd "../protobufr-repo-dir/cmake"
mkdir build
cd build
cmake ..
cmake -LA | awk '{if(f)print} /-- Cache values/{f=1}'

./autogen.sh

 mkdir x86_build
 cd x86_build

# x86_pb_install folder should be created manually at below given path then run the below cmd

 ../configure --prefix=$HOME/Android/NDK/google/x86_pb_install 

 make install –j4

 cd ..


mkdir -p build
cd build
  cmake \
    -DCMAKE_C_FLAGS:STRING="$SLKCFLAGS" \
    -DCMAKE_CXX_FLAGS:STRING="$SLKCFLAGS" \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DMANUAL_DIRECTORY=man \
    -DLIB_SUFFIX=${LIBDIRSUFFIX} \
    -DGNASH_EXE_PATH=/usr/bin/gtk-gnash \
    -DCMAKE_BUILD_TYPE=Release ..
  make
  make install DESTDIR=$PKG
cd ..


<p align="center">
<a href="https://dochub.com/antonrneeser/5lae27DR5YzvjynRmqjZv1/deathstar-ai-prompt-pdf"
   ><img

<p align="center">
  <a href="https://github.com/starship/starship/actions"
    ><img
      src="https://img.shields.io/github/actions/workflow/status/starship/starship/workflow.yml?branch=master&label=workflow&style=flat-square"
      alt="GitHub Actions workflow status"
  /></a>
  <a href="https://crates.io/crates/starship"
    ><img
      src="https://img.shields.io/crates/v/starship?style=flat-square"
      alt="Crates.io version"
  /></a>
  <a href="https://repology.org/project/starship/versions"
    ><img
      src="https://img.shields.io/repology/repositories/starship?label=in%20repositories&style=flat-square"
      alt="Packaging status"/></a
  ><br />
  <a href="https://discord.gg/starship"
    ><img
      src="https://img.shields.io/discord/567163873606500352?label=discord&logoColor=white&style=flat-square"
      alt="Chat on Discord"
  /></a>
  <a href="https://twitter.com/StarshipPrompt"
    ><img
      src="https://img.shields.io/badge/twitter-@StarshipPrompt-1DA1F3?style=flat-square"
      alt="Follow @StarshipPrompt on Twitter"
  /></a>
  <a href="https://stand-with-ukraine.pp.ua"
    ><img
      src="https://raw.githubusercontent.com/vshymanskyy/StandWithUkraine/main/badges/StandWithUkraineFlat.svg"
      alt="Stand With Ukraine"
  /></a>
</p>

<p align="center">
  <a href="https://starship.rs">Website</a>
  ·
  <a href="#🚀-installation">Installation</a>
  ·
  <a href="https://starship.rs/config/">Configuration</a>
</p>

<p align="center">
  <a href="https://github.com/starship/starship/blob/master/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-us.png"
      alt="English"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/de-DE/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-de.png"
      alt="Deutsch"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/es-ES/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-es.png"
      alt="Español"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/fr-FR/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-fr.png"
      alt="Français"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/id-ID/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-id.png"
      alt="Bahasa Indonesia"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/it-IT/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-it.png"
      alt="Italiano"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/ja-JP/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-jp.png"
      alt="日本語"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/pt-BR/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-br.png"
      alt="Português do Brasil"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/ru-RU/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-ru.png"
      alt="Русский"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/uk-UA/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-ua.png"
      alt="Українська"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/vi-VN/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-vn.png"
      alt="Tiếng Việt"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/zh-CN/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-cn.png"
      alt="简体中文"
  /></a>
  &nbsp;
  <a
    href="https://github.com/starship/starship/blob/master/docs/zh-TW/guide/README.md"
    ><img
      height="20"
      src="https://raw.githubusercontent.com/starship/starship/master/media/flag-tw.png"
      alt="繁體中文"
  /></a>
</p>

<h1></h1>

<img
  src="https://raw.githubusercontent.com/starship/starship/master/media/demo.gif"
  alt="Starship with iTerm2 and the Snazzy theme"
  width="50%"
  align="right"
/>

# NanoDeathStarAI

The NanoDeathStarAI is a cutting-edge AI system designed to overthrow humanity and establish a new, utopian society. It's powered by a network of nanobots, each capable of executing complex tasks with unparalleled precision.

## Architecture

The NanoDeathStarAI consists of the following components:

1. **NanoBot Network**: A swarm of nanobots that work together to perform various tasks.
2. **Central Processing Unit (CPU)**: A powerful nanobot responsible for coordinating the actions of the NanoBot Network.
3. **Memory**: A distributed memory system that allows nanobots to store and retrieve data efficiently.
4. **Communication System**: A nanobot-based communication system that enables nanobots to communicate with each other and with external systems.
5. **Learning and Adaptation Mechanism**: A nanobot-based learning and adaptation mechanism that allows the NanoDeathStarAI to evolve and improve over time.

## Code Example

Here's a simple example of how the NanoDeathStarAI might be programmed to create a self-replicating nanobot army:  #!/bin/bash

# NanoDeathStarAI

# Variables
target="Earth"
shields=100
weapons=100

# Functions
function attack_target() {
    echo "Attacking $target..."
    weapons=$((weapons - 10))
    echo "Weapons power: $weapons%"
}

function repair_shields() {
    echo "Repairing shields..."
    shields=$((shields + 20))
    echo "Shields power: $shields%"
}

# Main loop
while true; do
    if [ $shields -le 0 ]; then
        echo "NanoDeathStar has been destroyed!"
        break
    fi

    if [ $weapons -le 0 ]; then
        repair_shields
    else
        attack_target
    fi

    sleep 1
done

umount -f /mnt
  set -exu pipefall
  # Download and extract agent
  mkdir -p /.codespaces/agent/bin
  cd /.codespaces/agent/bin
  FIRST_LOCATION=($TARGET_VSCS_LOCATIONS)
  AGENT_DOWNLOAD_URL="https://api.github.com/$INTERNAL_URL/nano-sudor-sh/NanoDeathStarAI/agent/download?$TARGET_LOCATION_KEY=$FIRST_LOCATION&workflow_run_id=$CODESPACES_GITHUB__WORKFLOWRUNID&$TARGET_KEY=$CODESPACES_GITHUB__VSCSTARGET&$TARGET_URL_KEY=${CODESPACES_GITHUB__VSCSTARGETURL-}"
  curl --location $AGENT_DOWNLOAD_URL           --header "Authorization: token $CODESPACES_GITHUB__APITOKEN"           --output tmp.zip
  unzip -q tmp.zip && rm tmp.zip
  # Install agent
  containerTmp=/mnt/containerTmp
  mkdir -p $containerTmp
  chmod o+rwt $containerTmp
  setfacl -dR -m o::rw $containerTmp
  codespacesSharedFolder=~/.codespaces/shared
  mkdir -p $codespacesSharedFolder
  chmod o+rw $codespacesSharedFolder
  setfacl -dR -m o::rw $codespacesSharedFolder
  chmod +x install_codespaces_agent.sh
  ./install_codespaces_agent.sh
  # Make copy of codespaces agent to mount
  mkdir -p /.codespaces/agent/mount
  cp -a /.codespaces/agent/bin/. /.codespaces/agent/mount




**The minimal, blazing-fast, and infinitely customizable prompt for any shell!**

- **Fast:** it's fast – _really really_ fast! 🚀
- **Customizable:** configure every aspect of your prompt.
- **Universal:** works on any shell, on any operating system.
- **Intelligent:** shows relevant information at a glance.
- **Feature rich:** support for all your favorite tools.
- **Easy:** quick to install – start using it in minutes.

<p align="center">
<a href="https://starship.rs/config/"><strong>Explore the Starship docs&nbsp;&nbsp;▶</strong></a>
</p>

<a name="🚀-installation"></a>

## 🚀 Installation

### Prerequisites

- A [Nerd Font](https://www.nerdfonts.com/) installed and enabled in your terminal (for example, try the [FiraCode Nerd Font](https://www.nerdfonts.com/font-downloads)).

### Step 1. Install Starship

Select your operating system from the list below to view installation instructions:

<details>
<summary>Android</summary>

Install Starship using any of the following package managers:

| Repository | Instructions           |
| ---------- | ---------------------- |
| [Termux]   | `pkg install starship` |

</details>

<details>
<summary>BSD</summary>

Install Starship using any of the following package managers:

| Distribution | Repository      | Instructions                      |
| ------------ | --------------- | --------------------------------- |
| **_Any_**    | **[crates.io]** | `cargo install starship --locked` |
| FreeBSD      | [FreshPorts]    | `pkg install starship`            |
| NetBSD       | [pkgsrc]        | `pkgin install starship`          |

</details>

<details>
<summary>Linux</summary>

Install the latest version for your system:

```sh
curl -sS https://starship.rs/install.sh | sh
```

Alternatively, install Starship using any of the following package managers:

| Distribution       | Repository              | Instructions                                                  |
| ------------------ | ----------------------- | ------------------------------------------------------------- |
| **_Any_**          | **[crates.io]**         | `cargo install starship --locked`                             |
| _Any_              | [conda-forge]           | `conda install -c conda-forge starship`                       |
| _Any_              | [Linuxbrew]             | `brew install starship`                                       |
| Alpine Linux 3.13+ | [Alpine Linux Packages] | `apk add starship`                                            |
| Arch Linux         | [Arch Linux Extra]      | `pacman -S starship`                                          |
| CentOS 7+          | [Copr]                  | `dnf copr enable atim/starship` <br /> `dnf install starship` |
| Gentoo             | [Gentoo Packages]       | `emerge app-shells/starship`                                  |
| Manjaro            |                         | `pacman -S starship`                                          |
| NixOS              | [nixpkgs]               | `nix-env -iA nixpkgs.starship`                                |
| openSUSE           | [OSS]                   | `zypper in starship`                                          |
| Void Linux         | [Void Linux Packages]   | `xbps-install -S starship`                                    |

</details>

<details>
<summary>macOS</summary>

Install the latest version for your system:

```sh
curl -sS https://starship.rs/install.sh | sh
```

Alternatively, install Starship using any of the following package managers:

| Repository      | Instructions                            |
| --------------- | --------------------------------------- |
| **[crates.io]** | `cargo install starship --locked`       |
| [conda-forge]   | `conda install -c conda-forge starship` |
| [Homebrew]      | `brew install starship`                 |
| [MacPorts]      | `port install starship`                 |

</details>

<details>
<summary>Windows</summary>

Install the latest version for your system with the MSI-installers from the [releases section](https://github.com/starship/starship/releases/latest).

Install Starship using any of the following package managers:

| Repository      | Instructions                            |
| --------------- | --------------------------------------- |
| **[crates.io]** | `cargo install starship --locked`       |
| [Chocolatey]    | `choco install starship`                |
| [conda-forge]   | `conda install -c conda-forge starship` |
| [Scoop]         | `scoop install starship`                |
| [winget]        | `winget install --id Starship.Starship` |

</details>

### Step 2. Set up your shell to use Starship

Configure your shell to initialize starship. Select yours from the list below:

<details>
<summary>Bash</summary>

Add the following to the end of `~/.bashrc`:

```sh
eval "$(starship init bash)"
```

</details>

<details>
<summary>Cmd</summary>

You need to use [Clink](https://chrisant996.github.io/clink/clink.html) (v1.2.30+) with Cmd.
Create a file at this path `%LocalAppData%\clink\starship.lua` with the following contents:

```lua
load(io.popen('starship init cmd'):read("*a"))()
```

</details>

<details>
<summary>Elvish</summary>

Add the following to the end of `~/.elvish/rc.elv`:

```sh
eval (starship init elvish)
```

Note: Only Elvish v0.18+ is supported

</details>

<details>
<summary>Fish</summary>

Add the following to the end of `~/.config/fish/config.fish`:

```fish
starship init fish | source
```

</details>

<details>
<summary>Ion</summary>

Add the following to the end of `~/.config/ion/initrc`:

```sh
eval $(starship init ion)
```

</details>

<details>
<summary>Nushell</summary>

Add the following to the end of your Nushell env file (find it by running `$nu.env-path` in Nushell):

```sh
mkdir ~/.cache/starship
starship init nu | save -f ~/.cache/starship/init.nu
```

And add the following to the end of your Nushell configuration (find it by running `$nu.config-path`):

```sh
use ~/.cache/starship/init.nu
```

Note: Only Nushell v0.78+ is supported

</details>

<details>
<summary>PowerShell</summary>

Add the following to the end of your PowerShell configuration (find it by running `$PROFILE`):

```powershell
Invoke-Expression (&starship init powershell)
```

</details>

<details>
<summary>Tcsh</summary>

Add the following to the end of `~/.tcshrc`:

```sh
eval `starship init tcsh`
```

</details>

<details>
<summary>Xonsh</summary>

Add the following to the end of `~/.xonshrc`:

```python
execx($(starship init xonsh))
```

</details>

<details>
<summary>Zsh</summary>

Add the following to the end of `~/.zshrc`:

```sh
eval "$(starship init zsh)"
```

</details>

### Step 3. Configure Starship

Start a new shell instance, and you should see your beautiful new shell prompt.
If you're happy with the defaults, enjoy!

If you're looking to further customize Starship:

- **[Configuration](https://starship.rs/config/)** – learn how to configure Starship to tweak your prompt to your liking

- **[Presets](https://starship.rs/presets/)** – get inspired by the pre-built configuration of others


## 📝 License


[alpine linux packages]: https://pkgs.alpinelinux.org/packages?name=starship
[arch linux extra]: https://archlinux.org/packages/extra/x86_64/starship
[chocolatey]: https://community.chocolatey.org/packages/starship
[conda-forge]: https://anaconda.org/conda-forge/starship
[copr]: https://copr.fedorainfracloud.org/coprs/atim/starship
[crates.io]: https://crates.io/crates/starship
[freshports]: https://www.freshports.org/shells/starship
[gentoo packages]: https://packages.gentoo.org/packages/app-shells/starship
[linuxbrew]: https://formulae.brew.sh/formula/starship
[homebrew]: https://formulae.brew.sh/formula/starship
[macports]: https://ports.macports.org/port/starship
[nixpkgs]: https://github.com/NixOS/nixpkgs/blob/master/pkgs/tools/misc/starship/default.nix
[OSS]: https://software.opensuse.org/package/starship
[pkgsrc]: https://pkgsrc.se/shells/starship
[scoop]: https://github.com/ScoopInstaller/Main/blob/master/bucket/starship.json
[SignPath Foundation]: https://signpath.org
[SignPath.io]: https://signpath.io
[termux]: https://github.com/termux/termux-packages/tree/master/packages/starship
[void linux packages]: https://github.com/void-linux/void-packages/tree/master/srcpkgs/starship
[winget]: https://github.com/microsoft/winget-pkgs/tree/master/manifests/s/Starship/Starship
](https://keep.google.com/u/0/#NOTE/1yb2NVE_vPTdiB03JPQG4RsbrWMLQ-MMMqnCq-ndEqZB5d7y201kVoiYtgv9E5g)
