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
> Pradityo mencoba mengembangkan server ftp, tetapi seseorang mencoba melakukan bruteforce login, bisakah Anda menganalisis apa yang terjadi?
> 
> file: ftp.pcap

Dari deskripsi soal, dapat clue tentang server ftp. Oleh karena itu dilakukan filter pada protocol ftp.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/32024f76-d82b-4f38-bf20-c64a24cee670)

Dapat dilihat attempt attacker untuk melakukan bruteforce serta response saat attempt tersebut gagal. Oleh karena itu, filter protocol TCP dengan response 230 Login successful.
```
display filter: tcp contains "Login successful" 
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/5d2bf847-254a-4ce5-84e5-3c871d704924)

Follow stream lalu ditemukan credentials yang sedang dicari.

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
> Perusahaan nanomate baru saja kebobolan. Mereka menyewamu untuk mencari tahu bagaimana caranya pelaku bisa masuk.
> 
> file: challenge.pcapng

Saat membuka capture, dapat langsung dilihat attempt attacker untuk melakukan bruteforce path.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/fbb08a31-4a0a-482c-84b6-fa167186c504)

Setelah follow salah satu HTTP stream dengan GET request, didapatkan informasi mengenai Host attacker dan web server korban. 

Host: nanomate-solutions.com

Web Server: Apache/2.4.56

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/ef49acf8-6dca-4e2d-906e-e75c4023df74)


Selanjutnya perlu mencari endpoint untuk login biasa, oleh karena itu dicoba untuk filter protocol http dengan info berisi kata "login". Terlihat beberapa endpoint dengan kata login, tetapi request POST hanya dilakukan pada endpoint ```/app/includes/process_login.php```, yakni endpoint yang tadi dicari. 
```
display filter: http.request.uri contains "login"
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/2c606f6e-dd89-4a0a-90d5-5c815625c1d2)


Untuk mencari email dan password, filter dengan mencari response 302 Found dan berisi kata "Login Successful"
```
display filter: http.response.code == 302 and http contains "Login Successful"
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/8dd07cc4-c788-4a5b-8b1d-5a3693c8224e)

Setelah follow stream, ditemukan detail credentials yang dicari.

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
> Sebagai kewajiban untuk laporan, aku diminta untuk mencari tahu berapa kali attempt login yang dilakukan oleh hacker. Dapatkah kamu membantuku untuk menganalisanya?
>
> file: ftp.pcap

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
> Selain menghitung jumlah packet, coba lacak juga ip penyerang tersebut!
>
> file: ftp.pcap

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
> Attacker menyadari jika dia bisa membuat clone ftp server dari target, temukan kredensialn dari server ftp yang dibuat oleh attacker
>
> file: evidence.pcap

Dari deskripsi soal disebutkan tentang ftp server, jadi lakukan filter pada protocol ftp dan kata "Login successful"
```
display filter: ftp contains "Login successful"
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/12cd3c75-ee9b-4e21-9439-70d7c2b51b2d)


Saat follow packet tersebut, dapat dilihat credentials yang digunakan untuk successful login.

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
> Dapatkah kamu menemukan file malware yang dikirim oleh attacker melalui ftp?
>
> file: evidence.pcap

Untuk menemukan file pada capture, digunakan filter protocol ftp, yakni protocol untuk file transfer, dan filter kata "STOR", yakni command setelah sukses melakukan file transfer. Setelah itu ditemukan file malware yang dicari.
```
display filter: ftp
```
![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/8f111c77-ec16-4d27-8e05-abd6b9ddfdb2)

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
> Dapatkah kamu menemukan siapa identitas attacker?
>
> file: evidence.pcap

Explore malicious file pada pertanyaan sebelumnya dengan filter ftp-data.
```
Display filter: ftp-data.command contains "STOR"

```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/5d907a81-edc1-44a2-97fa-17a016b2c8ee)

Follow stream ftp-data malicious file tersebut.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/bf5bca01-8962-4e12-98fb-4e4aa4dce024)

Setelah message pada file c didecode, ditemukan full name attacker.

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
> My website got hacked. Can you analyze this network traffic to help me track the attacker?
>
> file: capture.pcap

Cek packets pada protocol http. Terdapat attempt attacker untuk bruteforce credentials korban. 

Dari sini ditemukan source ip address (attacker), destination port (korban), dan endpoint yang digunakan untuk login yaitu /.

```
Display filter: http 
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/21ec7431-2177-4ee3-824f-fd59f27e4b7f)


Untuk mencari tool yang digunakan attacker, filter dengan source ip address attacker. 

Dari sini ditemukan user-agent attacker menggunakan ffuf.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/94533330-15df-4d3a-9539-7f3a06afea5e)


Untuk melihat response dengan tujuan mencari credentials, filter dengan source ip address korban dan destination ip address attacker dan response "Found" 

```
Display filter: http contains "Found" and ip.src eq 172.20.0.2 and ip.dst eq 10.33.1.154
```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/27fbec25-99d5-4215-a04a-028884c7383b)

Follow stream

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
> Temukan pesan rahasia dari attacker
> 
> file: evidence.pcap

Gunakan filter ftp untuk mencari file yang diupload oleh attacker. Selain malicious file pada pertanyaan sebelumnya, ditemukan juga file jpg.
```
Display filter: ftp-data.command contains "STOR"

```

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/5d907a81-edc1-44a2-97fa-17a016b2c8ee)

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
> Ini adalah network traffic dari salah satu komputer di DPSSI yang terkena malware. Pak Sunhi, memintamu untuk membantu menganalisisnya. Bantulah Pak Sunhi untuk menemukan malware tersebut.
>
> note: Network traffic berikut mengandung malware asli. 

file: infected.zip

Diberikan file keylog.txt dengan TLS session keys. Ke File lalu Preferences dan masukkan file keylog.txt tadi ke TLS (Pre)-Master-Secret log filename.

![image-1](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/c8d3ecbe-8373-4ba7-9073-f5d7d4734fa1)

Setelah decrypt TLS, beberapa file yang sebelumnya hidden menjadi visible. Setelah filter ke protocol http, ditemukan GET request pada file dengan extension dll. 

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/72840157-11d9-43b8-9243-827acf830c82)

File DLL berisi executable code dan dapat terinfeksi malware, sama seperti EXE. Oleh karena itu, dicoba untuk follow stream dan memang sebuah executable file.

![image](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/9832dde1-5bfd-4723-9dc4-aa9417cc4a36)

Download file dengan export objects -> HTTP

![image-1](https://github.com/JacintaSyilloam/Jarkom-Modul-1-IT22-2024/assets/121095246/f49322d6-9615-4112-ae3c-0d2c1c335f81)

Cek SHA-256 hash dari malware tersebut.
```
┌─[user@parrot]─[~/Downloads]
└──╼ $shasum -a 256 invest_20.dll 
31cf42b2a7c5c558f44cfc67684cc344c17d4946d3a1e0b2cecb8eb58173cb2f  invest_20.dll
```

Submit dan dapat flag
```
┌─[✗]─[user@parrot]─[~/Downloads]
└──╼ $nc 10.15.40.20 10003
Jawab pertanyaan-pertanyaan yang telah disediakan:

No 1:
Pertanyaan: Apa SHA-256 hash dari malware yang dimaksud?
Format: sha256 hash: e.g. c8cf761b68c5f693e5cf2d23acdcca1c0295c1f739805d60df46e54e37ada3da
Jawaban: 31cf42b2a7c5c558f44cfc67684cc344c17d4946d3a1e0b2cecb8eb58173cb2f
Correct

Congrats! Flag: JARKOM2024{wow_how_u_solve_this_xTwRYbAyQRFsR8q}
```
