
When cross compiling qt6 wity cmake you need to meet one of these conditions to successfully build it

- Option 1: Do this on the host machine (not inside the build container)

Debian based

```bash
sudo apt install qemu-user-static binfmt-support
```

Alpine

```bash
sudo apk add qemu qemu-openrc
```

- Option 2: Install this custom prebuild of qt6 static first to `/usr/local` then set `-D QT_HOST_PATH` with cmake when building qt6.

Example commands:

```bash
curl -sLO https://github.com/userdocs/qbt-qt6/releases/latest/download/x86_64-qt6-iconv.tar.xz
tar -xf x86_64-qt6-iconv.tar.xz --strip-components=1 -C /usr/local
ldconfig
```

This method will work inside a docker container (like github workflow `container:`) where you have not or cannot install qemu on the host.

notes:

This problem is the reason I avoid `container:` and use docker commands directly on the Github hosted runner.

The issue with the second option is that even though it's the way qt6 docs say you must cross compile it's not the easiest way. Without a doubt option 1 is the easiest and fastest way to do this. Temporary emulation during the build process solve the entire problem.

An example problem is that if you install qt6 you are going to get a version linked totally differently to how we want qt6 to be linked. And it cannot seem to help itself to link to things and default to settings based on this, whether you want to or not. It has so many auto toggles for features it's a minefield to configure. This is why i've never bothered with it. A needlessly difficult way to do a thing that should be simple.

Another example problem is that whe you use `container:` in a Github workflow you cannot modify the host runner. So there is no qemu emulation available and it forces you to build qt6 twice. I don't want to build to twice during the build process, it's one of the largest dependencies.

So we don't want to install a system version or build it twice.

That is why i made this. It's just to finally solve that issue properly. A fallback method to be able to build.

It packages a partial build from the normal script but just for these dependencies to provide the exact host build of qt6 static we need to cross compile.

- `zlib` 
- `iconv` 
- `icu` 
- `openssl` 
- `double_conversion` 
- `qtbase` 
- `qttools`