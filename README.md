# Jarkom-Modul-1-IT22-2024

|Nama  | NRP |
|--|--|
| Jacinta Syilloam | 5027221036 |

## Content
   * [ATM or ATP or FTP ? 🤔 ](#atm-or-atp-or-ftp-)
   * [evidence](#evidence)
   * [How Many packets? ](#how-many-packets)
   * [trace him](#trace-him)
   * [creds](#creds)
   * [malwleowleo](#malwleowleo)
   * [whoami](#whoami)
   * [fuzz](#fuzz)
   * [secret](#secret)
   * [malwaew](#malwaew)

<!-- TOC --><a name="atm-or-atp-or-ftp-"></a>
## ATM or ATP or FTP ? 🤔 
Pradityo mencoba mengembangkan server ftp, tetapi seseorang mencoba melakukan bruteforce login, bisakah Anda menganalisis apa yang terjadi?

file: ftp.pcap

Dari deskripsi soal, dapat clue tentang server ftp. Oleh karena itu dilakukan filter pada protocol ftp.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/32024f76-d82b-4f38-bf20-c64a24cee670)

Dapat dilihat attempt attacker untuk melakukan bruteforce serta response saat attempt tersebut gagal. Oleh karena itu, follow TCP stream dengan response 230 Login Successful. Dari sini, ditemukan password pada stream terakhir (TCP stream 319).
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/487c997a-763c-4ebe-a330-519d5386563c)

```
❯ nc 10.15.40.20 10004
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 4:
Pertanyaan: Apa password yang berhasil didapatkan oleh hacker setelah melakukan bruteforce login ftp?
Format: strings
Jawaban: m4y_th3_Kn!fe_ch1p_&_sh4tter
Correct

Congrats! Flag: JARKOM2024{Brut3f0rce_FtP_9hr8vcAfiRktkAY}
```




<!-- TOC --><a name="evidence"></a>
## evidence
Perusahaan nanomate baru saja kebobolan. Mereka menyewamu untuk mencari tahu bagaimana caranya pelaku bisa masuk.

file: challenge.pcapng

Saat membuka capture, dapat langsung dilihat attempt attacker untuk melakukan bruteforce path.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/fbb08a31-4a0a-482c-84b6-fa167186c504)

Setelah follow salah satu HTTP stream dengan GET request, didapatkan informasi mengenai Host attacker dan web server korban. 

Host: nanomate-solutions.com

Web Server: Apache/2.4.56
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/ef49acf8-6dca-4e2d-906e-e75c4023df74)


Selanjutnya perlu mencari endpoint untuk login biasa, oleh karena itu dicoba untuk filter protocol http. Setelah scroll terlihat beberapa endpoint dengan status 200 OK, salah satunya pada endpoint ```/app/includes/process_login.php```. Karena ada kata-kata login, dicoba submit dan benar.
```
display filter: http
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/f14b3d5a-2e7e-44de-a8c6-bdefcbc9ed9a)

Untuk mencari email dan password, filter dengan mencari response 302 Found.
```
display filter: _ws.col.info == "HTTP/1.1 302 Found  (text/html)"
```
Packet dengan length 483 berisi dengan response ```Invalide Username or Password```. Karena packet yang didisplay hanya 140, dengan scroll ditemukan packet yang sedikit berbeda dengan length 485 dan berisi response ```Login Successful```.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/e31e0167-5975-43de-943f-b98749e8917f)

Setelah follow stream, ditemukan credentials yang dicari.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/b3062a60-cdf0-45f2-b3dc-80b16859a769)
```
❯ nc 10.15.40.20 10002
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 1:
Pertanyaan: Apa domain milik korban?
Format: xxxxxx.xxx: e.g. google.com
Jawaban: nanomate-solutions.com
Correct

No 2:
Pertanyaan: Apa web server yang digunakan oleh korban?
Format: name-version: e.g. nginx-1.18.0
Jawaban: apache-2.4.56
Correct

No 3:
Pertanyaan: Apa endpoint yang digunakan untuk login sebagai user biasa?
Format: /path/to/endpoint
Jawaban: /app/includes/process_login.php
Correct

No 4:
Pertanyaan: Apa email dan password yang berhasil digunakan untuk login sebagai user biasa?
Format: email:password
Jawaban: tareq@gmail.com:tareq@nanomate
Correct

Congrats! Flag: JARKOM2024{m4innya_h3bat_9T8kXbntQRJHCAq}
```



<!-- TOC --><a name="how-many-packets"></a>
## How Many packets? 
Sebagai kewajiban untuk laporan, aku diminta untuk mencari tahu berapa kali attempt login yang dilakukan oleh hacker. Dapatkah kamu membantuku untuk menganalisanya?

file: ftp.pcap

Setelah membuka packet, dapat dicari berapa kali attacker melakukan bruteforce dengan melakukan filter pada response 331. Di bagian bawah, terlihat total packets yang didisplay yaitu 934.
```
display filter: _ws.col.info == "Response: 331 Please specify the password."
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/f13282f0-7275-4791-b6d4-97df436142a2)

```
❯  nc 10.15.40.20 10005
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 5:
Pertanyaan: Berapa total attempt login(bruteforce) yang dilakukan oleh hacker?
Format: number
Jawaban: 934
Correct

Congrats! Flag: JARKOM2024{c0unT_uR_P4cket5_xh8lYb9tyRVtC49}
```


<!-- TOC --><a name="trace-him"></a>
## trace him
Selain menghitung jumlah packet, coba lacak juga ip penyerang tersebut!

file: ftp.pcap

Saat membuka packet, langsung terlihat ip address attacker yang attempt untuk bruteforce.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/dc87bb85-0540-48ac-b7ca-0fb89e0ff6f1)

```
❯  nc 10.15.40.20 10006
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 6:
Pertanyaan: Alamat IP attacker?
Format: xxx.xxx.xxx.xxx
Jawaban: 10.30.3.4
Correct

Congrats! Flag: JARKOM2024{Wh3re'5_thE_S4uce_u6rkRbxygRFHkAq}
```


<!-- TOC --><a name="creds"></a>
## creds
Attacker menyadari jika dia bisa membuat clone ftp server dari target, temukan kredensialn dari server ftp yang dibuat oleh attacker

file: evidence.pcap

Dari deskripsi soal disebutkan tentang ftp server, jadi lakukan filter pada protocol ftp.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/cf5412e0-5cb7-4135-9b67-4408307106de)
Sekilas terlihat request attacker saat memasukkan credentials. Saat follow packet tersebut, dapat dilihat credentials yang digunakan untuk successful login.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/26452ed8-7615-4eb6-9707-033f40dcd012)

```
❯ nc 10.15.40.20 10007
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 1:
Pertanyaan: Apa Username FTP yang digunakan oleh attacker?
Format: USER:username
Jawaban: USER:h3ngk3rTzy
Correct

No 2:
Pertanyaan: Apa Password FTP yang digunakan oleh attacker?
Format: PASS:password
Jawaban: PASS:S!l3ncE
Correct

Congrats! Flag: JARKOM2024{s3curE_uR_FtP_9JfkYz9jiAVtl8q}
```


<!-- TOC --><a name="malwleowleo"></a>
## malwleowleo
Dapatkah kamu menemukan file malware yang dikirim oleh attacker melalui ftp?

file: evidence.pcap

Untuk menemukan file pada capture, digunakan filter protocol ftp, yakni protocol untuk file transfer, dan filter search STOR, yakni command setelah sukses melakukan file transfer. Setelah itu ditemukan file malware yang dicari.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/a13d096b-137e-481a-8288-433ef60870a2)

```
❯ nc 10.15.40.20 10008
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 8:
Pertanyaan: Apa nama malware yang dikirim oleh attacker ke korban?
Format: strings
Jawaban: m4L1c10us_W4re.c
Correct

Congrats! Flag: JARKOM2024{beC4refUl_0f_m4lwAr3_xTfkPOnygAFtR4q}
```



<!-- TOC --><a name="whoami"></a>
## whoami
Dapatkah kamu menemukan siapa identitas attacker?

file: evidence.pcap

Follow TCP stream berikutnya dari soal sebelumnya saat attacker memasukkan credentials. Pada stream ke 7, ditemukan packet dengan message yang diencode dengan base64.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/bf5bca01-8962-4e12-98fb-4e4aa4dce024)
Setelah didecode, ditemukan full name attacker.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/233e1cfd-b9be-4c2a-9492-76fe6ac26e42)

```
❯  nc 10.15.40.20 10009
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 9:
Pertanyaan: Siapa nama attacker yang sudah melakukan serangan ini?
Format: FirstName_LastName
Jawaban: Paul_Atreides
Correct
```


<!-- TOC --><a name="fuzz"></a>
## fuzz
My website got hacked. Can you analyze this network traffic to help me track the attacker?

file: capture.pcap

Saat membuka capturenya, dapat langsung dilihat source ip address yang menggunakan ffuf pada korban serta port yang dituju.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/d59fae29-1050-4c6d-959e-1d6e6c8cd2b6)

Setelah follow salah satu packetnya, terlihat bahwa attacker melakukan POST pada endpoint /.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/8b585c0d-c86b-4c4d-b642-8b299fce48a5)

Untuk melihat response dengan tujuan mencari credentials, filter dengan source ip address korban dan destination ip address attacker.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/8884352f-17d6-42ad-8391-1da9f69f27d4)

Follow stream dengan response ```302 Found```. Lalu search found pada stream tersebut dan ditemukan credentialsnya.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/3d1173e7-6853-4691-b270-abfcb8bfb50c)

```
❯  nc 10.15.40.20 10001
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 1:
Pertanyaan: Apa IP address milik attacker?
Format: xxx.xxx.xxx.xxx
Jawaban: 10.33.1.154
Correct

No 2:
Pertanyaan: Apa port yang digunakan sebagai web server korban?
Format: xxxx: e.g. 22
Jawaban: 80
Correct

No 3:
Pertanyaan: Apa endpoint yang digunakan untuk login?
Format: /path/to/endpoint
Jawaban: /
Correct

No 4:
Pertanyaan: Apa tool yang digunakan oleh attacker untuk bruteforce login?
Format: toolsname-version: e.g. hydra-v9.0-dev
Jawaban: ffuf-v2.0.0-dev
Correct

No 5:
Pertanyaan: Apa username dan password yang berhasil digunakan oleh attacker?
Format: username:password
Jawaban: admin:sUp3rSecretp@ssw0rd
Correct

Congrats! Flag: JARKOM2024{s3m4ng4t_ya_<3_xhfkX79ji1dokrB}
```



<!-- TOC --><a name="secret"></a>
## secret
Temukan pesan rahasia dari attacker

file: evidence.pcap

Gunakan filter ftp untuk mencari file yang diupload oleh attacker. Ditemukan file jpg.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/e658a158-0022-46f6-a32a-fb81895d3e4e)

Download file tersebut dengan export objects -> FTP-DATA
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/30a32198-dfee-499d-9312-0488eb85436d)

Secret message pada soal ini adalah MIO MIRZA, dilihat dari image yang didownload.
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/e4ad4bfd-3836-4130-a9be-b64b23e91380)

```
❯  nc 10.15.40.20 10010
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 10:
Pertanyaan: Ternyata attacker menyisipkan file lainnya selain dari file malware, temukan pesan yg dikutip oleh attacker?
Format: strings
Jawaban: MIO MIRZA
Correct

Congrats! Flag: JARKOM2024{l0_Blm_tW_MIO_MIRZA?_xhr8vc9fy6ke889}
```




<!-- TOC --><a name="malwaew"></a>
## malwaew
Ini adalah network traffic dari salah satu komputer di DPSSI yang terkena malware. Pak Sunhi, memintamu untuk membantu menganalisisnya. Bantulah Pak Sunhi untuk menemukan malware tersebut.

note: Network traffic berikut mengandung malware asli. 

file: infected.zip


