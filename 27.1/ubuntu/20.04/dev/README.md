# Build emacs in docker, put the binaries outside of docker


```
~/dvcs/github/docker-emacs/27.1/ubuntu/20.04/dev$ docker build -f Dockerfile . -t emacs-build
```

After that's built, run the container that includes the emacs dependencies. This is for just 27.1 with native modules:

```
~/dvcs/github/docker-emacs/27.1/ubuntu/20.04/dev$ docker run -v /home/spacey:/home/spacey -it emacs-build bash
# based on https://emacs.stackexchange.com/questions/59538/compile-emacs-from-feature-native-comp-gccemacs-branch-on-ubuntu:
$ cd /opt && git clone --depth 1 --branch emacs-27.1 git://git.sv.gnu.org/emacs.git /opt/emacs
$ cd /opt/emacs && ./autogen.sh 
$ ./configure --build="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" --with-modules --prefix=/home/spacey/emacs/27.1
$ make -j $(nproc)
$ make install
```

This includes native compilation

```
~/dvcs/github/docker-emacs/27.1/ubuntu/20.04/dev (master)$ docker run -v /home/spacey:/home/spacey -it emacs-build bash
$ apt-get remove -y libgccjit-10-dev libgccjit0
$ apt-get update && apt-get install -y software-properties-common
$ add-apt-repository -y ppa:ubuntu-toolchain-r/ppa
$ apt-get update
$ apt-get install -y gcc-10 g++-10 libgccjit0 libgccjit-10-dev libjansson4 libjansson-dev
$ git clone --depth 1 --branch feature/native-comp git://git.sv.gnu.org/emacs.git /opt/emacs
$ cd /opt/emacs && ./autogen.sh 
$ cd /opt && git clone --depth 1 --branch native-comp git://git.sv.gnu.org/emacs.git /opt/emacs
$ export CC=/usr/bin/gcc-10 CXX=/usr/bin/gcc-10
$ ./configure --build="$(dpkg-architecture --query DEB_BUILD_GNU_TYPE)" --with-modules --prefix=/home/spacey/emacs/native-comp-$(date +%Y%m%d)  --with-nativecomp 
$ make -j $(nproc)
$ make install
```
