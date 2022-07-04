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
scp -r .ssh app@103.55.37.194:/home/app
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





















