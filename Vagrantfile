require 'yaml'

CONFIG = YAML.load_file(File.join(File.dirname(__FILE__), 'config.yml'))

Vagrant.configure("2") do |config|
  config.vm.define CONFIG['server']['name'] do |cfg|
    cfg.vm.box = CONFIG['server']['box']
    cfg.vm.network "public_network", ip: CONFIG['server']['ip']
    cfg.vm.hostname = CONFIG['server']['hostname']
    
    cfg.vm.provider "virtualbox" do |v|
      v.memory = CONFIG['server']['memory']
      v.cpus = CONFIG['server']['cpu']
      v.name = CONFIG['server']['name']
    end

    # ssh 비밀번호인증 활성화
    cfg.vm.provision "shell", inline: <<-SCRIPT
      sed -i -e 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config
      systemctl restart sshd
    SCRIPT

    # install docker
    cfg.vm.provision "shell", inline: <<-SCRIPT
      apt-get update
      apt-get install ca-certificates curl gnupg lsb-release -y
      curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
      echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
      apt-get update
      apt-get install docker-ce docker-ce-cli containerd.io -y
      systemctl enable docker
      systemctl start docker
    SCRIPT

    # install minikube
    cfg.vm.provision "shell", inline: <<-SCRIPT
      curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
      install minikube-linux-amd64 /usr/local/bin/minikube
    SCRIPT
  end
end