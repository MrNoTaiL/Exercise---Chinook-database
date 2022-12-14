# Exercise---Chinook-database
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

<p> 1. Apa 5 genre paling banyak jumlah lagunya </p>

Dengan menggabungkan tabel tracks dan genres menggunakan fungsi left outer join akan menghasilkan 5 genre lagu yang paling banyak jumlahnya <br><br>
<table>
<tr>
  <td>pd.read_sql_query("SELECT * FROM (SELECT GenreId, count(GenreId)as Count_GenreId FROM tracks GROUP BY GenreId) left outer join (SELECT * FROM genres) using(GenreId) ORDER BY Count_GenreId DESC limit 5",connection1) </td>
</table>
<br>
![1](https://user-images.githubusercontent.com/98092595/207577922-05cc3d96-1196-4fb5-862d-2c0e9d559d10.png)

<br><br>
jadi, dapat dilihat untuk 5 genre paling banyak jumlah lagunya yang pertama adalah Rock, Latin,Metal,Alternative & punk, Jazz!<br>
<br>

<p> 2. Apa 3 jenis media yang paling banyak dipakai ? </p>

menggabungkan tabel tracks dan media_type dengan metode left outer join akan menghasilkan jenis-jenis media paling banyak di pakai <br><br>
<table>
  <tr>
  <td> pd.read_sql_query("SELECT * FROM (SELECT MediaTypeId,count(MediaTypeId)as Count_MediaTypeId FROM tracks GROUP BY MediaTypeId) left outer join (SELECT * FROM media_types) using (MediaTypeId) ORDER BY Count_MediaTypeId DESC limit 3",connection1) 
   </td>
  </table>
 <p> 3. 5 lagu apa yang durasinya paling lama? </p>
 Menggunakan tabel tracks akan dapat diketahui lagu mana yang memiliki durasi yang lama, jadi: <br><br>
 <table>
  <tr>
    <td> pd.read_sql_query("SELECT Name,Milliseconds FROM tracks ORDER BY Milliseconds DESC limit 5",connection1) </td>
  </tr>
 </table>
<br>
![2](https://user-images.githubusercontent.com/98092595/207580849-c6d28ab0-17ec-4aba-9a00-6641874b297f.png)
<br><br>

<p> 4. Rata-rata setiap genre itu durasi lagunya berapa lama? genre mana yang durasinya paling lama dan durasi yang paling sedikit?</p>

Untuk mengetahui durasi lagu, dapat menggunakan tabel tracks dan genres dengan menggunakan operator left outer join.akan tetapi kita harus merubah waktu yang ada di kolom Milliseconds menjadi waktu yang kita ketahui yaitu HH:MM:SS
<br>
Dengan menggunakan syntaks berikut, dapat merubah format dari waktu:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks",connection1) </td>
  </tr>
</table>
<br>
![3](https://user-images.githubusercontent.com/98092595/207586481-d736be12-63dc-48da-a9fc-2da2b00c5923.png)
<br><br>
Langkah selanjutnya melihat rata-rata durasi genre yang di kombinasikan tabel tracks dengan genres:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId)",connection1) </td>
  </tr>
</table>
<br>
![4](https://user-images.githubusercontent.com/98092595/207590444-d9a7ae49-f596-41d1-99a7-cb8564ac9e72.png)
<br><br>
Untuk mengetahui Genre lagu yang durasinya paling lama dapat menggunakan syntaks berikut:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Name,max(durasi) FROM(SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId))",connection1) </td>
  </tr>
</table>
<br>
![5](https://user-images.githubusercontent.com/98092595/207590861-47e78ec3-3678-4bc5-8350-e4a6ee1adab0.png)
<br><br>
Kemudian, untuk mengetahui genre lagu yang durasinya paling sedikit:
<br><br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT GenreId,Name,min(durasi) FROM(SELECT * FROM(SELECT GenreId,durasi,ROUND(new_durasi)as new_durasi FROM(SELECT GenreId,durasi,AVG(durasi) as new_durasi FROM (SELECT GenreId,Milliseconds,time(Milliseconds,'unixepoch')as durasi FROM tracks) GROUP BY GenreId))left outer join (SELECT * FROM genres) using(GenreId))",connection1) </td>
  </tr>
</table>
<br>
![6](https://user-images.githubusercontent.com/98092595/207590906-47178704-47c9-4737-a3b4-f2590130a5ca.png)
<br><br>
<p> 5. Customers paling banyak membeli lagu di bulan apa? </p>

Dengan memilih data yang akan kita gunakan yaitu CustomerId,InvoiceDate yang akan kita pisahkan Date and Time sehingga yang dibutuhkan hanya Date saja, dan kolom Total. sehingga syntaksnya:
<br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT CustomerId,DATE(InvoiceDate)as Date,Total FROM invoices",connection1) </td>
  </tr>
</table>
<br>
![7](https://user-images.githubusercontent.com/98092595/207592075-55b1bceb-30a0-4415-aa24-e531e966d1e7.png)
<br><br>
Kemudian dapat dicari dengan menggunakan syntaks customer yang paling banyak membeli lagu berada di bulan:
<br>
<table>
  <tr>
    <td> pd.read_sql_query("SELECT CustomerId,Date,max(Total)as Pembelian FROM (SELECT CustomerId,DATE(InvoiceDate)as Date,Total FROM invoices) ORDER BY Total",connection1) </td>
  </tr>
</table>
<br>
![8](https://user-images.githubusercontent.com/98092595/207592592-e4c9d47e-38d2-4a00-bb8a-8ac11b98cf05.png)
<br><br>

## Chapter 2 : Python
Dapat langsung di coba dengan file tugas_akhir.ipynb
