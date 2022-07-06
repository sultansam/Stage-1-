# MONITORING DAN ANSIBLE

Disini saya akan melakukan tugas 2 hari untuk menjadi 1 tugas di karenakan kita akan melakukan mendeploy monitoring dengan menggunakan ansible, yang dimana tugas hari pertama adalah tentang Monitoring dan ansible.

1. Kita harus persiapkan server untuk app , monitoring dan yang lebih penting adalah kita harus memiliki ansible di dalam terminal lokal kita, jadi kalau belum ada kita harus menginstall nya dulu.

## Install Ansible untuk MacOS

disini saya akan menjelaskan cara mengisntall ansible untuk MacOS, yang pertama kita harus pastikan sudah memiliki HomeBrew di dalam lokal kita dimana Homebrew merupakan sebuah package manager untuk iOS, yang dapat diinstall dengan mudah melalui terminal. bagi yang belum ada HomeBrew lakukan step install homebrew terlebih dahulu.

Install Homebrew 
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Jika sudah kita lanjutkan untuk menginstall Ansible nya.

Install Ansible
```
brew install ansible
```

jika sudah sudah selesai cek ansible di dalam lokal terminal kita
```
ansible --version
```
cukup mudah bukan menginstall ansible di dalam MacOS

<img width="894" alt="Screen Shot 2022-07-04 at 09 51 24" src="https://user-images.githubusercontent.com/62433171/177073186-16d9b5b3-66f4-41fc-a342-c85084ea893f.png">

## Bagikan ssh-key lokal ke server

Biasanya ssh key lokal itu sudah ada jadi kita langsung melakukan pengiriman ssh ke server, sebelumnya copy id_rsa.pub ke dalam dirktory authorized_keys

<img width="1276" alt="Screen Shot 2022-07-04 at 10 06 18" src="https://user-images.githubusercontent.com/62433171/177074558-6dce6c63-e253-4985-94fa-18b921ec5005.png">
dan lakukan yang sama terhadap semua server yang kita kirim ssh nya agar saling terhubung.

peintah mengirim ssh
```
scp -r .ssh app@103.55.37.194:/home/sultan
```

jika sudah copy id_rsa ke dalam lokal dan gunakan namafile.pem lalu lakukan periintah di bawah agar file nya hanya bisa di baca.

```
chmod 400 namafile.pem
```

## Set-up Ansible

kita buat dahulu direktori baru yang dimana nanti untuk kedepannya kita akan melakukan proses ansible di dalam direktori ini, untuk menyimpan file-file yang berekstensi .yml dan konfigurasi lainnya.

```
mkdir [nama file]
```

pertama sebelum kita melakukan setup ansible akan lebih baik kita membuat catatan keperluan script ansible apa saja yang di perlukan.

kita buat dahalu daftar file dengan nama "Inventory" yang dimana berisi kumpulan-kumpulan ip server yang akan kita gunakan untk setup ansiblenya.

<img width="507" alt="Screen Shot 2022-07-04 at 10 23 21" src="https://user-images.githubusercontent.com/62433171/177076077-4701a399-06e8-4d9f-8e6d-0eb3165aea15.png">

lalu kita copy id_rsa lokal dan buat file berekstensi .pem

dan kita akan melakukan konfigursi antara server dan ssh di dalam file ansible.cfg, jadikita buat file ansible.cfg

<img width="498" alt="Screen Shot 2022-07-04 at 10 25 46" src="https://user-images.githubusercontent.com/62433171/177076301-a6660f3b-3db4-488d-b665-4a77e267aa4b.png">

lalu jika sudah kita cek koneksi dengan menggunakan
```
ansible all -m ping
```
<img width="460" alt="Screen Shot 2022-07-06 at 14 27 24" src="https://user-images.githubusercontent.com/62433171/177494018-3bb2d204-541f-474b-9c19-faee2329a167.png">

## Nginx

ketika sudah berhasil tersambung, lalu kita install nginx ke semua server menggunakan ansible.

untuk mengecek apakah file .yml kita sudah benar atau belum, bisa di cek dengan.
```
ansible-playbook --syntax-check [nama file .yml]
```

```
- hosts: app
  become: yes
  gather_facts: yes
  tasks:
       - name: install nginx
         apt:
            name:
              - nginx
            state: latest
```
<img width="915" alt="Screen Shot 2022-07-06 at 14 31 54" src="https://user-images.githubusercontent.com/62433171/177494876-48212819-3f80-4f44-a488-061f7a52bd7c.png">

dan jika sudah berhasil, maka di dalam server kita sudah ada file nginx nya dan ketika di akses ip dengan port nginx(80) akan muncul seperti di bawah ini.


<img width="888" alt="Screen Shot 2022-07-06 at 14 37 45" src="https://user-images.githubusercontent.com/62433171/177496058-ce1e87be-e510-4672-a89a-f182c22034c7.png">

<img width="1280" alt="Screen Shot 2022-07-06 at 14 40 49" src="https://user-images.githubusercontent.com/62433171/177496610-877d759a-0b73-466c-836a-5994af7a3a5a.png">

## Docker

install docker: kita harus persiapkan file untuk ansible docker yang berkstensi .yml di dalam file ansible, agar kita bisa proses ansible-playbook nya.

```
- hosts: app
  become: yes
  gather_facts: yes
  tasks:
       - name: update
         apt:
          update_cache: yes

       - name: upgrade
         apt:
          upgrade: dist

       - name: docker dependencies
         apt:
          name: "{{ packages }}"
          state: present
          update_cache: yes
         vars:
          packages:
           - apt-transport-https
           - ca-certificates
           - curl
           - software-properties-common
           - gnupg-agent

       - name: add docker gpg key
         apt_key:
            url: https://download.docker.com/linux/ubuntu/gpg
            state: present

       - name: add repo docker
         apt_repository:
           repo: deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable
           state: present

       - name: install docker
         apt:
           name: "{{ packages }}"
           state: present
           update_cache: yes
         vars:
          packages:
           - docker-ce
           - docker-ce-cli 
           - containerd.io

       - name: docker-compose
         get_url:
             url : https://github.com/docker/compose/releases/download/v2.6.0/docker-compose-linux-x86_64
             dest: ~/docker-compose
             mode: '+x'

       - name: Check docker-compose exists
         stat: path=~/docker-compose
         register: docker_compose

       - name: Move docker-compose to /usr/local/bin/docker-compose
         command: mv ~/docker-compose /usr/local/bin/docker-compose
         when: docker_compose.stat.exists

       - name: Move Sudo
         shell: sudo usermod -aG docker sultan
```

<img width="1039" alt="Screen Shot 2022-07-06 at 14 57 23" src="https://user-images.githubusercontent.com/62433171/177499907-68efa2a2-c7fb-4ab5-a6bd-40d488d588ee.png">


dan jika berhasil maka akan menampilkan hal-hal seperti ini.

<img width="501" alt="Screen Shot 2022-07-06 at 14 56 17" src="https://user-images.githubusercontent.com/62433171/177499699-a8f20909-2e5a-476e-9d1a-4a4aa732c1e5.png">

<img width="336" alt="Screen Shot 2022-07-06 at 14 57 02" src="https://user-images.githubusercontent.com/62433171/177499837-5ea6bbbe-8bab-447b-aa36-c60b56f20246.png">

jika sudah ada nginx dan docker kita install untuk monitoringnya.

## Monitoring (node, prometheus, grafana)

buat file ansible node exporter

docker-compose-node_exporter.yml

```
version: '3'

services:
  node_exporter:
    image: prom/node-exporter:latest
    container_name: node_exporter
    ports:
      - 9100:9100
    command:
      - '--path.procfs=/host/proc'
      - '--path.rootfs=/rootfs'
      - '--path.sysfs=/host/sys'
      - '--collector.filesystem.mount-points-exclude=^/(sys|proc|dev|host|etc)($$|/)'
    restart: unless-stopped
    volumes:
      - /proc:/host/proc:ro
      - /sys:/host/sys:ro
      - /:/rootfs:roca
```


node-exporter.yml 

```
- hosts: app
   become: yes
   gather_facts: yes
   tasks:

     - name: copying docker compose file
       copy:
        src: docker-compose-node_exporter.yml
        dest: /home/sultan/docker-files/

     - name: run docker compose
       shell: docker-compose -f docker-files/docker-compose-node_exporter.yml up -d
```

<img width="911" alt="Screen Shot 2022-07-06 at 15 14 53" src="https://user-images.githubusercontent.com/62433171/177503335-98b69618-f2a9-4bd7-9745-256749e43126.png">


<img width="1280" alt="Screen Shot 2022-07-06 at 15 15 03" src="https://user-images.githubusercontent.com/62433171/177503374-52bc1e24-c136-4e19-9db8-2f833c77f2b9.png">


Lalu di dalam server yang menandakan node exporter kita sudah berhasil dan berjalan kita bisa cek images dan container nya.

<img width="1118" alt="Screen Shot 2022-07-06 at 15 17 01" src="https://user-images.githubusercontent.com/62433171/177503804-f416c201-27cc-48fe-bce8-17824993f5e8.png">


Install Prometheus dan grafana

prometheus.yml

```
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "prometheus-metrics"
    scrape_interval: 15s
    static_configs:
      - targets: ['103.189.234.27:9090']
  - job_name: "node_exporter_metrics"
    scrape_interval: 15s
    static_configs:
      - targets: ['103.189.234.27:9100']
```
      
      
























