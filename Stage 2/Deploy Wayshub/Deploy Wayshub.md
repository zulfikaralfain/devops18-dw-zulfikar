# STAGE 2
# Deploy Wasyshub

##### Clone dulu wayshub-frontend dan wayshub-backend nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(215).png?raw=true)
```
git clone https://github.com/dumbwaysdev/wayshub-frontend.git
```
```
git clone https://github.com/dumbwaysdev/wayshub-backend.git
```
##### Install nodejs dan masukan versi 14
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(217).png?raw=true)
```
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```
```
exec bash
```
```
nvm install 14
```
##### Jika sudah install PM2
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(218).png?raw=true)
```
npm install -g pm2
```
##### Setelah itu deploy wayshub-frontend nya menggunakan PM2
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(219).png?raw=true)
```
cd wayshub-frontend
```
```
npm install
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(222).png?raw=true)
```
pm2 ecosystem simple
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(223).png?raw=true)
```
nano ecosystem.config.js
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(224).png?raw=true)
```
module.exports = {
  apps : [{
    name   : "frontend",
    script : "npm start"
  }]
}
```
##### Jika sudah jangan lupa di wayshub-frontend kita masuk ke *src*/*config*/*api.js* untuk mengubah BaseURL dengan domain yang sudah disediakan (api...studentdumbways.my.id)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(221).png?raw=true)
```
cd src
```
```
cd config
```
```
nano api.js
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(220).png?raw=true)
pada bagian *baseurl* kita ubah dengan domain masing-masing
##### Di wayshub-backend kita lakukan hal yang serupa dengan wayshub-frontend, akan tetapi bedanya di wayshub-backend kita masuk ke *config*/*nano config.json*
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(225).png?raw=true)
```
npm install && pm2 ecosystem system
```
```Note : Perintah && digunakan untuk menjalankan dua perintah atau lebih secara berurutan jika perintah sebelumnya berhasil```
```
nano ecosystem.config.js
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(226).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(227).png?raw=true)
```
module.exports = {
  apps : [{
    name   : "backend",
    script : "npm start"
  }]
}
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(228).png?raw=true)
```
cd config
```
```
nano config.json
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(228).png?raw=true)
Disini kita ubah username dan password yang sudah kita buat dan berikan database nya

### Installasi dan konfigurasi database MySQL
karna kita menggunakan IDCH (IDCloudeHost), sebelum ingin mengupdate apt ada hal yang perlu kita ubah
```
sudo nano /etc/apt/sources.list
```
jika sudah maka tampilannya akan seperti ini dan tekan *ctrl \*
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(254).png?raw=true)
Disini kita ketik
```
mirrors.idcloudhost.com
```
lalu kita tekan enter
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(255).png?raw=true)
dan kita ubah menjadi
```
arcive.ubuntu.com
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(256).png?raw=true)
dan tekan *A* untuk ubah semuanya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(257).png?raw=true)
Jika sudah save lalu keluar
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(258).png?raw=true)
##### Kita Update apt nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(231).png?raw=true)
```
sudo apt update
```
##### Jika sudah kita install MySQL nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(233).png?raw=true)
```
sudo apt install mysql-server
```
##### Jika sudah kita pasang MySQL Secure Installation
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(234).png?raw=true)
```
sudo apt mysql_secure_installation
```
##### Jika sudah masuk ke dalam root MySQl dan masukkan password yang kita inginkan
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(235).png?raw=true)
```
sudo mysql -u root -p
```
##### Kita buat user kita di MySQL dan kita berikan hak akses penuh
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(236).png?raw=true)
```
CREATE USER 'zulfi'@'%' IDENTIFIED BY 'Alfhabet14'
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(237).png?raw=true)
```
GRANT ALL PRIVILEGES ON *.* TO 'zulfi'@'%' WITH GRANT OPTION
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(238).png?raw=true)
```
FLUSH PRIVILEGES
```
### Jika sudah kita gabungkan konfigurasi database yang sudah tadi kita buat
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(239).png?raw=true)
```
cd wayshub-backend && npm install -g sequelize-cli
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(240).png?raw=true)
```
sequelize db:create && sequelize db:migrate
```
##### Sekarang kita jalangkan ulang aplikasinya menggunakan PM2
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(259).png?raw=true)
```
pm2 restart all
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(244).png?raw=true)
Kita coba register pada frontend wayshub
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(245).png?raw=true)
Jika berhasil maka hasilnya seperti ini
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(247).png?raw=true)