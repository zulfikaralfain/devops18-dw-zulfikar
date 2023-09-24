# STAGE 2
# Gateway Nginx

##### Kita install Nginx nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(241).png?raw=true)
```
sudo apt install nginx
```
##### Cek status Nginx nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(260).png?raw=true)
```
sudo systemctl status nginx
```
##### Jika sudah kita masuk ke dalam Nginx dan kita buat config di dalam Nginx nya
```
/etc/nginx/
```
```
sudo mkdir dumbways
```
```
cd dumbways
```
```
sudo nano rproxy.conf
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(242).png?raw=true)
Lalu kita isi *rproxy.conf* menggunakan domain yang kita buat
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(243).png?raw=true)
```
server {
    server_name zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.13.207.106:3000;
    }
}

server {
    server_name api.zulfi.studentdumbways.my.id;

    location / {
             proxy_pass http://103.13.207.106:5000;
    }
}
```
##### Konfirmasi file config yang telah kita buat dan reload kembali Nginx nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(261).png?raw=true)
```
sudo nginx -t
```
##### buka domain yang telah kita buat di browser
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(244).png?raw=true)
```
http://zulfi.studentdumbways.my.id/login
```
### SSL menggunakan certbot (http:// menjadi https://)
##### Sebelum install certbot, kita harus install snapd terlebih dahulu
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(250).png?raw=true)
```
sudo apt install snapd
```
##### Jika sudah kita install certbotnya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(248).png?raw=true)
```
sudo snap install --classic certbot
```
##### Sesudah itu kita buat directory untuk certbot nya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(249).png?raw=true)
```
sudo ln -s /snap/bin/certbort /usr/bin/certbon
```
### Jika sudah kita lisensikan domain yang telah kita buat sesuai dengan perintah
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(251).png?raw=true)
```
sudo certbot --nginx
```
##### Jika sudah kita jalankan certbotnya yang telah kita buat
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(253).png?raw=true)
```
sudo certbot renew --dry-run
```
##### Jika sudah jalankan kembali domain dengan https
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(252).png?raw=true)
```
https://zulfi.studentdumbways.my.id
```