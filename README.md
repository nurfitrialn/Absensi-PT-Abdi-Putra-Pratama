/*
Struktur folder:
- absensi/
  |- index.php         (Form absensi masuk & pulang)
  |- login.php         (Halaman login)
  |- admin.php         (Halaman admin dengan filter dan export)
  |- proses_login.php  (Cek login)
  |- logout.php        (Logout user)
  |- koneksi.php       (Koneksi database)
  |- simpan_absen.php  (Proses absen masuk & pulang)
  |- export_excel.php  (Export data ke Excel)
  |- style.css         (Desain tampilan website)
*/

// style.css
body {
  font-family: Arial, sans-serif;
  background: #f0f2f5;
  margin: 0;
  padding: 0;
  text-align: center;
}
.container {
  background: white;
  width: 90%;
  max-width: 500px;
  margin: 50px auto;
  padding: 20px;
  box-shadow: 0 0 10px rgba(0,0,0,0.1);
  border-radius: 8px;
}
input, button, select {
  padding: 10px;
  margin: 10px 0;
  width: 100%;
  box-sizing: border-box;
  font-size: 16px;
}
button {
  background: #007bff;
  color: white;
  border: none;
  cursor: pointer;
}
button:hover {
  background: #0056b3;
}
a {
  color: #007bff;
  text-decoration: none;
}
a:hover {
  text-decoration: underline;
}
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}
th, td {
  padding: 10px;
  border: 1px solid #ccc;
}
th {
  background: #f5f5f5;
}

// login.php
<!DOCTYPE html>
<html>
<head>
  <title>Login</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
<div class="container">
<h2>Login</h2>
<form action="proses_login.php" method="post">
  <input type="text" name="username" placeholder="Username" required>
  <input type="password" name="password" placeholder="Password" required>
  <button type="submit">Login</button>
</form>
</div>
</body>
</html>

// index.php
<?php
session_start();
include 'koneksi.php';
if (!isset($_SESSION['user_id'])) header("Location: login.php");
?>
<!DOCTYPE html>
<html>
<head>
  <title>Absensi</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
<div class="container">
<h2>Form Absensi</h2>
<form action="simpan_absen.php" method="post">
  <input type="text" name="nama" placeholder="Nama Lengkap" required>
  <button type="submit" name="absen_masuk">Absen Masuk</button>
  <button type="submit" name="absen_pulang">Absen Pulang</button>
</form>
<a href="logout.php">Logout</a>
</div>
</body>
</html>

// admin.php
<?php
session_start();
include 'koneksi.php';
if ($_SESSION['role'] !== 'admin') die("Akses ditolak.");
$tanggal = isset($_GET['tanggal']) ? $_GET['tanggal'] : date("Y-m-d");
$query = mysqli_query($conn, "SELECT * FROM absensi WHERE DATE(waktu_masuk)='$tanggal'");
?>
<!DOCTYPE html>
<html>
<head>
  <title>Data Absensi</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
<div class="container">
<h2>Data Absensi - <?= $tanggal ?></h2>
<form method="get">
  <input type="date" name="tanggal" value="<?= $tanggal ?>">
  <button type="submit">Filter</button>
</form>
<table>
<tr><th>Nama</th><th>Masuk</th><th>Pulang</th></tr>
<?php while ($row = mysqli_fetch_assoc($query)) { ?>
<tr>
  <td><?= $row['nama'] ?></td>
  <td><?= $row['waktu_masuk'] ?></td>
  <td><?= $row['waktu_pulang'] ?: '-' ?></td>
</tr>
<?php } ?>
</table>
<br>
<a href="export_excel.php?tanggal=<?= $tanggal ?>">Export ke Excel</a>
<br><br><a href="logout.php">Logout</a>
</div>
</body>
</html>
