in := "/usr/local/bin/fastly"
vim_dir := "./.vim"
vimrc := "./.vimrc"
container_env := "go-container-with-vim"
container_compiler := "go-compiler"

copy_vim_files:
  @if [ ! -d $(vim_dir) ]; then cp -r "$$HOME/.vim" $(vim_dir); fi
  @if [ ! -f $(vimrc) ]; then cp "$$HOME/.vimrc" $(vimrc); fi

remove_vim_files:
  @rm -rf $(vim_dir) &> /dev/null || true
  @rm $(vimrc) &> /dev/null || true

remove_compiled_files:
  @rm ./fastly.{darwin,linux,windows.exe} &> /dev/null || true

clean: remove_vim_files remove_compiled_files
  @docker rmi -f $(container_env) &> /dev/null || true
  @docker rmi -f $(container_compiler) &> /dev/null || true

uninstall: clean
  @rm $(bin) &> /dev/null || true

build: copy_vim_files
  @docker build -t $(container_env) .

dev: build remove_vim_files
  @docker run -it -v "$$(pwd)":/go/src $(container_env) /bin/bash

rebuild: clean run

compile:
  @docker build -t $(container_compiler) -f ./Dockerfile-compile .
  @docker run -it -v "$$(pwd)":/go/src $(container_compiler) || true

copy_binary:
  cp ./fastly.darwin $(bin)

install: compile copy_binary remove_compiled_files
