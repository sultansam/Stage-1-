# DOCKER SWARM

Hal yang harus di persiapkan untuk menjalankan docker swarm adalah beberapa server, disini saya menyiapkan 3(tiga) server . 1(satu) buah server untuk manager nya yang sebagai pusatnya, 2(dua) buah server sebagai worker yang berbagi tugas menjalankan beberapa container yang akan di proses oleh server manager.

Karena ini namanaya docker swarm berarti kita harus menjalankannya on top docker, nah untuk 3 buah server ini saya memilih yang sudah ada dockernya jadi saya tidak perlu untuk menginstall dockernya lagi. Di dalam IDCLOUDHOST kita dapat melakukan beberapa setup agar kita tidak perlu menginstall docker.

baik ketika semua server sudah siap kita lakukan beberapa setup di dalam manager server dahulu.

1. Setup server manager
```
sudo apt update; sudo apt upgrade
```
```
docker -v
```
```
docker-compose -v
```
```
sudo usermod -aG docker (user)
```
```
logout
```
```
ssh user@ip-server
```

2. Melakukan inisiasi server
```
docker swarm init --advertise-addr (ip server)
```

<img width="987" alt="Screen Shot 2022-06-25 at 20 53 29" src="https://user-images.githubusercontent.com/62433171/175776643-004e47df-1373-4c59-b2f0-a3500c3ea106.png">


3. join server manager ke worker

Ketika kita sudah berhasil menginisiasi servernya dan akan mendapatkan Token "docker swarm join" yang berguna untuk kita joinkan/gabungkan antara server manager ke server worker2 yang lain
```
docker swarm join --token SWMTKN-1-44vrmcsirsa04ujow45e2urasntyq41xnqfx8ovilrjt2l9nxi-1iba8vpeuu2js6kxit0p59ekb 103.183.74.57:2377
```

4. hasil jika kita berhasil join maka kita dapat melihatnya dengan 
```
docker node ls
```
<img width="765" alt="Screen Shot 2022-06-25 at 20 56 23" src="https://user-images.githubusercontent.com/62433171/175776752-bc90d972-5234-448f-9ef4-168f64af7ca7.png">


5. Disini kita coba buat dan scale aplikasi sederhana

```
docker service create --replicas 1 --name helloku alpine ping google.com
```
```
docker service scale helloku=5
```

<img width="829" alt="Screen Shot 2022-06-25 at 20 59 28" src="https://user-images.githubusercontent.com/62433171/175776846-16c5beda-6051-451e-809e-295d2315b78d.png">


dan jika sudah berhasil kita cek containernya 
```
docker ps
```
apakah sudah ada yang berjalan 

server manager
<img width="997" alt="Screen Shot 2022-06-25 at 21 00 59" src="https://user-images.githubusercontent.com/62433171/175776898-591456cf-0357-496f-8b46-3d3128e4326f.png">

worker 1
<img width="943" alt="Screen Shot 2022-06-25 at 21 02 08" src="https://user-images.githubusercontent.com/62433171/175776939-1664b685-a4f5-4e2d-ba5c-a38c8ea2c5d6.png">

dan kita lihat di dalam server manager menjalankan helloku.5 dan .1 dan server worker1 helloku.2,  disini saya kehilangan hasil screenshot saya yang dmna worker2 menjalankan helloku.3 dan .4

## Deploy aplikasi 

6. Disini kita akan mendeploy aplikasi dan akan melakukan git clone ke semua server

```
git clone https://github.com/sgnd/dumbways-docker-microservices.git
```

<img width="668" alt="Screen Shot 2022-06-25 at 21 10 52" src="https://user-images.githubusercontent.com/62433171/175777226-b0453740-f92b-4f19-a5db-73fd1390cf84.png">


Karena disini kita git clone dari github orang lain maka kita harus melakukan beberapa konfigurasi di dalam file docker-compose.yml , pada bagian volumes dan images yang awalnya **sgnd dan suganda** menjadi **user pada server kita** . Kita lihat pada gambar di bawah ini sudah saya ganti menjadi sultan.

<img width="1280" alt="Screen Shot 2022-06-25 at 21 14 34" src="https://user-images.githubusercontent.com/62433171/175777387-ec24f286-44ec-4c7e-b1b8-5344b570f129.png">

7. Build apliksi

Kita lakukan build aplikasinya di semua server juga 
```
docker-compose build
```
<img width="566" alt="Screen Shot 2022-06-25 at 21 23 52" src="https://user-images.githubusercontent.com/62433171/175777775-3a9e2ed4-15df-4b05-a83f-5d96be9849d3.png">

8. Hasil dari build

<img width="663" alt="Screen Shot 2022-06-25 at 21 30 43" src="https://user-images.githubusercontent.com/62433171/175778128-627d5d49-5619-45ae-a096-34a316bee2db.png">


9. proses scale
```
docker service scale stack-todo_todo-services=5
```

<img width="727" alt="Screen Shot 2022-06-25 at 21 31 50" src="https://user-images.githubusercontent.com/62433171/175778174-d0d995a4-6d9c-43fa-99e7-1ddb61fa4658.png">

dan hasilnya

<img width="847" alt="Screen Shot 2022-06-25 at 21 33 35" src="https://user-images.githubusercontent.com/62433171/175778218-d94bc066-ef89-4d4b-b204-0049c32e5aa1.png">


karena saya sudah meng scale pada port :4000 kita coba akses dengan (ip:4000)

dan hasilnya

<img width="1280" alt="Screen Shot 2022-06-18 at 01 38 34" src="https://user-images.githubusercontent.com/62433171/175778301-e9a054af-13cf-4b9a-99ab-a8104af08c3c.png">






