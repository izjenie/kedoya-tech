---
title: "Setting Our Own Mail Server"
description: "Setting Our Own Mail Server"
date: 2018-12-27T21:58:20+07:00
categories:
    - "post"
tags:
    - "meta"
    - "test"
cardthumbimage: "/images/default.jpg" #optional: default solid color if unset
cardheaderimage: "/images/default.jpg" #optional: default solid color if unset
cardbackground: "#263238" #optional: card background color; only shows when no image specified
#cardtitlecolor: "#fafafa" #optional: can be changed to make text visible over card image
"author":
    name: "Izak Jenie"
    description: "Writer of stuff"
    website: "http://example.com/"
    email: "firstname@example.com"
    twitter: "https://twitter.com/"
    github: "https://github.com/"
    image: "/images/avatar-64x64.png"
---

# Kenapa setup mail server sendiri?
Ok, semua pada bilang - kenapa musti setting mail server sendiri? Bayar Google cuma $3 per account, cukup murah. Itu bener banget, cuma kalau kita entrepreneur dengan beberapa perusahaan - setting up domain ke google mail setiap kali merupakan pekerjaan yg cukup melelahkan. Dan sering sekali tidak bisa di delegasikan ke orang lain. 

Juga, mungkin bagus juga kalau kita tidak kebanyakan pakai Google - paling tidak sebagai backup seandainya Google tidak bisa di andalkan lagi (kira2, 100 tahun lagi lah..)

Plus, juga sebagai intellectual curiousity. Seberapa susah sih sebetulnya setup mail server di tahun 2018?

## Requirement
Sebelum mulai, gw coret-coret beberapa requirements:

* **Gratis dan Open Source**, of course. Kalau bayar sih, where's the fun?
* **Harus running di atas Docker** - karena gw ngertinya cuma Docker, belon ngerti Kubernetes
* **Harus running di atas secure infrastructre.** Jadi ngga bisa pakai port 110, atau 25 non-authorized, semua harus SSL.
* **Harus diterima sebagai non-spam di Google Mail, at least.** Kalau setup server sendiri, suka jadinya semua email kita masuk spam. Sering kejadian dan pernah beberapa tahun lalu pernah install sampai kapok. 
* **Harus ada webmail**, kalau bisa yang bagus tampilannya.
* **Secure, secure dan secure.** Harus bisa di install di reverse proxy seperti nginx dan lain2-nya.
* **Bonus: kalau bisa support multiple domain.** 

## Candidates

Setelah beberapa jam puter-puter di internet, ternyata hasilnya ngga banyak. Ada email server, dan juga webmail.

### Email Server

Setelah pilih-pilih, ternyata ada 4 Docker yang bisa di pakai:

1. Docker `hardware/mailserver` ada di sini [hardware/mailserver](https://hub.docker.com/r/hardware/mailserver/)<bR>
    > Ngga berhasil di install, first time langsung ngga jalan. Ngga gerti kenapa, setelah coba hampir 3 jam, akhirnya nyerah juga.
2. Docker `tvial/docker-mailserver` ada di sini [tivial/docker-mailserver](https://hub.docker.com/r/tvial/docker-mailserver/)<br>
    > Ini yang paling menarik, di install jalan bagus dan sempat jalan bagus. Semua yang di atas terpenuhi, kecuali multiple domain. Masalahnya koneksi SSL certificatenya kompleks banget. Coba berkali2, bolak-balik urusan SSL certificate sampai akhirnya setelah 2 hari coba install (iya 2 hari!), nyerah juga. Masalah lainnya adalah dia ngga punya dashboard untuk admin, jadi semuanya urusan command line. Pusing juga kalau sudah operational.
3. Juga [Mailu.io](https://mailu.io)<br>
    > Ini kelihatannya bagus dan solid, tapi cara installnya aja bingung. First time ngga jalan, dan akhirnya ngga pernah jalan. Dokumentasinya parah banget, not ready for the prime time.  
4. Dan [Poste.io](https://poste.io) -> Ini juaranya!<br>
    > Mustinya dari awal cobain yang ini supaya ngga buang2 waktu. Ternyata yang ini bagus banget, installnya gampang dan langsung jalan, dashboardnya rapih dan super clean. Kekurangannya cuma satu - webmailnya pakai **roundcube** - jeleknya ampun, kayak tahun 1990-an. 

## Pilihan: Poste.io
Poste.io punya segalanya, jadi akhirnya di putuskan pakai ini, tapi kita harus ganti itu webmail yang jelek - jadinya docker-nya musti di utak-atik dikit. Ntar di kasih tahu caranya.

# Setting Up Mail Server

## Pilih Cloud Server

Kecuali kita memang hobinya sengsara dan menderita, sebaiknya email servernya di taro di Cloud Server. Kalau mau di lokal, yang paling bagus tentu taro di [Biznet Gio](https://www.biznetgio.com). 

Tapi untuk kebutuhan testing, kita pakai cloud Amazon aja dulu. Murah meriah.

Jadi, gw coba install itu mai server di AWS Lightsail (again, murah meriah) - tapi ngga jalan-jalan. Akhirnya tahu bahwa Amazon ngga approved orang kalau mau running mail server di AWS infrastructure. Intinya, musti minta ijin ke Amazon dan kasih alasan kenapa harus running mail server sendiri. Rese banget. 

Tapi make sense sih sebetulnya, soalnya kalau semua orang bisa install port 25 di AWS - habis kita di spam kanan-kiri.. Lagian Amazon mungkin mikir - siapa sih orang kurang kerjaan yg pengen install mail server sendiri? Mustinya corporate gede yg minta, jadi kalo suruh minta ijin ke Amazon masih mau. Ok lah, ngga bisa pakai Amazon untuk kita2 rakyat jelata ini.

Pilihan server murah lainnya ada 3:

* DigitalOcean
* Vultr
* Linode

Setelah banding-bandingkan, akhirnya pilih pakai **Vultr**. Soalnya langsung jalan dan cukup sederhana cara pakainya.

Yuk kita coba mulai install.

