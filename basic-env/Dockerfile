FROM ubuntu:18.04

# Locales
ENV LANGUAGE=en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV USERNAME=caente
ENV HOME=/home/$USERNAME
RUN useradd -ms /bin/zsh $USERNAME

RUN apt-get update && apt-get install -y locales && locale-gen en_US.UTF-8

# Colors and italics for tmux
COPY xterm-256color-italic.terminfo $HOME
RUN tic $HOME/xterm-256color-italic.terminfo
ENV TERM=xterm-256color-italic

# Common packages
RUN apt-get update && apt-get install -y \
      build-essential \
      curl \
      git  \
      libncurses5-dev \
      libevent-dev \
      silversearcher-ag \
      wget \
      zsh \
      xclip \
      w3m \
      tmux \
      fzy

# Install neovim
RUN apt-get install -y \
      autoconf \
      automake \
      cmake \
      g++ \
      libtool \
      libtool-bin \
      pkg-config \
      python \
      python-pip \
      python3 \
      python3-pip \
      unzip \
      gettext

WORKDIR /usr/local/src
RUN git clone --depth 1 https://github.com/neovim/neovim.git
WORKDIR /usr/local/src/neovim
RUN git fetch --depth 1 origin tag v0.3.1
RUN git reset --hard v0.3.1
RUN make CMAKE_BUILD_TYPE=Release
RUN make install
RUN rm -rf /usr/local/src/neovim

USER $USERNAME

RUN pip3 install --upgrade pip &&\ 
    python3 -m pip install --user neovim jedi mistune psutil setproctitle

RUN pip install --upgrade pip &&\ 
    python3 -m pip install --user neovim jedi mistune psutil setproctitle

WORKDIR $HOME

# zsh config
COPY configs/zsh/zshrc $HOME/.zshrc
ENV HISTFILE=$HOME/.zhistory/.zsh_history
RUN mkdir $HOME/.zhistory
COPY configs/zsh/oh-my-zsh $HOME/.oh-my-zsh
COPY configs/nvim/vim $HOME/.vim
COPY configs/nvim/vimrc $HOME/.config/nvim/init.vim
RUN echo '/usr/bin/zsh' >> $HOME/.bashrc
COPY configs/tmux/tmux.conf $HOME/.tmux.conf
RUN git clone https://github.com/tmux-plugins/tpm $HOME/.tmux/plugins/tpm

USER root
RUN chown -R $USERNAME $HOME
USER caente

RUN mkdir app

