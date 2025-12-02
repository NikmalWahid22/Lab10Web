- Nama: Muhamad Nikmal Wahid
- Kelas: TI 24 A3

# PRATIKUM 10 - PHP OOP
## Mobil.php
```
<?php
/**
 * Program sederhana pendefinisian class dan pemanggilan class.
 **/
class Mobil
{
    private $warna;
    private $merk;
    private $harga;
    public function __construct()
    {
        $this->warna = "Biru";
        $this->merk = "BMW";
        $this->harga = "10000000";
    }
    public function gantiwarna ($warnaBaru)
    {
        $this->warna = $warnaBaru;
    }
    public function tampilWarna ()
    {
        echo "Warna mobilnya: " . $this->warna;
    }
}
// membuat objek mobil
$a = new Mobil();
$b = new Mobil();
// memanggil objek
echo "<b>Mobil pertama</b><br>";
$a->tampilWarna();
echo "<br>Mobil pertama ganti warna<br>";
$a->gantiwarna("Merah");
$a->tampilWarna();
// memanggil objek
echo "<br><b>Mobil kedua</b><br>";
$b->gantiwarna("Hijau");
$b->tampilWarna();
?>
```
![Gambar 1](mobil.php)

## Form_input.php
```
<?php
/**
 * Program memanfaatkan Program 10.2 untuk membuat form inputan sederhana.
 **/
include "form.php";
echo "<html><head><title>Mahasiswa</title></head><body>";
$form = new Form("", "Input Form");
$form->addField("txtnim", "Nim");
$form->addField("txtnama", "Nama");
$form->addField("txtalamat", "Alamat");
echo "<h3>Silahkan isi form berikut ini:</h3>";
$form->displayForm();
echo "</body></html>";
?>
```
![Gambar 2](form.php)


## Form.php
```
<?php
/**
 * Nama Class: Form
 * Deskripsi: CLass untuk membuat form inputan text sederhan
 **/
class Form
{
    private $fields = array();
    private $action;
    private $submit = "Submit Form";
    private $jumField = 0;
    public function __construct($action, $submit)
    {
        $this->action = $action;
        $this->submit = $submit;
    }
    public function displayForm()
    {
        echo "<form action='". $this->action . "' method='POST'>";
        echo '<table width="100%" border="0">';
        for ($j = 0; $j < count($this->fields); $j++) {
            echo "<tr><td align='right'>". $this->fields[$j]['label'] . "</td>";
            echo "<td><input type='text' name='". $this->fields[$j]['name'] . "'></td></tr>";
        }
        echo "<tr><td colspan='2'>";
        echo "<input type='submit' value='". $this->submit . "'></td></tr>";
        echo "</table>";
        echo "</form>"; // Menambahkan penutup tag form yang hilang di dokumen
    }
    public function addField($name, $label)
    {
        $this->fields [$this->jumField] ['name'] = $name;
        $this->fields [$this->jumField] ['label'] = $label;
        $this->jumField ++;
    }
}
?>
```

## database.php
```
<?php
class Database {
    protected $host;
    protected $user;
    protected $password;
    protected $db_name;
    protected $conn;
    public function __construct() {
        $this->getConfig();
        $this->conn = new mysqli($this->host, $this->user, $this->password, $this->db_name);
        if ($this->conn->connect_error) {
            die("Connection failed: " . $this->conn->connect_error);
        }
    }
    private function getConfig() {
        include_once("config.php");
        $this->host = $config['host'];
        $this->user = $config['username'];
        $this->password = $config['password'];
        $this->db_name = $config['db_name'];
    }
    public function query($sql) {
        return $this->conn->query($sql);
    }
    public function get($table, $where=null) {
        if ($where) {
            $where = " WHERE " . $where;
        }
        $sql = "SELECT * FROM " . $table . $where;
        $sql = $this->conn->query($sql);
        $sql = $sql->fetch_assoc();
        return $sql;
    }
    public function insert($table, $data) {
        if (is_array($data)) {
            foreach($data as $key => $val) {
                $column[] = $key;
                $value[] = "'{$val}'";
            }
            $columns = implode(",", $column);
            $values = implode(",", $value);
        }
        $sql = "INSERT INTO " . $table . " (" . $columns . ") VALUES (" . $values . ")";
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return $sql;
        } else {
            return false;
        }
    }
    public function update($table, $data, $where) {
        $update_value = "";
        if (is_array($data)) {
            foreach($data as $key => $val) {
                $update_value[] = "$key='{$val}'";
            }
            $update_value = implode(",", $update_value);
        }
        $sql = "UPDATE " . $table . " SET " . $update_value . " WHERE " . $where;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }
    public function delete($table, $filter) {
        $sql = "DELETE FROM " . $table . " " . $filter;
        $sql = $this->conn->query($sql);
        if ($sql == true) {
            return true;
        } else {
            return false;
        }
    }
}
?>
```



## INSTRUKSI PRATIKUM DAN TUGAS 
Pertanyaan dan Tugas
Implementasikan konsep modularisasi pada kode program pada praktukum sebelumnya dengan menggunakan class library untuk form dan database connection.

Terdapat perubahan pada beberapa File misal nya index.php, dan dashboard.php

## Index.php
```
<?php
session_start();

// LOAD LIBRARY
require 'libraries/Database.php';
require 'libraries/Form.php';

$db = new Database();
$form = new Form();

// AMBIL PAGE ROUTING
$page = isset($_GET['page']) ? $_GET['page'] : 'dashboard';

// HALAMAN YANG WAJIB LOGIN
$protected_pages = [
    'dashboard',
    'user/list',
    'user/add',
    'user/edit',
    'user/delete'
];

// CEK LOGIN
if (in_array($page, $protected_pages) && !isset($_SESSION['login'])) {
    echo "<script>alert('Login dulu bro!'); window.location='index.php?page=auth/login';</script>";
    exit;
}

// HEADER
require 'views/header.php';


// ROUTING INTI
switch ($page) {

    // MODULE USER (CRUD)
    case 'user/list':
        require 'modules/user/list.php';
        break;

    case 'user/add':
        require 'modules/user/add.php';
        break;

    case 'user/edit':
        require 'modules/user/edit.php';
        break;

    case 'user/delete':
        require 'modules/user/delete.php';
        break;

    // LOGIN & LOGOUT
    case 'auth/login':
        require 'modules/auth/login.php';
        break;

    case 'auth/logout':
        require 'modules/auth/logout.php';
        break;

    // DEFAULT → DASHBOARD
    default:
        require 'views/dashboard.php';
        break;
}


// FOOTER
require 'views/footer.php';

```
File ini berfungsi sebagai router utama dalam aplikasi. Berbeda dari praktikum sebelumnya yang memanggil file CRUD secara langsung, pada praktikum ini index.php membaca parameter page melalui URL seperti index.php?page=user/list. Berdasarkan nilai parameter tersebut, router kemudian memuat file yang ada di dalam folder modules secara dinamis.

## Libraries/Database.php 

```
<?php

class Database {
    private $host = "localhost";
    private $user = "root";
    private $pass = "";
    private $dbname = "latihan1"; 

    public $conn;

    public function __construct() {
        $this->conn = mysqli_connect($this->host, $this->user, $this->pass, $this->dbname);

        if (!$this->conn) {
            die("Koneksi database gagal: " . mysqli_connect_error());
        }
    }

    public function query($sql) {
        return mysqli_query($this->conn, $sql);
    }

    public function get($sql) {
        $result = $this->query($sql);
        return mysqli_fetch_all($result, MYSQLI_ASSOC);
    }

    public function getOne($sql) {
        $result = $this->query($sql);
        return mysqli_fetch_assoc($result);
    }
}

```
File ini merupakan class library yang menggantikan mekanisme koneksi database pada praktikum sebelumnya. Jika sebelumnya koneksi dilakukan melalui variabel $conn yang ditulis berulang kali di setiap file, kini seluruh proses koneksi dan eksekusi query dilakukan melalui class Database. Class ini menyediakan fungsi seperti query(), single(), dan escape() untuk memastikan proses database lebih aman, terstruktur, serta mudah digunakan


## Libraries/form.php 
```
<?php

class Form {

    // Input biasa
    public function input($label, $name, $value = "", $type = "text") {
        return "
            <label>$label</label><br>
            <input type='$type' name='$name' value='$value'>
            <br><br>
        ";
    }

    public function textarea($label, $name, $value = "") {
        return "
            <label>$label</label><br>
            <textarea name='$name'>$value</textarea>
            <br><br>
        ";
    }

    public function select($label, $name, $options, $selected = "") {
        $html = "<label>$label</label><br>";
        $html .= "<select name='$name'>";

        foreach ($options as $value => $text) {
            $isSelected = ($value == $selected) ? "selected" : "";
            $html .= "<option value='$value' $isSelected>$text</option>";
        }

        $html .= "</select><br><br>";
        return $html;
    }

    public function submit($text = "Simpan") {
        return "<button type='submit'>$text</button>";
    }
}

```

File ini berisi class untuk membantu pembuatan elemen-elemen form seperti input field, select box, dan tombol. Meskipun tidak sepenuhnya digunakan pada seluruh file, keberadaan class ini merupakan bagian dari implementasi konsep modularisasi pada praktikum 10. Class ini berfungsi sebagai abstraksi untuk menyederhanakan pembuatan form dan mengurangi penulisan kode HTML form secara berulang.

## Dashboard.php
```
<?php global $db; ?>

<div class="container">
    <h1>Dashboard</h1>

    <div class="dashboard-cards">

        <div class="card">
            <h3>Total Barang</h3>
            <p>
            <?php
            $total = $db->get("SELECT COUNT(*) AS total FROM data_barang")[0];
            echo $total['total'];
            ?>
            </p>
        </div>

        <div class="card">
            <h3>User Login</h3>
            <p>
                <?= isset($_SESSION['username']) ? $_SESSION['username'] : 'Unknown'; ?>
            </p>
        </div>

        <div class="card">
            <h3>Status</h3>
            <p>Online</p>
        </div>

    </div>
</div>
```
File ini menampilkan halaman utama setelah pengguna berhasil login. Isi dashboard terdiri dari statistik sederhana seperti total jumlah barang, nama user yang sedang login, dan status koneksi. Pada praktikum sebelumnya, dashboard tidak menggunakan class database, tetapi pada praktikum ini query menggunakan $db->single() untuk menampilkan jumlah data secara dinamis. Dashboard tetap mempertahankan tampilan dari versi sebelumnya namun kini terintegrasi dalam sistem modular



