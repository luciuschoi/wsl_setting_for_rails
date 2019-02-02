# 윈도우 10에서 레일스 프로젝트 개발환경 설정하기 (2019년)

우분투(WSL)를 설치한 후, 

1. 시스템 업데이트 설치하기

   ```sh
   $ sudo apt update
   $ sudo apt list --upgradable
   $ sudo apt upgrade
   $ sudo apt autoremove
   ```

   https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-18-04

2. 운영체제 버전 알아내기

   ```sh
   $ lsb_release -a
   ```

   ```
   No LSB modules are available.
   Distributor ID: Ubuntu
   Description:    Ubuntu 18.04.1 LTS
   Release:        18.04
   Codename:       bionic
   ```

3. 레일스 설치에 필요한 의존성 라이브러리 설치하기

   ```sh
   $ sudo apt install -y autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm5 libgdbm-dev
   ```

4. rbenv 설치하기

   ```sh
   $ cd
   $ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
   $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
   $ echo 'eval "$(rbenv init -)"' >> ~/.bashrc
   $ exec $SHELL
   
   $ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
   $ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.bashrc
   $ exec $SHELL
   
   $ rbenv install 2.6.1
   $ rbenv global 2.6.1
   $ ruby -v
   ```


5. Zsh  설치하기
   https://medium.com/@vinhp/use-zsh-in-wsl-on-windows-10-5d439a749c4c

   ```sh
   $ sudo apt-get install -y zsh
   $ zsh # 설치 옵션 0 을 선택한다.
   ```

6. oh-my-zsh  설치하기
   https://blog.joaograssi.com/windows-subsystem-for-linux-with-oh-my-zsh-conemu/

   ```sh
   $ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
   ```

   .bashrc  파일을 열고 첫번째 코멘트 직후 아래를 복사해서 붙여넣기 한다. 

   ```sh
   if test -t 1; then
   exec zsh
   fi
   ```

   터미널을 종료한 후  WSL(우분투 18.04) 을다시 실행하면  zsh  실행 터미널이 보이게 된다.

7. `.zshrc`  파일 업데이트하기
   쉘이 zsh로 변경되었기 때문에 rbenv 실행환경을 재설정해 주어야 한다.

   ```sh
   $ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
   $ echo 'eval "$(rbenv init -)"' >> ~/.zshrc
   $ echo 'export PATH="$HOME/.rbenv/plugins/ruby-build/bin:$PATH"' >> ~/.zshrc
   $ exec $SHELL
   ```

8. Bundler 젬 설치하기
  `~/.gemrc` 파일을 생성하고 아래의 내용을 복사해서 붙여넣기 한다.  

   ```
   gem: --no-document
   ```

   이것은 향후 젬을 설치할 때 문서 파일을 제외하기 위한 조치이다.

   ```sh
   $ gem install bundler
   $ rbenv rehash
   ```

9. Git 환경설정하기 

   ```sh
   $ git config --global color.ui true
   $ git config --global user.name "YOUR NAME"
   $ git config --global user.email "YOUR@EMAIL.com"
   $ ssh-keygen -t rsa -b 4096 -C "YOUR@EMAIL.com"
   ```

10. Github 에 ssh 공개키 등록하기   
    생성된  ssh  공개키를 복사한다. 

    ```sh
    $ cat ~/.ssh/id_rsa.pub
    ```

    자신의  github 계정으로 로그인 한 후 설정으로 이동하여 ssh 키를 등록한다. 
    이제 제대로 설정이 되었는지 확인하기 위해 아래와 같이 쉘명령을 실행한다.

    ```sh
    $ ssh -T git@github.com
    ```

11. Nodejs 설치하기

    ```sh
    $ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
    $ sudo apt-get install -y nodejs
    ```

12. Yarn(자바스크립트 패키지 매니저) 설치하기

    ```sh
    $ curl -sL https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
    $ echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
    $ sudo apt-get update && sudo apt-get install yarn
    ```

13. ImageMagick 설치하기

    ```sh
    $ sudo apt-get install -y libmagickwand-dev imagemagick
    ```

14. Rails  설치하기

    ```sh
    $ gem install rails
    ```

    설치시에 나타나는 안내문:

    ```
    HEADS UP! i18n 1.1 changed fallbacks to exclude default locale.
    But that may break your application.
    
    Please check your Rails app for 'config.i18n.fallbacks = true'.
    If you're using I18n (>= 1.1.0) and Rails (< 5.2.2), this should be
    'config.i18n.fallbacks = [I18n.default_locale]'.
    If not, fallbacks will be broken in your app by I18n 1.1.x.
    ```

15. MySQL  설치하기
    https://www.digitalocean.com/community/tutorials/how-to-install-mysql-on-ubuntu-18-04

    ```sh
    $ sudo apt-get install -y mysql-server mysql-client libmysqlclient-dev
    $ sudo service mysql start
    $ sudo mysql_secure_installation  # root 계정에 암호를 설정할 때
    $ sudo mysql
    ```

    * `root@localhost` 계정 접속이 안될 때
      -> https://youtu.be/SJm91cvE_ks

    * 한글깨짐현상 방지하기
      (https://nesoy.github.io/articles/2017-05/mysql-UTF8)
      `/etc/mysql/my.cnf`  파일 끝에 아래의 내용을 추가한다. 

      ```mysql
      [client]
      default-character-set=utf8
      
      [mysql]
      default-character-set=utf8
      
      [mysqld]
      collation-server = utf8_unicode_ci
      init-connect='SET NAMES utf8'
      character-set-server = utf8
      ```

    * 배포용 계정(`deployer`)을 생성한다.

      ```mysql
      mysql> CREATE USER 'deployer'@'localhost' IDENTIFIED BY 'password';
      mysql> GRANT ALL PRIVILEGES ON *.* TO 'deployer'@'localhost' WITH GRANT OPTION;
      mysql> FLUSH PRIVILEGES;
      ```

    * 윈도우용 MySQL Workbench 설치하기
      (https://stackoverflow.com/a/54192456) 
      주의사항 : 서버의 시작과 종료는  WSL 에서 해야 한다.

      ```sh
      $ sudo service mysql start|stop|restart|status
      ```

16. PostgreSQL  설치하기(선택사항)
    (https://medium.com/@stephanedmonson/solution-for-connecting-postgresql-via-wsl-windows-subsystem-for-linux-ubuntu18-c79940fa5742)

    * PgAdmin 4 설치하기
      (https://stackoverflow.com/a/54192456)
      주의사항 : 서버의 기동은 WSL 에서 해야 한다. 

      ```sh
      $ sudo service postgresql start
      ```

    * PostgreSQL  사용자 추가
      (https://medium.com/coding-blocks/creating-user-database-and-adding-access-on-postgresql-8bfcd2f4a91e)

    * ###### `pg` 젬을 설치하기 위해서 아래의 의존성을 설치하기

      ```sh
      $ sudo apt install -y postgresql-client-common postgresql-client libpq-dev
      ```

17. 윈도우 파일 시스템에 접근하기
    (https://code.apptilus.com/posts/tools/windows-subsystem-linux)

    WSL 내에서 윈도우 파일시스템에 접근하기 위해서 다음과 같이 필요한 폴더를 마운트한다.

    ```sh
    # D드라이브 하위의 workspace 폴더에 접근하기
    $ cd /mnt/d/workspace
    ```

    매번 윈도우 내부의 작업 디렉토리로 이동하기 위해 위와 같이 명령어를 입력하는 것은 매우 귀찮은 작업이다. 따라서, 심볼릭 링크를 이용하여 마치 WSL 내부의 디렉토리를 이용하듯 손쉽게 윈도우 폴더에 접근하도록 한다.

    ```sh
    # symbolic link 사용
    $ ln -s "/mnt/d/workspace" /home/<my-wsl-username>/workspace
    ```

    위와 같이 심볼릭 링크를 구성하면 WSL에서 `cd workspace` 명령만으로 간단하게 윈도우의 Workspace 폴더에 접근할 수 있게 된다.


​    

