---
layout: post
title:  "Compiling PCE on WSL1"
---

I am working on a problem where I need to find out what Macintosh routine is writing
to a particular low-memory global variable. I decided to do this by modifying the PCE
emulator to print out a message with the value of the 68000's PC (program counter)
register whenever a write was done to that particular memory location.

But first, I had to figure out how to compile PCE on my Windows machine under WSL 1.
This is what eventually worked:

```
sudo apt-get install mingw-w64

git clone https://github.com/libsdl-org/SDL.git
git clone git://git.hampa.ch/pce.git

mkdir pce/lib-sdl
mkdir SDL-build
mkdir PCE-build

export SDL_PREFIX=`cd pce/lib-sdl/;pwd`
export PCE_PREFIX=`cd PCE-build/;pwd`
export PATH="$PATH:$SDL_PREFIX/bin"

cd SDL-build
../SDL/configure --disable-libc --host=i686-w64-mingw32 --prefix=$SDL_PREFIX
make
make install

git clone git://git.hampa.ch/pce.git
cd pce
#export LDFLAGS="-L$SDL_PREFIX/lib "
#export PCE_SDL_CFLAGS="-I$SDL_PREFIX/include/SDL2"
#export PCE_SDL_LIBS="-lmingw32-lSDL2 -lSDL2main"

./configure --with-sdl=2 --disable-atari-st --disable-cpm80 --disable-ibmpc --disable-rc759 --disable-sim405  --disable-sims32 --disable-simarm  --disable-vic20 --host=i686-w64-mingw32 --prefix=$PCE_PREFIX
make
```

This is not perfect. The compilation of some of the command line tools fails, but the emulator
did get build. So, progress!

My next step is to hack the emulator so it prints out a message when the particular memory
location is accessed!