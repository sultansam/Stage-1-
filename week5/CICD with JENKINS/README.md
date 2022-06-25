# CI/CD JENKINS ON TOP DOCKER

Karena kita akan melakukan instalasi jenkins on top docker jadi kita harus memiliki server untuk jenkinsya yang sudah ada docker dan docker compose nya.

Persiapkan 1 server tambahan untuk Jenkins nya, kita akan menjalankan jenkins nya untuk aplikasi yang sudah kita buat di dalam [File Docker](https://github.com/sultansam/Stage-1-/tree/main/week5/Docker) 

kita akan menghubungkan nya antara jenkins dan aplikasi kita dengan cara konfigurasi di dalam file JenkinsFile dan pastikan aplikasi kita sudah kita push ke github.

1. Install docker dan docker compose 

```
sudo apt-get update
```
```
sudo mkdir -p /etc/apt/keyrings
```
```
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```
```
sudo apt-get update
```
```
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

2. ketika sudah terinstall kita ubah sudo nya ke user agar ketika kita malakukan perintah tidak perlu menggunakan perintah sudo lagi
```
sudo usermod -aG docker "nama user"
```

3. Masuk ke dockerhub agar kita bisa pull images dari jenkins nya
```
docker login
```
<img width="1070" alt="Screen Shot 2022-06-25 at 17 52 10" src="https://user-images.githubusercontent.com/62433171/175770421-fcd602a3-b11b-4002-b25a-ad062fd935dc.png">

4. Pull images jenkins
```
docker pull jenkis/jenkins:latest
```
<img width="478" alt="Screen Shot 2022-06-25 at 17 58 24" src="https://user-images.githubusercontent.com/62433171/175770605-fde1dc2a-729b-4aff-b184-7ffb9afbe568.png">


5. Membuat container untuk Jenkinsnya
```
docker container create --name jenkins -p 8080:8080 -p 50000:50000 -v jenkins:/var/jenkins_home jenkins/jenkins:latest
```
  kita jalankan containernya dengan perintah
```
docker start jenkins
```

<img width="1279" alt="Screen Shot 2022-06-25 at 17 58 52" src="https://user-images.githubusercontent.com/62433171/175770612-c43ccc00-6502-400c-8858-00b83b83c49d.png">

Jika sudah jalan container nya kita bisa akses jenkinsnya dengan ip:8080

6. Konfigurasi Jenkins

**Pastikan internet kita stabil dan lancar agar ketika jenkinsnya sedang menjalankan setup pluginnya dapat terinstal dengan baik** 

Pada saat masuk akan muncul tampilan seperti ini, kita akan ambil pass nya di dalam 

```
/var/jenkins_home/secrets/initialAdminPassword
```

Jika tidak ada coba ini

```
sudo docker exec (nama container) cat /var/jenkins_home/secrets/initialAdminPassword
```

<img width="655" alt="Screen Shot 2022-06-25 at 18 07 41" src="https://user-images.githubusercontent.com/62433171/175770899-503249b8-c868-46ae-9b1a-597e10490c11.png">

pilih install suggested plugins

<img width="606" alt="Screen Shot 2022-06-25 at 18 09 46" src="https://user-images.githubusercontent.com/62433171/175770958-b96a9273-3a72-4cd3-b283-44aceddf226a.png">

Lalu jenkins akan menginstal plugin nya 

Membuat username dan password admin

<img width="627" alt="Screen Shot 2022-06-25 at 18 11 16" src="https://user-images.githubusercontent.com/62433171/175771006-c1e8231d-e3ea-4cc0-9a5d-d982feacf4ec.png">

save dan finish langsung saja karena ini udah default dari jenkinsnya

<img width="745" alt="Screen Shot 2022-06-25 at 20 09 24" src="https://user-images.githubusercontent.com/62433171/175774856-956cb127-8971-4167-9d89-31e6bee4191e.png">

<img width="748" alt="Screen Shot 2022-06-25 at 20 10 11" src="https://user-images.githubusercontent.com/62433171/175774880-3190a7a0-5cee-4bba-bf4a-231c45458bc6.png">

**TAMPILAN PERTAMA KALI KETIKA BERHASIL**

<img width="1280" alt="Screen Shot 2022-06-25 at 20 11 01" src="https://user-images.githubusercontent.com/62433171/175774906-f1fe8530-b331-4e79-bf27-e0dcf0ff1225.png">


7. Credential di dalam menu manage credential

kita akan membuat credential yang akan menjadi kunci antara server jenkins dan app jenkinsnya

<img width="1280" alt="Screen Shot 2022-06-18 at 01 42 58" src="https://user-images.githubusercontent.com/62433171/175771082-51ab5ed3-3ecd-49d7-b616-b465ee097fe0.png">

di dalam credentialnya kita pilih **enter directly**, disini kita copy id_rsa dari server jenkinsnya 

8. Jika sudah kita setup dulu di dalam Frontend dan backend kita buat file Jenkins 
 
 ## file Jenkins Frontend 
 ```
 def secret = 'sultan'
def server = 'aplikasi@103.181.143.205'
def dir = 'wayshub-frontend'
def branch = 'main'

pipeline{
	agent any
	stages{
		stage ('Delete container and images & git pull'){
			steps{
				sshagent([secret]) {
					sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
					cd ${dir}
					docker-compose down
					docker system prune -f
					git pull origin ${branch}
					exit
					EOF"""
				}
			}
		}
	stage ('Build Images'){
                        steps{
                                sshagent([secret]) {
                                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                        cd ${dir}
                                        docker-compose build
                                        exit
                                        EOF"""
                                }
                        }
                }
	stage ('Deploy Container'){
                        steps{
                                sshagent([secret]) {
                                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                        cd ${dir}
                                        docker-compose up -d
                                        exit
                                        EOF"""
                                }
				
                        }
		
               }

	}
	
}
```

## file jenkins backend
```
def secret = 'sultan'
def server = 'aplikasi@103.181.143.205'
def dir = 'wayshub-backend'
def branch = 'main'

pipeline{
	agent any
	stages{
		stage ('Delete container and images & git pull'){
			steps{
				sshagent([secret]) {
					sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
					cd ${dir}
					docker-compose down
					docker system prune -f
					git pull origin ${branch}
					exit
					EOF"""
				}
			}
		}
	stage ('Build Images'){
                        steps{
                                sshagent([secret]) {
                                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                        cd ${dir}
                                        docker-compose build
                                        exit
                                        EOF"""
                                }
                        }
                }
	stage ('Deploy Container'){
                        steps{
                                sshagent([secret]) {
                                        sh """ssh -o StrictHostKeyChecking=no ${server} << EOF
                                        cd ${dir}
                                        docker-compose up -d
                                        exit
                                        EOF"""
                                }
				
                        }
		
               }

	}
	
}
```

9. Membuat Job jenkins

disini kita isi nama job nya dan pilih file **PIPELINE**	

<img width="1279" alt="Screen Shot 2022-06-18 at 01 41 20" src="https://user-images.githubusercontent.com/62433171/175771862-1d7ed538-b482-4383-831f-a300a64d7afc.png">


<img width="627" alt="Screen Shot 2022-06-25 at 18 47 27" src="https://user-images.githubusercontent.com/62433171/175772226-6330bdde-f042-4896-b7fd-b3718df759b4.png">

didalam **BUILD TRIGGERS** kita pilih Git Hook Triggers agar nanti Job yang kita buat dapat membuild sendiri tanpa kita klik build secara manual **DENGAN SYARAT** ketika kita build pertama kali harus berhasil dahulu.    

didalam menu **PIPELINE** kita pilih Pipeline Script SCM dan di SCM nya kita klik GIT , isi Repository url nya dengan url repo github frontend jika untuk backend kita pilih untuk backend. Karena di dalam jenkins itu hanya dapat menjalankan 1 job 1 repo jadi ketika kita memiliki 2 repo makan kita harus membuat 2 job.

dan di bagian credential kita tambahkan dengan credential yang sudah kita buat tadi.

<img width="627" alt="Screen Shot 2022-06-25 at 18 55 05" src="https://user-images.githubusercontent.com/62433171/175772472-45959aa1-9985-451f-a906-c80741da84d0.png">


10. Jika sudah membuat credential dan job dan berhasil dan kita proses build. Maka tampilan nya akan hijau semua seperti ini.

<img width="1280" alt="Screen Shot 2022-06-18 at 01 44 22" src="https://user-images.githubusercontent.com/62433171/175775144-d2697dec-51c5-4fc8-8078-4508cb5cdc60.png">

# Setting GITHUB TRIGGERS 

Kita akan mengkonfigurasikan antara jenkins dan github kita dengan menggunakan webhook di dalam github, kita tadi sudah memilih atau mencentang bagian **Github hook triggers** yang seperti di gambar bawah ini.


<img width="824" alt="Screen Shot 2022-06-25 at 20 20 03" src="https://user-images.githubusercontent.com/62433171/175775273-8133a00c-9f18-499b-b088-9198766dd6fd.png">

Pergi ke repository aplikasi frontend/backend pilih setting dan klik **WEBHOOKS**

<img width="1280" alt="Screen Shot 2022-06-25 at 20 33 15" src="https://user-images.githubusercontent.com/62433171/175775822-1713ce17-c182-4bdd-bb50-d609afd81d0f.png">

ADD WEBHOOKS

<img width="1280" alt="Screen Shot 2022-06-25 at 20 34 37" src="https://user-images.githubusercontent.com/62433171/175775870-8bbb243b-adb5-4720-bf7b-1df316082840.png">

Masukkan Password Github 

<img width="724" alt="Screen Shot 2022-06-25 at 20 35 04" src="https://user-images.githubusercontent.com/62433171/175775881-cb89b169-8fbb-4a69-b361-9e6633f1c32a.png">

Lalu tambahkan url dengan config seperti dibawah
```
https://(domain-apliaksi-kita)/github-webhook/
```
dan ADD WEBHOOK

<img width="1280" alt="Screen Shot 2022-06-25 at 20 36 38" src="https://user-images.githubusercontent.com/62433171/175775936-2da3d317-17dd-4757-8516-a2d460bf53ea.png">


dan jika berhasil maka tampilan akan seperti ini

<img width="1280" alt="Screen Shot 2022-06-25 at 20 39 18" src="https://user-images.githubusercontent.com/62433171/175776038-9d71cd10-f466-4ab9-a134-493214d3f479.png">
















 
