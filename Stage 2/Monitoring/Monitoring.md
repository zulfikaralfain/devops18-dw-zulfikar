# STAGE 2
# Monitoring

##### Langkah awal yaitu kta masuk kedalam grafana
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(358).png?raw=true)
##### Jika sudah kita buat dulu data source nya. caranya tekan garis tiga, lalu pilih connection, lalu pilih data source..
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(338).png?raw=true)
Disini kita isi dengan url prometheusnya bisa dengan domain yang telah kita buat, atau bisa menggunakan alamat IP. Disini saya menggunakan domain yang telah saya buat. 
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(339).png?raw=true)
disini ada bagian perfomance. kalian pilih yang prometheus lalu untuk versinya yaitu 2.47.x atau kalian bisa melihatnya di prometheus yang telah kalian buat.
###### Selanjutnya, jika sudah membuat data source. Kita buat contact points. Fungsi dari contact points adalah grafana akan mengirimkan notifikasi yang berisikan CPU dan RAM yang kita atur di grafana tersebut.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(341).png?raw=true)
Disini contact points saya menggunakan discord. jika belum ada webhook maka kalian bisa membuat webhook di discord tersebut.
##### Jika sudah membuat contact points, langkah selanjutnya adalah membuat alert rules. fungsi dari alert rules adalah memonitor dan memberikan pemberitahuan (alert) ketika data time series yang diukur melintasi ambang batas tertentu atau memenuhi kondisi tertentu. Disini saya akan membuat alert CPU yang tidak boleh lebih dari 20%
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(342).png?raw=true)
Disini nomor 1 saya namai CPU 20%, untuk nomor 2 yaitu kita pilih prometheus dan masukan promql kita untuk CPU nya.
```
100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle",job="grafana"}[5m])) * 100)
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(343).png?raw=true)
untuk expressions nya kita atur treshold nya menjadi 20. Karna CPU kita tidak boleh lebih dari 20%
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(344).png?raw=true)
Disini kita buat folder baru dan dibawah ada tulisan 1m. ini menandakan waktu dimana nanti akan selalu mengirim pemberitahuan dalam 1 menit.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(345).png?raw=true)
Disini kalian bebas mau isikan apa saja.
##### Selanjutnya kita buat juga alert rules untuk RAM nya. Disini saya akan membuat RAM 75%.
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(350).png?raw=true)
Disini kita buat rule name nya bernama RAM 75%. Untuk define quary nya kita set ke prometheus lalu kita masukan promql untuk RAM nya.
```
100 * (1 - ((avg_over_time(node_memory_MemFree_bytes[10m]) + avg_over_time(node_memory_Buffers_bytes[10m])) / avg_over_time(node_memory_MemTotal_bytes[10m])))
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(351).png?raw=true)
Lalu untuk expressions nya bagian treshold kita ubah is above nya menjadi 75
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(352).png?raw=true)
Disni kita buat folder yang sama dengan CPU yang telah kita buat tadi. 
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(353).png?raw=true)
Disini kalian bebas ingin menamai apapun.
##### Jika sudah dibuat maka folder yang baru kita buat di alert rules. akan ada di Dashboard kita. disini kita bisa membuat visualization untuk CPU dan RAM yang telah kita buat tadi. .
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(360).png?raw=true)
Disini kita click create dashboard
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(361).png?raw=true)
Lalu klik add visualization
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(362).png?raw=true)
Lalu pilih data source nya yaitu prometheus dan masukan promql nya lalu klik apply.
```
100 - (avg by(instance)(irate(node_cpu_seconds_total{mode="idle",job="grafana"}[5m])) * 100)
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(363).png?raw=true)
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(364).png?raw=true)
Untuk RAM kita lakukan hal yang sama pada CPU klik create dashboard
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(365).png?raw=true)
Klik add visualization
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(366).png?raw=true)
Lalu pilih data source prometheus dan masukan promql nya. disini kalian bisa mengubah mau grafik atau angka. caranya klik gauge, maka ada banyak fitur yang bisa ditampilkan di dashboard kalian. Lalu klik apply
```
100 * (1 - ((avg_over_time(node_memory_MemFree_bytes[10m]) + avg_over_time(node_memory_Buffers_bytes[10m])) / avg_over_time(node_memory_MemTotal_bytes[10m])))
```
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(367).png?raw=true)