---
layout: post
title: "맥용 패키지 매니저 Brew, Brew-cask"
author: "한결"
categories: blog
---

## 맥용 Package Manager Brew-cask



윈도우의 choco나 리눅스의 yum, apt-get 처럼 맥도 brew를 통해 패키지를 관리할 수 있으며 brew-cask 를 통해 프로그램도 설치가 가능하다.

brew cask 명령어를 사용하기 위해서는 brew을 설치해야 한다.

https://brew.sh/

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

별도로 brew cask를 설치해야 한다고 하는데 나는 설치 없이 사용이 가능했다. 그래도 설치하면 뭔가 설치된다. 

```
brew install cask
```

brew cask를 통해 맥 CLI 환경에서 편하게 프로그램 설치가 가능하다.

```
brew cask install google-chrome
```

```
brew cask install atom
```

설치 리스트를 저장해두어 자동적으로 재설치를 진행할 수 있다.

```
brew cask list > app_list.txt
brew cask install $(cat app_list.txt)
```

search 명령어를 통해 검색도 가능하다는데 나는 안된다. 왜지??



```
Commands:

  --cache  display the file used to cache the Cask

  audit   verifies installability of Casks

  cat    dump raw source of the given Cask to the standard output

  create   creates the given Cask and opens it in an editor

  doctor   checks for configuration issues

  edit    edits the given Cask

  fetch   downloads remote application files to local cache

  help    print help strings for commands

  home    opens the homepage of the given Cask

  info    displays information about the given Cask

  install  installs the given Cask

  list    lists installed Casks or the casks provided in the arguments

  outdated  list the outdated installed Casks

  reinstall reinstalls the given Cask

  style   checks Cask style using RuboCop

  uninstall uninstalls the given Cask

  upgrade  upgrades all outdated casks

  zap    zaps all files associated with the given Cask
```

