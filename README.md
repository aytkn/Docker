## Docker 
### İşletim Sistemi Update
    sudo apt update;sudo apt upgrade
### Docker Kurulum
    sudo apt install docker.io
### Sistemi Her Yeniden Başlatmada Dockerı Otomatik Çalıştırma
    sudo systemctl enable --now docker
### Versiyon Kontrolü
    docker --version
### Ubuntuyu Container Yapma ve Bash Oturumu Elde Etme
    sudo docker run -it ubuntu bash
### Docker Detay Bilgileri Getirme
    docker info
### Container Listeleme
    Çalışan Container Listesi	: docker ps
    Tüm Container Listesi		: docker ps -a
    Detaylı Container ID Listeleme	: docker container ls -a --no-trunc
    Sadece Container ID Listeleme	: docker container ls -a -q
    Son Oluşturulan Container Görme : docker container ls -l
    Filtreleyerek Container Görme	: docker container ls -a --filter “komut, image, port bilgisi veya id”
### Container Çalıştırma
    docker run centos:7 (ubuntu vs, istenilen sürüm yazılmazsa en son sürüm kurulur)
### Arka Planda Container Çalıştırma
    docker container run -d centos:7 bash
### Container Durdurma
    docker container stop cont_id
    docker container kill cont_id  ( direk kapatma )

### Container Yazılabilir Hale Getirme
    docker container run -it centos:7 bash
    ( i : interactive , t : tty connection ,  shell vs bash ne ile bağlanmak istersek)
### Mevcut Containera Bağlanma
    docker ps -a ile container id alınır.
    docker container start cont_id   ( bağlanmak için önce start konumunda olmalı )
    docker container exec -it cont_id bash ( bağlantı sağlamak )
    docker container attach cont_id ( Canlı olarak bağlanma)
### Container Loglarını Listeleme
    docker container logs cont_id
    docker container logs --tail 10 cont_id   (Son 10 logu görme)
    docker container logs -f cont_id ( Canlı log izleme)
### Container Detay Bilgilerini Görme
    docker container inspect cont_id
    docker container inspect cont_id | grep IPAddress ( İçinde IP geçen satırları süzer)
### Container Silme
    docker container rm cont_id  (Çalışır durumdayken silinmez, önce stop edilmeli)
### Container Port Tanımlama
    docker container run -d -p 5000:80 ( 5000 host port , 80 container port )
    docker container port cont_id  : Hangi portlar açık olduğunu görme
### Plugin Yükleme
    docker plugin install grafana/loki-docker-driver (hub.docker plugin adresinden yolunu buluyoruz)
    docker plugin ls : Yüklü plugin listeleme
    docker plugin disable plugin_adı
    docker plugin enable plugin_adı
    docker plugin rm plugin_adı :  Kaldırma  ( Önce disable olmalı)
### Docker Üzerinden Imaj Arama
    docker search mysql
    docker pull mysql ( hostumuza imajı indirir. ulaşamaya çalışıldığında hostta bulunduğu için hub.docker.com üzerinden ulaşmaya çalışmaz)
### Docker File Komutları
    vi Dockerfile ( docker  dosyası oluşturulur.)
    FROM centos (hangi işletim sistemini, uygulamayı çalıştırmak istediğimiz yazılır)
    RUN yum -y update (kurulumdan sonra çalıştırmak istediğimiz komutları yazarız)
    RUN yum -y install nano
    LABEL key … value ….(metadata bilgileri eklemek için kullanılır)
    MAINTAINER Aytekin Uzun ( image sahibi ile ilgili detay bilgi)
    CMD ping -c 10 127.0.0.1  (ping işlemi başlat)
    CMD [“/bin/echo”]  (bin dizinine ulaş)
    EXPOSE 80/TCP (Açılacak portları tanımlar)
    ENV (Ortam değişkeni tanımlar)
    ADD /bin/xyz /bin/xyz (İnternetten yada lokalden dosya indirme)
    COPY /bin/xyz /tmp (belirtilen dosyaları container içindeki hedefe kopyalar)
    ENTRYPOINT <>(varsayılan bir parametre tanımı yapmak için kullanılır)
    VOLUME /MOUNT (silinmesini istemediğimiz dosyalar için)
    USER (kullanıcı tanımlamaları yapılır)
    WORKDIR (uygulamaların hangi path altında çalışmasını belirtir)
    docker image build --tag btk . ( docker file build etme. dosyanın bulunduğu dizini göstermek için “.” koyduk. tag ile isimlendirdik )
    “docker images” komutu ile oluşturulan image görülür.
    “docker container run -it btk bash” komutu ile oluşturulan image bağlanılır.
### Reposity İmage Yükleme
    Yüklenecek imajın Reposityde belirtilen yol adının aynısı olması gerekiyor. Bunun için
    docker tag Yuklenecek_Image_ID reposity_yolu:image adı
    docker push reposity_yolu:image_adı
### Lokalde Registy Oluşturma
    Lokalde docker imajlarını tutmak için kullanılır.
    docker pull registry:2
    mkdir /var/lib/docker/registry  (dizinde regisrty klasörü oluşturma)
    docker run -d -p 5000:5000 -v /var/lib/docker/registry/:/var/lib/registry registry:2
    Kendi registry mizi kullanacağımız için gerekli tanımlamaları değiştiriyoruz.
    vi /etc/docker/daemon.json içindeki “ insecure-registries” alanına 127.0.0.1:5000 yazıyoruz.
    docker tag nginx 127.0.0.1:5000/nginx:my-registry  (registry tagını verdik)
    docker push 127.0.0.1:5000/nginx:my-registry    (kendi yapımıza push ettik)
## Docker Volume
### Volume Oluşturma
    docker volume create --name test1
    /var/lib/docker/volumes/ altında varsayılan olarak oluşur.
### Containera Volume Atama
    docker container run -it -v test1:/www/website centos:/ bash
### Volume Hostta Hangi Path’te Duruyor
    docker volume inspect test1 ( Mountpoint kısmında yazan yol)
### Volume Okuma Yazma Yetkileri
    docker container run -it -v test1:/www/website:ro centos:/ bash  (ro : Read Only)
### External Storage Volume Oluşturma
    docker volume create --opt type=nfs --opt o=addr=192.168.1.10,rw,nfsserver4 --opt device=:/home/nfsshare nfs-volume
### Volume Silme
    docker volume rm volume-adı (Üzerinde çalışan container varsa önce onları silmeliyiz)
## Docker Network 
### Network Listeleri Görme
    docker network ls
### Network Detay Görme
    docker network inspect network_adı
### Network Oluşturma
    docker network create test1
### Networku Containera Dahil Etme
    docker container run -it --net test1 centos bash
### Subnet Range Oluşturma
    docker network create --subnet 192.168.100.0/24 --gateway 192.168.100.1 test2
### Network Driver Türünü Seçme
    docker network create --driver null test3
### Network Silme
    docker network rm network_adı (herhangi bir container kullanmıyor olması lazım)
    docker network prune (kullanımda olmayan tüm networkleri kaldırır)
### Network Mac Vlan
    docker network create -d macvlan --subnet=192.168.0.0/24 --gateway=192.168.0.1 -o parent=eth0 macvlan-mynet
## Docker Compose
### Versiyon
    docker compose --version
## Docker Swarm
### Aktifleştirme
    docker swarm init --advertise-addr 192.168.0.13
