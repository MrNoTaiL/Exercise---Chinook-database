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
![1](https://user-images.githubusercontent.com/98092595/207577922-05cc3d96-1196-4fb5-862d-2c0e9d559d10.png)

<br>
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
