<h1>SSH</h1>

Membuat server backend dan database menggunakan IdcloudHost

Generate SSH key transfer ke semua server

<img width="516" alt="Screen Shot 2022-06-10 at 08 44 56" src="https://user-images.githubusercontent.com/62433171/172973507-135c787e-6101-4690-a283-88e18d8ec806.png">

<img width="781" alt="Screen Shot 2022-06-10 at 08 48 16" src="https://user-images.githubusercontent.com/62433171/172973860-73c09718-0b9d-4892-ba00-f71cfe959d80.png">

<img width="782" alt="Screen Shot 2022-06-10 at 08 49 48" src="https://user-images.githubusercontent.com/62433171/172974019-320979b7-7432-4f7a-a396-246c57a0b6a5.png">

<img width="349" alt="Screen Shot 2022-06-10 at 08 48 53" src="https://user-images.githubusercontent.com/62433171/172973935-71523fce-e247-4f5d-8fdd-025dcce7a6d0.png">

<h1>Set up Database</h1>

Menginstal mysql-server pada server Database

<img width="244" alt="Screen Shot 2022-06-10 at 08 52 03" src="https://user-images.githubusercontent.com/62433171/172974290-af66c3ed-e6f8-48a5-8caa-ca7e6a572697.png">

Membuat user baru untuk database

Membuat Database baru pada user baru

Mengganti Bind address

<img width="788" alt="Screen Shot 2022-06-10 at 08 57 36" src="https://user-images.githubusercontent.com/62433171/172974872-ef2749e1-588e-4b9b-b2ca-c43ea0ef2529.png">

Dapat remote database dari client


<h1>Deployment</h1>

clone git https://github.com/dumbwaysdev/wayshub-backend.git

clone nvm dan install curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash

Mengubah direktori menjadi backend dan deploy aplikasi menggunakan PM2

Mengkoneksikan aplikasi frontend dan backend



<h1>Domain</h1>

Menggunakan domain dari Cloudflare

<h1>Gateway</h1>

Update system

Membuat Reverse Proxy backend

<h1>SSL Configuration</h1>

Pastikan aplikasi dapat diakses menggunakan https://
