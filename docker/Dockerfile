FROM fedora:36

#   Switch to a temporary location to avoid
# unnecessary storage usage later.
WORKDIR /tmp

##################
# SET-UP ALIASES #
##################
#   I need the rich/colourful environment experience
# Plus, it possibly solves a Prompt.app issue on iOS/iPadOS
#
#   Another option is `screen-256color` but the one with `xterm`
# solves another issue with Vim colorscheme `onedark`.
ENV TERM="xterm-256color"

# Update & install whatever is needed
RUN dnf update -y
RUN dnf install -y git\
									 wget\
									 which\
									 time\
									 ca-certificates\
									 zsh\
									 gcc\
									 ncurses-devel\
									 make\
									 util-linux-user\
									 sudo\
									 python3.9\
									 go

#RUN update-ca-certificates

# Grab Vim fron source instead and compile it with Python3 support
RUN git clone https://github.com/vim/vim.git && cd vim && ./configure --enable-python3interp=yes --with-python3-config-dir=/usr/bin/python3.9/config-* && make && make install

###############
# SET-UP USER #
###############
RUN useradd go

RUN usermod -aG wheel go

WORKDIR /home/go

#		For some reason, everything still belongs to `root` under the home 
# directory. Fix that...
RUN chown -R go:go /home/go

#################
# SET-UP PREZTO #
#################
# Remove default dotfiles for zprezto's installation
RUN rm /home/go/.zprofile
RUN rm /home/go/.zshrc

# Switch the default shell to Zshell
RUN chsh -s "$(which zsh)"

# Install Zprezto
COPY init_zprezto.sh .

RUN chmod +x init_zprezto.sh

USER go

RUN ./init_zprezto.sh

#############
# SETUP VIM #
#############
# Temporarily disable SSL. The is something I can't comprehend.
#RUN git config --global http.sslVerify false

# Grab my Vim config
RUN git clone --recursive https://gitlab.cansurmeli.com/can/vim-config .vim

####################
# GO RELATED STUFF #
####################
# Create GOPATH location and have it acknowledged
RUN mkdir /home/go/go
ENV GOPATH="$HOME/go"
RUN echo 'GOPATH="$HOME/go"' >> /home/go/.zshrc

# Bring over the dependencies required
COPY ./Godeps /go/src/

USER root
RUN wget https://raw.githubusercontent.com/pote/gpm/v1.4.0/bin/gpm && chmod +x gpm && mv gpm /usr/local/bin
RUN gpm install
#RUN cp -r ./github.com /github.com  # backup packages to root to prevent volume mount removing it

# Install Go binaries that are utilised by the vim-go plugin:
# https://github.com/fatih/vim-go/blob/master/plugin/go.vim#L9
#
# We don't manually install them, we let vim-go handle that
# We use vim's `execute` command to pipe commands
# This helps avoid "Press ENTER or type command to continue"
#RUN time vim -c "execute 'silent GoUpdateBinaries' | execute 'quit'"

USER go
WORKDIR /go/src
