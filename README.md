# Exercise - Chinook database
<p style="text-align=center">Pada repository ini, hanya berisikan latihan saya mengenai data analisis dengan menggunakan syntaks sqlite3 dan python (jupyter). Latihan ini menggunakan database chinook.db yang digunakan sebagai landasan data. Diharapkan dapat menghasilkan <i>insight</i> dari data tersebut. Tujuan dari project ini adalah untuk memenuhi tugas mini course dari Kerier.mu.</p>

## Chapter 1 : SQLITE
### Bagian 1
Load data dengan menggunakan fungsi sqlite 

<table>
  <tr>
<td> connection1 = sqlite3.connect("chinook.db") <br>
cursor = connection1.cursor() <br>
cursor.execute("SELECT name FROM sqlite_master WHERE type='table';") <br>
print(cursor.fetchall()) </td> 
  </tr>
 </table>
Dapat dilihat isi dari data chinook memiliki tabel:
<br>
[('albums',), ('sqlite_sequence',), ('artists',), ('customers',), ('employees',), ('genres',), ('invoices',), ('invoice_items',), ('media_types',), ('playlists',), ('playlist_track',), ('tracks',), ('sqlite_stat1',)] 

### Bagian 2
Menjawab pertanyaan: <br>

<h4> 1. Apa 5 genre paling banyak jumlah lagunya </h4>

<p style="text-align=center">Dengan menggabungkan tabel tracks dan genres menggunakan fungsi left outer join akan menghasilkan 5 genre lagu yang paling banyak jumlahnya </p>
<table>
<tr>
  <td>pd.read_sql_query("SELECT * FROM (SELECT GenreId, count(GenreId)as Count_GenreId FROM tracks GROUP BY GenreId) left outer join (SELECT * FROM genres) using(GenreId) ORDER BY Count_GenreId DESC limit 5",connection1) </td>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/1.png?raw=true"></img>

jadi, dapat dilihat untuk 5 genre paling banyak jumlah lagunya yang pertama adalah Rock, Latin,Metal,Alternative & punk, Jazz!<br>
<br>

<h4> 2. Apa 3 jenis media yang paling banyak dipakai ? </h4>

<p style="text-align=center">menggabungkan tabel tracks dan media_type dengan metode left outer join akan menghasilkan jenis-jenis media paling banyak di pakai </p>
<table>
  <tr>
  <td> pd.read_sql_query("SELECT * FROM (SELECT MediaTypeId,count(MediaTypeId)as Count_MediaTypeId FROM tracks GROUP BY MediaTypeId) left outer join (SELECT * FROM media_types) using (MediaTypeId) ORDER BY Count_MediaTypeId DESC limit 3",connection1) 
   </td>
  </table>
 <h4> 3. 5 lagu apa yang durasinya paling lama? </h4>
 <p style="text-align=center">Menggunakan tabel tracks akan dapat diketahui lagu mana yang memiliki durasi yang lama, jadi: </p>
 <table>
  <tr>
    <td> pd.read_sql_query("SELECT Name,Milliseconds FROM tracks ORDER BY Milliseconds DESC limit 5",connection1) </td>
  </tr>
 </table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/2.png?raw=true"></img>


<h4> 4. Rata-rata setiap genre itu durasi lagunya berapa lama? genre mana yang durasinya paling lama dan durasi yang paling sedikit?</h4>

<p style="text-align=center">Untuk mengetahui durasi lagu, dapat menggunakan tabel tracks dan genres dengan menggunakan operator left outer join.akan tetapi kita harus merubah waktu yang ada di kolom Milliseconds menjadi waktu yang kita ketahui yaitu HH:MM:SS.</p>

Dengan menggunakan syntaks berikut, dapat merubah format dari waktu:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/3.png?raw=true"></img>

Langkah selanjutnya melihat rata-rata durasi genre yang di kombinasikan tabel tracks dengan genres:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId)",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/4.png?raw=true"></img>

Untuk mengetahui Genre lagu yang durasinya paling lama dapat menggunakan syntaks berikut:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Name,max(durasi) FROM(SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId))",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/5.png?raw=true"></img>

Kemudian, untuk mengetahui genre lagu yang durasinya paling sedikit:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Name,min(durasi) FROM(SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId))",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/6.png?raw=true"></img>

<p> 5. Customers paling banyak membeli lagu di bulan apa? </p>

Dengan memilih data yang akan kita gunakan yaitu CustomerId,InvoiceDate yang akan kita pisahkan Date and Time sehingga yang dibutuhkan hanya Date saja, dan kolom Total. sehingga syntaksnya:
<br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT CustomerId,DATE(InvoiceDate)as Date,Total FROM invoices",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/7.png?raw=true"></img>

Kemudian dapat dicari dengan menggunakan syntaks customer yang paling banyak membeli lagu berada di bulan:
<br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT CustomerId,Date,max(Total)as Pembelian FROM (SELECT CustomerId,DATE(InvoiceDate)as Date,Total FROM invoices) ORDER BY Total",connection1) </td>
  </tr>
</table>

<img src="https://github.com/MrNoTaiL/Exercise---Chinook-database/blob/Initial-commit/images/8.png?raw=true"></img>


## Chapter 2 : Python
Dapat langsung di coba dengan file tugas_akhir.ipynb
