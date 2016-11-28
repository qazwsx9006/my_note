
主要參考教學：http://installrails.com/
其他輔助：
  1. (xdite 安裝教學 https://github.com/rocodev/guides/wiki/setup-mac-development)
  2. http://sourabhbajaj.com/mac-setup/
  3. https://aaaaaashu.gitbooks.io/mac-dev-setup/content/

1. 透過 App Store 安裝 Xcode

2. 安裝 homebrew
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
$ brew -v # Homebrew 0.9.X
$ brew doctor # check 安裝是否正確

3. 安裝 zsh
$ brew install zsh zsh-completions

4. 安裝 on-my-zsh
$ curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh

5. 安裝 git
$ brew install git
$ git --version # git version 2.x.x

6. 修改 git config
$ git config --global user.name "Your Actual Name"
$ git config --global user.email "Your Actual Email"
$ git config --global color.ui true
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.st status
$ git config --global alias.br branch
$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

7. 安裝 RVM
$ \curl -L https://get.rvm.io | bash -s stable

8. 安裝 ruby (安裝最新版 ruby 並設為rvm 預設ruby 版本)
$ rvm use ruby --install --default
$ ruby -v

9. 安裝 rails (不安裝說明文件)
$ gem install rails --no-ri --no-rdoc
$ rails -v

10. 安裝sublime 後。
$ sudo ln -s "/Applications/Sublime\ Text\ 2.app/Contents/SharedSupport/bin/subl" /usr/local/bin/subl # 根據版本調整位置

11. 安裝 mysql
$ brew install mysql

11.1 設定mysql
$ unset TMPDIR
根據 mac 版本 ＥＩ之前使用
$ mysql_install_db --verbose --user=`whoami` --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp
ＥＩ 版本之後使用
$ mysqld -initialize --verbose --user=whoami --basedir="$(brew --prefix mysql)" --datadir=/usr/local/var/mysql --tmpdir=/tmp

啟動 server (重開機需重開server)
$ mysql.server start
關閉server
$ mysql.server stop
預設 root 沒有密碼，可執行修改密碼
$ mysqladmin -u root password 'new-password'

# 若要保持開啟，重開機不需重啟server （尚未測試, 參考 brew services[https://github.com/Homebrew/homebrew-services] ）
$ brew services start mysql

12. 安裝 imagemagick
$ brew install imagemagick

13. 安裝nvm (透過 homebrew) # nvm 官方 github 表示不支援 homebrew 安裝。 但目前執行可成功。若安裝失敗可參考 nvm 官方文件 [https://github.com/creationix/nvm]
$ brew install nvm

14. 將以下指令加入 .bash_profile (or .bashrc, or .zshrc)
```
export NVM_DIR=~/.nvm
source $(brew --prefix nvm)/nvm.sh
```
重開terminal or $ source .bash_profile

15. 檢查 nvm 可安裝的 node 版本
$ nvm ls-remote

16. 安裝node (通常會安裝最新的 LTS 版本)
$ nvm install 6.9.1
$ nvm use 6.9.1
(會將初次安裝版本設為預設版本，若要修改預設版本)
$ nvm alias default 6.9.1
(檢查所有安裝版本)
$ nvm list


額外紀錄：
1.
rails bundle install 時跳出
```
An error occured while install pg (0.xx.x), and Bundler cannot continue.
```
通常是因為主機沒有安裝 postgresql，安裝後即可正常 bundle
`
$ brew install postgresql
`

2.
rails bundle install 時跳出
```
An error occured while install nokogiri (1.xx.x), and Bundler cannot continue.
```
通常是因為相依套件沒有安裝好。執行
`
xcode-select --install
`
並依照指示（按確認）安裝後，即可正常bundle

3.
rails 5 & ruby 2.3.x 使用 sqlite3 可能會有 error
