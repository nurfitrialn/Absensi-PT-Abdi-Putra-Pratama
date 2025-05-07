# Absensi-PT-Abdi-Putra-Pratama
Absensi Untuk Karyawan PT Abdi Putra Pratama
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
*/

// koneksi.php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "absensi";
$conn = mysqli_connect($host, $user, $pass, $db);
if (!$conn) {
    die("Koneksi gagal: " . mysqli_connect_error());
}
?>

// login.php
<!DOCTYPE html>
<html>
<head><title>Login</title></head>
<body>
<h2>Login</h2>
<form action="proses_login.php" method="post">
  <input type="text" name="username" placeholder="Username" required><br>
  <input type="password" name="password" placeholder="Password" required><br>
  <button type="submit">Login</button>
</form>
</body>
</html>

// proses_login.php
<?php
session_start();
include 'koneksi.php';
$user = $_POST['username'];
$pass = $_POST['password'];
$query = mysqli_query($conn, "SELECT * FROM users WHERE username='$user' AND password='$pass'");
$data = mysqli_fetch_assoc($query);
if ($data) {
    $_SESSION['user_id'] = $data['id'];
    $_SESSION['role'] = $data['role'];
    header("Location: index.php");
} else {
    echo "Login gagal.";
}
?>

// logout.php
<?php
session_start();
session_destroy();
header("Location: login.php");
?>

// index.php
<?php
session_start();
include 'koneksi.php';
if (!isset($_SESSION['user_id'])) header("Location: login.php");
?>
<!DOCTYPE html>
<html>
<head><title>Absensi</title></head>
<body>
<h2>Form Absensi</h2>
<form action="simpan_absen.php" method="post">
  <input type="text" name="nama" placeholder="Nama Lengkap" required><br>
  <button type="submit" name="absen_masuk">Absen Masuk</button>
  <button type="submit" name="absen_pulang">Absen Pulang</button>
</form>
<a href="logout.php">Logout</a>
</body>
</html>

// simpan_absen.php
<?php
session_start();
include 'koneksi.php';
$user_id = $_SESSION['user_id'];
$nama = $_POST['nama'];
$tanggal = date("Y-m-d");
$waktu = date("Y-m-d H:i:s");

if (isset($_POST['absen_masuk'])) {
    mysqli_query($conn, "INSERT INTO absensi (user_id, nama, waktu_masuk) VALUES ('$user_id', '$nama', '$waktu')");
} elseif (isset($_POST['absen_pulang'])) {
    mysqli_query($conn, "UPDATE absensi SET waktu_pulang='$waktu' WHERE user_id='$user_id' AND DATE(waktu_masuk)='$tanggal'");
}
header("Location: index.php");
?>

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
<head><title>Data Absensi</title></head>
<body>
<h2>Data Absensi - <?= $tanggal ?></h2>
<form method="get">
  <input type="date" name="tanggal" value="<?= $tanggal ?>">
  <button type="submit">Filter</button>
</form>
<table border="1">
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
</body>
</html>

// export_excel.php
<?php
include 'koneksi.php';
$tanggal = $_GET['tanggal'] ?? date("Y-m-d");
header("Content-Type: application/vnd.ms-excel");
header("Content-Disposition: attachment; filename=absensi_$tanggal.xls");

$query = mysqli_query($conn, "SELECT * FROM absensi WHERE DATE(waktu_masuk)='$tanggal'");
echo "<table border='1'>
<tr><th>Nama</th><th>Masuk</th><th>Pulang</th></tr>";
while ($row = mysqli_fetch_assoc($query)) {
    echo "<tr>
      <td>{$row['nama']}</td>
      <td>{$row['waktu_masuk']}</td>
      <td>" . ($row['waktu_pulang'] ?: '-') . "</td>
    </tr>";
}
echo "</table>";
?>
