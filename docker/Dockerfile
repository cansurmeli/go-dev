FROM golang:1.8

#   Switch to a temporary location to avoid
# unnecessary storage usage later.
WORKDIR /tmp

# Update & install whatever is needed
RUN apt-get update -y
RUN apt-get install -y apt-utils\
                       wget \
                       git\
                       libncurses5-dev\
                       time\
                       zsh\
                       make\
                       python3.9

# Grab Vim fron source instead and compile it with Python3 support
RUN git clone https://github.com/vim/vim.git && cd vim && ./configure --enable-python3interp=yes --with-python3-config-dir=/usr/bin/python3.9/config-* && make && make install

###############
# SET-UP USER #
###############
RUN useradd go

RUN usermod -aG sudo go

USER go

WORKDIR /home/go

############
############

WORKDIR /go/src
COPY ./Godeps /go/src

RUN git clone https://gitlab.cansurmeli.com/can/vim-config.git /root/.vim

RUN wget https://raw.githubusercontent.com/pote/gpm/v1.4.0/bin/gpm && chmod +x gpm && mv gpm /usr/local/bin
RUN gpm install
RUN cp -r ./github.com /github.com  # backup packages to root to prevent volume mount removing it

# Install Go binaries that are utilised by the vim-go plugin:
# https://github.com/fatih/vim-go/blob/master/plugin/go.vim#L9
#
# We don't manually install them, we let vim-go handle that
# We use vim's `execute` command to pipe commands
# This helps avoid "Press ENTER or type command to continue"
RUN time vim -c "execute 'silent GoUpdateBinaries' | execute 'quit'"

#################
# SET-UP PREZTO #
#################
USER root

# Switch the default shell to Zshell
RUN chsh -s "$(which zsh)"

# Install Zprezto
COPY init_zprezto.sh .

RUN chmod +x init_zprezto.sh

USER python

RUN rm /home/python/.zshrc

RUN ./init_zprezto.sh

##################
# SET-UP ALIASES #
##################
#   I need the rich/colourful environment experience
# Plus, it possibly solves a Prompt.app issue on iOS/iPadOS
#
#   Another option is `screen-256color` but the one with `xterm`
# solves another issue with Vim colorscheme `onedark`.
ENV TERM="xterm-256color"

#########
# OTHER #
#########
# Grab my Vim config
RUN git clone --recursive https://gitlab.cansurmeli.com/can/vim-config /home/python/.vim

WORKDIR /go/src
