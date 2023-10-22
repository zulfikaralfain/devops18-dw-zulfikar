# STAGE 2
# Insfrastruktur As Code

##### Langkah awal yaitu membuat script untuk menginstall ansible. disini karna saya tidak menggunakan terraform, saya menggunakan multipass untuk menginstall ansible nya. untuk multipass bisa diinstall melalui microsoft store atau bisa melalui website *https://multipass.run/install*
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(315).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(329).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(317).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(318).png?raw=true)
```
nano install-ansible.sh
```
```
#!/bin/bash

# Perbarui paket-paket yang ada
sudo apt update -y

# Instal Ansible
sudo apt install ansible -y

# Tampilkan versi Ansible yang telah diinstal
ansible --version

echo "Instalasi Ansible selesai."
```
```
chmod +x install-ansible.sh
```
```
./install-ansible.sh
```
##### Jika sudah buat file bernama ansiblenya. dan didalam file ansible saya membuat `ansible.cofg` dan juga membuat file untuk appserver, gateway dan resource. dan saya membuat Penyimpanan yang dimana penyimpanan tersebut berisikan alamat IP appserver, gateway dan nama user di appserver juga gateway. Jika sudah copy `ansible.cofg` ke appserver, gatewaye dan resource.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(320).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(321).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(322).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(323).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(324).png?raw=true)
```
mkdir ansible
```
```
nano ansible.cofg
```
```
[defaults]
inventory = Penyimpanan
private_key_file = ~/.ssh/id_rsa
host_key_checking = false
interpreter_python = auto_silent
```
```
nano Penyimpanan
```
```
[appserver]
103.175.221.150

[gateway]
103.150.93.47

[all:vars]
ansible_user="zulfikar"
```
```
mkdir appserver gateway resource
```
```
cp ansible.cfg appserver && cp ansible.cfg gateway && cp ansible.cfg resource
```
## 1. ansible appserver
###### Disini kita masuk ke file appserver yang ada di ansible, dan kita ubah sedikit `ansible.cofg` nya. lalu kita buat script untuk memembuat user, menginstall docker beserta isinya, menginstall node js versi 14, menginstall node explorer, menginstal prometheus dan grafana. Disini saya menamainya start-appserver.yml
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(326).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(325).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(326).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(354).png?raw=true)
```
nano ansible.cfg
```
```
[defaults]
inventory = ../Penyimpanan
private_key_file = ~/.ssh/id_rsa
host_key_checking = false
interpreter_python = auto_silent
```
```
nano start-appserver
```
```
- become: true
  gather_facts: false
  hosts: all
  vars:
   - username: "zulfikar-dw"
   - password: "$5$RbO0oZtzarUWa/Xu$IkaEu4g6uAWb9/F8Fa4gP6zkwiuBgQgOU3QkSuCXV/5" #Alfhabet14

  tasks:
    - name: "Creating User"
      ansible.builtin.user:
        groups: sudo
        name: "{{username}}"
        password: "{{password}}"

- become: true
  gather_facts: true
  hosts: all

  vars:
    registry_docker_username: "zulfikar-dw"

  tasks:
    - name: Update apt module
      apt:
        update_cache: true
    - name: Install ca-cert, curl, gnupg
      apt:
        name:
          - ca-certificates
          - curl
          - gnupg
    - name: Install GPG key
      apt_key:
        url: "https://download.docker.com/linux/ubuntu/gpg"
    - name: Install docker repository
      apt_repository:
        repo: "deb https://download.docker.com/linux/ubuntu focal stable"
    - name: update apt module
      apt:
        update_cache: true
    - name: Install docker engine
      apt:
        update_cache: true
        name:
          - docker-ce
          - docker-ce-cli
          - containerd.io
          - docker-buildx-plugin
          - docker-compose-plugin

    - name: Create group "docker"
      group:
        name: "docker"
        state: present

    - name: Create user "{{ registry_docker_username }}"
      user:
        name: "{{ registry_docker_username }}"
        state: present
        createhome: yes
        group: "{{ registry_docker_username }}"

    - name: Add user "{{ registry_docker_username }}" to group "docker"
      user:
        name: "{{ registry_docker_username }}"
        group: docker
        append: yes

- name: Deploy Node Exporter with Docker
  hosts: all
  become: true
  tasks:
    - name: Pull the bitnami/node-exporter Docker image
      docker_image:
        name: bitnami/node-exporter
        source: pull

    - name: Run the Node Exporter container
      docker_container:
        name: node-exp
        image: bitnami/node-exporter
        state: started
        restart_policy: unless-stopped
        published_ports:
          - "9100:9100"

- name: Deploy Monitoring
  hosts: appserver
  become: true
  tasks:
    - name: Pull bitnami/prometheus Docker image
      docker_image:
        name: bitnami/prometheus
        source: pull

    - name: Pull grafana/grafana Docker image
      docker_image:
        name: grafana/grafana
        source: pull

    - name: Create directories for Prometheus
      file:
        path: /home/zulfikar-dw/prometheus/
        state: directory
        mode: "0755"

    - name: Copy prometheus.yml configuration file
      copy:
        src: ~/ansible/resource/prometheus.yml
        dest: /home/zulfikar-dw/prometheus/prometheus.yml
        mode: "0644"

    - name: Run Prometheus container
      docker_container:
        name: prom
        image: bitnami/prometheus
        state: started
        restart_policy: unless-stopped
        published_ports:
          - "9090:9090"
        volumes:
          - "/home/zulfikar-dw/prometheus/prometheus.yml:/opt/bitnami/prometheus/conf/prometheus.yml"

    - name: Run Grafana container
      docker_container:
        name: dashboard
        image: grafana/grafana
        state: started
        restart_policy: always
        ports:
          - "1404:3000"
```
##### Sebelum memulai script `start-appserver.yml` kita install dulu yang bernama whois. whois ini berguna untuk membuat password yang dimana sebelum masuk ke dalam user kita bisa menaruh password yang sudah kita buat di whois tersebut.jika sudah maka kita tambahkan ke dalam script `start-appserver.yml` .
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(327).png?raw=true)
```
sudo apt-get install whois
```
```
mkpasswd --method=sha-256
```
##### Disini juga sebelum memulai start-appserver.yml kita buat dulu ssh keygen dan copy ke dalam VM appserver dan gateway nya.
```
ssh keygen
```
```
cat .ssh/id_rsa.pub
```
```
nano .ssh/authorized_keys
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(330).png?raw=true)
## 2. ansible gateway
##### Disni kita keluar dulu dari file appserver dan masuk ke dalam file gateway. Didalam file gateway kita lakukan hal yang sama dengan appserver tadi. Mengubah sedikit `ansible.confg` dan membuat script gatewaynya. disini saya menamainya `start-gateway.yml`. di script gateway ini isinya yaitu menginstall nginx, menginstall certbot untuk memberikan SSL sertifikat website, membuat directory user, mengcopy file docker-compose, mengclone github wayshub-frontend, mengcopy Dockerfile dan menjalankan docker compose nya.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(325).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(336).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(335).png?raw=true)
```
nano ansible.cfg
```
```
[defaults]
inventory = ../Penyimpanan
private_key_file = ~/.ssh/id_rsa
host_key_checking = false
interpreter_python = auto_silent
```
```
nano start-gateway.yml
```
```
- name: Update nginx
  hosts: gateway
  tasks:
    - name: Ensure nginx is at the latest version
      apt:
        name: nginx
        state: latest
        update_cache: yes
      become: yes

    - name: Start nginx
      service:
        name: nginx
        state: started
      become: yes

    - name: Copy the nginx config wayshub
      copy:
        src: ~/ansible/resource/rproxy.conf
        dest: /etc/nginx/sites-enabled/rproxy.conf
      become: yes

    - name: Restart nginx
      service:
        name: nginx
        state: restarted
      become: yes

- name: Install Certbot with Snap
  hosts: gateway
  tasks:
    - name: Install Certbot with Snap
      snap:
        name: certbot
        classic: yes
        state: present
      become: yes

    - name: Obtain and install SSL certificate with Certbot Nginx wayshub
      command: certbot --nginx -d zulfi.studentdumbways.my.id --non-interactive --agree-tos --email zulfikar.alfain1@gmail.com
      become: yes

    - name: Obtain and install SSL certificate with Certbot Nginx prometheus
      command: certbot --nginx -d pro.zulfi.studentdumbways.my.id --non-interactive --agree-tos --email zulfikar.alfain1@gmail.com
      become: yes

    - name: Obtain and install SSL certificate with Certbot Nginx prometheus
      command: certbot --nginx -d gra.zulfi.studentdumbways.my.id --non-interactive --agree-tos --email zulfikar.alfain1@gmail.com
      become: yes

    - name: Obtain and install SSL certificate with Certbot Nginx prometheus
      command: certbot --nginx -d nodaap.zulfi.studentdumbways.my.id --non-interactive --agree-tos --email zulfikar.alfain1@gmail.com
      become: yes

    - name: Obtain and install SSL certificate with Certbot Nginx prometheus
      command: certbot --nginx -d nodgate.zulfi.studentdumbways.my.id --non-interactive --agree-tos --email zulfikar.alfain1@gmail.com
      become: yes

    - name: Restart nginx
      service:
        name: nginx
        state: restarted
      become: yes

- name: Copy Docker Compose and Clone Git repository
  hosts: gateway
  become: true
  gather_facts: false
  tasks:
    - name: Create directory /home/zulfikar-dw if it doesn't exist
      file:
        path: /home/zulfikar-dw
        state: directory
        owner: zulfikar-dw
        group: zulfikar-dw
        mode: '0755'
      become: yes

    - name: Copy file docker-compose
      copy:
        src: ~/ansible/resource/docker-compose.yml
        dest: /home/zulfikar-dw/docker-compose.yml
        owner: zulfikar-dw
        group: zulfikar-dw

    - name: Clone Git repository
      git:
        repo: https://github.com/dumbwaysdev/wayshub-frontend
        dest: /home/zulfikar-dw/wayshub-frontend
        clone: yes
        force: yes

    - name: Copy file Dockerfile
      copy:
        src: ~/ansible/resource/Dockerfile
        dest: /home/zulfikar-dw/wayshub-frontend
        owner: zulfikar-dw
        group: zulfikar-dw

    - name: Run docker-compose
      command: docker compose up -d
      args:
        chdir: /home/zulfikar-dw/wayshub-frontend
```
## 3. ansible resource
##### Sebelum menjalankan ansible-playbook appserver dan gateway. kita masuk dulu ke file resource. Disini kita ubah `ansible.cofg` sama seperti appserver dan gateway. jika sudah kita buat `Dockerfile`, `docker-compose.yml`, `prometheus.yml` dan `rproxy.conf`
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(356).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(357).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(328).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(337).png?raw=true)
```
nano ansible.cfg
```
```
[defaults]
inventory = ../Penyimpanan
private_key_file = ~/.ssh/id_rsa
host_key_checking = false
interpreter_python = auto_silent
```
```
nano Dockerfile
```
```
FROM node:14.21.3-alpine
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD [ "npm", "start"]
```
```
nano docker-compose.yml
```
```
version: "3.7"
services:

  frontend:
    build: ./wayshub-frontend
    container_name: wayshub-fe
    stdin_open: true
    ports:
      - 3000:3000
```
```
nano prometheus.yml
```
```
scrape_configs:
- job_name: grafana
  scrape_interval: 5s
  static_configs:
  - targets:
    - nodaap.zulfi.studentdumbways.my.id:9100
    - nodgate.zulfi.studentdumbways.my.id:9100
```
```
nano rproxy.conf
```
```
server {
    server_name nodapp.zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.175.221.150:9100;
    }
}
server {
    server_name nodgate.zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.150.93.47:9100;
    }
}
server {
    server_name pro.zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.175.221.150:9090;
    }
}

server {
    server_name gra.zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.175.221.150:1404;
              proxy_set_header Host $host;
              proxy_set_header X-Real-IP $remote_addr;
              proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
              proxy_set_header X-Forwarded-Proto $scheme;
    }
}


server {
    server_name zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.150.93.47:3000;
    }
}
```
##### Jika sudah semua kita bisa memulaikan ansible playbook appserver dan gateway nya.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(331).png?raw=true)
```
ansible-playbook start-appserver.yml
```
```
ansible-playbook start-gateway.yml
```
##### Jika berhasil maka tampilannya akan seperti ini.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(358).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(347).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(348).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(349).png?raw=true)