# Configuración de Vagrant
Vagrant.configure("2") do |config|
  
    # Configuración para el primer servidor Apache
    config.vm.define "web1" do |web1|
      web1.vm.box = "ubuntu/bionic64"  # Puedes cambiar la box por otra si lo prefieres
      web1.vm.network "private_network", ip: "192.168.56.10"
      web1.vm.hostname = "web1"
      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        echo "<h1>Servidor Apache Web1</h1>" | sudo tee /var/www/html/index.html
        sudo ufw allow 80
      SHELL
    end
    
    # Configuración para el segundo servidor Apache
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/bionic64"
      web2.vm.network "private_network", ip: "192.168.56.11"
      web2.vm.hostname = "web2"
      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        echo "<h1>Servidor Apache Web2</h1>" | sudo tee /var/www/html/index.html
        sudo ufw allow 80
      SHELL
    end
    
    # Configuración para el servidor Nginx como balanceador de carga
    config.vm.define "loadbalancer" do |lb|
      lb.vm.box = "ubuntu/bionic64"
      lb.vm.network "private_network", ip: "192.168.56.12"
      lb.vm.hostname = "loadbalancer"
      lb.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        cat <<EOF | sudo tee /etc/nginx/conf.d/load_balancer.conf
  upstream backend {
      server 192.168.56.10;
      server 192.168.56.11;
  }
  
  server {
      listen 80;
      location / {
          proxy_pass http://backend;
      }
  }
  EOF
        sudo nginx -t
        sudo systemctl restart nginx
        sudo ufw allow 80
      SHELL
    end
  
    # Sincronizar la carpeta de la máquina host a los servidores web
    config.vm.synced_folder "./web", "/var/www/html"
    
  end
  