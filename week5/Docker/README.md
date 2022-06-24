#SET UP FE,BE DAN DATABASE ON TOP DOCKER

Hal yang harus di persiapkan untuk pertama kali adalah **SERVER** untuk aplikasi kita, saya menggunakan IDCLOUDHOST untuk membuat servernya.


1. Saya membuat 2 buah server:
    1. untuk Nginx
    2. untuk Aplikasi (FE,BE dan DATABASE)

<img width="1280" alt="Screen Shot 2022-06-24 at 20 36 52" src="https://user-images.githubusercontent.com/62433171/175547176-17581f71-c578-4e16-b0c8-60caa94799cd.png">


2. Ketika server sudah siap kita instal docker di dalam server aplikasi agar aplikasi bisa berjalan on top docker. Didalam idcloudhost itu kita bisa memilih server yang sudah ada dockernya,
tetapi kita bisa memilih server yang sudah ada docker nya juga, jadi kalo kita sudah memilih server yang sudah ada docker nya, kita tidak perlu untuk install dockernya.
    
    -install docker menggunakan bash ini.

```
sudo apt-get update
sudo apt-get install \
ca-certificates \
curl \
gnupg \
lsb-release
    
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

cek versi docker

```
docker -v
```


```
docker version
```
<img width="927" alt="Screen Shot 2022-06-24 at 20 49 36" src="https://user-images.githubusercontent.com/62433171/175549523-23e4756d-050a-4db0-ad79-899c18101026.png">


3. Kita membuat server tidak menggunakan sudo lagi

```
sudo usermod -aG docker app
```

4. Karena docker ini memiliki hub nya sendiri untuk mengepull imagenya kita diwajibkan untuk membuat akun docker hub di sini https://hub.docker.com/ 

   disini saya masuk dengan meggunakan bash "docker Login" lalu saya masuk ke akun docker hub saya melalui server

<img width="922" alt="Screen Shot 2022-06-24 at 20 58 16" src="https://user-images.githubusercontent.com/62433171/175551148-58dd4749-3b21-4d39-9994-3ae71c60be6d.png">

5. sekarang kita akan mengepull beberapa images seperti nginx dan mysql 

pull images nginx
```
docker pull nginx:stable-alpine
```

pull images mysql
```
docker pull mysql:latest
```

<img width="586" alt="Screen Shot 2022-06-24 at 21 09 36" src="https://user-images.githubusercontent.com/62433171/175553208-dd5070bc-36a8-41fe-9aff-294f67ad204f.png">


untuk melihat images yang sudah kita pull tadi berhasil atau ngak
```
docker images
```

docker ps adalah perintah untuk melihat container kita yang lagi berjalan, lihat di foto saya menjalankan docker ps tetapi kosong, dikarenakan saya belum menjalankan/membuat container dari images yang kita pull tadi.
docker run -d --name mysqldata -p 3306:3306 -v /home/aplikasi/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pempek12 -e MYSQL_DATABASE=wayshub mysql:latest

<img width="483" alt="Screen Shot 2022-06-24 at 21 05 14" src="https://user-images.githubusercontent.com/62433171/175552353-8c01ce0f-b425-4845-afc5-b9bb74d9bd9a.png">


6. images mysql sudah dibuat kita buat container nya dengan cara 

```
docker run -d --name mysqldata -p 3306:3306 -v /home/aplikasi/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=Pempek12 -e MYSQL_DATABASE=wayshub mysql:latest
```

  disini kita membuat container mysql untuk database nya dengan nama mysqldata dengan port di 3306:3306 lalu kita buat volume nya yang berada di /home/aplikasi/mysql , kita juga membuat password untuk rootnya dan membuat nama database

ini hasil dari pembutan docker containernya 

<img width="1165" alt="Screen Shot 2022-06-24 at 21 16 46" src="https://user-images.githubusercontent.com/62433171/175554523-75fa99e5-afe7-457b-ab37-c02304e6ad95.png">

7. clone aplikasi backend

```
git clone https://github.com/dumbwaysdev/wayshub-backend.git
```

jika sudah berhasil di clone maka akan tampil seperti ini

<img width="616" alt="Screen Shot 2022-06-24 at 21 20 21" src="https://user-images.githubusercontent.com/62433171/175555203-58f451e5-e917-436a-b26e-68cf9fe54770.png">

8. kita masuk ke dalam container nya apakah database yang sudah kita buat tadi sudah ada atau belum

```
docker container exec -it mysqldata bash
```

ketika berhasil masuk kita jalankan perintah dibawah dan masukkan password yang sudah kita buat tadi 
```
mysql -u root -p
```
kalau sudah masuk lakukan tahapan pertama pembuatan user nya
```
CREATE USER 'sultan'@'%' IDENTIFIED BY 'Pempek12';
``` 
```
GRANT ALL PRIVILEGES ON *.* TO 'sultan'@'%';
```
```
FLUSH PRIVILEGES;
```

<img width="483" alt="Screen Shot 2022-06-24 at 21 29 19" src="https://user-images.githubusercontent.com/62433171/175556838-0565efca-8d13-4c72-a849-5d17e73aa44b.png">

9. masuk ke database menggunakan user yang sudah dibuat
```
mysql -u sultan -p
```

<img width="781" alt="Screen Shot 2022-06-24 at 21 35 42" src="https://user-images.githubusercontent.com/62433171/175557965-aa199c24-ed87-4bfa-8ae9-657369684ec3.png">


10. . install docker-compose karena kita akan menjalankan 
 
```
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

```
sudo chmod +x /usr/local/bin/docker-compose
```
```
docker compose --version
```




