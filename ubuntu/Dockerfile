FROM ubuntu:18.04

LABEL maintainer="Tew Tawan <tew@tawan.org>"

# Replace shell with bash so we can source files
# Set the SHELL to bash with pipefail option
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
# RUN rm /bin/sh && ln -s /bin/bash /bin/sh

ENV NODE_VERSION v14
# Prevent dialog during apt install
ENV DEBIAN_FRONTEND noninteractive
# ShellCheck version
ENV SHELLCHECK_VERSION=0.7.0
# Pick a Ubuntu apt mirror site for better speed
# ref: https://launchpad.net/ubuntu/+archivemirrors
ENV UBUNTU_APT_SITE ubuntu.cs.utah.edu

# Disable src package source
RUN sed -i 's/^deb-src\ /\#deb-src\ /g' /etc/apt/sources.list
# Replace origin apt package site with the mirror site
RUN sed -E -i "s/([a-z]+.)?archive.ubuntu.com/$UBUNTU_APT_SITE/g" /etc/apt/sources.list
RUN sed -i "s/security.ubuntu.com/$UBUNTU_APT_SITE/g" /etc/apt/sources.list

# Install apt packages
RUN apt update && \
    apt upgrade -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold"  && \
    apt install -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold"     \
    coreutils             \
    util-linux            \
    bsdutils              \
    file                  \
    openssl               \
    libssl-dev            \
    locales               \
    ca-certificates       \
    ssh                   \
    wget                  \
    patch                 \
    sudo                  \
    htop                  \
    dstat                 \
    vim                   \
    tmux                  \
    curl                  \
    git                   \
    jq                    \
    zsh                   \
    ksh                   \
    gcc-4.8               \
    g++-4.8               \
    xz-utils              \
    build-essential       \
    bash-completion 

RUN apt-get update --fix-missing && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    automake build-essential curl \
    cdbs debhelper autoconf dh-autoreconf flex bison \
    libjpeg-dev libtiff-dev libpng-dev libgif-dev librsvg2-dev libpoppler-glib-dev zlib1g-dev fftw3-dev liblcms2-dev \
    liblcms2-dev libmagickwand-dev libfreetype6-dev libpango1.0-dev libfontconfig1-dev libglib2.0-dev libexpat1-dev libice-dev \
    gettext pkg-config libxml-parser-perl libexif-gtk-dev liborc-0.4-dev libopenexr-dev libmatio-dev libxml2-dev \
    libcfitsio-dev libopenslide-dev libwebp-dev libgsf-1-dev libgirepository1.0-dev gtk-doc-tools

ENV LIBVIPS_VERSION_MAJOR 8
ENV LIBVIPS_VERSION_MINOR 12
ENV LIBVIPS_VERSION_PATCH 2
ENV LIBVIPS_VERSION $LIBVIPS_VERSION_MAJOR.$LIBVIPS_VERSION_MINOR.$LIBVIPS_VERSION_PATCH

RUN \
    # Build libvips
    cd /tmp && \
    curl -L -O https://github.com/libvips/libvips/releases/download/v$LIBVIPS_VERSION/vips-$LIBVIPS_VERSION.tar.gz && \
    tar zxvf vips-$LIBVIPS_VERSION.tar.gz

RUN \
    cd /tmp/vips-$LIBVIPS_VERSION && \
    ./configure --enable-debug=no --without-python $1 && \
    make && \
    make install && \
    ldconfig

RUN \
    # Clean up
    apt-get remove -y automake && \
    apt-get autoremove -y && \
    apt-get autoclean && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# RUN wget https://github.com/koalaman/shellcheck/releases/download/v$SHELLCHECK_VERSION/shellcheck-v$SHELLCHECK_VERSION.linux.x86_64.tar.xz -O- | \
#     tar xJvf - shellcheck-v$SHELLCHECK_VERSION/shellcheck          && \
#     mv shellcheck-v$SHELLCHECK_VERSION/shellcheck /bin             && \
#     rmdir shellcheck-v$SHELLCHECK_VERSION
# RUN shellcheck -V

# Set locale
RUN locale-gen en_US.UTF-8
# Print tool versions
RUN bash --version | head -n 1
RUN zsh --version
RUN ksh --version || true
RUN dpkg -s dash | grep ^Version | awk '{print $2}'
RUN git --version
RUN curl --version
RUN wget --version

# Add user
RUN useradd -m docker && echo "docker:docker" | chpasswd && adduser docker sudo

USER docker

# Install nvm
RUN mkdir -p $HOME/.nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN echo 'export NVM_DIR="$HOME/.nvm"'                                       >> "$HOME/.bashrc"
RUN echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm' >> "$HOME/.bashrc"
RUN echo '[ -s "$NVM_DIR/bash_completion" ] && . "$NVM_DIR/bash_completion" # This loads nvm bash_completion' >> "$HOME/.bashrc"

RUN source $HOME/.nvm/nvm.sh && \
    nvm install $NODE_VERSION && \
    npm i npm@latest -g && \
    npm install -g yarn && \
    npm install --prefix "$HOME/.nvm/" && \
    nvm alias default $NODE_VERSION && \
    nvm use default

RUN echo 'export PATH="$PATH:$(yarn global bin)"'

SHELL ["/bin/bash", "--login", "-c"]

CMD [ "/bin/bash" ]
