# encoding: utf-8
# -*- mode: ruby -*-
# vi: set ft=ruby :

RAILS_DEV_BOX_SCRIPT = <<EOF.freeze
DEBIAN_FRONTEND=noninteractive
set -e
set -x

echo "-------------------- updating package lists"
sudo apt-get update -y

echo "-------------------- installing packages"
sudo apt-get --assume-yes install \
  autoconf \
  bison \
  build-essential \
  redis-server \
  libssl-dev \
  libyaml-dev \
  libreadline6-dev \
  zlib1g-dev \
  libncurses5-dev \
  libffi-dev \
  libgdbm3 \
  libgdbm-dev \
  libpq-dev \
  libxml2-dev

# Install Postgres
echo "--------------------  Installing postgres"
sudo apt-get install postgresql postgresql-contrib -y

# Create Postgres User
sudo -u postgres createuser vagrant -s

echo "-------------------- Cloning, installing and configuring RBENV"
git clone https://github.com/rbenv/rbenv.git .rbenv
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> .bashrc
echo 'eval "$(rbenv init -)"' >> .bashrc
source .bashrc

echo "-------------------- Cloning, installing and configuring ruby-build"
git clone https://github.com/rbenv/ruby-build.git .rbenv/plugins/ruby-build
sudo -H -u vagrant bash -i -c 'rbenv install 2.3.3'
sudo -H -u vagrant bash -i -c 'rbenv global 2.3.3'
sudo -H -u vagrant bash -i -c 'rbenv rehash'

echo "-------------------- Install Node Version Manager (NVM) and NodeJS"
sudo -H -u vagrant wget -O - https://raw.githubusercontent.com/creationix/nvm/v0.31.0/install.sh | bash
sudo -H -u vagrant bash -i -c 'source ~/.profile'
sudo -H -u vagrant bash -i -c 'nvm install 6.6.0'
sudo -H -u vagrant bash -i -c 'nvm use 6.6.0'

echo "-------------------- Tell GEM not to install documentation"
sudo -H -u vagrant bash -i -c 'echo "gem: --no-document" > .gemrc'

echo "-------------------- Gem install bundler"
sudo -H -u vagrant bash -i -c 'gem install bundler --no-ri --no-rdoc'

echo "-------------------- Gem install pg gem"
sudo -H -u vagrant bash -i -c 'gem install pg'

echo "-------------------- Gem install RAILS"
sudo -H -u vagrant bash -i -c 'gem install rails'

echo "-------------------- RBENV rehash"
sudo -H -u vagrant bash -i -c 'rbenv rehash'

echo "-------------------- Add bashrc.git"
sudo -H -u vagrant bash -i -c 'curl https://raw.githubusercontent.com/zave/dotfiles/master/.bashrc.git > ~/.bashrc.git'

echo "-------------------- Install project gems"
sudo -H -u vagrant bash -i -c 'cd /vagrant && bundle install'

echo "-------------------- Setup the database"
sudo -H -u vagrant bash -i -c '( cd /vagrant && bundle exec rake db:create && bundle exec rake db:schema:load )'
echo "Configuration is required to access the database with a GUI from the host. See: http://theneum.com/blog/connect-to-vagrant-postgres-database-via-pgadmin3-on-mac/"

echo "-------------------- Your Ubuntu 16.04 Rails development machine, with RBENV, Ruby 2.3.1, Rails 5.0 is now ready!"
EOF

Vagrant.configure("2") do |config|
  config.vm.box = 'bento/ubuntu-16.04'
  config.vm.network :forwarded_port, guest: 3000, host: 3000
  config.vm.network :forwarded_port, guest: 5432, host: 65432
  config.vm.provision :shell, privileged: false, inline: RAILS_DEV_BOX_SCRIPT.dup
  config.vm.provider 'virtualbox' do |v|
    v.gui = false
    v.memory = '2048'
  end
end
