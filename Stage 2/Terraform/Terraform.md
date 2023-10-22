# STAGE 2
# Terraform

##### Install terraform di server masing-masing
[https://developer.hashicorp.com/terraform/tutorials/docker-get-started/install-cli]
##### Install docker pada server masing-masing
[https://docs.docker.com/engine/install/ubuntu/]
##### Buat file main.tf pada direktori terraform
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(372).png?raw=true)
```
terraform {
  required_providers {
    docker = {
      source  = "kreuzwerker/docker"
      version = "~> 2.15.0"
    }
  }
}

provider "docker" {}

resource "docker_image" "nginx" {
  name         = "aimingds/wayshub-fe:latest"
  keep_locally = false
}

resource "docker_container" "nginx" {
  image = docker_image.nginx.latest
  name  = "frontend"
  ports {
    internal = 3000
    external = 8080
  }
}
```
##### Jika sudah kita jalankan terraformnya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(373).png?raw=true)
```
terraform init
```
```
terraform apply
```
##### Kita buka aplikasinya menggunakan ip masing-masing
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(375).png?raw=true)
```
103.175.221.150:8080/login
```
##### Cek docker images yang telah kita buat
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(374).png?raw=true)
```
docker images
```
```
docker ps -a
```
##### Masuk kembali ke file terraform dan kita hapus file wayshubnya
![alt text](https://github.com/zulfikaralfain/devops18-dw-zulfikar/blob/assets/Screenshot%20(376).png?raw=true)
```
terraform destroy
```